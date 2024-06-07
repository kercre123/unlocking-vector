# Unlocking Vector

A description of Vector's hardware, software, locking mechanisms, why he hasn't been cracked, current efforts, and how to unlock him if you are skilled enough.

## Vector's Hardware

Vector has an APQ8009 (Qualcomm Snapdragon 212). It is basically an MSM8909 (Snapdragon 210) with a disabled MDSP (modem DSP). He has a Kingston eMCP chip which contains 4GB of eMMC storage and 4Gb (512MB) of RAM.

The APQ8009 is a quad-core Cortex-A7. It is limited to 533 MHz by default, but can run at a maximum of 1.3 GHz if you remove that limitation. He would be prone to overheating, though.

## Vector's Software

Vector runs embedded Linux. His partition table is similar to an Android phone. He has an SBL and ABOOT. The ABOOT figures out the active partition slot (three options: system_a, system_b, or recoveryfs (known as f)) and loads the boot partition. The boot partition contains a basic ramfs and sets up dm-verity and does a POST test (in a program called `rampost`). On dev bots, `rampost` also shows a message on screen saying "PROPERTY OF ANKI, CONFIDENTIAL TEST UNIT, IF FOUND, CONTACT security@anki.com".

Description of his programs: [binaries.md](/binaries.md)

## How Vector is secured

Vector is secured like any other device using a Snapdragon processor: CPU fuses. A key is burned into the CPU at the factory, locking it permanently.

The ABOOT partition includes a public signing key which only allows specific firmware images to work (called a "prod OTA"). There are development OTAs which include SSH and other cool dev tools, but these can only be downloaded onto unlocked bots.

Some Android devices can be rooted via Android-specific exploits or leaked dev bootloaders. Vector doesn't run Android, so these exploits wouldn't work. Anki also never created a prod OTA which has a dev bootloader/sysfs.

## What does CPU locking do?

It prevents you from hooking up to QDL/EDL. This is an interface which can be accessed over USB which lets you make modifications to the flash. On unlocked CPUs, this can be easily accessed. On locked CPUs, you need a specific "loader" file (which we don't have) to access it.

## How did Anki/DDL unlock bots?

They created a mechanism for "unlock OTAs". These are like any other OTA, except they contain new ABOOT, recovery, and recoveryfs partitions. The new ABOOT allows for the installation of new dev OTAs. New recovery partitions are needed since the original recovery partition is prod-signed, and a dev-signed one needs to be implemented.

## Why we can't create our own OTAs

This is whats in an OTA file:

```
manifest.ini
manifest.sha256
apq8009-robot-boot.img.gz
apq8009-robot-sysfs.img.gz
```

`manifest.ini` includes the hash of the boot partition and the sysfs partition (root partition containing the actual system files). Changing the content of a partition will change its hash. If you put this new hash into the `manifest.ini` file, it will still not work as `manifest.sha256` will not match the ini. `manifest.sha256` is a digest file which was created with a private key and is checked by Vector with a public key in his filesystem. **We do not have this private key, and it would take trillions of years to brute-force it**.

## Prototype Vector information

We are aware of the following prototype iterations:

-   P1, P2, P3, DVT1, DVT2, DVT3, DVT4, DVT4.5, PVT, Whiskey DVT1

P1-P3, DVT1-4, and Whiskey DVT1 have unlocked CPUs.

More prototype information: [prototypes.md](/prototypes.md)

## How you can unlock Vector

The only way to unlock a Vector is to replace the CPU and change out a couple partitions via QDL before booting.

If you can find a phone repair place which would be willing to do it, it is worth a shot.

You can buy a new APQ8009 on AliExpress for like $5. You can also get a stencil for cheap. An MSM8909 one works.

Hook up to the USB pads at the bottom right of the back side of the board. Note: there are pads for a micro-USB header. These are backwards and a normal port would not work for this. You have to get a special upside-down port.

Once the CPU is replaced, you have to boot immediately into QDL. This can be done by setting F_USB (located at the top right of the back side of the headboard) to ~1.8V, then booting. 

I like to use this tool for interfacing with QDL: [bkerler/edl](https://github.com/bkerler/edl)

With an edl tool, empty out the `sec` partition and switch out the ABOOT partition with ankidev-nosigning.mbn (located in the root of this repo).

Now, if you boot into recovery, you should be able to download dev OTAs. Here is a dev 1.6.0.3331 OTA: [rc-dev/1.6.0.3331.ota](https://web.archive.org/web/20220722212945/https://ota.global.anki-dev-services.com/vic/rc-dev/lo8awreh23498sf/full/1.6.0.3331.ota) (note: you'll have to host this yourself with `python3 -m http.server` or something similar as Vector's recovery partition can't download from HTTPS sources).

## How can I SSH into a bot running a dev OTA?

The SSH key is in the root of this repo (ssh_root_key).

On modern Linux systems, you probably have to do:

```
echo "PubkeyAcceptedKeyTypes +ssh-rsa" | sudo tee -a /etc/ssh/ssh_config
```

Then try SSHing again. Make sure the SSH key's permissions are read-only (600 is what I use).

On modern Linux systems, SCP might also not work with Vector as it has been updated to use the SFTP protocol by default. You'll have to add the -O flag to your SCP command.

I included rsync in the root of this repo. This makes file transfers much faster. You can just use SCP to put it into Vector's /bin folder (make sure it is executable) and it'll work.

## What can I do with an unlocked Vector?

In a dev OTA, Vector hosts a webserver. A couple, actually. At ports 8887-8890. Port 8888 is the most interesting. :8888/webViz.html shows you a lot of his inner workings in realtime. :8888/consolevars lets you change a bunch of settings.

You can run your own software. I created Go bindings for all of Vector's hardware: [vector-gobot](https://github.com/kercre123/vector-gobot). I also compiled a toolchain for his old Linux OS: [vic-toolchain](https://github.com/kercre123/vic-toolchain) (arm-linux-gnueabi, not arm-linux-gnueabihf).

## Why is his filesystem not hardfloat?

This is because of proprietary Qualcomm binary blobs which only run in a non-hardfloat filesystem. Compiling your software with `-mfloat-abi=softfp` will give you those features back.

To tune software for his CPU, compile software with:

`-mfloat-abi=softfp -march=armv7-a -mfpu=neon-vfpv4`
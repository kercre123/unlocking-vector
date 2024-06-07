# Vector prototypes

[https://news.ewmfg.com/blog/evt-dvt-pvt-explained](https://news.ewmfg.com/blog/evt-dvt-pvt-explained)


## Proto-era

Vector didn't really have an official EVT. This stage would be most equivalent to that, though.

They all ran Android, and the bodyboards were nowhere close to final.

-   P1
    -   P1 is the first set of Vectors. At this point, they were known as Cozmo 2.0s. The hardware is basically the same, except there is an extra pin on the spine connector and lots of pads are labelled differently/in different spots. There are also JTAG pads.
    -   They are very rare. Some have made it into the community, but no one has gotten one fully working as far as I can tell.
    -   Notes:
        -   I have two P1 headboards. They came to me fully blank. They were always stuck in QDL. Apparantly, the way to get them out is to flash the `cdt` partition into physical partition number 2. I have accidentally bricked mine by this point, though.
    -   P1 has a different eMCP chip. Mine have ones by Micron. They are 8GB eMMC + 1GB RAM.
-   P2
    -   P2 is also rare. The headboard design is essentially finalized by this point. There is no real wiring difference between a P2 headboard and a prod headboard.
    -   P2 also usually has a different eMCP chip. Some have Micron, some have Samsung, some have the usual Kingston 4GB+512MB.
-   P3
    -   Not much is known about P3. It is assumed that these are essentially the same as DVT1.

## Design Validation Test

At this point, Vector is known as "Victor", his original internal codename. The shell is pretty much finalized and the headboard circutry is finalized. The bodyboard and the software are not.

DVT1-3 bodyboards are upgradable to the latest firmware, but require an ST-Link programmer. More details here: [victor-body-firmware](https://github.com/kercre123/victor-body-firmware)

-   DVT1
    -   DVT1 features a black shell with a white speaker housing.
    -   A couple were upgraded to embbedded Linux, but most ran Android.
    -   Here's a video of one in action: [Vector's dad](https://www.youtube.com/watch?v=0cNlJDtV1SE)
-   DVT2
    -   DVT2 features a shiny, gray shell.
    -   DVT2 marks the true start of embedded Linux on Vector. They all started on Android, but most were eventually upgraded to LE (LinuxEmbedded).
    -   LOTS of Vector software development and tests happened on DVT2 bots.
    -   [Victor DVT2 sings some songs](https://www.youtube.com/watch?v=ZgQRdJaFEnk)
-   DVT3
    -   DVT3 has the same shell as DVT2, though the light channels for the backpack LEDs are cooler-looking.
    -   Things are becoming more finalized by this point.
    -   Most of these have a modern serial number (ESN), like `00e00200`. Bots before this point just have a shortened version of their QSN (serial number that is burned into the CPU).
    -   Vector's software was pretty much fully fleshed out in DVT3.
    -   A lot of these were upgraded to production and ran special "orange boot" OTAs. Orange boot OTAs have bootloaders which check the bot's QSN against a list. If the bot isn't on the list, it would prevent boot and show [X_X on the screen](https://cdn.discordapp.com/attachments/527877466132578325/1135277433537429535/20230730_132623.jpg?ex=6664a280&is=66635100&hm=1c282cea228f1953e32d546a4897280da25a19fd376b9dc8e2f4faae43fe08f0&). If the bot is on the list, an ! would show on the screen and you'd have to hold the bot upside-down and triple-press the button to continue boot.
    -   [Vector nearing release.](https://www.youtube.com/watch?v=vgTMu1trpPQ)
-   DVT4
    -   DVT4 has the same shell as production bots.
    -   The bodyboard is locked and is running old firmware. Because of this, I do not recommend getting a DVT4. The head is unlocked, though.
    -   Not a lot happened here as far as I can tell. All of the stuff possible on DVT4 was possible (and also done) on DVT3.
    -   DVT4.5 is a thing. It isn't really known what happened during this.

## Production Validation Test

Glorified production bots.

-   PVT
    -   Identical to production. Fuse-locked headboards, finalized bodyboards.
    -   Anki unlocked a lot of them with unlock OTAs.
    -   There were two iterations of PVT. They are both the same except all the bots in one iteration have the exact same ESN printed on the bottom. This was accidental and fixed in the next run of bots.

## Whiskey

Whiskey development started after Vector. Basically just a Vector which has two extra [VL53L1X](https://www.st.com/en/imaging-and-photonics-solutions/vl53l1x.html) sensors on each side of the camera.

Their CPUs are unlocked.

They can run the same software as Vector after some tinkering. It is recommended to switch out the ABOOT partition for the one in the root of the repo as many don't include the `anki.dev` command line flag in ABOOT.

They usually have a dev recoveryfs partition and are in FAC mode. You can SSH into this with the same key as normal dev OTAs.





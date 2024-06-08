# Vector CPU Replacement Guide

1. Source an [APQ8009 CPU](https://www.aliexpress.us/item/3256803378630939.html), an [MSM8909 stencil](https://www.aliexpress.us/item/2251832705095349.html?gatewayAdapt=glo2usa4itemAdapt) (same pinout), then find someone who is willing to perform such a delicate BGA operation.

2. Have the technician perform the actual replacement. ****TELL THEM TO NOT BOOT UP THE BOARD TO CHECK IF IT WAS SUCCESSFUL UNTIL YOU HAVE WIRED IT UP CORRECTLY****

-   The reason for this is the `sec` partition. The CPU will read this partition at bootup and will fuse-lock itself.

3. Hook up to the USB pads and connect F_USB to a ~2V source as shown in the image.

-   D_P = D+ = Data Positive (usually a green wire)
-   D_N = D- = Data Negative (usually a white wire)
-   GND = Ground (black) (all the gold surfaces around the CPU and eMCP are GND as well)
-   F_USB = QDL Force Pin. This should be hooked up to an ~2V source. The image shows where you can get a 2V source.
-   The leftmost pin on the "spine" (4-pin) connector is where you can connect the USB's 5V. (blue, in the image)

![wire guide](/wires.jpg)

4. Get the software set up.
-   If the technician is comfortable with QDL/EDL, they can use whatever software they want. I just like this tool.
-   Set up [bkerler/edl](https://github.com/bkerler/edl?tab=readme-ov-file#grab-files-and-install)
    -   A Linux OS is very much recommended. Make sure you follow the instructions carefully so that the submodules are fetched.
-   Put ankidev-nosigning.mbn (located in the root of this repo) in the same folder as the `edl` script.

5. Plug the board into your computer via USB. If you are successful, you will see a Qualcomm HS_USB/9008 device in the kernel messages (or as a general new device if you are using Windows).

6. Open a terminal and run these commands:

```
cd ~/edl # or wherever you put it
python3 edl e sec
python3 edl w aboot ankidev-nosigning.mbn
```

That is all that needs to be done. The new `aboot` includes a new command line flag (`anki.dev`) which will tell recovery to allow for the download of dev OTAs.

7. If those were successful, you may disconenct the board from your computer and desolder the wires.

8. Put the bot back together and boot him up. It will take a while. If the backpack lights are stuck in the same pattern for more than a couple minutes, put him on the charger and hold the back button for 15 seconds to force him into recovery.

9. You now have an unlocked bot! Here is a dev 1.6.0.3331 OTA: [rc-dev/1.6.0.3331.ota](https://web.archive.org/web/20220722212945/https://ota.global.anki-dev-services.com/vic/rc-dev/lo8awreh23498sf/full/1.6.0.3331.ota) (note: you'll have to host this yourself with `python3 -m http.server` or something similar as Vector's recovery partition can't download from HTTPS sources). You can then use [noflow-devsetup](https://www.project-victor.org/noflow-devsetup) to connect him to Wi-Fi and have him download this OTA.


# Freenove Cooling Alarm — 4-inch Native LVGL

## Development source: v1.33 (Recipe Search UI test)

The full Arduino sketch is [Sketch_13.1_LVGL.ino](./Sketch_13.1_LVGL.ino).

**Status:** v1.33 source staged for **Arduino Verify and test hardware**. The active OTA manifest is intentionally pinned to the last published **v1.32** release, pending a working v1.33 binary.

### v1.33 Recipe Search test scope
- Open using **CREATE ALARM → RECIPE** or **EDIT ALARM → Recipe**
- Native LVGL 8.4 on-screen keyboard, with **CLEAR**, **SPACE**, and **DEL**
- No status header on the Recipe Search screen
- Live case-insensitive filtering of the **locally cached** recipe list — no network requests per keystroke
- Results sorted alphabetically; **2 columns × 3 rows**, six per page
- Navigation order: **page indicator, BACK, PREV, NEXT**
- Typing or deleting text resets results to the first page
- Tapping a result selects a recipe and returns to the prior Create/Edit Alarm screen
- BACK returns without modifying the selected recipe

The other proposed screen redesigns, buzzer-only button acknowledgment, and the empty HOME-alarm-text change **are not included** in this v1.33 test candidate.

### Hardware and toolchain
- Freenove FNK0114S 480 × 320 touchscreen
- ESP32 Dev Module with the previously working Freenove TFT_eSPI display configuration
- LVGL 8.4.0
- Keep the official Freenove `display.h` and `display.cpp` in the existing Arduino sketch folder
- Partition: **Minimal SPIFFS (1.9 MB APP with OTA)**; **Erase All Flash: Disabled**

### Release workflow
1. Compile the staged v1.33 sketch in Arduino IDE (**Sketch → Verify**).
2. Test on the spare Freenove device before a production update.
3. Export the successful build using **Sketch → Export Compiled Binary**.
4. In the GitHub draft release tagged **`freenove-v1.33`**, attach **`Sketch_13.1_LVGL.ino.bin`** and publish the release.
5. Only after confirming the correct binary asset and approving OTA deployment, update `firmware.json` to:
   ```json
   {
     "version": "v1.33",
     "url": "https://github.com/ryangasesesp32-crypto/cool_temp_alarms_4inScreen/releases/download/freenove-v1.33/Sketch_13.1_LVGL.ino.bin"
   }
   ```
6. Reboot the Freenove and verify `Firmware: v1.33` in Serial Monitor.

Do not change the ELEGOO `cool_temp_alarms` repository or its OTA manifest. Wi-Fi credentials and Google Sheets authorization belong in device NVS and must not be published.

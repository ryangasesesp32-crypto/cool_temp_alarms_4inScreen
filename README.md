# Freenove Cooling Alarm — LVGL 4-inch

## Current development source: v1.32

The complete Arduino sketch is [Sketch_13.1_LVGL.ino](./Sketch_13.1_LVGL.ino).

**Status:** Source staged for testing. Do not activate the v1.32 OTA manifest until a matching, verified compiled binary is attached to a GitHub release.

### Hardware and toolchain
- Freenove FNK0114S, 480 × 320 screen (ST7796), resistive touch
- ESP32 Dev Module; Arduino IDE with ESP32 board support
- LVGL 8.4.0 and the working Freenove TFT_eSPI configuration
- Retain the original Freenove `display.h` and `display.cpp` in the Arduino sketch folder
- Partition: Minimal SPIFFS (1.9 MB APP with OTA); Erase All Flash: disabled

### v1.32 candidate changes
- New HOME layout based on the LCD Wizard design, with monospaced fonts and smart auto-fit for alarm and recipe text
- Separate LED-only startup test removed; combined LED + buzzer test retained
- CREATE bypasses the confirmation screen and uses two short acknowledgment chirps after saving
- Legacy inactive graphics renderer removed; native LVGL retained

### OTA release procedure
1. Verify the v1.32 sketch in Arduino IDE and test it on the spare Freenove first.
2. Use **Sketch → Export Compiled Binary** to export `Sketch_13.1_LVGL.ino.bin`.
3. Create a GitHub release with the exact tag **`freenove-v1.32`** and attach the binary.
4. Verify the binary asset, then update `firmware.json` to:
   ```json
   {
     "version": "v1.32",
     "url": "https://github.com/ryangasesesp32-crypto/cool_temp_alarms_4inScreen/releases/download/freenove-v1.32/Sketch_13.1_LVGL.ino.bin"
   }
   ```
5. Restart the device and review Serial Monitor for OTA success and the installed `Firmware: v1.32`.

Do not change the original ELEGOO `cool_temp_alarms` repository or its OTA manifest. Wi-Fi and Sheets credentials stay in NVS; never commit private credentials.

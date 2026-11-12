# Freenove Cooling Alarm — 4-inch Native LVGL

## Development source: v1.34 — Approved UI consolidation (no keyboard)

The complete current Arduino sketch is [Sketch_13.1_LVGL.ino](./Sketch_13.1_LVGL.ino).

**Status:** Corrected v1.34 source staged for Arduino IDE Verify and physical testing. The **live OTA manifest remains on v1.33**, the last version the user reported installed successfully. Do not activate the v1.34 manifest until a verified, matching compiled binary is attached to a published GitHub release.

### v1.34 changes
- Button acknowledgments use **buzzer only**; reserve LED indications for actual alarms/startup hardware check
- HOME: no placeholder alarm/recipe text when no alarms are pending
- CREATE ALARM: full-width use of horizontal COOK (25%) and RECIPE (75%) selection buttons, editable C1/C2 time fields, red CANCEL and green CREATE
- EDIT ALARM TIME: plain title, time preview, **-1h / -5m / +5m / +1h** adjustment buttons, CANCEL and SAVE
- COOK SELECTION: **3 columns × 3 rows**, alphabetical first names, page display and BACK/PREV/NEXT
- RECIPE SELECTION: **no on-screen keyboard**; restored Venue → Type → Recipe selection with **large recipe buttons** (2 columns × 3 rows), pagination, BACK/PREV/NEXT, and no status header on the recipe list
- Preserve original alarm scheduling defaults, Wi-Fi, Google Sheets, NVS, storage and OTA implementations
- Corrected v1.34 compile-time syntax issue in the button text-fitting function: `caption.indexOf('\n')`

### Hardware / Arduino IDE
- Freenove FNK0114S 480 × 320 touch display; ESP32 Dev Module
- LVGL 8.4.0 and the previously working Freenove TFT_eSPI configuration
- Retain official Freenove `display.h` and `display.cpp` in the Arduino sketch directory
- Partition: **Minimal SPIFFS (1.9 MB APP with OTA)**; **Erase All Flash: Disabled**

### Recommended validation and OTA release sequence
1. Compile the staged v1.34 sketch with **Sketch → Verify** in Arduino IDE.
2. Test on the spare Freenove device. Check alarm functions, buttons, time adjustment, cook selection, and recipe selection.
3. Use **Sketch → Export Compiled Binary**, confirming the binary file is named `Sketch_13.1_LVGL.ino.bin`.
4. Attach that binary to the **draft** GitHub release tagged `freenove-v1.34` and publish it.
5. Verify release URL, asset identity, size, and test results; **only then** activate the v1.34 OTA manifest:
   ```json
   {
     "version": "v1.34",
     "url": "https://github.com/ryangasesesp32-crypto/cool_temp_alarms_4inScreen/releases/download/freenove-v1.34/Sketch_13.1_LVGL.ino.bin"
   }
   ```
6. Reboot the test Freenove to verify `Firmware: v1.34` and confirm the UI behaves as designed.

The separate ELEGOO `cool_temp_alarms` repository and manifest must remain unchanged. Keep private Wi-Fi and Google Sheets credentials in device NVS, not in public source.

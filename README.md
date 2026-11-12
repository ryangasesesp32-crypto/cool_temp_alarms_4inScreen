# Freenove Cooling Alarm — 4-inch Native LVGL

## Development source: v1.35 — Smart Navigation & 18px Minimum UI

The current Arduino sketch is [Sketch_13.1_LVGL.ino](./Sketch_13.1_LVGL.ino).

**Status (2026-11-11):** v1.35 source is committed to `main` for compilation and testing. This source has **not** been verified in Arduino IDE or on the physical Freenove device. The **OTA manifest remains v1.34**, which is the currently published release with a binary asset. Do not update `firmware.json` to v1.35 before a matching, tested `Sketch_13.1_LVGL.ino.bin` exists in the v1.35 release.

### v1.35 changes
- PREV/NEXT navigation is smart: controls turn grey and do nothing when no page or item exists in that direction. Applicable paginated screens keep their original navigation behavior.
- Set **18 px as the minimum active UI font size** throughout this Freenove project; longer dynamic labels use `...` if they cannot fit.
- The startup diagnostic console also uses an 18px minimum font, with fewer, taller rows.
- CREATE ALARM: C1/C2 time buttons display the alarm type and scheduled time only, with no **EDIT TIME** caption. Tapping still opens the existing time editor.
- HOME: SETTINGS button is yellow with black lettering.
- The existing five-chirp due-alarm pattern, alarm timing, Wi-Fi, Google Sheets, storage, OTA behavior, and physical button logic are retained.

### Hardware / Arduino IDE
- Freenove FNK0114S: ST7796 480 × 320 landscape resistive-touch screen; ESP32 Dev Module
- LVGL 8.4.0 and previously working Freenove TFT_eSPI configuration
- Keep vendor `display.h` and `display.cpp` in the sketch directory
- Partition: **Minimal SPIFFS (1.9 MB APP with OTA)**; **Erase All Flash: Disabled**

### Verification and release process
1. Compile `Sketch_13.1_LVGL.ino` using **Sketch → Verify**.
2. Test on the spare Freenove device: pagination limits, text truncation, alarms and chirps, C1/C2 time editing, and touch controls.
3. Export the compiled binary named **Sketch_13.1_LVGL.ino.bin**.
4. Attach the verified binary to the GitHub release tagged `freenove-v1.35`; publish that release.
5. Only after the binary, release URL and device behavior are validated, change [firmware.json](./firmware.json) to `v1.35` with this exact binary URL:

   ```json
   {
     "version": "v1.35",
     "url": "https://github.com/ryangasesesp32-crypto/cool_temp_alarms_4inScreen/releases/download/freenove-v1.35/Sketch_13.1_LVGL.ino.bin"
   }
   ```

6. Verify OTA on the home/test device before promoting the same build to any onsite Freenove device.

The separate ELEGOO `cool_temp_alarms` repository and OTA manifest must remain unchanged. Keep private Wi-Fi and Google Sheets credentials in device NVS, not in public source.

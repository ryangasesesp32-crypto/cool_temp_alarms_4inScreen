# Freenove Cooling Alarm — 4-inch Native LVGL

## v1.41 — Code-review fixes (compiled)

v1.41 is the first build in this repository compiled outside the Arduino IDE: `arduino-cli`, ESP32 core 3.3.12 (ESP-IDF v5.5.5), **ESP32 Dev Module**, **Minimal SPIFFS (1.9MB APP with OTA)**, Freenove's FNK0114S `TFT_eSPI`/`TFT_eSPI_Setups`/`lvgl` 8.4.0 libraries and `display.h`/`display.cpp` from Freenove `Sketch_13.1_LVGL`, ArduinoJson 7.4.3. The binary is 1,658,336 bytes (84% of the OTA slot) and has the same flash header (4MB, DIO, 80MHz) as the published v1.39 binary. **It has not been tested on hardware.**

It keeps the v1.40 solid red Alarm Due border and changes only the following:

- **Alarms created without a verified clock never fired.** They were saved with no due time and stayed silent even after a later synced boot. CREATE is now refused with `CLOCK NOT VERIFIED` until the device has booted with NTP time.
- **Device stayed dark after a boot without Wi-Fi/NTP.** A saved closed-hours state could never clear because the store schedule needs verified time. The screen now opens for that session; the saved closed state is kept for the next verified boot.
- **Wrong CREATE error.** A full alarm list (40) showed `SAVE FAILED - RETRY`. It now shows `ALARM LIST FULL`; a real storage failure still shows `SAVE FAILED - RETRY`.
- **Recipe names longer than 31 characters** were not preselected when editing an alarm's recipe (the saved alarm keeps 31 characters). The match now compares the stored length.
- **Sheet data download:** running out of memory while parsing is reported as `OUT_OF_MEMORY` instead of `INVALID_JSON`.
- **SELECT button:** 30 ms debounce so contact bounce cannot count as a second press.
- **Clear All Alarms failure** is logged to Serial (the old startup-log message could not appear after boot).
- **Build fix:** `cool_mono_bold_12` now guards `.user_data` with `LV_USE_USER_DATA` like the other fonts. The boot banner prints the real firmware version.

Not changed (reviewed, left as is): OTA and Sheets HTTPS still use `setInsecure()` (no certificate check); the Sheets token is still sent in the URL; venues/types are still a fixed list; Config replies over about 4,000 bytes are applied but cannot be backed up to NVS. The buzzer on GPIO18 does not conflict with the display: Freenove's FNK0114S setup uses HSPI on GPIO 12/13/14/15.

- [Arduino source](./Sketch_13.1_LVGL.ino) and identical [v1.41 text snapshot](./CoolingAlarm_Freenove_v1_41.txt).
- **OTA is still v1.39.** [firmware.json](./firmware.json) stays on the published v1.39 binary until a `freenove-v1.41` release with the compiled `Sketch_13.1_LVGL.ino.bin` (SHA-256 `46c351122bfaf61f623008e31136295dc355055b4d05a2627019472dfcb01e9d`) is published.

## v1.40 — Solid red Alarm Due border diagnostic (source only)

This **test build** isolates whether fast LVGL border redraws contribute to the visible screen wave and slower-sounding chirps on the Freenove FNK0114S.

- On ALARM DUE, draw the same-thickness **solid red border** below the status header, continuously visible until the alarm is acknowledged.
- **Do not update any LVGL border properties per chirp**. The screen, recipe, and DONE action remain as before.
- **Do not change** the Google Sheets configurable chirp/gap/rest durations, the non-blocking five-chirp loop, external/onboard LED synchronization, button/confirmation sounds, alarm timing, storage, or OTA behavior.
- This deliberately leaves existing `delay(8)` and LVGL handler behaviour unchanged, so it tests only the effect of removing border flashing.
- [Arduino source](./Sketch_13.1_LVGL.ino) and identical [v1.40 text snapshot](./CoolingAlarm_Freenove_v1_40.txt).
- **OTA is still v1.39.** The [firmware.json](./firmware.json) stays on the published v1.39 binary until v1.40 is compiled, attached to a release, and published. Source changes alone do not update the hardware.

**Hardware test:** keep Config at your chosen chirp/gap/rest settings; compare the actual sound and check whether the screen wave disappears. If chirps are still slow, test timing independently of LVGL as the next experiment.

## v1.39 — Google Sheets configurable Alarm Due buzzer cadence (source ready)

The archived v1.39 [plain-text snapshot](./CoolingAlarm_Freenove_v1_39.txt) targets the Freenove FNK0114S 480×320 LVGL 8.4.0 device. The current Arduino sketch on main has since moved to v1.40 for the solid-border diagnostic.

The **independent** Google Sheets `action=config` endpoint is already read at **every startup**, even when `UpdateDataNow` is `No`. v1.39 adds these settings (milliseconds) to that response:

| Sheet setting | Apps Script JSON key | Example | Accepted range |
| --- | --- | ---: | ---: |
| `Chirp` | `chirp` | 20 | 10–1000 ms |
| `PauseBetweenChirp` | `pauseBetweenChirp` | 20 | 10–5000 ms |
| `PauseBetweenLoop` | `pauseBetweenLoop` | 4000 | 100–120000 ms |

The firmware accepts the exact Sheet-case keys as alternatives. The deployed Apps Script **must include all three fields** in its `{ "success": true, "config": { ... } }` response. Updating the cells alone is **not sufficient** unless Apps Script already forwards them. An example is `"chirp":20, "pauseBetweenChirp":20, "pauseBetweenLoop":4000` in the `config` object.

- Accept numeric JSON values or digit-only strings; **apply all three together** only if all are valid. No partial settings change.
- Persist last-known-good alarm timings in ESP32 NVS; restore them before the network request on reset, and retain them if offline or missing/invalid in the response.
- Defaults on an unconfigured device: five 60ms chirps, four 30ms gaps, 2000ms rest. With the example Sheet values, five chirps occupy 100ms + 80ms gaps + 4000ms rest = **4180ms per cycle**.
- The Alarm Due chirp cadence stays **non-blocking** and continues synchronizing the red LED and flashing screen border.
- **Normal button chirp stays 20ms; confirmation chirps stay 60ms / 20ms / 60ms.** The five-chirp count, alarm scheduling and all other UI and Sheets data-update behavior stay the same.

**Release status:** Source only until Arduino compilation and on-device validation. The [v1.39 release](https://github.com/ryangasesesp32-crypto/cool_temp_alarms_4inScreen/releases) is staged as a **draft** to receive a matching `Sketch_13.1_LVGL.ino.bin`; publishing it activates the v1.39 binary URL. At the user's request, [`firmware.json`](./firmware.json) has **already been staged for v1.39**. **OTA download will fail until a matching compiled binary is attached and the draft release is published.**

## v1.38 — Global button-label font limit (source ready)

The archived v1.38 source is [CoolingAlarm_Freenove_v1_38.txt](./CoolingAlarm_Freenove_v1_38.txt); the current development sketch is now v1.39. Target: Freenove FNK0114S 480 × 320 landscape, LVGL 8.4.0.

- **Every button label** now has a **20px bold maximum**, with centered horizontal/vertical alignment and smart fitting.
- Long captions automatically step down to the available **18px regular** font (project minimum) and use `...` on overflow. One- and two-line buttons retain their permitted line counts.
- One shared caption helper is used for HOME, Settings, Advanced Settings, roster/recipe buttons, editing, confirmation, and other native navigation buttons.
- This cap supersedes the previously approved 24px Edit Alarm time-button font; non-button text (including header, recipe information, and alarm details) retains its current sizes.
- No changes to alarm timing, Sheet APIs, OTA logic, screen touch targets, acknowledgement, or physical buttons.

**Release/OTA status:** v1.37 was already **published** with a compiled binary on November 12, 2026, **before** this 20px typography change. v1.38 is therefore a separate next build so devices already running v1.37 can detect it later. v1.38 compilation and hardware behavior are not yet verified. At the user's request, the OTA [firmware.json](./firmware.json) **now points to v1.38**, even though the [v1.38 release](https://github.com/ryangasesesp32-crypto/cool_temp_alarms_4inScreen/releases) remains a **draft without a .bin**. **Warning:** Until the matching `Sketch_13.1_LVGL.ino.bin` has been attached and the release published, OTA checks may detect v1.38 but downloads will fail. Attach the compiled firmware and publish the draft to make the staged OTA URL usable.

## v1.37 — Published approved screen changes

A snapshot of the v1.37 source is [CoolingAlarm_Freenove_v1_37.txt](./CoolingAlarm_Freenove_v1_37.txt). The [published v1.37 release](https://github.com/ryangasesesp32-crypto/cool_temp_alarms_4inScreen/releases/tag/freenove-v1.37) includes the existing compiled binary. The latest `.ino` on main is now v1.38.

- Center button labels; smart-fit text and a minimum 18px font.
- PREV and NEXT adjacent, grey and untappable when navigation is unavailable.
- **Advanced Settings:** live `Device Mode: Test` / `Device Mode: Production` display.
- **View Roster:** 3 columns × 3 rows, nine cooks per page, first names only, alphabetical order and enlarged gaps.
- **View Recipes:** black background, six purple cards with teal borders, Page | BACK | PREV | NEXT.
- **Recipe Information:** compact recipe panel, left-aligned venue/type, and full-width BACK.
- **Edit Alarm:** equal-width first name / time / c1-c2 controls; AM/PM time (previously up to 24px bold), smart-fit recipe and full PENDING/DONE/CANCELLED labels.
- Alarm timing, Google Sheets, HOME structure, Alarm Due sound/LED/acknowledgement, and confirmation behavior unchanged.

**Verification:** the published v1.37 binary existed before v1.38 was started. It has not been independently tested on hardware here. Do not confuse the published v1.37 binary with the newer v1.38 source.

## v1.36 — Approved screen updates (source staged for testing)

The Arduino source is [Sketch_13.1_LVGL.ino](./Sketch_13.1_LVGL.ino). A matching copy suitable for downloading as plain text is [CoolingAlarm_Freenove_v1_36.txt](./CoolingAlarm_Freenove_v1_36.txt). Target: Freenove FNK0114S 480 × 320 landscape touch display with LVGL 8.4.0.

**Development status:** v1.36 firmware source is on `main`; it has passed source-level checks of navigation hit regions and expected UI code paths. A compiled binary and hardware validation have **not** been performed in this session. The OTA manifest currently targets the published v1.36 release. These notes describe the earlier v1.36 source-staging phase.

### Screen changes
- **View Alarms:** 3 × 2 cards, six alarms per page. First line `c1 02:24 AM Brian`; second line recipe name. No title. Bottom navigation `page | HOME | PREV | NEXT` with disabled grey PREV/NEXT when unavailable. Cancelled alarms remain hidden.
- **Alarm Details:** first-name and 12-hour alarm details, recipe, tappable status. Red **BACK** on left and green **EDIT** on right. Existing status transition rules and due-time restrictions remain unchanged.
- **Confirmation:** on alarm-related confirmations, reuse the exact HOME `homeAutoFit()` sizing and positions for alarm and recipe. Question left-aligned below; red NO on left, green YES on right. All non-alarm confirmations retain their separate message content.
- **Settings:** existing 4-row × 2-column grid, six distinct active button colors; two inactive positions.
- **Advanced Settings:** matching 4-row × 2-column grid, distinct active button colors; three inactive positions. BACK left, HOME right on bottom row.
- **Cooling Info:** c1 60°C → below 21°C in 2 hours; c2 21°C → below 4°C in 6 hours total; existing alarm time scheduling untouched.
- **Cooking Info:** six categories from the supplied `tblCookInfo` reference (83°C, 74°C, 71°C, 70°C, 63°C, 60°C), three entries per page across two pages, wrapped descriptions, pagination at top right, bottom `BACK | PREV | NEXT`, and smart navigation. See reference table below.

### Cooking guide reference — local v1.36 display values
| Item | Temperature |
| --- | ---: |
| Whole Poultry and Stuffed Items | 83°C |
| Ground Meat Items (including Poultry), Shellfish, Pieces of Poultry & Eggs | 74°C |
| Pork | 71°C |
| Fish | 70°C |
| Whole muscle meat (including Roast Beef), (excluding Poultry), fish, and cook-to-order eggs. | 63°C |
| Plants (fruits, vegetables, and grains) | 60°C |

**Data integration caveat:** The current Apps Script data endpoint returns config, cooks, and recipes; it does not yet return `tblCookInfo`/`CoolInfo`. The temperatures above and the cooling targets are **built into the v1.36 screen code for testing**. Automatic updating from the tables requires a future compatible API/data-cache change; do not confuse this display test with live Sheets integration.

### Invariants
- The original HOME screen appearance, layout, and cook naming behavior remain unchanged.
- Existing C1/C2 alarm creation, due-time calculation, alert chirps, LEDs, storage, Google Sheets actions, calibration, and physical SELECT control remain untouched.
- Minimum text size is 18px, with smart wrapping/ellipsis rather than smaller fonts.
- Separate ELEGOO `cool_temp_alarms` repository remains unchanged.

### Testing and OTA sequence
1. In Arduino IDE, open `Sketch_13.1_LVGL.ino` with the same working Freenove `display.h` and `display.cpp`, LVGL 8.4.0, TFT_eSPI and board setup.
2. Select **ESP32 Dev Module**, **Minimal SPIFFS (1.9MB APP with OTA)**, **Erase All Flash: Disabled**; run **Sketch → Verify**.
3. Test the screen navigation, smart button disabled states, six-card alarm pagination, alarm status cycling, confirmations, cooking guide wrapping, and cooking/cooling information on the spare Freenove.
4. Export `Sketch_13.1_LVGL.ino.bin` and attach it to a release with tag `freenove-v1.36`.
5. Once a matching compiled binary has been validated, update `firmware.json` to:

   ```json
   {
     "version": "v1.36",
     "url": "https://github.com/ryangasesesp32-crypto/cool_temp_alarms_4inScreen/releases/download/freenove-v1.36/Sketch_13.1_LVGL.ino.bin"
   }
   ```

6. Test OTA on the home/spare device first. Confirm the booted version and full screen behavior before further rollout.

Do not commit Wi-Fi credentials, private Google Sheets tokens, or private NVS data into GitHub.

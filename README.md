# Freenove Cooling Alarm — 4-inch Native LVGL

## v1.38 — Global button-label font limit (source ready)

The current development sketch is [Sketch_13.1_LVGL.ino](./Sketch_13.1_LVGL.ino) and its identical downloadable plain-text copy is [CoolingAlarm_Freenove_v1_38.txt](./CoolingAlarm_Freenove_v1_38.txt). Target: Freenove FNK0114S 480 × 320 landscape, LVGL 8.4.0.

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

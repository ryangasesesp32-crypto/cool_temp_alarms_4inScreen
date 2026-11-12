# Freenove Cooling Alarm — 4-inch Native LVGL

## v1.37 — Approved screen changes (source and TXT ready)

The latest Arduino source is [Sketch_13.1_LVGL.ino](./Sketch_13.1_LVGL.ino), with an identical downloadable [CoolingAlarm_Freenove_v1_37.txt](./CoolingAlarm_Freenove_v1_37.txt). Target: Freenove FNK0114S 480 × 320 landscape, LVGL 8.4.0.

- **All button labels:** centered horizontally and vertically. **20px bold is the maximum**; automatically fall back to the available 18px regular font where needed, and show `...` if the caption still will not fit. Applies to HOME, Settings, Advanced Settings, selection and editing screens, and confirmation buttons. Non-button text (including HOME information and status header) keeps its existing sizing.
- Keep PREV and NEXT adjacent throughout the navigation. Unavailable directions remain grey and disabled.
- **Advanced Settings:** show live `Device Mode: Test` or `Device Mode: Production` right-justified on the title line.
- **View Roster:** 3 columns × 3 rows, nine cooks per page, first names only, alphabetically sorted, larger spaces, and HOME | PREV | NEXT navigation.
- **View Recipes:** copy Create Alarm's recipe-selection styling: black background, purple cards with teal borders in a 2 × 3 grid, automatic two-line fit, and Page | BACK | PREV | NEXT.
- **Recipe Information:** compact 63px recipe panel with centered smart-fit label, venue and type below, and a 63px full-width BACK button.
- **Edit Alarm:** equal-width first-name / time / c1-c2 top controls; time shown with AM/PM and smart 24px bold font; smart-fit recipe text; full PENDING/DONE/CANCELLED labels.
- Unchanged: alarm scheduling, Google Sheets behavior, existing HOME layout, Alarm Due acknowledgements/chirps/LEDs, confirmation rules, and physical button behavior.

**Testing and release status:** source-level checks and screen touchbox geometry checks passed. Arduino compilation and on-device validation of v1.37 have **not** yet been independently verified here. A **draft** `freenove-v1.37` release exists without a compiled binary. At the user's request, [`firmware.json`](./firmware.json) already advertises **v1.37** and the future `Sketch_13.1_LVGL.ino.bin` asset URL. **CAUTION:** OTA checks may detect v1.37 now, but the download will fail until the matching binary has been uploaded to the draft release and the release published. Compile, attach, verify, then publish as the next manual step.

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

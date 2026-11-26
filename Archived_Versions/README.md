# Archived versions of iQuery (prod line)

Older Power Apps exports of iQuery, kept for history and rollback.
The app we are working on is the single app file in the repo root (.msapp since 16 Nov; before
that, export package .zip files). Don't use the files in this folder for new fixes unless Nofil asks.

Dates are the app's "last saved" time (Header.json, UTC), not the upload date.

## Current version (repo root)

- `iQuery_26_Nov_20261126154018.zip` (export package, v1.55.3, app Id 91ca5a81, same Id as
  `iQuery_Dev_20_Nov.msapp`): last saved 2026-11-25 18:15 UTC (appVersion 2026-11-25T18:15:56Z),
  exported 2026-11-26 15:40 UTC. Base for the next work. Includes Change_History 01-07: every
  Src/*.pa.yaml file is identical to `iQuery.msapp` (checked by script 2026-11-26). Holds the
  flow definitions (Microsoft.Flow: iQuery-Request_Access_Email, iQuery-Submit_Feedback,
  Export-iQuery-ReportData).

## Archived

| File | App version | Last saved (UTC) | Notes |
|---|---|---|---|
| `iQuery.msapp` | v1.55.4 | 2026-11-25 18:25 | Records 01-07. Only code difference from `iQuery_Dev_20_Nov.msapp`: pnqSubject.Items = record 07 AFTER (exact). App Id 12afa482 (different from the 26 Nov package, which has 91ca5a81). Same code as `iQuery_26_Nov_20261126154018.zip`. |
| `iQuery_Dev_20_Nov.msapp` | v1.55.3 | 2026-11-20 16:53 | Records 01-06. Same code as `iQuery_16_Nov_v2.msapp` (every Src/*.pa.yaml identical, checked 2026-11-20); only the description, app Id, control IDs, data source metadata and image region (weu instead of neu) differ. Base for Change_History 07. |
| `iQuery_16_Nov_v2.msapp` | v1.55 | 2026-11-16 17:43 | Records 01-06. All 7 record 06 AFTER blocks match it exactly (checked by script 2026-11-16); those 7 OnSelect formulas are the only difference from `iQuery_16_Nov.msapp`. Published to the TEST environment on 2026-11-16. Same code as `iQuery_Dev_20_Nov.msapp`. |
| `iQuery_16_Nov.msapp` | v1.54.5 | 2026-11-16 16:13 | Records 01-05 (05 = corrected read-back, pasted and saved). Only HiddenSaveButton.OnSelect differs from `iQuery_Dev_154_5`. Base for Change_History 06. |
| `iQuery_Dev_154_5_20261116034138.zip` | - | 2026-11-16 03:40 | Records 01-05 (05 first "stay on the query" version). Published to the TEST environment on 2026-11-16. Last export package: holds the flow definitions (Microsoft.Flow). |
| `iQuery_15_November_20261116022020.zip` | - | 2026-11-15 05:41 | Exported 2026-11-16 02:20 UTC, but identical to `iQuery_Dev_154_4` file by file (record 05 was pasted but not saved before this export). |
| `iQuery_Dev_154_4_20261115054503.zip` | - | 2026-11-15 05:41 | Records 01-04 (04 with both 2026-11-15 corrections). Published to the TEST environment on 2026-11-15. Base for Change_History 05. |
| `iQuery_Dev_154_3_20261115052040.zip` | - | 2026-11-15 05:18 | Records 01-04 with record 04 correction 1 (Label41 still had the _tab.batchDocNo delegation bug) + temporary count_debug_label. |
| `iQuery_Dev_153_20261115042618.zip` | - | 2026-11-13 17:35 | Re-export (2026-11-15) of the same app as `iQuery_Dev_153_20261113174127.zip`. Records 01-03. Record 04 not in it. |
| `iQuery_Dev_153_20261113174127.zip` | - | 2026-11-13 17:35 | Includes Change_History 01, 02 and 03 (02 and 03 applied in dev). Base for Change_History 04. Same app as the 15 Nov re-export. |
| `iQuery_Dev_20261113051208.zip` | - | 2026-11-13 05:09 | Includes Change_History 01 and 02 (02 applied in dev). Base for Change_History 03. |
| `iQuery_Dev_12_Nov_20261113023644.zip` | v1.52 | 2026-11-12 02:02 | Includes Change_History 01. Published to production (confirmed 2026-11-12). Base for Change_History 02. |
| `iQuery_Dev_11_Nov_v2_20261112021619.zip` | v1.52 | 2026-11-12 02:02 | Same app as the 12 Nov export (identical .msapp); first export with Change_History 01. |
| `iQuery_Dev_11_Nov_20261112011618.zip` | v1.51 | 2026-11-04 03:22 | Base for Change_History 01. Production version before that change. |

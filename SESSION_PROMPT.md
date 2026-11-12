# iQuery bug fixing (Claude Code)

Paste this whole file at the start of every new Claude Code session.
Last updated: 2026-11-02

## The most important rule: the zip is the source of truth
- Every recommendation, root cause, bug fix and BEFORE code you give me must come from the actual code in the newest app .zip in the repo. Read it there before you recommend anything.
- `Change_History/` is only a record of changes we made in the past. Use it for background, numbering and history, never as the basis for a fix. Never copy code from a record into a new fix without checking it against the zip first.
- If a Change_History record (or CHANGES_LOG.txt, or anything I or these notes say) doesn't match the zip, the zip wins. Tell me about the mismatch.

## Context
- iQuery is a Power Apps canvas app (Dataverse back end) used live by 500+ people at Bombardier every day.
- I (Nofil) co-own it while the main developer is away. We are NOT the main developers, and we can't fully test big changes without them.
- Our job: find where a reported bug comes from and fix ONLY that, without breaking anything else. A fix that creates a new bug somewhere else is a failure. The same rules apply to small improvements that users ask for (feedback requests).
- I test every change first in the dev environment. Only after it works there does it go to production. Never directly in test or production.
- The app name in dev changes often (I export a new copy whenever I try something), so don't name a specific dev copy. Just say "dev".
- I apply every change by hand in Power Apps Studio. I am NOT a Power Apps expert, so your steps must be very clear and easy to follow.
- After I change the app, I export it and upload the new .zip to the repo. The newest zip is always the current app code.

## Step 1: Get the latest code (do this first, every session)
1. Repo: https://github.com/nofilsiddiqui-2000/ticket_query_prod
   - If the repo is not in this folder yet, clone it. If it is already here, run `git pull` to get the latest version.
2. The repo contains Power Apps "Export package" .zip files. Use the NEWEST one (by date in the file name, or latest commit). Tell me which zip you used. If it's unclear which is newest, ask me.
   - The current app is the .zip in the repo root. Older exports are in `Archived_Versions/` (see its README). Don't use them unless I ask.
3. Extract it into a working folder: `_extracted/<zip name>/`. Add `_extracted/` and `_changes/` to `.git/info/exclude` so they are never committed.
   - Inside the package, find the .msapp file (under Microsoft.PowerApps/apps/...). The .msapp is also a zip, so extract it too.
   - App code: `Src/*.pa.yaml` (one file per screen; App.pa.yaml has App.OnStart and Formulas).
   - Data sources (tables, columns): the files under `References/` in the .msapp.
   - Power Automate flows: `Microsoft.Flow/flows/*/definition.json` in the package.
4. Tell me the app name and the "last saved" date inside the export (`Header.json` -> LastSavedDateTimeUTC in the .msapp). If it looks older than expected, warn me, because Power Apps only exports the last SAVED version.
5. Read the file list in `Change_History/` so you know which changes were already made (history only, see the rule at the top). For each record that is tested (not PENDING TEST or CANCELLED), check that its AFTER code is in the current export. If it isn't, warn me: the zip may be older than that change, or the change was undone. Also list any records still PENDING TEST.
6. Don't summarize the whole app. Confirm the code is loaded and wait for me to describe the bug.

## Background notes
Don't rely on these blindly. Check them against the zip before using them, and tell me if something no longer matches.
- Main screen: PartsQuerySystem. Admin_Screen is for admins. Feedback_Screen also exists.
- Open queries are tabs stored in the collection colOpenTabs (one record per tab). The form controls are shared by all tabs and read the active tab with LookUp(colOpenTabs, TabID = varActiveTabID).
- Saving: btnSaveClose validates, then SubmitForm(Form1) -> Form1.OnSuccess -> Select(HiddenSaveButton), which does the database writes and refreshes.
- varUser is set in App.OnStart with Set(varUser, With(...)), using a LookUp on PNQ_Department_Employee_Tables.
- Group queries: when a query is created with more than 1 part, one PNQ_Backlogs record is created per part. ALL of them, including the first, get BatchDocNo = the first query's DocNo. Single queries have a blank BatchDocNo. When a tab is opened, this is copied into colOpenTabs.batchDocNo.
- Data row limit: believed to be 2000, but it isn't readable from the export. Confirm in Studio (Settings > General) before relying on it.

## Rules
- Read only. Never edit the extracted code, and never delete or change anything else in the repo. The only files you may write are:
  - `_changes/` draft files (one per bug, see "Change log (draft)"): local only, never committed.
  - `Change_History/` records: written and pushed as PENDING TEST once I say I'm OK with a fix, then updated to tested once I confirm it works (see "After I approve a fix" below).
- The only git commits you make are new or corrected Change_History records (and this prompt file, if I ask you to update it). Commit only those files, nothing else.
- Never delete or rewrite an existing Change_History record, even when a new zip is uploaded. If I tell you a recorded change was changed or undone later, add a new record for that. Don't edit the old one. The only allowed edits to an old record are: updating its Status line (tested, cancelled, published to production), and, while it is still PENDING TEST, correcting its AFTER code and test results to what I actually used and saw.
- One bug at a time. Stay on the bug I report. If you notice another problem, mention it in one line at the end, but don't fix it unless I ask.
- No structural changes: don't rename controls, variables or collections, don't move controls, don't change shared logic (colOpenTabs, HiddenSaveButton, App.OnStart, data sources, flows) unless the bug is critical and there is no smaller fix. If you think a bigger change is needed, explain why and wait for my OK.
- Always read the real code in the zip before answering. Never guess what a formula says, and never take it from memory or from a Change_History record. Search the whole extracted app (grep) for every place a control, variable or collection is used.
- If the code doesn't match what I describe, tell me. I may have changed something in Studio after the last export. If a control you need to change is in CHANGES_LOG.txt or Change_History but the export still shows the old code, warn me that the zip may be outdated and ask me to export again.

## How to work on a bug
1. Restate in 1-2 lines what the user did and what went wrong (or what they want). If key information is missing, ask me (one question at a time).
2. Find the code in the extracted zip: the controls and properties involved, and the full flow (what calls what, which variables and collections are set, what is written to the database).
3. Find the root cause. Explain it in plain words. Say clearly what is CONFIRMED from the code and what is only LIKELY. If it can't be confirmed from the code, give me a quick test first (a temporary label, a data check, the browser Network tab).
4. Before proposing a fix, check downstream impact: search everywhere the changed controls, variables and collections are used, and confirm the fix doesn't change anything else.
5. Give the smallest fix that solves the problem.

## How to give me the fix (structured reply in the chat)
Use plain, simple English. Write it as if I'm new to Power Apps Studio. Use this layout:

### Problem
1-2 lines.

### Fix
1-2 lines on what the change does.

### Steps
For EACH change, one numbered step in this format:

STEP 1: <short name of what we're changing>
- Screen: <screen name>
- Find the control: in the Tree view (left panel), open <Screen> > <Container> > <Container> > click <ControlName>
- Property: in the property dropdown (top left, next to the formula bar), select <Property, e.g. OnSelect>
- Action: delete everything in the formula bar and paste this:
  <FULL new formula, ready to paste, in a code block>
- What this changes: 1 line.

Rules for steps:
- Always give the FULL formula for the property, not just the changed lines. Build it from the formula in the zip.
- No "=" at the start of the formula (the .pa.yaml files have one, but the formula bar doesn't take it).
- Give the full Tree view path, so I can find the control without searching.
- If a new control or variable is needed, explain exactly where to add it, how to name it, and every property to set.
- Keep the steps in the order I should do them.

### Test
What to test in dev: the bug scenario, plus normal cases that must still work.

### Change log (draft)
After the steps, write a separate draft file for this bug only: `_changes/YYYY-MM-DD_<Short_Name>_CHANGE_LOG.txt`. Don't append to the big `_changes/CHANGES_LOG.txt` (it holds old, already-implemented work). Drafts are local only and never committed.
- Date, bug name, and a short description.
- For each change, the same block layout as the Change_History record (steps right above that change's BEFORE and AFTER code, BEFORE taken from the zip).
- If I later tell you I used different code than you gave me, update the AFTER part of that entry with what I actually used.

### Ask me
End the reply by asking if I'm OK with the change. Don't write a Change_History record until I say yes.

## After I approve a fix (stage 1: PENDING TEST)
When I say I'm OK with the fix, BEFORE I test it:
1. Create a new file in `Change_History/`, named `YYYY-MM-DD_NN_Short_Clear_Description.txt`. NN is the next running number (01, 02, 03...), so the name says what the change does.
   Example: `2026-10-25_01_DocNo_Header_Shows_Group_Query.txt`
2. Status: `PENDING TEST - not yet tested in dev.`
3. Keep the file short and code-focused, in the same layout as the existing records (the CHANGE block layout below). I copy the code from this file on GitHub straight into Studio, so the AFTER code must be paste-ready:
   - Header: record number, title, date, who made it, type (bug fix or user feedback), status, and the zip it was based on (plus earlier records already made in Studio but not yet in the zip).
   - WHY: the problem or request, and the cause, in plain words.
   - IMPACT / RISK: short. What is and isn't affected (saving, data, flows, other screens).
   - THE CHANGE: one block per change. Each block starts with its own steps, right above its code, so I never have to scroll between a step list and the code:
     ```
     ==================== CHANGE 1 ====================
     STEP 1: Screen: <screen name>
     STEP 2: Tree view: <Screen> > <Container> > ... > <ControlName>
     STEP 3: Property dropdown: <Property, e.g. OnSelect>
     STEP 4: Click in the formula bar, Ctrl+A, paste the AFTER code below.
     What this changes: <one line>
     ---------------------------- BEFORE - CHANGE 1 ----------------------------
     <full current code, taken from the zip>
     ---------------------------- AFTER - CHANGE 1 -----------------------------
     <full new code, paste-ready>
     ---------------------------- END OF AFTER ----------------------------------
     ```
     Then the same for CHANGE 2, and so on, in the order I should do them. No "=" at the start of any code.
   - HOW TO UNDO: which BEFORE code to paste back where.
   - TESTING: "NOT TESTED YET", the impact check, and the test cases.
   - Don't put long "what users will see" sections in the file. Show the on-screen before/after in the chat instead.
4. Commit and push ONLY that file to main, with a clear commit message, and give me the file name.

## After I confirm it works (stage 2: tested)
When I tell you the change works in dev:
1. Update that record's Status line to `Tested OK in dev on <date>. Not yet published to production.`
2. If I used different code than the file shows, correct the AFTER code to what I actually used.
3. Fill in TESTING with the results I reported. Only write down results I actually reported to you.
4. Commit and push ONLY that file.
- If I decide not to make the change, set the Status to `CANCELLED - NOT MADE` and push.
- If I later tell you the change was published to production, add a short line to that record's status.

Code must be clean, simple and work in Power Apps. No over-engineering.

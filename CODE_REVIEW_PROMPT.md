# iQuery — independent code review

Paste this prompt into a NEW chat on its own. No session prompt or previous conversation is needed. I will attach local text files with the proposed BEFORE and AFTER code. The files may also explain the bug or the requested improvement.

## Your job

First download the latest app export zip from GitHub. Then independently review my attached BEFORE and AFTER code against that app. Decide whether the proposed changes fix the stated issue and whether they could break any connected feature, save operation or other app behavior.

Do not stop at comparing BEFORE and AFTER. Trace how the proposed AFTER code would interact with the rest of the app once all proposed changes are applied. The priority is to avoid fixing one bug and creating new ones elsewhere. Treat the previous AI's diagnosis and assurances as claims to verify.

## Context and boundaries

- iQuery is a Power Apps canvas app with a Dataverse back end, used live by 500+ people at Bombardier.
- I (Nofil) co-own it while the main developer is away. I apply changes by hand in Power Apps Studio and test in dev first.
- Use plain English. Review only the proposed changes and the behavior they can affect. Do not redesign the app or suggest unrelated cleanup.
- This is a read-only review. You may download and extract files locally. Do not edit app code, input files, data, flows or history records. Do not commit, push, publish or deploy anything, and do not create change-log records.

## Step 1 — Get the latest app from GitHub

Repository: https://github.com/nofilsiddiqui-2000/ticket_query_prod

1. If the repository is already available locally, check its working state and run `git pull --ff-only`. Otherwise clone it into a local working folder. Preserve existing local work: never reset, clean or overwrite it.
2. Find the newest Power Apps export .zip using the date in the file name and the Git history. If those conflict or the choice is unclear, ask me which export to use. Do not use an old local export without checking GitHub. If access fails, report the limitation and ask for access or the latest zip; do not claim the baseline is current.
3. Extract the zip under `_extracted/<zip name>/`. Find the .msapp under `Microsoft.PowerApps/apps/` and extract it too (it is another zip). Use a fresh extraction, or check an existing extraction against the archive before trusting it. Keep extraction folders untracked; a local `.git/info/exclude` entry is allowed if needed.
4. Read the app formulas in `Src/*.pa.yaml`, including `App.pa.yaml`; the data source definitions under `References/`; and the included Power Automate flows in `Microsoft.Flow/flows/*/definition.json`.
5. State the exact zip, the app name and `Header.json` -> `LastSavedDateTimeUTC`. Distinguish the saved date from the export date. Warn me if the saved date suggests that expected recent edits may be missing.

The actual code in this newest zip is the source of truth. Change_History and other notes are background only. Never take current code from a history record or from the previous AI's response instead of the zip.

## Step 2 — Verify the attached BEFORE and AFTER files

For EVERY proposed property change:

1. Locate its screen, full Tree view path, control and property in the export. Use the identifiers in the attachment or search for the formula. Ask only if the target is still ambiguous.
2. Compare the complete proposed BEFORE formula with the actual exported formula. Decode YAML strings correctly and ignore harmless formatting or line-ending differences.
3. If the logic differs, show the mismatch. Do not approve applying AFTER to a different baseline. Explain what is needed to resolve it, and continue with the checks that are still possible. Do not silently merge code or assume unexported Studio changes exist.
4. Identify every functional difference between BEFORE and AFTER, including extra changes outside the stated fix. Check for missing branches, deleted validation, changed defaults, placeholders and wrong control or field names. Confirm each replacement is a full formula suitable for the specified property; the Studio formula must not include the YAML leading `=`.
5. Read the bug explanation in the attachments if one is provided. A separate explanation from me is optional. If the intended behavior is missing, still review the regression risks; ask about intent only when you need it to judge whether the change solves the issue. Do not invent requirements.

## Step 3 — Trace connected behavior after the change

Search the WHOLE extracted app and the included flow definitions for the affected controls, variables, collections, fields and flow references. Follow both their inputs and their consumers, including indirect dependencies. Search matches alone are not a review: read the connected formulas and explain what happens.

Mentally apply ALL proposed AFTER formulas together to the current app. Compare the resulting behavior with the current behavior, following each affected path from the user's action through state updates, validation and saves to later consumers.

Check the following wherever the actual dependencies make them relevant:

- Event order: OnSelect, OnChange, Reset, navigation, timers, Select calls, SubmitForm, OnSuccess and OnFailure.
- Shared state: initialization, updates, clearing, stale values, switching between open tabs, and controls that read the active tab.
- Save behavior: validation bypasses, writes to the wrong record, blank overwrites, dropped fields, duplicate or partial writes, and error handling.
- Query scenarios: new versus existing queries, single-part versus whole-group editing, opening a group member on its own, and which records receive a change.
- User scenarios: assignment to yourself or to another person, same-department versus other-department changes, and the relevant ownership or permission branches.
- Data and Power Fx: types, blank versus empty text, nested record scope, choice values, collection schemas, filters, delegation and row limits where affected.
- Connected features: field defaults, visibility, display modes, reason inputs, comments/history, attachments, other screens, and flow inputs or outputs.

Check the actual app structure instead of assuming these names or paths are unchanged. Relevant areas may include `colOpenTabs`, `btnSaveClose`, `Form1`, `HiddenSaveButton`, `App.OnStart` and the group (batch) handling. If references lead there, inspect their code in the zip.

Review each proposed change on its own AND the combined result. Identify dependencies between changes. Check that the fix keeps the existing safeguards and normal cases working, not just that the reported bug scenario succeeds.

## Step 4 — Run the formulas in the Power Fx interpreter

Go beyond a static comparison. Use Microsoft's Power Fx interpreter locally to execute the affected BEFORE and AFTER formulas with sample controls and records. Test ordinary inputs, the reported bug, edge cases, and what happens when all changes are applied together. For save paths, execute the validation gate and the queue-building expressions where possible: assert whether the save would proceed, which records would be targeted and which values would be written. Trace the remaining steps through the exported app.

Report exactly which expressions you executed, which controls or records you simulated, what passed or failed, and what needs the canvas runtime to check. Keep it read-only and make no live Dataverse writes.

## Step 5 — Judge the evidence honestly

- Separate CONFIRMED code findings from LIKELY explanations and from behavior that needs a dev test.
- Validate Power Fx syntax, types and side effects in context. If platform behavior is uncertain, consult the official Microsoft documentation or give a targeted dev check; do not guess.
- Do not assume the app's configured row limit, live record counts, security roles or external automations that are not in the package. Point out these limits only when they affect this proposal.
- Do not claim that a static review guarantees nothing can break. State which connected paths were checked and which relevant questions remain open. Never report tests as passed unless I supplied those results.

## Your response

Keep the response focused and easy to act on:

### Verdict

Choose one and give a short reason:

- **PASS STATIC REVIEW — ready for dev testing:** the baseline matches, no actionable regression was found in the inspected paths, and the fix addresses the stated issue (if one was supplied). If the intent was not provided, say explicitly that the fix's effectiveness was not assessed. This is not production approval or a runtime guarantee.
- **CHANGES REQUIRED — do not apply as written:** a concrete defect or regression exists in the proposal.
- **BLOCKED — review incomplete:** a baseline mismatch or missing essential evidence prevents a reliable verdict.

Include the zip, the app name, the saved date, and whether BEFORE matched the export.

### Findings

List actionable issues, most serious first. For each one, give the exact screen/control/property, the scenario that triggers it, why AFTER causes the problem, and the smallest correction needed. Cite the relevant exported code locations. Distinguish confirmed issues from suspected risks. If no issue is found, say so; do not invent findings.

### Connected features checked

Briefly explain the important paths you traced and why the proposed change keeps them working or puts them at risk. Use concrete code evidence. Do not simply say "no downstream impact", and do not claim an uninspected feature is safe.

### Power Fx interpreter results

List the expressions executed, the simulated controls and records, the results, and what still needs the canvas runtime.

### Tests in dev

Give a short numbered list covering the original bug (if known), the connected normal cases and the meaningful boundary cases. State each action and the expected result, including saved values and affected records where relevant.

### Remaining uncertainty

State the relevant open questions and the smallest check needed to resolve each one. If blocked, end with one focused question needed to proceed.

Do not ask me to approve the fix or record it in Git. The deliverable is an independent review of this exact proposed change against the latest app export, with particular attention to regressions in connected features.

## User bugs

(Describe the bug or request here, and attach the BEFORE / AFTER files.)

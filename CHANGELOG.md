# Awesonix Mail release notes for administrators

This file lists every version with its upgrade steps, commands, and operator-facing changes. The web app and the phone app show a shorter changelog for users, in `CHANGELOG.USER.md`.

## 0.115.1–0.115.2 — Standard image labels and a neutral offline archive name · 2026-11-17 to 2026-11-18 (Development)

- **Image labels.** The image now carries four standard labels and no others: `org.opencontainers.image.title`, `version`, `revision`, and the new `created`. The four custom labels of earlier versions are gone. They held the project name, the lab flag, the build number, and the clean or dirty state. The `created` label holds the build time in UTC, and the build number is the Unix form of that time. A script that read a custom label must read a standard label instead.
- **No build attestation.** The image is one plain manifest. Earlier images had a build attestation beside it. A tool that reads the attestation finds none.
- **Offline archive name.** The offline image archive now loads the image as `awesonix-mail:<version>`. Earlier archives held a local development name, and the retag line of the Docker, Synology, and Unraid guides used it. Use the line in the guide of the same release.
- **Upgrade.** No step and no migration. The running service does not change.
- **Rollback.** Start the previous image. It carries the earlier labels, with no `created` label.

## 0.114.5–0.115.0 — Every user has a default calendar · 2026-11-16 to 2026-11-17 (Development)

- **Default calendar.** A user with no calendar of their own now gets one named `Default`. A birthday, Google or ICS calendar does not count as one. The server creates it the first time the user's calendars are read, on the web or on the phone, and after a settings restore. The name is stored in English, and each app shows a label in the user's language while the name is unchanged. A user who has calendars but no default setting gets the setting moved to their first calendar; no new calendar is made. Meeting invitations keep creating their own calendar, named "Calendar" in the language of the receiving account.
- **Protection.** The calendar that the default setting names cannot be deleted or archived. The server answers HTTP 409 with the code `calendar.defaultProtected`, and both apps tell the user to choose another default first. A save or restore with an empty default keeps the stored one.
- **Upgrade.** No step and no migration. The first read creates the missing calendar, and a phone receives it with its next sync. Each creation writes one log line, `calendar-default: ensured case=created|repaired user=<id>`, without an address.

## 0.114.4 — Rule editor folder list stays in the window · 2026-11-16 (Development)

- **Rule editor folder list.** The "move to …" folder list of a rule took a fixed 68vh share of the window, so at about 900 px it opened over the top edge and hid the dialog title, and it drew behind the small rule panel. The list now caps its height at the space the menu has, keeps a collision padding, and draws above the panel. This fixes two reported problems of the folder list.
- **Two rows named "Promotions".** The two rows are two real folders, for example a provider container such as `[Gmail]/Promotions` beside a folder named `Promotions`. The folder list is correct; both rows render the same label.

## 0.114.1–0.114.3 — Same answer for a repeated phone request · 2026-11-15 (Development)

- **Repeated phone requests.** The first answer to a phone settings change, such as adding a mailbox, or to a mailbox connection probe carried the time with nanoseconds on Linux, while the Database keeps microseconds. A repeated request got the stored time, so the two answers differed in the last digits. The time is now cut to microseconds before it is used, so the first answer and a repeated one are identical. No Database change and no upgrade step.

## 0.114.0 — Database set-up in the first-run wizard, with live progress · 2026-11-14 (Development)

- **The wizard asks for the database first.** The set-up wizard has six steps now, and the database is step 1 of 6. The environment variables still win: when the database host and password are set, the wizard does not ask.
- **Fast start and live progress.** The container starts answering within seconds. A status page shows the state, and the health state is `starting` from the first second until the build ends. The build of the database runs in the background while the owner answers the other steps. Progress shows in the page and in the container log.
- **Optional variables.** `AWESONIX_MAIL_DATABASE_HOST`, `_PORT`, `_NAME`, `_USER`, and `_PASSWORD` are optional in the Docker, Synology, TrueNAS, and Unraid templates. An empty value counts as not set. Host and password decide: both empty means the wizard collects the settings. Name and user alone never count as set. A distribution compose file with no database values reaches the wizard.
- **Partial settings.** A set host and password with an empty name or user stops the start and names the missing variable. The Unraid descriptions of port, name, and user say to keep the default.
- **Limits.** The Synology file no longer sets `cpus` and `pids_limit`. The Docker and TrueNAS files show them as commented examples. The Unraid template keeps its limits in ExtraParams.
- **Stop grace period.** The stop grace period is 120 seconds in all templates and in the development compose file, so that a migration step can finish. The Unraid template adds `--stop-timeout=120` to ExtraParams.
- **Health check.** The start period of the image health check is 60 minutes. A person who does not enter the settings sees `starting` for 60 minutes, then `unhealthy`. Docker does not restart the container for that, but a tool that restarts unhealthy containers would.
- **New file `database.json`.** The wizard saves the settings in `database.json` in the secrets folder, next to `master.key`. It is plain text with the mode 0600 and holds the password. Include it in backups.
- **Recovery unlock code.** If the saved settings do not work, the container log prints a one-time unlock code, and the browser can unlock the database step. The code has 8 characters, is valid for 15 minutes, works once, and is void after 5 wrong tries. After an unlock, `database.json.old` holds the old password. Delete it.
- **Languages.** The 14 language packs get 142 new texts each. Agents wrote them, and no native speaker has reviewed them. The existing text `shell.setup.database.managed` now reads `Database · connected`.
- This update adds no database migration.
- **Upgrade.** Update the image and the template. The five database variables may stay as they are, or may be empty. Check the stop grace period of your own compose files.
- **Rollback.** Start the previous image. It reads the same environment variables and the same schema, and it ignores `database.json`. An install that the wizard configured needs the five variables set by hand.
- **Risks.** The password travels over plain HTTP on the local network before 1.0. The database step is open to the first visitor, like the administrator step. A LAN client can burn the unlock code.
- **Risks of the checks.** MariaDB 11.4 does not check the REFERENCES privilege. A dirty schema after a hard kill still needs a documented manual fix. The wizard is tested with MariaDB 11.4.
- **Risk of the build.** A stop or restart of the database server while a build step runs leaves the schema dirty, and the build does not resume. It needs the manual repair in the troubleshooting guide. A server that is only not reachable between steps does not cause this.
- **Not run.** The health state and the start on Synology and Unraid. Real screen readers. A native review of the 142 new texts in the 14 language packs.

## 0.113.0 — British English as the source language, and language selectors for many languages · 2026-11-14 (Development)

- A new pack `en-GB` is named "English" and uses British spelling and words, such as Bin, Auto-bin, Organiser, Colour, and Postcode. It becomes the source and default language. `en-US` stays and is named "English (United States)". It is now a variant of the source (`"variantOf": "en-GB"` in its `info.json`).
- **Existing accounts.** An explicit `en-US` choice stays `en-US`, in the database, in web storage, and on the phone. The migration 000195 rows are not changed, and this update adds no migration. An Automatic account follows the browser or phone language: `en-US` stays `en-US`, `en-GB` is `en-GB`, and a bare `en` or an `en-AU`, `en-CA`, or `en-IN` tag now gives `en-GB`. A stored bare `en` in an old backup follows the same rule.
- **Server texts follow the source.** Some server texts follow the source for an Automatic account. They are the AI answer language "app", briefings, meeting texts, the public note share page, and fallback texts. They change from American to British spelling. The language name that the AI receives for the source is now "English".
- **Language menus.** A language name shows its region when two packs share a language, for example "Englisch (Vereinigtes Königreich)" and "Englisch (Vereinigte Staaten)". Own names are not affected.
- **Language selectors.** Interface language, My language, and the AI answer language use a searchable list on the web and a full-screen picker on the phone. The search matches the own name, the English name, and the code, ignoring case and accents. A name that starts with the typed text ranks first. The components read the language list from the registry, so a new pack folder appears in every selector.
- **Filter languages.** The three presets stay. Custom pick shows the chosen languages as chips with a count. A menu on the web, or a picker on the phone, adds search, Select all, and "Done (N selected)". At most 24 languages can be chosen, and at least one must stay chosen.
- **No contract change.** The requests, the stored values, and the settings backups are the same as before. Filter settings save in order: the first change goes out at once, and the latest list follows. All packs have 11 new messages (`languagePicker.*`); no native speaker reviewed the translations.
- The keyword lists hold both spellings of "licence". The filter-group word limits are unchanged.
- The Android channel name is now "Synchronisation". The iOS permission prompts say "synchronises". The phone needs a new build to follow the default language.
- **Rollback.** Start the previous image. A stored `en-GB` reads as Automatic in an older build, and a settings backup with `en-GB` drops the language with a warning. No data is lost, and no migration needs an undo step.
- **Risks.** With `en-GB` as the interface language, the 24-hour time of the date formats "locale" and "iso" shows no leading zero (4:00, not 04:00). This is what the browser gives for `en-GB`, and the `eu` format already did so. The command-palette keywords keep the old words as well. Some Go diagnostics (error and log texts) keep American words; the list is in the plan folder.
- **Not run.** Real screen readers, soft keyboards, and the Android back gesture on a device were not tested. The Java unit tests of the Android texts and the Xcode project were not run here.

## 0.112.0 — Eight more languages · 2026-11-13 (Development)

- Eight language packs ship with regional codes: Japanese (`ja-JP`), Korean (`ko-KR`), Chinese Simplified (`zh-CN`), Russian (`ru-RU`), Ukrainian (`uk-UA`), Polish (`pl-PL`), Spanish (`es-ES`), and Italian (`it-IT`).
- Before this update the eight were candidates with `"shipping": false`. The owner decided on 2026-11-13 to ship all of them.
- Each language ships in the web app and the phone app. It also ships the Help, the texts from the server, the keyword lists for filter groups, and the iOS permission texts. The iOS texts sit in one `.lproj` folder per language code. The language menus list the new languages in their own names.
- This update adds no database migration. The upgrade order is unchanged: upgrade the server first, then the phone app. Existing accounts keep their languages and their Filter languages lists.
- **Risks.** No native speaker has reviewed any of the eight packs. Agents wrote and reviewed them. The keyword lists are drafts, and mail can land in the wrong group.
- **Not run.** Nobody ran device smoke tests for CJK fonts, line breaking, long Russian, Ukrainian, and Polish labels, or the regional `.lproj` folders on iOS. Nobody checked the Gmail and Apple Mail phrases of the glossaries against the live products. The file `docs/development/localization-candidates.md` lists all doubts.
- Not changed: the number words, the duration units, and the relative-date words still sit in the server code for the five first languages only. The new languages do not get them.
- **Rollback.** Start the previous image, and restore the backup when you want to remove data written after the upgrade. No migration needs an undo step.
  - Going back past 0.111.0: read the guard of migration 000195 in the operations guide.
  - Going back past 0.112.0: check rules with long word lists first (see the caveat below).
- **Keyword lists hold more words.** A has-any filter condition now holds at most 400 words and 8,192 bytes. The earlier limit was 200 words and 4,096 bytes (owner decision of 2026-11-13). With 13 shipping languages, the full expansion of one keyword group held more than 200 words. The largest groups are promotions (237), security (210), service (192), bookings (191), and billing (175). Rule-group conversion and reversal failed for accounts in the filter mode "All languages".
- The web request body limit for a filter rule is now 256 KiB, up from 128 KiB. The limit is `groups * terms * 8,192 + 64 KiB`. The phone reads the new limits from the web contract. The frozen phone copies of contract version 2.1 keep 200 words and 4,096 bytes on purpose.
- **No migration for the new limit.** Rules live in `local_mail_filter_rules.condition_json`, a LONGTEXT column with a JSON_VALID check.
- **Rollback caveat for long lists.** Check rules with long lists before you roll back.
  - The down guard of migrations 000193 and 000194 counts only rules at condition schema 4 or higher. It does not block a plain list of 201 to 400 words.
  - A build older than 0.112.0 refuses to save such a rule, and the phone app drops it. The older incoming path still matches subject lists.
  - The command `rules-groups reverse` writes up to 400 words. Its promise that an older build can read the rules holds only up to 200 words.
  - The phone sends a rule in one command of at most 60 KiB. A rule above that size shows "too large, edit on the web", from about eight full lists.
- **Rule sentences use the marks of the language.** The pack file `client/rule-sentence.json` has four optional fields: `wordSeparator`, `listSeparator`, `clauseSeparator`, and `sentenceEnd`. The defaults are the old ASCII marks. ja-JP and zh-CN set no word space, the list mark `、`, the clause mark `、` (ja-JP) or `，` (zh-CN), and the end mark `。`. ko-KR keeps the defaults. The Japanese result needs a native look.
- Not changed: the briefing assembler still ends its lines with an ASCII period. This stays open.
- **Pack text fixes.** The ko-KR phone text `mail.flaggedOnly` now matches the web folder name. In the eight packs, 26 phone texts `contacts.import.*` now equal the web texts, because the parity tests require it.
- The default text of the Android app is untouched.
- The offline-catalog check of the phone app now verifies every script. A missing or lazy catalog of a new language fails the build.

## 0.111.0 — Regional language codes · 2026-11-13 (Development)

- **Before upgrading, back up the database.** The update adds migration 000195. It rewrites the stored short codes `en`, `hu`, `de`, `nl`, and `fr` to `en-US`, `hu-HU`, `de-DE`, `nl-NL`, and `fr-FR`. It does so in five places: the interface language, My language, the AI answer language, the meeting review language, and the Filter languages lists. It also removes the default value of the column `review_locale`. No stored choice resets to Automatic.
- **Upgrade the server first, then the phone app.** An older phone app keeps sending the short codes. The server resolves each short code and stores the regional code. An older settings backup restores its languages in the same way. A settings export from this version holds regional codes. An older server drops them with a warning, so import such an export only into this version or later.
- **Rollback.** The migration has a down step that moves the five regional codes back to the short codes. A guard refuses the down step while a stored value, such as a later added language, has no short form. Change those values first. The default rollback is a restore of the backup. The operations guide has the steps.
- Every language pack folder is now named `<language>-<REGION>`. The five first packs are `en-US`, `hu-HU`, `de-DE`, `nl-NL`, and `fr-FR`. The validator refuses any other name. It also requires exactly one source pack, which is the fallback language. The code reads the source language from the packs, so the code names no language.
- A language tag that names a region now finds its pack. A tag that equals a pack code matches without regard to case. Otherwise the tag matches the one pack with the same language and script. A tag with two candidates, or with another script, falls back to the source language. The server, the web app, and the phone app give the same answer.
- A stored regional choice is never changed to another code. A stored short code, such as `hu`, resolves to its regional pack, and migration 000195 rewrites the stored short codes.
- The language menus show each language in its own name. The English entry now reads "English (United States)". The menus no longer ask the browser for the own name of a language.
- Not changed: the number words, the duration units, and the relative-date words of the five first languages still sit in the server code. A new language does not get them from its pack.

## 0.110.2–0.110.4 — Dependency security updates · 2026-11-13 (Development)

- This update raises dependency versions to clear the advisories of the third-party check (issue #31). It adds no feature and changes no setting. It adds no migration, and it needs no database backup beyond your usual one.
- Install the new phone app (0.110.2) with the server update. It carries Capacitor 8.6.2, which fixes the critical advisory GHSA-rvm3-566m-v7fv in the Android and iOS shells.
- Server: Go 1.27.4 builds the image (module level `go 1.27.0`, `toolchain go1.27.4`). The Go modules `x/crypto` 0.59.0, `x/net` 0.62.0, `x/text` 0.44.0, `grpc` 1.85.0 and `golang-jwt/jwt` 5.3.1 moved, with the modules that they require. A source build on Windows with Go 1.26 downloads Go 1.27.4 on first use.
- Web app: the editor packages (Tiptap with ProseMirror) moved. The web app main file grows by about 406 KB; the page code is the same.
- The checks of this version covered logins, the settings backup, note import, a mail send between two test accounts, and the compose editor. Web push and Google Calendar had no live account on the test stack. Only the unit tests cover them.

## 0.110.0–0.110.1 — Language packs and filter languages · 2026-11-13 (Development)

- **Before upgrading, back up the database.** The update adds three migrations (000192 to 000194). The first accepts any well-formed language tag as the interface language. The other two add a table for My language and a table for Filter settings. Each migration has a down step that names what it loses. A down step is refused while a rule holds a keyword group.
- Install the new phone app (0.110.0) with the server update. An older phone app does not know the new settings or keyword groups. Install the new app before you create a rule with a keyword group.
- My language is new. It is your mother tongue. You set it next to Interface language in General, Language & region, on the web and on the phone. It does not change the language of the app. Existing accounts start with it empty.
- Filter languages are new. They have their own Filter settings page in the Mail menu, after Rules, on the web and on the phone. Choose "My language + English", "All languages", or "Custom pick". Existing accounts keep all five languages that exist today: English, Hungarian, German, Dutch, and French.
- A rule condition can use a keyword group, such as Invoice and payment words. The rule shows the group by name. It matches only the words of your filter languages. The word panel shows these words for each language. Words that you type yourself match in any language. Suggested rules and rule offers count only the words of your filter languages.
- Existing rules keep matching as before. Administrators can convert old rules to keyword groups with the command `awesonix-mail rules-groups convert`. It shows a dry run and changes nothing until you add `--apply --yes --owner`. It converts a rule only after it proves that the rule matches the same words. The command `rules-groups reverse` writes the words back.
- My language and Filter languages are part of the settings export, and the import restores them. The import drops an unknown language code and warns about it. An older server refuses an export from 0.110.0.
- The interface-language menus show each language in its own name, on the web and on the phone. The menus are sorted alphabetically, with Automatic first. The AI answer-language menu shows the names in the app language. The order of these menus changes. The old order (English, Hungarian, German, Dutch, French) no longer applies.
- Help is translated into Hungarian, German, Dutch, and French. What's new stays in English.
- Under the hood: you add a language with one folder and one command, with no code edits. The checks fail if a generated file is out of date.

## 0.109.0 — Full translations · 2026-11-11 (Development)

- **Before upgrading, back up the database.** The update adds one column to the assistant history table (migration 000191). It changes no existing data. Install the new phone app to get the translated phone texts.
- The app speaks Hungarian, German, French, and Dutch in full. Every text of the web app and the phone app was checked again and retranslated where needed. The product owner reviewed the Hungarian texts. Independent reviews checked German, French, and Dutch. No native speaker has reviewed them yet.
- Texts from the server appear in your interface language. This covers assistant action cards (summaries, titles, undo texts, and failures), assistant errors, and settings import warnings. It also covers the suggested name of a rule and notification texts. Older assistant history keeps its English text.
- Phone notifications show their titles, texts, and Android channel names in your language. Web notifications do the same for the generic texts.
- Weekday and month names follow the interface language. The date format setting changes only the order and the numbers. The 12-hour marker follows the interface language too. File sizes on the phone use the decimal mark of your language.
- The public note share page follows the browser language of the reader. It supports Hungarian, German, French, and Dutch, and shows English otherwise.
- Stock folders of your mail provider show a translated label. These are Inbox, Sent, Drafts, Bin, Spam, Archive, All Mail, Starred, Important, Chats, Promotions, Updates, Social, and Forums. The server names do not change. Custom folders keep their names.
- Replies and forwards use "Re:" and "Fwd:" in every language. Local prefixes on incoming mail, such as "AW:" or "WG:", do not stack. The phone writes the "wrote:" line of a reply in your language. It does the same for the forwarded header.
- Rules read as natural sentences in German, Dutch, Hungarian, and French.
- The iOS permission prompts are in Hungarian, German, French, and Dutch.
- The English texts are consistent. "Message" names the object, "Spam" replaces "Junk", and the button is "New message". "Sync" names every sync action. Briefings are "Daily briefing" and "Mail activity briefing". Counts use correct plurals, such as "1 draft" and "2 drafts".
- Mailboxes show "Paused", and user accounts show "Suspended".
- The phone loads its mail settings again for accounts that have a local folder.

## 0.108.0 — Faster, reliable briefings · 2026-11-10 (Development)

- **Before upgrading, back up the database.** This update deletes the data of the old briefing method. Published briefings stay readable.
- Briefings read whole mails in one or a few AI calls. A weekly briefing now takes seconds instead of many minutes. Mail that arrives during a briefing no longer stops it.
- Each AI connection has Context window and Answer limit settings, below Timeout. Leave them empty to use the size that the server reports, or 131,072 tokens.
- A mail that the AI cannot summarise appears as a sender-and-subject line with its reason, and the briefing shows as partial. A briefing fails only when the AI provider is unavailable or too many mails fail.
- A mail stored in two folders counts once.
- Briefings and Insights write all text, including actions, in the configured answer language. Names, references and amounts stay as written in the mail.
- Insights show a short summary and an Action needed list. Account notices with a required step and a date offer a task with that deadline.
- An older briefing whose items are all actions loads its last page again.
- The Vigil counter uses the same style as the notification counter. The phone app's AI connection editor also has Context window and Answer limit.

## 0.107.0–0.107.3 — Complete briefings and Mail workflows · 2026-11-09 (Development)

- Setup validates the administrator user name before advancing; email addresses remain in their separate field.
- Briefings wait for complete periods and required analysis. Clear sections keep Updates and Promotions concise and retain explicit actions, events and birthdays.
- Selection presets affect the current page. Settings keeps Support Awesonix visible; the Awesonix command-palette sequences are g c and j p.
- Create rules from an exact message on web or phone, with editable conditions, separate apply confirmation and retained recovery state.
- My Day provides Calendar and Tasks beside Mail, or below New mail on compact screens, preserving Mail and compose work.
- Dependency maintenance reports remain informational, including critical findings and Unknown coverage. No dependency update or schedule is enabled.
- A TrueNAS Custom App template is prepared; actual platform qualification remains pending.

## 0.104.0–0.105.0 — Meeting guests · 2026-11-06 to 2026-11-07 (Development)

- **Before upgrading, back up the database and update phone apps with the server.** Schema rollback is refused once meetings exist.
- Invite up to 200 guests to your calendar events. Invitations, updates and cancellations send only after confirmation; delivery failures appear beside each guest.
- Control guest invitations and guest-list visibility. Guests on this server invited at their account email can edit when permitted. Editing defaults to off; inviting others and seeing guests default to on. Guests cannot change permissions or cancel for everyone.
- Receive invitations and automatic updates in your calendar, and answer Yes, Maybe or No from mail, Calendar or your phone. Offline answers wait for connection; unavailable sending saves the answer with a notice. Time or recurrence changes reset answers.
- Junk invitations wait until moved out. Signed invitations reject unsigned updates; forwarded guests need permission and a DKIM-signed answer. Response notes remain inside Awesonix Mail.
- Cancelled meetings remain visible until removed. Newer invitations or updates restore removed meetings. Choose whether response emails stay in Inbox or move to Archive.
- Meeting mail does not stay in Sent: provider copies from the first seven days move to Bin, without permanent deletion. Mirror-only mailboxes stay unchanged. DAV apps display meetings; edit guests, time zones and repetition in Awesonix Mail. Settings backups preserve meeting details; restoring sends no mail.

## 0.103.4–0.103.5 — Briefings · 2026-11-03 (Development)

- Briefings arrive at their configured time and retain usable results after partial failures. Weekly and monthly briefings identify their limit of the newest 1,000 analysed messages.
- Ready briefings send an Assistant notification by default; disable it in notification settings.

## 0.103.3 — 2026-11-02 (Development)

- Failed read, star and move changes on the phone settle to server state after three attempts. Unsent mail, edited drafts and destructive operations preserve local content; Mail activity explains failures.

## 0.103.0–0.103.2 — Folder spacing and interface scale · 2026-11-02 (Development)

- Choose Condensed, Normal or Relaxed folder spacing across devices. Phone message lists keep dates visible beside shortened subjects.

## 0.101.6–0.102.0 — Keyboard shortcuts · 2026-11-01 to 2026-11-02 (Development)

- Choose the Awesonix Left-handed shortcut profile. Standard shortcuts add Search, shortcut help, Administration, Delete/Bin and selection-wide read/unread actions, respecting editors and permissions.

## 0.101.3–0.101.5 — Phone mail refresh · 2026-11-01 (Development)

- Phone refresh recovers from stalls and reconnection, preserving saved mail and pending actions. Errors identify connection, access or storage problems.
- Affected phones recover after the server update without an app update.

## 0.101.0–0.101.1 — Filter offers without AI · 2026-10-31 to 2026-11-01 (Development)

- Newsletter filter offers work without AI. Exclude senders or offer again from Rules; backups preserve exclusions, and older imports add their exclusions.
- Folder creation reuses matching folders, retains edits after failures and explains restrictions on read-only mailboxes.

## 0.100.0 — 2026-10-31 (Development)

- Switch interactive mailboxes back to mirror-only. Started provider changes finish; pending changes wait until interactive access returns, without duplicate sends.
- Imported rules retain their enabled state and wait for missing folders. Later edits replace the wait; export names rules with missing folders.

## 0.99.2–0.99.3 — Selecting and marking mail · 2026-10-30 to 2026-10-31 (Development)

- Select the current mail page with one checkbox. Read/unread actions affect the whole selection, including mixed selections.

## 0.99.0–0.99.1 — Smarter Auto-bin rules · 2026-10-30 (Development)

- Rules support sender patterns, multilingual expiry dates and delivery-aware parcel cleanup. Starred mail receives no rule deadline; long-lived mail stays unless explicitly included.
- Filter offers suggest conditions and explain retention decisions. Backups preserve rule conditions and deadlines; incompatible older versions refuse the backup.

## 0.98.3 — 2026-10-30 (Development)

- Rule previews and tall dialogs fit the screen, and duration choices remain accessible.

## 0.98.1–0.98.2 — Readable accent colours · 2026-10-30 (Development)

- Accent colours, focus indicators and unread counts stay readable across themes on web and phone.

## 0.98.0 — 2026-10-30 (Development)

- Build, test, duplicate and reorder rules as editable sentences on web and phone, with match explanations and history. Auto-bin supports one year.
- Matching rules act in order until Stop; later rules can apply other actions after mail is placed. Word lists support whole words, prefixes and phrases.

## 0.94.0–0.97.0 — Mail insights and suggestions · 2026-10-28 to 2026-10-29 (Development)

- Mail highlights suggested tasks, events and contact updates for review. Saving links the item and stops repeat suggestions; existing contact values stay unchanged.
- Newsletters offer filters with conditions and matching-mail previews. Analysis remains opt-in or on request; local models may take about ten seconds longer.

## 0.93.0 — 2026-10-28 (Development)

- Phone notifications open fully saved messages, including trusted images. Failed notification checks retry; deleted messages are excluded.
- Mailboxes open faster, allow early mail actions and support pull-to-refresh with visible progress.

## 0.92.2 — 2026-10-27 (Development)

- Write signatures once; sent mail includes plain-text and HTML forms. Web offers rich-text editing; phones edit plain signatures and display formatted signatures read-only.

## 0.92.0–0.92.1 — Contact names · 2026-10-26 (Development)

- Choose eight contact-name formats or a custom display name. Display, sorting and backups preserve the choice across web and phone.

## 0.90.3–0.91.6 — Themes, palettes and accents · 2026-10-25 (Development)

- Choose Original or Neutral palettes across devices, with clearer text, unread dates and accent controls. HTML mail retains its original light presentation in dark mode.

## 0.90.1–0.90.2 — Portable account settings · 2026-10-25 (Development)

- Settings export supports Android contact mirrors. Importing founder settings into regular accounts preserves their role, mailbox limit and installation policies.

## 0.90.0 — 2026-10-24 (Development)

- Choose three font styles with previews. Your choice follows your account, phone and settings backups.

## 0.85.0–0.89.2 — Notes, sharing and phone support · 2026-10-21 to 2026-10-23 (Development)

- Create formatted notes with images, checklists, tags and reminders on web and phone, including offline edits. Import, export or save mail as notes; conflicts preserve copies.
- Share pages with viewers or editors, or publish expiring, revocable links. Shared additions use the owner's storage; private notes cannot be shared.
- Private notes encrypt text and images with a four-to-eight-digit PIN, automatic locking and optional phone biometrics. Repeated failures slow unlocking; sensitive privacy changes require password and second-factor checks.
- Backups include private notes, but exclude sharing, public links and PINs. Restored private notes remain locked until you set a PIN.
- Administrators can set storage limits and trusted proxies for correct login limits and audit addresses. Phone reminders support local HTTP servers.

## 0.84.0 — 2026-10-21 (Development)

- Open event locations in your chosen web map service or an installed phone map app.

## 0.83.0 — 2026-10-19 (Development)

- Server administrators can use `recover-founder` to recover the founder account. Recovery shows a temporary password once, signs out sessions and optionally removes the authenticator.
- Docker and Synology disable remote diagnostics by default. To enable it, set `AWESONIX_MAIL_DEBUG_LOG_ADDRESS` to `0.0.0.0:8182` and publish the port. Unraid is unchanged. Rule history distinguishes applied, blocked and failed actions.

## 0.82.1–0.82.2 — Installation guides and help · 2026-10-14 (Development)

- Illustrated guides cover installation, database setup, sign-in, browser-first phone pairing, HTTPS, backups and recovery for Docker, Unraid and Synology.

## 0.82.0 — 2026-10-14 (Development)

- Phone navigation includes mailbox folder trees, calendars, task boards, address books and contact labels. Calendar visibility follows your account; due tasks appear in Calendar.

## 0.81.6–0.81.8 — Rich mail on phones · 2026-10-14 (Development)

- Phone mail preserves rich formatting and fits wide messages to the screen. Previously saved messages refresh when opened online.
- Allowed remote images load through your server to protect your device address, limited to normal image formats and 10 MB.

## 0.81.5 — 2026-10-13 (Development)

- Phone fields, lists, checkboxes and grouped settings follow the app's theme, fonts and sizes.

## 0.81.3–0.81.4 — Opening and closing Vigil · 2026-10-13 (Development)

- Closing Vigil restores the previous phone screen. On web, it restores the agenda if it was open before.

## 0.81.2 — 2026-10-13 (Development)

- Downloaded releases include user, administrator, installation, operations and troubleshooting manuals.

## 0.81.0–0.81.1 — Vigil on the phone · 2026-10-13 (Development)

- Phone Vigil shares browser conversations, uses the open item as context and shows mail insights. Prepare, apply and undo changes within your approved access.

## 0.80.1 — 2026-10-13 (Development)

- Search ignores accents, matches word beginnings and covers every folder from Unified inbox. The search index rebuilds once after updating.
- Search reflects changes from other mail clients; missing message content downloads again when opened.

## 0.80.0 — 2026-10-13 (Development)

- Phone synchronisation reduces storage activity and transfers changed calendar and task items. Phones offline over 30 days refresh their mail list.
- First startup converts saved mail; downgrading requires downloading the list again. Mail links open in the appropriate browser or app.

## 0.79.3 — 2026-10-12 (Development)

- Phone mail changes send promptly and resume after interruptions. New mail appears with its notification; mail and contact changes recover from temporary conflicts.

## 0.79.1–0.79.2 — Bulk contact changes · 2026-10-10 (Development)

- Bulk contact deletion recovers from temporary conflicts; clear banners explain remaining errors.

## 0.78.0–0.79.0 — Phone contacts and startup · 2026-10-10 (Development)

- Phone copies avoid duplicate contacts and stay read-only; edit them at their source. Using Awesonix as contact provider enables two-way synchronisation instead.
- **Merge phone copies** previews duplicates and preserves email and phone values after confirmation. Ambiguous names remain for review; the phone starts in your saved appearance and language.

## 0.77.1–0.77.2 — Paired-phone access and recovery · 2026-10-10 (Development)

- Change Full, Read-only and Administration access in browser settings without pairing again. Removing read access requires pairing again.
- Calendar and contact integration reconnects after pairing; a manual reconnect control appears when needed.

## 0.76.3–0.77.0 — Event suggestions and answer language · 2026-10-10 (Development)

- Suggested events retain locations and end times without duplication. Choose an answer language for Vigil; reply drafts retain the message's language.

## 0.76.2 — 2026-10-10 (Development)

- Full phone access includes device management, account security and backups; optional Administration enables administrator changes. Sensitive changes still require password and authenticator confirmation.
- Read-only contacts display their names, and multiline task descriptions no longer prevent Tasks from opening.

## 0.76.1 — 2026-10-10 (Development)

- Vigil suggestions follow messages across folders without duplicate analysis. Suggested appointments open at the detected date and time; unusable suggestions leave other insights intact.

## 0.69.0–0.76.0 — Phone administration and security · 2026-10-09 to 2026-10-10 (Development)

- Phone administrators manage users, mailboxes, synchronisation, retention, diagnostics and storage. Sensitive changes require password and second-factor confirmation; read-only administrator phones cannot change settings.
- Account suspension revokes phones. Other administrators cannot demote, suspend or delete the founder, or reset the founder's password or authenticator.
- Backups and recovery-code saves report cancellation accurately; codes remain visible until confirmed. Sensitive fields clear when the app backgrounds; conflicting edits preserve newer settings.

## 0.65.0–0.68.0 — Account settings on the phone · 2026-10-09 (Development)

- Manage mailboxes, identities, signatures, rules, AI preferences, notifications and account security from your phone. Sensitive changes use the same checks as web.
- Export encrypted backups through Android's file picker or iOS sharing. Imports review contact and calendar sharing; conflicting settings preserve newer changes.

## 0.64.0–0.64.2 — Browser-first phone pairing · 2026-10-09 (Development)

- Pair phones with a one-use, ten-minute browser code. Temporary server failures preserve pairing and local data; local IP addresses support HTTP before 1.0.0.
- Administrators can allow up to 100 mailboxes per user, with 25 by default; existing mailboxes remain available.

## 0.57.9–0.61.0 — Vigil actions and AI improvements · 2026-10-07 to 2026-10-08 (Development)

- Vigil searches your mail, calendars, tasks and contacts and prepares changes for approval, with Undo for reversible actions. Ambiguous matches require clarification; edits preserve unspecified details.
- Standing instructions replace saved facts and migrate into backups. AI privacy settings govern cloud briefings; automatic analysis covers only recent mail received after enabling it.

## 0.57.5–0.57.8 — Mail reading and delivery · 2026-10-06 (Development)

- Phone mail opens faster, reflects actions immediately and preserves HTML formatting. Sent mail appears within minutes, including mail sent from other apps.

## 0.57.3 — 2026-10-03 (Development)

- Automatic mail analysis waits through a busy AI queue without timing out.

## 0.57.2 — 2026-10-03 (Development)

- Deleted messages stay deleted. Updating removes copies that reappeared as unread since 0.56.0.

## 0.57.1 — 2026-10-03 (Development)

- First-run and sign-in guides include screenshots; setup displays the formatted licence agreement.

## 0.57.0 — 2026-10-03 (Development)

- Phone and compact web readers offer a pinned action toolbar with a configurable Reply or Reply all button and the full message menu.
- Android Back closes dialogs before navigating, then backgrounds the app.

## 0.55.0–0.56.3 — Auto-bin and mail responsiveness · 2026-10-02 (Development)

- Schedule Auto-bin manually or with rules on web and phone. Deadlines remain visible, editable and available offline; starring and restoration protect messages.
- New mail and conversation changes appear promptly, including during simultaneous updates. Notifications open the message directly.

## 0.47.3–0.54.3 — 2026-09-10 to 2026-09-24 (Development)

- **Spam is permanently deleted after 30 days, like Bin.** Saving a finite remote-retention period starts automatic enforcement; new installations default to Never.
- Android shares calendars and address books with device apps and copies system contacts into Awesonix. Large folders open faster; browser tabs show unread counts.

## 0.45.0–0.47.2 — Offline phone workspaces · through 2026-09-10 (Development)

- Full and Read-only pairing provide phone access to Mail, Contacts, Calendar, Tasks, Agenda, settings and Vigil. Permitted edits queue offline and preserve recoverable conflicts.
- Opened mail stays offline for 30 days; starred and explicitly saved mail stays indefinitely. Protected local data survives interruptions; failed changes retain content for review.

## 0.44.5–0.44.12 — Consistent phone appearance and controls (Development)

- Phone themes, dates and controls match your preferences. Notification badges show current unread counts; notifications support individual dismissal and marking all read.

## 0.44.4 — Development

- Revoking phone data access clears protected local data and returns the phone to pairing.

## 0.44.3 — Development

- Phone About shows the correct product version, build date and source revision.

## 0.44.0–0.44.2 — Resumable AI analysis and briefings (Development)

- Analysis and briefings resume after interruptions, retaining previous insights and explaining incomplete coverage. Choose whether Vigil receives the current page as context.
- Insights stay on each message; per-email completion alerts are removed, and optional completion notifications default to off.

## 0.41.0 — Development

- The notification centre covers mail, reminders, security, synchronisation and administration. Browser and phone delivery follows configurable quiet hours, previews, sounds and badges.

## 0.39.0 — Development

- Android and iOS offer browser-approved pairing, read-only synchronisation, encrypted offline mail and attachment sharing. Review or revoke phones in browser settings.
- Protected storage secures credentials and mail; revocation or a changed server identity blocks saved data.

## 0.38.0 — Development

- Vigil offers persistent conversations, pinned context, configurable memory, opt-in briefings and actions requiring confirmation. Daily Recap and Mail Activity Briefing have independent schedules.
- Recaps resume after server restarts without duplicate summaries. Inbox mail arrives promptly during large downloads; HTML messages have clearer spacing.

## 0.31.0–0.32.0 — Setup, backups and synchronisation (Development)

- Backups include tasks, local mail, drafts, attachments, pictures and administrator settings. Installations use external MariaDB; Docker and Unraid setup is simpler.
- Connected calendars refresh hourly; new mail arrives during initial downloads, and temporary delays appear as waiting.

## 0.30.0 — Development

- In-product help includes release notes, licences and version details. First-run setup requires licence review and acceptance.

## 0.29.5 — 2026-08-27

- Mail uses Star, Unstar and Starred labels. Task suggestions and editing improve; Calendar Today opens the current month, and idle browsers recover their connection.

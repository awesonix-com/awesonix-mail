# Awesonix Mail Help

## Everyday navigation

Open Mail, Calendar, Tasks, Notes, and Contacts from the left rail. The search in the top bar filters the active Mail, Contacts, Tasks, or Notes workspace. Find your personal preferences and the administrator controls in **Settings**.

## Mail

- The **Select** presets affect only the current message page.
- Use **Create rule** from the message or reader menu; review its preview and any account-wide apply confirmation.
- The calendar icon opens **My Day**. Calendar covers the selected day and next 29 dates; Tasks shows due-today and overdue active tasks.
- On compact screens, My Day fills content below **New message** and retains its toggle. Closing it restores Mail and unsent compose work.

- Add or change mailboxes in **Settings > Account > Mailboxes**.
- Use **Unified inbox** to read all enabled accounts together.
- Star important messages from the toolbar or the message list.
- Create local folders for long-term filing. Local folders do not change the provider mailbox.
- Set synchronisation and local cache limits in **Settings > Mail > Synchronisation & storage**.
- Messages in Bin or Spam are permanently deleted automatically after 30 days in that folder.
- On a phone, the reader toolbar shows one reply button. To choose **Reply all** or **Reply**, open **Settings > Mail > Reading & conversations > Toolbar reply button**.
- If you move a message to Bin or permanently delete it, its related notification is also dismissed. If you restore the message, the old notification does not come back.

### Auto-bin

- To open the message menu, right-click a message, or select **More** (three dots) in the reader.
- In the message menu, use the **Auto-bin** submenu to choose a preset or a date. You can also remove the schedule there.
- If a deadline is active, the menu label shows **Auto-bin:** and a short date.
- The reader shows a clock chip beside the sender only if a deadline is set.
- When the deadline expires, the message moves to Bin, also if it is unread.
- If you star or restore the message, the deadline is cleared.
- Incoming rules can assign Auto-bin presets to new arrivals only.

### Filter languages

- **My language** is your mother tongue. Set it in **Settings > General > Language & region**, next to **Interface language**. It does not change the language of the app.
- Choose the languages of keyword groups in **Settings > Mail > Filter settings**, the page after **Rules**.
- Under **Filter languages**, pick one preset: **My language + English**, **All languages**, or **Custom pick**.
- Keyword groups in rules, such as Invoice and payment words, show by name. They match only the words of the filter languages.
- Words you type yourself in a rule match in any language.
- If My language is not set, or the app does not have your language, only English is used.

## Notifications

### In the app

- Select the bell in the top bar to see unread notifications.
- **Open** goes to the destination, if one is available, and marks the item read.
- **Dismiss** removes the item without opening it.
- **Mark all read** clears the unread list.
- If a destination is missing or you no longer have access to it, the item stays visible with a safe explanation. It does not open unrelated content.

### Delivery settings

Open **Settings > General > Notifications** to do these tasks:

- Turn on external delivery.
- Choose the browser and mobile categories.
- Control the sound and badge intent.
- Hide private previews.
- Set quiet hours.

For browser notifications, select **Allow notifications on this browser**, then give the browser permission. Quiet hours delay only external delivery. In-app items still appear immediately.

### New mail

- The server keeps a grace period of ten seconds for new-mail notifications.
- Visible browser workspaces and active Android or iPhone mail workspaces also get a refresh request before the system alert and sound. They have up to sixty seconds to acknowledge it.
- Overlapping notifications share the refresh work. The background polling frequency does not change.
- If the Android app is closed, it tries to prepare a limited number of notified message bodies in its encrypted cache.
- This preparation is best effort. An offline or suspended app can still show an alert before the content is ready.

### Opening a mail notification

- A mail notification opens that exact email. The current list page or filter does not matter.
- On the phone, the reader shows loading or recovery guidance. It does not silently return to the Inbox.
- The reader automatically retries temporary failures a limited number of times.
- If the message stays unavailable, the reader shows retry and back controls.
- Recovery does not restore deleted messages.

### Reminders

- The server schedules calendar reminders and task due notifications.
- Date-only tasks use the configured reminder time in the Calendar time zone.
- An optional overdue reminder can run once on the next day.
- If you complete, archive, delete, or reschedule an item, its pending reminders that no longer apply are cancelled.

## Mobile app

### Pairing

1. In the browser, open **Settings > Account > Device access**.
2. Choose **Full** or **Read-only** access.
3. Choose any extra approvals: security, backup, device management, or administration.
4. Select **Create pairing code**.
5. In the app on the phone, scan the QR code or type the eight-character code.

The pairing code works once and expires after ten minutes.

### Access levels

- With **Full** access, you can work in Mail, Contacts, Calendar, and Tasks. An encrypted command queue keeps your changes safe if the app restarts.
- With **Read-only** access, the app hides the write controls.
- Contact and calendar mirrors from a provider stay read-only with both access levels. Before you edit them, copy them into an Awesonix-owned collection.

### Using the app

- On Android, use **Device calendar and contacts** in the app to share Awesonix calendars and address books with device apps.
- System contacts on Android also sync one way into Awesonix. This feature is not available on iPhone yet.
- To change Auto-bin on the phone, use the message action sheet. If a deadline is set, tap the clock chip in the reader to open it directly.
- Auto-bin choices made offline stay visibly pending until the server accepts them. If a conflict occurs, the app keeps your choice so you can refresh online and apply it again.
- **Available offline** means that the selected sanitised message and all listed retained objects are complete in encrypted device storage.
- Use **Open or share attachment** to open the file in the native Android or iOS file surface.
- With **Full** access, you can add guests, send meeting mail online, and answer invitations. An answer made offline shows **Waiting to send** and goes out when the phone is online again. The phone **Outbox** lists only these queued answers.
- Update the phone app together with the server. An older app shows meetings without guests and cannot send or answer.
- To wipe this device without deleting server data, use **Account > Remove account**.
- To revoke a lost device, use the browser.

### Settings on the phone

- **Settings** on the phone mirrors the browser.
- Appearance, language, notifications, mail behaviour, and calendar preferences save even offline. They synchronise when the phone reconnects.
- Mailboxes, identities, rules, privacy, mail storage, AI, Vigil behaviour, security, and device access need a connection.
- These connected settings show the current values from the server. The phone refuses changes to them while it is offline.

### Sensitive changes

These changes ask for your account password. If you set up an authenticator, they also ask for your authenticator code.

- Adding or removing a mailbox
- Deleting identities or rules
- Changing AI provider keys
- Changing your password or authenticator
- Using the settings backup
- Changing calendar and contacts (DAV) passwords
- Removing another paired phone

After ten password checks in five minutes, the phone must wait a few minutes.

### Security

In **Settings > Account > Security**, you can do these tasks:

- Change your picture and password.
- Set up or turn off the authenticator.
- Replace your recovery codes.
- Export or import the encrypted settings backup.

On Android, the backup is saved with the system file picker. On iPhone, it is saved with the share sheet.

### Administration on the phone

If you are an administrator and your phone was paired with administration approval, **Administration** and **Diagnostics** appear at the end of Settings. They include these items:

- Users and the mailboxes of all users
- Synchronisation tuning
- Cache quotas and cleanup
- Remote retention
- Notification tests
- Log access tokens
- Search index rebuilds

Every administrative change asks for your password. The change is also checked against your current administrator role.

## AI Assistant

Open **Settings > General > AI configuration** to enable AI features and set up a provider.

1. Choose a supported provider or an OpenAI-compatible endpoint.
2. Enter the endpoint, model, and API key that the provider requires.
3. Test the connection before you save.
4. Review the privacy and mailbox-analysis options before you enable automatic analysis.

### API keys and privacy

- API keys are secret credentials. If possible, create a dedicated key.
- At the provider, restrict the permissions and budget of the key.
- If the key may have been exposed, rotate it.
- If mail content must not leave the installation, you can use a local provider.
- Awesonix Mail does not send product analytics, telemetry, usage monitoring, or automatic crash reports to Awesonix.
- Services that you connect on purpose may receive the data that they need to do their work. Their own terms apply. These services include AI, mail, calendar, contacts, and authentication providers.
- Operational logs and diagnostics stay local to your installation, unless you choose to share them.

### Mail assistant

- Select an email before you use the mail assistant.
- **Re-analyse** runs as background work. You can navigate away or close the browser.
- When you come back, you see the state of the analysis: queued, running, complete, partial, or failed.
- The previous insight stays readable until a new insight is published successfully.
- To retry a failed analysis, use its status card.
- **Clear history** removes the conversation and suggestion state for that message. The next analysis then starts clean.

### Vigil chat

- Vigil shows the exact context that goes with each chat turn.
- To leave out the current page for one turn, clear **Include current page**.
- To remove pinned context that you no longer want, use its remove button.
- Vigil reads the whole messages of the period in a few large groups, one group at a time. A restart does not repeat finished groups.
- A new briefing waits for complete period coverage. Preparation or retry keeps the previous completed briefing readable.
- A message that the AI cannot summarise shows as a sender and subject line, "summarised from subject only". The briefing is then partial. Details gives the reason.
- All briefing text, including actions, uses your Answer language. With **Same as the message**, it uses the app language.
- Each AI connection has **Context window** and **Answer limit** fields below **Timeout**. Leave them empty for the automatic values.
- Insights show a short summary and an **Action needed** list. A notice about your account with a required step and a date can offer a task.
- Briefings use clear sections, concise Updates and Promotions, explicit actions, and upcoming events and birthdays.
- Completion notifications never open Vigil or move the keyboard focus automatically.

## Calendar and tasks

- Use Calendar to combine Awesonix-owned, Google, ICS, and DAV calendars.
- Use **Today** to return to the current date after you browse another period.
- Create task boards to keep personal work and project work separate.
- Review an AI task suggestion before you create the task. If more than one task board exists, choose the destination task board in the editor.

### Meetings

- Add guests to an event in one of your own calendars under **Add guests**, below the time. It suggests contacts and people you wrote to. A meeting can have up to 200 guests besides you.
- In new meetings, **Guest permissions** has **Modify event** off, and **Invite others** and **See guest list** on. Turning on **Modify event** also turns on the other two and locks them.
- After you save, Calendar asks **Send invitation emails?**, **Send update emails?**, or **Send cancellation emails?**. Choose **Send** or **Not now**. Your changes stay saved in both cases.
- Each guest gets a separate mail with an `invite.ics` attachment. Meeting mail does not stay in Sent.
- Each guest shows **Yes**, **No**, **Maybe**, or **Awaiting**, with a note when the guest wrote one. A new date, time, or repetition resets the answers to **Awaiting**.
- **Get an email when guests respond** keeps answer mail in your Inbox. When it is off, answer mail moves to Archive.
- Invitations that you receive are added to your default calendar automatically. Answer with **Yes**, **Maybe**, or **No** on the mail card or in the event details. For a repeating meeting, choose **This event** or **All events**.
- A cancelled meeting stays in your calendar, marked **Cancelled**, until you choose **Remove from calendar**.
- Click an event to open its details. Use **Email guests** to write to the guests.
- DAV apps show meetings but cannot change their time zone, guests, or repetition. They cannot delete a sent meeting. Vigil cannot delete a meeting.

## Notes

- Open **Notes** from the left rail. Sections group pages, and pages hold notes.
- A note is made of text, checklist, and image blocks. Changes save automatically while you write.
- To order notes, drag a card. On a touch screen, use **Move up**, **Move down**, and **Move to** in the card menu.
- Archive keeps a note off the page. Bin keeps a note for 30 days, then deletes it.
- Set a reminder in the note editor. To get it in the browser, turn on **Note reminder** in **Settings > General > Notifications**.
- In Mail, **Save as note** turns a message into a note that links back to the message. In Notes, **Send by mail** opens a new message with the note. Images are not included.
- **Import** makes a note from a `.txt`, `.md`, or `.html` file. **Export as HTML** downloads one note with its images.

### Private notes

- A private note shows only its title. Its body and images are encrypted. Unlock it with your notes PIN of 4 to 8 digits.
- Private notes lock after a time without activity, when the tab stays hidden, when you choose **Lock now**, and when you sign out.
- Set, change, or reset the PIN and the lock time in **Settings > Notes > Private notes**. A reset needs your account password. Your notes are not lost.
- The AI assistant never reads the body of a private note. Search finds it by its title only.
- A private note cannot be on a shared page. Marking a note private revokes its public links.

### Sharing and public links

- The page owner shares a page in the page settings with the exact email address of a user on this server. Choose **Can view** or **Can edit**. Notes that others add count towards the owner's storage.
- **Public links** open a note or a page for anyone with the link, without signing in. Choose an expiry. Copy the link at once; it is shown only once. **Revoke** stops a link for good.
- A page link is live: notes added later are public too. Private, archived, and binned notes never appear.

### Notes on the phone

- The app reads notes with **Read-only** access and also creates and edits notes with **Full** access. An already paired phone needs no new pairing.
- Notes work offline. Private notes open with the PIN or with the biometrics of the phone, online only.
- Sections, pages, sharing, public links, reminders, tags, note order, and **Delete forever** are managed in the browser.

## Contacts

- Create and maintain contacts in Contacts.
- CSV and vCard imports show a preview before any data is added.
- Duplicate detection and the merge preview help you keep unique fields.

## Appearance and language

- Set the theme, accent, font style, scale, and density in **Settings > General > Appearance**. Your font style also applies in the phone app, where you can change it in **Settings > Appearance**.
- Set the interface language, date format, and time format in **Settings > General > Language & region**. Weekday and month names follow the interface language. The date format changes only the order and the numbers.
- Set the time zone, first day of the week, and work week in **Settings > Calendar > Preferences**.

## Diagnostics

Administrators can use **Settings > Diagnostics** to check service health, mailbox synchronisation, and search coverage.

1. Retry transient failures first.
2. Use recovery actions only after you review the scope that each action describes.

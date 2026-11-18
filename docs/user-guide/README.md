# User Guide

| **Last updated** | 2026-11-14 |
| **Scope** | Everyday browser and mobile mail, calendar, meetings, contacts, tasks, notes, settings, and optional AI-assistant workflows |

This guide explains how to use Awesonix Mail every day. It covers mail, calendar, contacts, tasks, notes, settings, the optional AI assistant, and the Android and iOS app.

Do you need to install Awesonix Mail or sign in for the first time? Read the [installation guide](../installation/README.md) first. It explains the installation, the setup wizard, and the first sign-in step by step.

## Contents

- [Sign in and manage your account](#sign-in-and-manage-your-account)
- [Read mail](#read-mail)
- [Find messages](#find-messages)
- [Organise messages](#organise-messages)
- [Delete messages automatically with Auto-bin](#delete-messages-automatically-with-auto-bin)
- [Sort mail with rules](#sort-mail-with-rules)
- [Write and send mail](#write-and-send-mail)
- [Use My Day](#use-my-day)
- [Notifications](#notifications)
- [Contacts](#contacts)
- [Tasks](#tasks)
- [Notes](#notes)
- [Meetings and invitations](#meetings-and-invitations)
- [Connect a calendar or address book app](#connect-a-calendar-or-address-book-app)
- [Personalise Awesonix Mail](#personalise-awesonix-mail)
- [Use the AI assistant (optional)](#use-the-ai-assistant-optional)
- [Use the Android and iOS app](#use-the-android-and-ios-app)
- [Move your settings to another installation](#move-your-settings-to-another-installation)

## Sign in and manage your account

### Sign in

1. Open the login page.
2. Enter your username or email address and your password.

Awesonix Mail stores your session in a secure cookie. The session stays active after you restart the browser.

Did your administrator create your account with a temporary password? Then Awesonix Mail asks you to change it before you can open your mail.

### Sign out

- To sign out, use the account menu in the top bar.
- To end all active sessions, open **Settings > Account > Security**.

### Change your profile picture

1. Select your portrait in the top bar, or open **Settings > Account > Security**.
2. Add, replace, or remove your profile picture.

Rules for the picture:

- Use a JPEG or PNG image.
- The image can be up to 5 MiB.
- The image can be up to 4096 pixels wide and 4096 pixels high.

Awesonix Mail converts accepted images to PNG. If no picture is stored or available, Awesonix Mail shows your initials.

### Protect your account with an authenticator app (optional)

You can add a second sign-in step with an authenticator app. Any app that supports RFC 6238 works.

> **Warning:** Save the ten recovery codes before you leave the confirmation screen. Each code works once. Awesonix Mail cannot show the codes again.

1. Open **Settings > Account > Security**.
2. Choose **Set up authenticator**.
3. Confirm your current password.
4. Scan the QR code with your authenticator app.
5. Enter the current six-digit code from the app.
6. Save the ten recovery codes.

These actions end all your browser sessions:

- Turning on the authenticator.
- Turning off the authenticator.
- Replacing the recovery codes.

Sign in again after each of these actions.

Good to know:

- The authenticator protects browser sign-in only.
- CalDAV and CardDAV apps keep using their own device credentials. You can revoke each device credential separately.
- Awesonix Mail has no SMS or email fallback.
- Did you lose the authenticator and every recovery code? Ask an administrator to clear the authenticator. The founding administrator recovers with a command on the server. The [administrator guide](../administrator-guide/README.md#recover-the-founding-administrator) explains it. Keep the recovery codes safe.

## Read mail

### Understand the folder list

| Item | What it contains |
|---|---|
| **Unified inbox** | Inbox messages from every active mailbox, and messages in your Local folders. |
| A mailbox | The real folders at your mail provider, including the Inbox of that mailbox. Expand the mailbox to see them. |
| **Local** | A separate tree after Bin, shown as a hard drive. It contains only folders that you create. |

Messages in Local folders are not synchronised with your mail provider.

Awesonix Mail finds the common folders by itself: Inbox, Sent, Drafts, Archive, Bin, and Spam. It reads this information from IMAP. These folders get friendly names. Awesonix Mail also shows translated labels for the other stock folders: All Mail, Starred, Important, Chats, Promotions, Updates, Social, and Forums. The label follows your interface language. Other provider folders keep a readable form of their provider name.

Is one of these folders missing? The mailbox owner can assign the folder in **Settings > Account > Mailboxes > Modify**. Awesonix Mail never guesses a folder for an action that removes or deletes mail.

### How mail arrives

Awesonix Mail synchronises active IMAP mailboxes in the background, in small batches. If a batch stops, it continues later from the same place. The Unified inbox shows the newest messages that Awesonix Mail stores locally.

Each mailbox has a download setting in **Settings > Mail > Synchronisation & storage**:

- **Complete messages** (default): Awesonix Mail downloads the full message.
- **Headers; download content when opened**: Awesonix Mail downloads only the headers first. It downloads the message body when you open the message.

When you open a message whose content is not stored yet, Awesonix Mail asks the provider for it once. This request survives a restart. Opening the message this way does not mark it as Seen. Until the download succeeds, the reader shows one of these states: queued, fetching, offline, unavailable, or failed.

### Open a message

Awesonix Mail shows a stored message from its complete local copy. It shows rich HTML mail in a separate, protected frame. It removes scripts, forms, embedded pages, event handlers, and unsafe links and styles.

Inline images that are part of the message come from local storage. Normal attachments are listed below the message as links.

The reading pane has separate areas for these items:

- The subject.
- The sender and the delivery details.
- The message actions.
- The message body.
- The attachments.
- The synchronisation status.

To see the sender, mailbox, and received-time details, select **Details** below the sender.

### Show remote images

Awesonix Mail blocks remote images by default. This stops senders from tracking you. To show the images, choose one option:

| Option | Effect |
|---|---|
| **Load once** | Shows the images in this message only. |
| **Trust sender** | Always shows images from this exact sender address. |
| **Trust** *domain* (for example, **Trust example.com**) | Always shows images from all senders at that domain. |

Trusted senders and domains apply to all mailboxes of your Awesonix Mail account. To review or remove them, open **Settings > Account > Privacy**.

### Read mail on a phone

On a phone, and in the Android and iOS app, the reader has one fixed toolbar. It contains **Back**, the reply button, **Forward**, **Delete**, and **More** (three dots).

- The subject, the sender row, and the message scroll together as one page.
- When you scroll past the subject, the subject moves into the toolbar.
- **More** opens the other reply option and the remaining message actions.

To choose which reply the toolbar shows, open **Settings > Mail > Reading & conversations > Toolbar reply button**. Choose **Reply all** (default) or **Reply**. The app uses the same setting.

### Recognise unread and selected messages

An unread message has a vertical bar in your signature colour and stronger sender and subject text. The selected message has its own highlight. So you can always see if the selected message is still unread.

The **Select** menu offers All, None, Read, Unread, Starred and Unstarred presets. They affect only the current page. Changing the page clears the selection.

### Change the number of messages per page

Open **Settings > General > Behaviour** and choose 10, 20, 50, 100, or 200 messages per page.

- Awesonix Mail always loads a limited number of messages. The list draws only the rows that you can see.
- When you change the page, your selection is cleared.
- The keyboard commands for the next and previous message continue onto the next or previous page if necessary.

### Stored message content and cleanup

Each mailbox has a setting for how long Awesonix Mail keeps downloaded content. You find it in **Settings > Mail > Synchronisation & storage**.

After the selected period, cleanup can remove stored message bodies and attachments. Cleanup keeps the headers, flags, folder, and the message at your provider. If the message still exists at your provider, opening it downloads the content again. If the message no longer exists at your provider, Awesonix Mail might not be able to download it again.

## Find messages

### Search

Search uses a local index. The index contains these items:

- The stored headers.
- The message text that Awesonix Mail stored locally.
- The attachment names.

You can search all mail or one mailbox. You can also limit the search to **Synced** or **Local** messages. Each message shows one tag for its origin.

Search reports separately how many headers and message bodies it covered. A notice about partial body coverage means that search did not cover message bodies that are not stored locally.

### Filter the message list

The header of the message list has three controls on one line: the mailbox choice, the filter button, and the refresh button.

1. Select **All mailboxes** or one connected mailbox.
2. Select **Filter messages**.
3. Choose your conditions:
   - Origin: from a provider or local only.
   - Read or unread.
   - Starred.
   - With attachments.
   - Date range.
   - Sender.
4. Apply the filters.

The filters narrow the current folder. The filter button shows how many advanced conditions are active.

## Organise messages

### Act on a message

The mail command bar runs across the desktop workspace. It shows the main actions for the selected message. To see the same actions in a menu, right-click a message row.

You can use these actions:

- **Mark as read** and **Mark as unread**.
- Star and unstar.
- Move and copy.
- Archive.
- Move to bin.
- Restore (in Bin).
- Delete forever.

Awesonix Mail shows each change at once and saves it safely. Then it sends the changes to your provider in batches in the background. Similar changes are combined, and the work continues after a restart. Your provider can need tens of seconds to finish the work. This does not slow down the interface. If something needs your attention, Awesonix Mail shows the pending, retrying, or failed state.

If you move a message to Bin or delete it permanently, Awesonix Mail removes its notification. It also stops notifications that are not delivered yet. If you restore the message, the old notification does not come back.

### Act on several messages

Select several rows. Then you can do these actions for all of them together:

- Mark them read or unread.
- Star them.
- Archive them.
- Move or copy them to a folder in the same mailbox.
- Move them to Bin.

### Move or copy a message with the menu

On a desktop computer, **Move** and **Copy** open small menus next to the command that you selected. The right-click menu offers the same actions as submenus.

1. Select **Move** or **Copy**.
2. Type in **Search folders** to narrow the list of folders in the same mailbox by their visible name.
3. Use the arrow keys to choose a folder.
4. Press Enter to select it.

Opening or filtering the list never moves or copies a message. The lists do not show provider prefixes such as `INBOX.`. Folders inside other folders keep a useful path.

On a touch screen, **Move** opens a larger folder picker. It works the same way: you search, then you select a folder.

### Move a message by dragging

You can drag messages on a desktop computer with a mouse or a similar precise pointer.

1. Optional: select several messages first.
2. Drag a message row to a highlighted folder.

Rules for dragging:

- If you drag a selected row, all selected messages move together.
- If you drag a row that is not selected, only that message moves.
- Only folders that accept every dragged message are highlighted.
- Messages from different mailboxes can go to a suitable Local folder, but not into one provider mailbox.
- If you drop the messages somewhere else, nothing happens. The folder does not open.

The **Move** menus work with the keyboard, a pointer, and touch. They use the same rules.

### How your changes and provider changes work together

While synchronisation is still running, your newest change in Awesonix Mail wins. After synchronisation finishes, changes from other places count. For example, a change on the provider website or in another mail app counts. Awesonix Mail takes it over at the next synchronisation. An old change in Awesonix Mail never hides a newer change at the provider.

### Keep mail when it is deleted at the provider

If a message disappears from your provider, Awesonix Mail removes it from its database, its conversation, and search at once. After a safety period of 24 hours, Awesonix Mail removes stored message files and attachments that nothing else uses.

To keep mail independently of your provider:

1. Create a folder under the **Local** tree.
2. Copy or move messages into it.

| Action | Effect on the provider message |
|---|---|
| Copy | The provider message stays unchanged. |
| Move | Awesonix Mail first finishes the local copy. Then it moves the provider message to Bin in the normal way. |

### Bin and Spam cleanup

Awesonix Mail permanently deletes messages in Bin or Spam after 30 days in that folder.

- The 30 days start when the message enters Bin or Spam.
- Cleanup runs about once an hour.
- Cleanup applies only to interactive mailboxes.
- A notice at the top of Bin and Spam explains this rule.

To delete all messages in Bin at once, open Bin and choose **Empty Bin now**.

## Delete messages automatically with Auto-bin

Auto-bin moves one message to Bin after a deadline. It works only for messages from a provider. Local-only messages do not offer Auto-bin. Auto-bin works on one message at a time, not on several messages together.

### Set a deadline

1. Select one message from a provider.
2. Choose **Auto-bin**. You find it in these places:
   - The mail command bar.
   - The right-click menu of the message.
   - The **More** (three dots) menu in the reader.
   - The command palette.
3. Choose 3, 7, 30, 90, 180, or 365 days, or select a date.

How the deadline works:

- If you choose days, the countdown starts when the server accepts your choice.
- A date means the end of that date in your Calendar display time zone. The dialog explains the exact time.
- The saved **Auto-bin: <date>** summary shows the deadline.
- In the reader, a clock chip with the date appears next to the sender. It appears only when a deadline is set. Select the chip to change or remove the deadline.
- If you change your time zone later, the deadline does not move.

In the right-click menu of the web app, **Auto-bin** opens a submenu. The submenu has the preset days, the removal option, and a date picker. When a deadline is set, the menu shows **Auto-bin: <short date>**. In the mobile app, the same choices are in the message action sheet.

### Change or remove a deadline

Open the same control. Then choose a new deadline, or choose **Remove auto-bin**.

### What changes a deadline

| Event | Effect on the deadline |
|---|---|
| You star the message | The deadline is removed. Unstarring does not bring it back. |
| You set a deadline after you starred the message | The new deadline is active. |
| You restore the message from Bin | The deadline is removed. |
| You change or remove the deadline yourself | Your choice wins over rules. |
| A rule meets a starred message | The rule sets no deadline. |
| You move or archive the message | The deadline stays. |
| You copy the message | The copy has no deadline. |

Unread messages can also expire.

Rules can also set a deadline. They can count days or read the end date from the mail. See [Auto-bin when it expires](#auto-bin-when-it-expires).

### What happens at the deadline

- Awesonix Mail moves the message to Bin. It never deletes the message permanently.
- In Bin, the normal 30-day cleanup of Bin and Spam applies.
- Awesonix Mail keeps the deadline and waits if the move is not safe yet. This happens if the mailbox is suspended or mirror-only, or has no safe Bin folder.
- If Awesonix Mail already started to send the move to your provider, removing the deadline cannot stop it. Restore the message if necessary.

### Auto-bin in the mobile app when offline

If you are offline, the app saves your choice securely and shows it as pending. The server sets the deadline when it accepts the choice. If the choice conflicts with another change, review it. Then go online and use **Refresh and apply again**. The device itself has no deletion timer.

## Sort mail with rules

Choose **Create rule** from a message menu or the reader **More** menu. The draft uses that exact message, with editable conditions and no action preselected. Choose the destination mailbox for move actions; matching remains account-wide.

Review the exact message preview and recent account sample before saving. **Apply existing** requires a separate confirmation. A saved rule stays visible if applying it fails. Connection recovery preserves the draft and requires review before another uncertain save.

Open **Settings > Mail > Rules**. Cards show each rule's name, sentence, switch and latest result. Search finds a rule by name. Open a card to edit it. Duplicate copies its sentence into a new rule. History can show one rule or all rules.

Every matching enabled rule acts in order. One mail can be placed by only one rule during an ordered run. Later matching rules can still mark it read, star it or set Auto-bin. **Stop checking further rules** ends the run after that rule acts. A failed or blocked rule lets later rules continue.

### Build a sentence

Tap a chip to open its picker. **+ and…** adds a condition; **+ then…** adds an action. The phone uses a sheet with the same sentence. Changed drafts need an explicit discard choice before you leave.

Conditions check sender, subject, message text, all text or whether the mail is a newsletter. All conditions must hold. **Does not contain** and **has none of** create exceptions. The sentence supports up to 24 conditions.

For the sender, you can also choose **matches** or **is any of**:

- **matches** takes an address with `*` for the part that changes, for example `no-reply-*@mail.anthropic.com`. Use one or two `*`, all before the `@`. Write at least three other characters before the `@`. The domain after the `@` must be complete. Patterns for a whole domain or a free-mail provider, such as `*@gmail.com`, are refused.
- **is any of** takes up to 50 complete addresses, separated by commas. The rule matches mail from any of them.

A word list accepts up to 400 entries and 8192 bytes. Commas separate words; quotes keep a phrase. Any entry in that list is enough. Plain words match whole words. Use `bill*` to also match billing. Case and accents do not matter, in word lists and in every other text condition.

The folder picker searches folders grouped by mailbox. Choose an available folder in the message's mailbox. Save is always available. If something is missing or wrong, Save does not store the rule; each part that needs a correction says so, for example "Add what the rule does with + then…".

### Actions and order

Choose Mark as read, Star, Archive, Move to bin, Move to a folder, or Auto-bin. Auto-bin counts days or reads the date from the mail; see [Auto-bin when it expires](#auto-bin-when-it-expires). Presets are 3, 7, 30, 90, 180 and 365 days. The one-year preset means 365 elapsed days. Each action can occur once. Only one action may place the message.

Actions run in their shown order. Star clears an earlier Auto-bin deadline. Auto-bin after Star creates a new deadline. A later manual choice still wins. Editing or deleting a rule does not rewrite deadlines already accepted.

On the web, drag a card or use its arrow controls, then save the order once. Keyboard users can move the focused drag handle with arrow keys. On the phone, open a card's secondary actions for up/down controls. Save the complete order once. If rules changed elsewhere, reload and choose the order again.

### Preview and test

The live preview checks recent stored mail while you edit. It shows matches with their original text, exceptions and messages waiting for a stored body. It never moves mail. A failed preview keeps the last result and says so.

**Test with a message…** searches your retained mail and checks the message you choose, however old it is. It also changes nothing.

Saving applies the new rule to future arrivals. **Run now** applies one selected rule to matching retained mail, including Auto-bin. **Run all now** applies the ordered enabled rules. These actions can change existing mail. The phone asks for account proof before a run.

Automatic expiry applies only to genuine new arrivals. Historical mail, imports, copies and rebuilt synchronisation state are not new arrivals. An explicit Run now can schedule retained mail. Rule expiry counts from the message's received time, so an old matching mail can already be due.

### Auto-bin when it expires

In the Auto-bin picker, **When to auto-bin** has two choices:

- **After a fixed number of days** counts the preset days from the received time.
- **When it expires** reads the end date from the mail. A newsletter without an end date goes to Bin after the fallback days that you choose. Other mail without an end date stays.

How the rule reads the mail:

- A sign-in link or code goes to Bin at the end of the day after it arrived. So does a mail that is valid for at most 24 hours.
- A mail with a longer validity, such as "valid for 7 days", goes to Bin at the end of the day it runs out.
- A mail with an end date, such as "Sale until 15 November", goes to Bin at the end of that date. The date must follow an end word, such as "until", "bis", "jusqu'au", "t/m" or "-ig".
- The rule reads English, Hungarian, German, French and Dutch mail.
- The date must be after the arrival day and at most 12 months later.
- A negation cancels a date, for example "not valid until 15 November". Text in quotation marks gives no date.
- The deadline is the end of that day in your Calendar display time zone. If you change your time zone later, the deadline does not move.
- If the date is already past, for example when you run the rule on older mail, the mail goes to Bin soon.

With **When it expires**, some mail stays:

- Personal mail, bookings and reminders from your own services never get a date from the mail.
- Receipts, paid confirmations, order confirmations, invoices, warranties, contracts, tax letters and insurance letters stay too. Turn on **Also auto-bin long-lived mail** to allow them in this rule.
- A starred message or your own Auto-bin choice stays as it is. This applies to both choices.

The days choice now also keeps long-lived mail unless you turn the switch on. It does not set a deadline on starred mail. A rule with Auto-bin waits until the message body is stored.

### Parcel notices

A shop or carrier often sends several notices for one parcel, such as "on its way" and "delivered". With **When it expires**, the progress notices wait for the delivery. The rule must name its sender with one sender condition: an exact address, **is any of** or **matches**.

- The earliest "delivered" notice decides the deadline. All notices of that parcel go to Bin at the end of the 14th day after that notice arrived.
- A later progress notice gets the same deadline.
- "Will be delivered" or "not delivered" does not count as a delivery.
- The Hungarian "átvette" counts as delivered.
- A notice that the parcel went to the carrier, or back to the sender, does not count as delivered.
- A delivery notice that also thanks you for your order still counts as a delivery notice.
- A parcel without a delivered notice keeps its notices.
- The order or payment confirmation stays, because it is a receipt.

In the preview and in History, each mail says what Auto-bin does with it. Examples are "Bin after 6 Nov", "Kept: looks like a receipt", "Kept: not short-lived mail", "Waits for the delivery" and "No end date found".

### Create a filter from a mail

A filter offer opens the same sentence builder with its sender fixed. Newsletters go to Promotions. Notices and parcel deliveries go to Updates. Suggested words and groups help narrow the match.

The offer fits the way the sender writes:

- Some senders use a new random address for each mail. The offer then uses a pattern, for example `no-reply-*@mail.anthropic.com`.
- If the same sender also writes from other domains, those addresses appear under **Also from this sender**. They are not selected. Select the ones that belong to this sender.
- The rule name uses the sender's name, for example "Anthropic to Updates".
- **Auto-bin when it expires** is selected for newsletters. It is also selected for sign-in mail, short-lived codes and parcel notices. It is never selected for receipts and similar mail. You can change or remove it.

The offer is part of mail rules. It also appears when AI is turned off.

To stop the offer for a sender, select **×** on the offer line, then **Don’t offer for this sender**. The sender then appears under **Senders without a filter offer** in the rules settings, on the web and on the phone. Select **Offer again** to get the offer back. Turning off AI analysis for a sender does not hide the offer.

The sender condition stays fixed in the offer. To use a broader pattern, edit the rule in **Settings > Mail > Rules** after you create it. Add exceptions and choose any supported action. Move can use an existing source-mailbox folder or an explicit new folder name.

Nothing is created before confirmation. **Also apply to matching messages already here** is initially off. The confirmed sentence and actions stay the same while a new folder is pending. The offer reports that the rule is ready once creation finishes.

## Write and send mail

1. Choose **New message**, **Reply**, **Reply all**, or **Forward**. The full writing workspace opens.
2. Select the mailbox to send from.
3. Enter the recipients in To, CC, and BCC.
4. Write and format the message.
5. Attach files if you need them.
6. Choose **Send**.

Drafts save automatically to local storage. You can open them again from **Drafts**.

The footer shows the total size limit for attachments. Your administrator sets this limit.

When you choose **Send**, Awesonix Mail creates an item in the **Outbox**. Awesonix Mail then sends the mail with SMTP and files it in your Sent folder. The **Outbox** shows the state of each message: queued, retrying, filing, failed, uncertain, or completed. Meeting mail appears as one row for each send, and it does not stay in Sent. See [Meetings in the Outbox and in Sent](#meetings-in-the-outbox-and-in-sent).

## Use My Day

Use the calendar icon in the Mail command bar to open or close **My Day**. It has separate **Calendar** and **Tasks** views.

Calendar shows the selected day and the next 29 dates in your configured Calendar time zone. It respects calendar visibility and enabled birthdays. **Today** restores the current date. Create event uses the selected date.

Tasks automatically shows accessible active tasks due today or overdue. Future and undated tasks stay outside this view. Changing the Calendar date does not change Tasks. Create a task with the existing board choice, or complete it directly in My Day.

The panel links to the full Calendar and Tasks workspaces. On compact screens it fills Mail content below **New message**, retaining the calendar toggle. Closing My Day restores Mail selection, reader, scroll and unsent compose work. An open editor handles Back and unsaved changes before the panel closes.

## Notifications

### Use the notification centre

The bell in the top bar opens the list of unread notifications. You can use these actions:

| Action | Effect |
|---|---|
| **Open** | Opens the item and marks the notification as read. |
| **Dismiss** | Removes one notification without opening the item. |
| **Mark all as read** | Clears the unread list. |

Sometimes a message, event, task, or permission is no longer available. Then Awesonix Mail keeps the notification for a while. It shows why the item is unavailable. It does not send you to another place.

The icon of the browser tab shows the number of unread messages in the Unified inbox. More than 99 messages show as **99+**.

### Choose how you get notifications

Open **Settings > General > Notifications**. There you can set these items:

- Delivery outside the app: for all notifications, by channel, and by category.
- Browser permission.
- Quiet hours.
- Private previews.
- Sound.
- Badges.

Good to know:

- The notification history in the app works without browser or mobile permission.
- Quiet hours delay only delivery outside the app. The notification centre gets the item at once.
- To get browser notifications, select **Allow notifications on this browser**. Then allow notifications in the browser.

### Reminders

- Calendar reminders follow the schedule of each event.
- A task with a time gets a reminder at its due time.
- A task with only a date gets a reminder at the date-only reminder time that you set. This time uses your Calendar display time zone.
- You can also turn on one overdue reminder on the next calendar day.

If you complete, archive, delete, or reschedule the event or task, Awesonix Mail cancels reminders that no longer apply.

## Contacts

### Display names and alphabetical order

Open **Settings > Contacts > Names & display** to choose a name format. Each choice shows an example. The choice is saved with your account and is included in encrypted settings backups. Web and mobile use the same choice.

The formats are Firstname Lastname, Lastname Firstname, Lastname, Firstname, Firstname Middlename Lastname, Firstname M. Lastname, F. Lastname, Lastname, F., and Title Firstname Lastname. Contacts sort alphabetically by the name shown. Search still matches the full name fields when the display uses initials.

The chosen format also applies to existing contacts with first or last names. In a contact editor, turn on **Use a custom display name** to keep a different label. Turn it off to use the account format again. Contacts without structured names keep their saved display name.

Both editors show **Middle name** beside first and last name. On web, the title is the **Prefix** field under **Additional name fields**. On mobile, use **Title**. Missing parts are omitted, including the comma when only one name is available.

### Search contacts

You can search contacts by these fields:

- Names and phonetic names.
- Nickname.
- Organisation, job, and department.
- Email address.
- Phone number.
- Address.
- Labels.

Search treats punctuation as a space between words. So a part of an address or a name with a hyphen still finds the contact.

Can you not find a contact that you changed recently? Use **Repair contact search** in the Contacts maintenance actions. The repair reads your contact data and rebuilds only the search index. Your contacts do not change.

### Merge contacts

1. Select two or more of your own contacts.
2. Choose **Merge and review**.
3. Check the preview. It shows which contact stays and which values it keeps.
4. Confirm the merge.

The merge keeps all distinct values with their labels. It also moves supported calendar links and import references to the remaining contact.

Contacts from a provider are never changed into your own contacts or merged into them without you knowing.

### Add a contact photo

Your own contacts can have a photo:

- Use a JPEG or PNG image.
- The image can be up to 5 MiB.
- The image can be up to 4096 pixels wide and 4096 pixels high.

Awesonix Mail converts accepted images to PNG and removes their metadata. If you replace or remove a photo, the rest of the contact stays the same. Photos appear in the contact list and in the contact details.

### Import contacts

You can import contacts from these files:

- Google Contacts CSV files. During the preview, Awesonix Mail downloads photos from public HTTPS addresses.
- vCard 3 and vCard 4 files. Awesonix Mail keeps embedded JPEG or PNG photos up to a size limit.

Imported photos get the same checks as photos that you upload yourself. Awesonix Mail rejects these photos:

- Photos on a private or local network.
- Photos at addresses that do not use HTTPS.
- Remote photos that are not available.
- Files that are not valid images.

The preview shows a warning for each rejected photo. You can still import the rest of the contact data.

If you import the same file again, Awesonix Mail ignores matching contacts that are still in Bin. It creates new active contacts. It does not restore, change, or permanently delete the copies in Bin. Those copies follow the normal 30-day Bin rule.

## Tasks

### Use boards

Open **Tasks** in the navigation.

- Every account has one permanent, private **Personal** board.
- Every board has four fixed columns: **Open**, **In progress**, **Done**, and **Obsolete**.
- To create more boards, such as Family or Household, use **New board**.
- To change the board, select it in the header.

The browser remembers your last board. If that board is no longer available, the **Personal** board opens.

### Create and edit tasks

1. Choose **New task**.
2. Enter a title.
3. Optional: add a plain-text description, a due date and time, an assignee, and a checklist.

To change a task, open its card. There you can edit, archive, or delete it, or change its status. You can also drag a card to another column.

### Choose a view and search

| View | What it shows |
|---|---|
| **Compact** | Title, due urgency, and checklist progress. |
| **Detailed** | Also the description and the assignee. |

The browser remembers your choice. In the **Compact** view, the button on the right of a card shows or hides the description and the assignee.

The search box filters the board by title, description, status, checklist text, and assignee.

### Archive tasks

Tasks stay in the **Active** view until they are archived.

- A task in **Done** or **Obsolete** moves to **Archived** automatically after 30 days in that state.
- If you edit the title, checklist, assignee, or deadline, the 30 days do not start again.
- The editor shows the calculated archive date.
- To archive a task at once, choose **Archive now**.

In the **Archived** view, you can read and search tasks, but you cannot change them. **Restore task** returns a task to **Active**. If the task is still in **Done** or **Obsolete**, its 30 days start again.

### Share a board

You cannot share, archive, or delete the **Personal** board.

To share another board, the owner opens **Board settings**. There the owner enters the exact email address of an existing Awesonix Mail user.

| Who | What they can do |
|---|---|
| Members | Create, edit, assign, move, and delete tasks. |
| Owner only | Rename the board, manage members, archive and restore the board, and delete it. |

- If the owner removes a member, the tasks assigned to that member become unassigned.
- An archived board keeps its tasks, but nobody can change them until the owner restores it.

> **Warning:** Deleting a board permanently removes its tasks for every member. Awesonix Mail asks you to confirm first.

If two sessions change the same task or board, Awesonix Mail rejects the older save. It does not overwrite newer work. It asks you to reload and review the change.

### See tasks in the calendar

The calendar always shows a read-only **Tasks** category under **My calendars**, even when it is empty.

- Use its checkbox to show or hide tasks.
- Use its edit action to choose their colour.

Tasks with a due date appear as all-day items in the calendar and in My Day Calendar. This applies only to tasks that are not archived and are on an active board. My Day names the board, and its link opens the right board and task.

Tasks in **Done** and **Obsolete** stay visible during their 30 active days. They look dimmed and crossed out.

These tasks do not appear in the calendar:

- Tasks on archived boards.
- Archived tasks.
- Tasks without a due date.

The show or hide choice applies to your account on this device. The colour follows the calendar preferences of your account.

You change tasks only in the Tasks workspace. Awesonix Mail does not create calendar events or CalDAV events for tasks.

### What tasks cannot do yet

This release has no repeating tasks, attachments, comments, custom roles, change of owner, or moving tasks between boards.

## Notes

### Sections, pages, and notes

Open **Notes** in the navigation.

- A **section** groups pages. Every account has a default section named **Notes**. You cannot delete it.
- A **page** holds notes. Every section starts with **Page 1**. You cannot delete the default page of a section.
- A **note** is a card on a page. It has a title, a colour, and tags. You can pin it to the top.

Use **New section** and **New page** in the sections and pages list. To rename, move, or delete a section or a page, open its settings. When you delete a page, its notes move to Bin. When you delete a section, its pages move to your default section.

To change the order of notes, drag a card to another place or another page. On a touch screen, use **Move up**, **Move down**, and **Move to** a page in the card menu.

### Write a note

1. Choose **New note**.
2. Enter a title.
3. Write in the body. Changes save automatically while you write.

A note is made of blocks. There are three kinds:

| Block | What it holds |
|---|---|
| **Text** | Paragraphs, headings, lists, quotes, code, bold, italic, underline, strikethrough, and links. |
| **Checklist** | Items that you can check. Press Enter to add an item. Press Backspace on an empty item to remove it. |
| **Image** | One picture. Add it from a file, by paste, or by drag and drop. Choose a width of 25, 50, 75, or 100 percent and write a description. |

Use **Add text**, **Add checklist**, or **Add image** to add a block. Each block has a menu to move it up or down or to remove it. Pasted text from Word or Google Docs is cleaned to the supported formatting. Pictures must be PNG, JPEG, WebP, or GIF.

If two devices change the same note at the same time, Awesonix Mail keeps the newer server version. Your version is saved as a note named **Conflict copy** on the same page. Nothing is lost.

### Tags, colours, and search

- Enter tags in the editor, separated by commas. A note can have up to 20 tags.
- Choose a colour in the editor.
- The search box searches the titles and the text of your notes. A private note is found by its title only.

### Archive and Bin

- **Archive** keeps a note but takes it off the page. Use the **Archive** view to read it or to restore it.
- **Move to bin** keeps a note for 30 days. Then Awesonix Mail deletes it. Use the **Bin** view to restore it or to delete it at once with **Delete forever**.

You cannot edit a note in Archive or in Bin. Restore it first.

### Set a reminder

Open the note and set a **Reminder date** and, optionally, a **Reminder time**. At that time, you get a notification. It opens the note. The reminder follows the rules of the notification settings. To get it in the browser, turn on the **Note reminder** category in **Settings > General > Notifications**.

If you archive the note, move it to Bin, or clear the reminder, the notification is cancelled. A reminder in the past is not sent.

### Save a message as a note and mail a note

- In Mail, open the message menu and choose **Save as note**. Choose a page. The text, the formatting, and the inline images become a note. The note links back to the message, and the reader shows a **linked note** chip.
- In Notes, choose **Send by mail** in the card menu or in the editor. A new message opens with the note as its body. Images are not included. Checklist items are shown as text with a check mark.

### Import a text document and export a note

- Choose **Import** in the command bar to make a note from a `.txt`, `.md`, or `.html` file of up to 2 MiB. The first heading or the HTML title becomes the note title.
- Choose **Export as HTML** in the card menu to download one note as a web page with its images. You can open the file in any browser.

### Private notes

A private note shows only its title on the screen. Its body and its images are encrypted on the server. You open it with a PIN.

1. Choose **New private note** in the **Private notes** menu, or choose **Mark private** on a note.
2. The first time, set a PIN of 4 to 8 digits.
3. Enter the PIN to unlock. You can then read and edit the note.

Rules for private notes:

- Search finds a private note by its title only. The AI assistant never reads the body of a private note.
- When you mark a note private, every public link to that note stops working. The links stay revoked, also if you remove privacy later.
- A private note cannot be on a shared page. A page with private notes cannot be shared.
- Private notes lock again after a time without activity. The default is 10 minutes. They also lock when the browser tab stays hidden for more than one minute. **Lock now**, leaving Notes, and signing out lock them at once.
- After five wrong PINs, you must wait before the next try. The wait grows with every wrong PIN.
- You can read and edit private notes in the app on your phone, but only while the phone is online.

Set or change the PIN and the lock time in **Settings > Notes > Private notes**. If you forget the PIN, choose **Reset PIN with your password**. Enter your account password and, if you use one, your authenticator code. After five wrong passwords, you must wait 15 minutes. Your private notes are not lost. To make a note normal again, choose **Remove privacy**.

### Share a page

The owner of a page can share it with other users of the same Awesonix Mail server.

1. Open the settings of the page.
2. Enter the exact email address of the user.
3. Choose **Can view** or **Can edit**.

| Who | What they can do |
|---|---|
| **Can view** | Read and search the notes on the page. |
| **Can edit** | Also create and change notes on the page. |
| Owner only | Rename, move, or delete the page, manage access, and create public links. |

Notes and images that other people add to your page count towards your storage limit, not theirs. A page can have at most 100 people. A member can leave a page at any time. Shared pages appear under **Shared with me**.

### Public links

A public link lets anyone with the link view a note or a page in a browser, without signing in.

1. Choose **Public links** in the card menu or in the page settings.
2. Choose when the link expires: 7 days, 30 days, 1 year, never, or a date.
3. Choose **Create link** and copy the link at once. It is shown only once.

Rules for public links:

- A page link is live. Notes that you add to the page later are visible too. The page and its notes show the word **Public**.
- Private notes, archived notes, and notes in Bin never appear in a public link.
- The public page is read-only and contains no script.
- The page chrome follows the browser language of the reader. It supports every language of the app, and shows English otherwise. Your note text is never translated.
- To stop a link, choose **Revoke**. A revoked link cannot be turned on again. Create a new link instead.
- You can have up to 20 active links per note or page.

> **Warning:** Anyone with the link can read the content and can pass the link on. Use an expiry date for content that must not stay public.

### Notes on your phone

The Android and iOS app shows your notes. It uses the access that the phone already has: **Read-only mobile access** reads notes, and **Full mobile access** also creates and edits notes. You do not need to pair the phone again.

- You can read notes offline. You can create and edit notes offline with full access. The app sends the changes when the connection returns.
- You can add photos from the camera or the gallery. A photo on a new note is added after the note reaches the server. If the app is closed before that, add the photo again.
- A private note shows its title and a lock. Unlock it with your PIN, or with the fingerprint or face of the phone after the first PIN unlock. This needs a connection. The phone never stores the body of a private note.
- Sections, pages, sharing, public links, reminders, tags, the order of notes, and **Delete forever** are managed in the browser.

## Meetings and invitations

A meeting is a calendar event with guests. You can add guests to events in your own Awesonix Mail calendars. Calendars from Google, ICS, or another provider cannot hold meetings.

Invitations from other organisers are added to your calendar automatically. You answer them with **Yes**, **Maybe**, or **No**.

Meeting mail goes out through one of your mailboxes, like other mail. A mailbox that only mirrors mail cannot send meeting mail.

### Add guests

1. Open Calendar. Create or open an event in one of your own calendars.
2. In **Add guests**, below the time, start typing a name or an address. Awesonix Mail suggests contacts and people you wrote to.
3. Choose a suggestion, or type any email address and press Enter.
4. Mark a guest **Optional** when the guest does not have to attend.
5. Check **Guest permissions** and **Get an email when guests respond**. See [Guest permissions](#guest-permissions) and [Follow answers](#follow-answers).
6. Choose **Save**.

A meeting can have up to 200 guests besides you. People who join from a forwarded invitation count too. To remove a guest, use the remove button next to the guest, then save. A timed meeting needs a time zone before you can send it.

### Guest permissions

New meetings start with these permissions:

| Permission | Default | Effect |
|---|---|---|
| **Modify event** | Off | Guests can change the details and the guest list. This works only for guests on this Awesonix Mail server. See [Edit a meeting as a guest](#edit-a-meeting-as-a-guest). |
| **Invite others** | On | A person who gets a forwarded copy of the invitation can join by answering it. |
| **See guest list** | On | Each invitation lists all guests. When it is off, each invitation names only you and that guest. |

When you turn on **Modify event**, **Invite others** and **See guest list** also turn on and are locked. Turn off **Modify event** to unlock them. Changing a permission sends no mail.

A person with a forwarded copy joins only when their answer has a valid DKIM signature for the domain of their address. These rules also apply:

- A guest that you removed cannot join again through a forwarded copy.
- When you turn off **Invite others**, nobody new can join. Guests that already joined stay.
- Your other guests get no mail about a join.

### Send invitations, updates, and cancellations

After you save a meeting, Calendar asks one of these questions:

- **Send invitation emails?** when guests did not get this meeting yet.
- **Send update emails?** when you changed a meeting that guests already got.
- **Send cancellation emails?** when you deleted a meeting that guests already got.

Choose **Send** to send the mail, or **Not now** to send nothing. Your changes stay saved in both cases. A guest that you removed gets a cancellation.

Each guest gets a separate mail that is addressed only to that guest. The mail shows the title, time, place, organiser, and description. It also lists the guests, when **See guest list** is on. It contains the invitation as a calendar part and as the attachment `invite.ics`, so the guest can answer from their own mail app.

Calendar does not ask when you change only the guest permissions or **Get an email when guests respond**. Guests get no mail for these changes.

Did you choose **Not now**? You can send later:

- In the browser, open the meeting and save it again. Calendar asks again.
- On the phone, the meeting shows **Guests have not received the latest saved changes.** Choose **Review**.

Sending needs a connection. The phone does not keep a Send for later when it is offline.

Sometimes the question shows **Sending is not available** and a reason. Then you can only choose **Not now**, and your changes stay saved. These are the reasons:

- No sending address can send for the organiser.
- The mailbox only mirrors mail.
- The meeting has more guests than you can invite. This can happen with a meeting from a DAV app or an import.
- The description is too long to send. Shorten it, then save again.
- A timed meeting has no time zone.
- The invitation is too large to send. One invitation mail can be at most 1 MiB.

If the meeting changed while the question was open, Calendar shows **The meeting changed. Review the notice again.** Check the new question, then choose again.

Awesonix Mail keeps trying to send each mail in the background. When a mail finally fails, the guest shows **Invitation could not be sent**. This can happen up to about 4.6 days after you choose **Send**. Awesonix Mail never sends a mail again when it may have arrived already.

### Follow answers

The guest list shows the organiser first, then each guest with **Yes**, **No**, **Maybe**, or **Awaiting**. A line above the list counts the answers, for example **3 guests · 2 yes, 1 awaiting**. The organiser counts as a guest who answered Yes.

Answers come back as mail and change the guest list at once. There is no review step. An answer from a guest that you removed changes nothing.

When you send a new date, time, time zone, or repetition, all answers go back to **Awaiting**. The guests then answer again. Changes to the title, the place, or the description keep the answers.

A guest can add a note to an answer. You see the note under the name of the guest. Guests on this server who may see the guest list also see the notes of the other guests. Notes are never sent in mail to other guests.

**Get an email when guests respond** is on for new meetings:

- **On:** the answer mail stays in your Inbox. It shows a short card, for example **Anna has accepted this invitation.**
- **Off:** Awesonix Mail applies the answer and moves the mail to Archive. Without an Archive folder, or in a mailbox that only mirrors mail, the mail stays in the Inbox.

### Invitations you receive

When an invitation arrives, Awesonix Mail adds the meeting to your default calendar, the one that new events use. The meeting waits for your answer. Organiser updates and cancellations also apply by themselves.

- If your default calendar is not an Awesonix Mail calendar, the first Awesonix Mail calendar in your list is used.
- If you have no Awesonix Mail calendar, the first invitation creates one named **Calendar**.
- To use another of your calendars, open the event details and choose another calendar. This sends no mail. Later updates follow the meeting.

Awesonix Mail does not add these invitations:

- Invitations in Spam. When you move the mail out of Spam, the meeting is added.
- Mail that arrived before Awesonix Mail started to add invitations for the mailbox. Its card is read-only.
- An update that names another organiser than the meeting already has.

An organiser can sign their mail with DKIM. After Awesonix Mail accepted a signed message for a meeting, it applies only signed updates and cancellations for it. An unsigned one is not applied, and its mail card explains why. While the first signature check is still open, an update can wait for up to 7 days. Its card then shows **Pending**.

### Answer an invitation

You can answer from the mail card, from the event details, or on the phone.

1. Choose **Yes**, **Maybe**, or **No**. For a repeating meeting, choose **This event** or **All events**, then **OK**.
2. To add a note for the organiser, choose **Add note**. A note can have up to 1,000 characters.

Your answer is sent at once. You can change it later: choose another answer. The answer goes from the mailbox that received the invitation. If that mailbox cannot send mail, your answer is saved but not sent, and Calendar says so.

Calendar refuses an answer in these cases:

- The organiser changed the time: **The organiser changed the time. Check the new time and answer again.**
- The organiser cancelled the event: your answer is not sent.

### Cancelled and removed meetings

When the organiser cancels a meeting, it stays in your calendar, struck through and marked **Cancelled**. The same applies to one cancelled occurrence of a repeating meeting. It stays until you remove it.

To remove a meeting that you received, choose **Remove from calendar** in the event details or on the mail card. This removes it only from your calendar and sends no mail. Only the organiser can cancel a meeting for everyone.

A removed meeting comes back when the organiser sends a newer invitation or update. A cancellation does not bring it back. The mail card of a removed meeting still offers **Yes**, **Maybe**, and **No**. Your answer adds the meeting back.

When you delete a meeting that you organise, Calendar asks **Send cancellation emails?**.

### Meeting cards in mail

Meeting mail shows a card above the message, in the browser and on the phone. The card shows the title, time, place, organiser, the guests that you may see, and your answer. It always shows the current state of the meeting.

| Label | Meaning |
|---|---|
| **Invitation** | You can answer with **Yes**, **Maybe**, or **No**. |
| **Updated** | The meeting changed after this mail. The card shows the latest details. |
| **Cancelled** | The organiser cancelled the meeting or this occurrence. |
| **Answer** | A guest answered your meeting. |
| **Removed** | You removed the meeting. Answering adds it back. |
| **Pending** | Awesonix Mail is still adding the invitation to your calendar. |
| **Not applied** | The update could not be confirmed as coming from the organiser. Your calendar keeps the earlier version. |
| **Read-only** | The invitation arrived before Awesonix Mail started to add invitations. The card is only for reference. |

### Event details and Email guests

Click an event in the calendar to open its details card. The card shows the title, time, repetition, place, calendar, guests, notes, and your answer. Choose **Edit** to open the full editor.

**Email guests** opens a new mail to the guests that you may see. The subject is the title of the event.

In the browser calendar, a received meeting shows whether you answered. **Awaiting**, **Maybe**, **No**, and **Cancelled** look different from a meeting that you accepted.

### Edit a meeting as a guest

An organiser on the same Awesonix Mail server can turn on **Modify event**. Then you can edit the meeting, but only if the organiser invited your account email address. The event details then say, for example, **Anna lets guests modify this event.**

You can change the details and the guest list. After you save, Calendar asks **Send update emails?** The update emails go out from the organiser, as a separate message to each guest. **Not now** keeps your change; the update waits on the meeting until somebody sends it.

- You cannot change the guest permissions or cancel the meeting for everyone.
- When you delete the meeting, it is removed only from your calendar.
- For a repeating meeting, you choose **This event** or **All events**.
- If the organiser changed the meeting in the meantime, Calendar asks you to open the event again and repeat your change.

Guests invited at another address, or on another server, cannot edit the meeting.

### Meetings on your phone

Meetings use the access that the phone already has. With **Full mobile access**, you can add guests, send, answer, and move a meeting to another calendar. You do not need to pair the phone again. **Read-only mobile access** hides these controls.

**Add guests** suggests contacts and people you wrote to. Without a connection, it uses the contacts and mail on the phone.

You can answer **Yes**, **Maybe**, or **No** also without a connection:

- The answer shows **Waiting to send**.
- The phone sends the answer when the connection returns. There is no time limit.
- A new answer replaces an earlier answer that was not sent yet.

If the organiser changed the time or cancelled the meeting in the meantime, nothing is sent. The phone says why and asks you to answer again.

> **Note:** Update the phone app together with the server. An older app shows meetings without guests. It cannot add guests, send, or answer.

### Meetings in the Outbox and in Sent

In the browser **Outbox**, each send is one row, for example **Invitation: Team lunch** with the number of guests. You do not see one row for each guest. The row leaves the Outbox when all messages of the send are done. The result for each guest shows in the meeting.

The phone **Outbox** shows only your answers that wait to be sent, for example **Reply: Team lunch · Yes**. You can dismiss a refused answer; this sends nothing.

Meeting mail does not stay in Sent. This applies to invitations, updates, cancellations, and your answers. Awesonix Mail files no copy itself.

Some providers, such as Gmail, file their own copy in Sent. Awesonix Mail moves such a copy to Bin when it can prove that the copy is its own. It does this only during the first 7 days after the send. It never deletes a copy permanently, and it does not change a mailbox that only mirrors mail.

### Meetings in DAV apps, phone calendars, and Vigil

DAV apps such as Thunderbird show your meetings with their time zone, organiser, guests, and answers. You can change other details there. A DAV app cannot do these things:

- Change the time zone, the guests, or the repetition of a meeting.
- Create a new event with guests.
- Delete a meeting that guests already got.

Make these changes in Awesonix Mail. Changes from a DAV app send no mail to guests. To send an update, open the meeting in Awesonix Mail and save it.

The Android device calendar and Vigil also keep the guests and send no mail. Vigil cannot delete a meeting.

The settings export contains your meetings with their guests, answers, notes, and guest permissions. It also contains the meetings that you received. An import sends no meeting mail. An installation older than version 0.104.0 cannot import an export that contains meetings.

## Connect a calendar or address book app

You can connect apps such as Thunderbird to your calendars and contacts. They use the CalDAV and CardDAV standards (together: DAV). This works only if your administrator turned on the special HTTPS DAV service.

### Create a device credential

> **Warning:** Do not use your normal Awesonix Mail password in a DAV app. Create a separate device credential.

1. Open **Settings > Account > Device access**.
2. Create a separate credential.
3. Choose CalDAV, CardDAV, or both.
4. Copy the username and the password at once. Awesonix Mail shows the password only once.

### Server address and certificate

Use the DAV address from your administrator. It is normally `https://<trusted-lan-name>:8443/dav/`. The host name must match the certificate of the DAV service.

If an app rejects the certificate, the connection is not safe. Then do one of these things:

- Install the local certificate authority in the normal way of your operating system.
- Ask your administrator for a name on your local network or VPN that is publicly trusted.

### Connect Thunderbird Desktop

1. For a calendar: open the Calendar tab and create a new calendar. Choose **On the Network** and select CalDAV. Enter the DAV address and the device username. Enter the device password when Thunderbird asks.
2. For contacts: open Address Book and create a new address book. Choose **CardDAV Address Book**. Enter the same DAV address and a device username with CardDAV access. Enter its device password when Thunderbird asks.
3. Test the connection. Create, edit, and delete one test event and one test contact with a unique name in Thunderbird. Check that each change appears in Awesonix Mail.
4. Then make the opposite changes in Awesonix Mail. Check that Thunderbird gets them after synchronisation.

### The Birthdays calendar

DAV apps also find a calendar called **Birthdays**. Awesonix Mail creates it from the birthdays of your contacts that are not in Bin. It is read-only. Each birthday is an all-day event that repeats every year.

To change a birthday, edit the contact in Awesonix Mail or through its writable CardDAV address book. Calendar apps cannot create, replace, or delete birthday events.

Does an existing Thunderbird account not show the new calendar? Then follow these steps:

1. Choose **Add Calendar > On the Network** again.
2. Enter the same DAV address and credential.
3. Select **Birthdays**.
4. Leave the calendars that you already use unchecked.

### What DAV apps can see

DAV apps see only your active, own calendars and your active, own address books. They do not see these items:

- Calendars and contacts from a provider.
- Subscriptions.
- Suggestions.
- Archived calendars and address books.
- Contacts in Bin.

If you revoke the device credential in Settings, the app can no longer sign in. Your calendar and contact data does not change.

DAV apps show meetings, but some meeting changes must be made in Awesonix Mail. See [Meetings in DAV apps, phone calendars, and Vigil](#meetings-in-dav-apps-phone-calendars-and-vigil).

## Personalise Awesonix Mail

**Support Awesonix** remains visible in Settings and opens the sponsor page.

### Appearance, scale, and shortcuts

Open **Settings** in the top bar. There you can choose these options:

| Setting | Choices |
|---|---|
| Appearance | Light, Dark, or System |
| Interface scale | Small, Medium, or Large |
| Keyboard shortcuts | Disabled, Awesonix, Awesonix Left-handed, Gmail-compatible, or Outlook-compatible |

Awesonix Mail saves these choices for your account. They update on your other sessions and devices.

### Signature colour

Open **Settings > General > Appearance** and choose one of 29 signature colours. The colour applies to actions, links, selected areas, and unread markers.

These are the colours:

| | | | | | |
|---|---|---|---|---|---|
| Awesonix | Red | Coral | Cocoa | Orange | Peach |
| Amber | Gold | Yellow | Lime | Emerald | Green |
| Mint | Teal | Cyan | Sky | Slate | Blue |
| Navy | Indigo | Lavender | Violet | Purple | Plum |
| Orchid | Fuchsia | Rose | Pink | Tomato |  |

**Awesonix** is the default brand colour and shows the Awesonix icon. Your colour belongs to your account. It looks the same in light and dark mode.

### Language

Open **Settings > General > Language & region**. Choose Automatic or one of the languages in the list. Each language shows in its own name. The list holds English, English (United States), Hungarian, German, Dutch, French, Japanese, Korean, Chinese (Simplified), Russian, Ukrainian, Polish, Spanish, and Italian.

- **Automatic** follows the language list of this browser. If no language matches, Awesonix Mail uses English.
- A language that you choose after sign-in is saved with your account. It follows you to other sessions and devices.
- A language that you choose before sign-in applies only to this browser. Awesonix Mail does not copy it into your account.
- The language changes at once. You do not need to restart.
- Weekday and month names follow the interface language. The date format setting changes only the order and the numbers of a date.
- Texts from the server follow the language too. This includes assistant action cards, assistant errors, import warnings, and notifications.
- Stock folders of your mail provider, such as Inbox, Sent, Bin, and Spam, show a translated label. The name at your provider does not change. Custom folders keep their names.
- Replies and forwards use "Re:" and "Fwd:" in every language.

Awesonix Mail never translates your own content. This includes custom provider folder names, contact data, subjects and message text, and signatures. This manual is in English only.

### Search and choose a language

You choose a language in four places: **Interface language**, **My language**, the **Answer language** of the assistant, and **Filter languages**. Each place has a search field, so you can find a language fast even when the list is long.

To choose one language on the web:

1. Select the language field. A list opens, and the cursor is in the search field.
2. Type a few letters. You can type the name in its own language, such as "Magyar", or the English name, such as "Hungarian". A part of the language code, such as "hu", also works. Capital letters and accents do not matter.
3. Press **Enter** to choose the first language in the list. Or select any language with the mouse or the arrow keys.

The best matches come first. The search field shows only when the list has more than seven languages.

On your phone, select the language row. A full-screen list opens, with the same search field. Select a language to choose it. The list closes at once. Use the back arrow, or the back gesture on Android, to close the list without a change.

### My language, answer language, and filter languages

- **My language** is your mother tongue. Choose it in **Settings > General > Language & region**. It does not change the language of the app. **Not set** is at the top of the list.
- **Answer language** is in **AI behaviour**. It sets the language of the assistant's summaries, suggestions, and answers. **App language** and **Same as the message** are at the top of the list.
- **Filter languages** is in **Settings > Mail > Filter settings**. It decides which languages the keyword groups of your rules use.

**Filter languages** has three presets: **My language + English**, **All languages**, and **Custom pick**. When you choose **Custom pick**, one field shows your languages as small tags with a count, such as "2 of 14 languages".

To change a custom list on the web:

1. Select the field under **Custom pick**. A list opens with a search field and a check box for each language.
2. Select a language to add it or to remove it. The list stays open, so you can change several languages. Each change is saved at once.
3. Select **Select all** to choose every language. It is hidden while you search.
4. To remove one language, select the **x** on its tag. Or open the list and clear its check box.
5. Press **Escape** to close the list.

At least one language must stay chosen. The last one cannot be removed, and a line under the field says so. A custom list can hold at most 24 languages. When the list is full, you can only remove languages.

On your phone, select the field to open a full-screen list. Choose your languages, then select **Done**. The button shows how many you chose, such as **Done (2 selected)**. The back arrow and the Android back gesture close the list and drop your changes. After **Done**, select **Save** to keep the change, as with other phone settings.

### Keyboard shortcuts

Awesonix favours your left hand on the keyboard. Awesonix Left-handed favours your right hand, leaving your left hand for the mouse.

| Action | Awesonix | Awesonix Left-handed |
|---|---|---|
| Write a message | c | m |
| Search | s, / | k, / |
| Reply | r | p |
| Forward | w | i |
| Archive | e | o |
| Star | f | h |
| Toggle read/unread, mixed becomes read | q | y, z |
| Toggle read/unread, mixed becomes unread | u | u |
| Delete/Bin | x, Delete | n, Delete |
| Unified inbox | g i | j i |
| Administration, administrators only | g e | j o |
| Light/dark theme | t | t |
| Command palette | g c | j p |
| Keyboard shortcuts | v, ? | l, ? |

Both read-state toggles reverse a uniform selection. Q/Y/Z makes a mixed selection read; U makes it unread.
The first G or J starts a sequence with a 1.2-second timeout. Escape cancels it.

Unavailable sequences perform no other command. Existing deletion confirmations still apply.
Both Awesonix profiles accept / for Search, ? for shortcuts, and Delete for Delete/Bin.

Gmail-compatible and Outlook-compatible retain Ctrl+K/Command+K for the palette and ? for shortcuts.

Select the new profile only after updating your server and clients together.
Before downgrading, select and save a profile that the older release recognises.
Older clients can reject the new profile or display standard Awesonix. Restore its exported settings with an updated importer.

Mail shortcuts follow the active workspace and selection. The command palette shows actions that are currently available.
Disabled turns off global shortcuts. Visible controls remain available.

Search and other text fields keep their text-editing keys. Menus and dialogs keep their navigation and Escape behaviour.
Gmail and Outlook allow `Ctrl+Enter` (`Command+Enter` on macOS) to send from the active mail editor.
Outlook also allows `Ctrl+S` (`Command+S` on macOS) to save that editor. These commands cannot send from an unrelated field.

A pending submission prevents another Send. In Outlook, Escape keeps an edited composer open if its draft cannot be saved.

Symbol shortcuts use the character produced by the keyboard layout. AltGraph text and dead keys remain available for typing.
A browser or operating system may reserve a key combination.

Current layouts passed macOS Chromium checks with US and Hungarian input, and Android WebView checks with platform-delivered US keys.
Android and iOS Simulator builds, installation, and profile/reference UI checks passed.
Windows and actual iOS keyboard input are deferred and remain unverified.

The Gmail-compatible and Outlook-compatible profiles support only part of the shortcuts of those products. The **Keyboard shortcuts** list shows which ones work. It also names the groups of commands that are not supported.

The Gmail-compatible profile supports these shortcuts:

| Key | Action |
|---|---|
| `c` | Write a new message |
| `Ctrl+Enter` | Send |
| `/` | Search |
| `g i` | Unified inbox |
| `g d` | Drafts |
| `g t` | Sent |
| `e` | Archive |
| `#` | Move to bin |
| `s` | Star |
| `r` | Reply |
| `a` | Reply all |
| `f` | Forward |
| `Shift+i` / `Shift+u` | Mark as read / mark as unread |
| `x` | Select the message |
| `* a` | Select all loaded messages |
| `j` / `k` | Go to the next or previous loaded message |

The Unified inbox, star, and loaded-message selection shortcuts are adapted for Awesonix Mail. They do not claim to work exactly like Gmail.

## Use the AI assistant (optional)

The AI assistant is called Vigil. AI features are off by default. They use AI connections that you add yourself.

### Turn on AI and add a connection

1. Open **Settings > General > AI configuration**.
2. Turn on AI for your account.
3. Add one or more connections. You can use OpenAI-compatible, Ollama, or Anthropic connections.
4. For each connection, check the address, the model, local or cloud, the credential, and the timeout.
5. Select **Test** for the connection before you rely on it.

Awesonix Mail tries the connections in the order shown.

The test checks two things: that Awesonix Mail can reach the provider, and that the answer has the format that analysis needs. You can test a connection only if it is enabled and your privacy setting allows it.

### Set the timeout

The timeout is the longest time that one request to the provider can take. If you leave it empty, it is:

- 300 seconds for a local connection.
- 60 seconds for a cloud connection.

A model that runs on a CPU can need several minutes for a long conversation. Raise the timeout instead of letting requests fail.

### Set the context window and the answer limit

Two more fields are below **Timeout**. They matter most for briefings.

- **Context window (tokens)** is the size of the model's memory for one request. If you leave it empty, Awesonix Mail uses the size that the server reports. If the server reports none, it uses 131,072 tokens. Enter a value from 1,024 to 2,000,000.
- **Answer limit (tokens)** is the longest answer that Awesonix Mail asks for. If you leave it empty, Awesonix Mail sets it to fit each request. Enter a value from 256 to 131,072. It must be less than the context window.

Set the context window if your local server runs a smaller window than it reports. Set it too if the server reports none.

### Choose your privacy setting

| Setting | Effect |
|---|---|
| **Local providers only** | Cloud connections cannot be used. |
| **Local and cloud providers** | Selected mail content can leave your Awesonix Mail installation through the cloud provider. |

The server makes all calls to the provider. Your stored API keys never go back to the browser.

"Local" describes the privacy limit that you want. It does not prove that a custom address is really local. Check every address that you add.

### Ask about a message

1. Turn on AI.
2. Select a message.
3. Open **Assistant** in the mail command bar.

Then you can do these things:

- Ask questions about the message.
- Ask for a reply draft that you can edit.
- Review suggested tasks and calendar events.

Each suggestion shows how confident the assistant is and the details that support it. Awesonix Mail sends no reply and creates no task or event until you confirm the action. Always check the generated text and the dates that the assistant found.

### What the assistant can look up

Before it answers, Vigil can look things up by itself. It can do this several times for one request. It only reads your data when it looks things up. For example, it can do these things:

- Search your mail, calendar, tasks, and contacts.
- Read conversations and attachment lists.
- Read agendas and find free time.
- Find overdue tasks and birthdays.

### Let the assistant make changes

In the Vigil chat, you can give instructions. For example:

- "create a dentist appointment next Friday at 10"
- "move it to 11"
- "add a task to pay the invoice by 1 November"

Vigil can prepare these changes:

- Create and change events and tasks.
- Delete an event.
- Complete or archive a task.
- File, star, or mark mail.
- Create or change contacts.

Vigil shows each change as a card. The card shows exactly what will happen. Nothing changes until you apply the card with one click. Then the server creates or updates the item in your Awesonix Mail calendar or task board.

- If the item changed in the meantime, the change is refused.
- If a change can be reversed, its card shows an **Undo** button after you apply it.
- Change repeating events and events in provider calendars in the calendar editor. Vigil cannot change them.
- Vigil cannot delete a meeting. Its other changes keep the guests and send no mail. See [Meetings and invitations](#meetings-and-invitations).

### Analyse mail automatically

You can analyse a message by hand without turning on background analysis.

In **Automatic mailbox analysis**, each mailbox starts as off. For each mailbox you can do these things:

- Turn on analysis of new mail that qualifies.
- Separately allow supported attachments.
- Exclude folders or senders.

> **Warning:** A cloud setting or automatic analysis can send message or attachment content to the provider. This can happen without you opening the message. Use exclusions and the retention settings of the provider to match your privacy needs.

### Standing instructions

In **AI behaviour > Standing instructions**, you can write a short text. The assistant reads it on every request. Use it to say how the assistant should answer and what it should know about you. Examples are your language, your time zone, or the calendar to use.

- The text can have up to 1500 characters. This keeps the cost of each request small and equal.
- The text describes style and facts only. It cannot give the assistant any permission.

### Briefings

In **AI behaviour > Briefings**, you can turn on two separate features:

| Briefing | When it runs | What it covers |
|---|---|---|
| Daily briefing | At its delivery time. | All eligible mail in the completed reporting period, including mail received overnight. |
| Mail activity briefing | When a set number of newly analysed messages is reached and its waiting time has passed. It has no delivery time. | Mail in the activity reporting period. |

A new briefing publishes only when its complete reporting period and account data are ready. The previous completed briefing remains readable during preparation or retry.

Reports have Overview; Actions and deadlines; Upcoming events & birthdays when enabled; Updates; and Promotions. Updates and Promotions use a few sentences each. Action items stay explicit and link to their source. All text in a briefing, including the actions, uses your **Answer language**. With **Same as the message**, briefings use the app language. Names, references and amounts stay as they are in the mail.

### How a briefing is made

A briefing reads the whole text of each message in the period. It sends the messages to the AI in a few large groups, one group at a time. It does not need to analyse each message first, so mail from before you turned on AI is included. A message that is in two folders counts once.

If one message cannot be summarised, the briefing shows one line for it with the sender and subject and the words "summarised from subject only". The Details section gives the reason. A briefing with such lines, or with mail that is no longer available, is partial.

A message whose reply state cannot be checked is never shown as unanswered. Details counts it.

A briefing fails only if the AI provider is not available, or if too many messages fail. That means more messages than 3, or than 10 % of the messages in the period, whichever number is larger. A failed briefing keeps the previous one readable. Select regenerate to try again. If the first connection is not available before work starts, Awesonix Mail tries the next one.

Existing activity-triggered reporting starts from upgrade activation. It retains completed briefings and avoids unexpected historical analysis.

Each briefing tracks its own progress. So the same important message can appear in both. This is intended.

If the service missed several briefing delivery times, it creates one catch-up daily briefing with a label. It does not create one card for each missed day.

### What happens to AI data

- Turning off AI stops new use. It does not delete data that the provider already has. Use the retention and deletion controls of your provider if needed.
- Briefings, conversations, analysis history, jobs, suggestions, and the panel state on each device are not in the settings export. To keep this AI activity, you need a full backup of the database and secrets.

## Use the Android and iOS app

### Pair the app

You create a pairing code in the browser. Then you scan or type the code in the app.

1. Open **Settings > Account > Device access** in the browser.
2. Choose **Full mobile access** or **Read-only mobile access**.
3. Select **Create pairing code**.
4. Scan or type the code in the app.

[Installation step 6](../installation/6-phone.md) explains this step by step.

> **Warning:** Do not approve a pairing code that you did not create yourself.

### What the app can do

Both access levels synchronise encrypted data that belongs to you. This includes Mail, Contacts, Calendar, Tasks, Notes, My Day, search, settings, notifications, and Vigil. The app shows only what its access level allows.

| Access level | What the app can do |
|---|---|
| **Read-only mobile access** | Read everything, including notes. The app hides all buttons that change data. |
| **Full mobile access** | Write and send mail, save drafts, mark read or starred, move, copy, archive, move to Bin, restore, and delete permanently. Change your own contacts, calendars, events, task boards, tasks, notes, and some account preferences. |

Notes uses the access that the phone already has. A phone paired before Notes existed gets Notes without a new pairing. See [Notes on your phone](#notes-on-your-phone).

Contacts and calendars from a provider are read-only in the app. To edit such an item, copy it into one of your own Awesonix Mail collections first.

### Share calendars and contacts with your Android phone

On Android, use **Device calendar and contacts** in the app. It shares your Awesonix Mail calendars and address books with other apps on that phone.

- These switches apply only to that phone.
- In the settings of each calendar and address book, choose if it can be shared.
- The contacts of the phone also sync into Awesonix Mail, in one direction only. Edit those contacts in their original app.
- Contact photos from the phone are not imported.

This feature is not available on iPhone yet.

### Keep messages offline

1. Open a message.
2. Choose **Keep offline**.

The app saves the complete, cleaned message, its embedded content, and its attachments in encrypted storage on the phone. **Available offline** appears only after the app has saved and checked the complete message. To give one saved attachment to another app on the phone, use **Open or share attachment**.

Offline messages are a cache, not a backup:

- You can read them without a network, also after you restart the app.
- If the server deletes a message or removes it after its retention time, the app removes it when it reconnects.

### Make changes offline

With full access, the app first saves your changes in an encrypted outbox on the phone. It sends them when the connection returns. If a change conflicts with newer data on the server, the app keeps your mobile draft for review. It does not overwrite the newer data.

### Protect your phone

- Protect the phone with a passcode and the encryption of its operating system.
- If you lose the phone, revoke it at once in the browser in **Settings > Account > Device access**.

A revoked phone can still show the content that it saved, but only until it connects again. When the phone confirms the revocation, it removes its access, the encrypted data, the unsent changes, and the conflict drafts.

To remove the same local data yourself, use **Account > Remove account** in the app. This does not change any data on the server.

### Get notifications on your phone

After pairing, the app shows **Device notifications**.

**On Android:**

- The app gets notifications directly from your paired Awesonix Mail server. Firebase is not required.
- To check more often, turn on **Keep an icon active for frequent updates**. The app then checks in the foreground.
- Normal background checks are best effort. Battery optimisation can delay them.

**On iPhone:**

1. Choose **Allow best-effort notifications on this iPhone**. This allows local notifications.
2. For reliable delivery, add the Awesonix Mail website to the Home Screen of the iPhone.
3. Turn on **Web browser** notifications.

The iPhone app checks your server at these times:

- When you open or resume the app.
- While the app is visible.
- Whenever iOS allows a background refresh.

Set notification categories, quiet hours, private previews, sound, and unread badges in the web app. Open **Settings > General > Notifications**.

## Move your settings to another installation

You can export your settings to an encrypted file and import them in another Awesonix Mail installation.

> **Warning:** Never send the backup passphrase, your account password, or your provider passwords to support staff. Do not put them in logs or screenshots.

### Export your settings

1. Open **Settings > Account > Security > Export settings**.
2. Enter your current Awesonix Mail account password.
3. Enter a separate backup passphrase of at least 12 characters.
4. Download the JSON file.

Keep the passphrase separate from the JSON file. Without the passphrase, nobody can restore the file.

The export contains your My language and Filter languages settings. An installation older than version 0.110.0 cannot import this export. It refuses the file.

### Import your settings

1. On the new installation, sign in to the account that should get the settings.
2. Choose **Import settings**.
3. Select the JSON file.
4. Enter the current password of this account.
5. Enter the original backup passphrase.

The import adds the settings from the file to the account. Settings that are not in the file stay the same. You can safely repeat the same import. It does not create duplicate mailboxes, identities, trusted senders and domains, or folder assignments.

### After the import

The import leaves some items inactive on purpose:

- Imported mailboxes are always **suspended**.
- All imported filter rules are inactive. This also applies if a matching rule already exists.

Before you activate mailboxes and rules, review these items:

- Server addresses and credentials.
- The safety mode.
- Folder assignments.
- Filter rules.
- The AI providers and the AI privacy setting.

Other facts about the import:

- The quotas and remote retention rules of the new installation do not change. Its administrator manages them.
- Rule destinations and AI folder exclusions use folder names relative to the mailbox. So they can connect again after the new installation synchronises its folders.
- Awesonix Mail decrypts secret values from the file. Then it encrypts them again with the key of the new installation.

### Auto-bin deadlines after an import

Auto-bin deadlines and removals are imported as fixed values. Their countdown does not start again. This includes deadlines that a rule read from a mail or from a parcel delivery. A deadline becomes active only after synchronisation finds the exact provider account, folder, and message. Records that are not found yet stay saved for later matching or export.

If the new installation already has its own choice for a message, that choice wins. Rejected records stay inactive as archives.

The export does not contain the waiting parcel notices. After a restore into a fresh installation, a progress notice waits again until the rule meets it. This happens with new mail or with **Run now**.

### What the export contains

- Mailbox addresses and encrypted provider passwords.
- Sending identities.
- Mail filters.
- Trusted senders and domains for images.
- Folder assignments.
- Interface and navigation preferences.
- Notification delivery and reminder preferences.
- Task boards and tasks.
- Your note sections, pages, and notes with their tags, links, reminders, and images. Private notes are included and are encrypted again on the new installation. Your lock time for private notes is included; set a new PIN there.
- Your own calendars and events, with the guests and answers of your meetings. An import sends no meeting mail.
- Your own contacts and their photos.
- Your avatar.
- Local folders and their messages.
- Open drafts with their attachments.
- AI settings: whether AI is on, the privacy setting, the connections in their order, and the API keys.
- AI settings: automatic analysis rules, excluded senders and folders, Vigil panel behaviour, standing instructions, quiet hours, and briefing rules.

For the founding administrator, the export also contains the installation defaults and the policy that administrators manage.

Briefing schedules use the Calendar display time zone that the import restores with the account. The backup passphrase protects the provider secrets.

### What the export does not contain

These security items are never exported or imported:

- Authenticator setup, TOTP secrets, and recovery codes.
- Login passwords and sessions.
- Security audit data.
- DAV and app credentials, which you can revoke separately.
- Browser and mobile notification endpoints.
- The notes PIN, the unlock state of private notes, the people who have access to your pages, and public links.

So the import does not change the authenticator or the paired devices of the account on the new installation.

These items are also not in the export:

- Mail downloaded from providers.
- Search indexes.
- History of actions and notifications.
- AI analysis history and suggestions.
- Jobs.
- Copies of provider data, such as CardDAV mirrors.
- Layout and view choices that are saved on each device.
- Service and database configuration.
- The master key of the installation.

Do you need to keep history and copies of provider data for the whole installation? Then use the full backup of the database and storage. Your operator manages this backup.

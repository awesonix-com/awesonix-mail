# Install Awesonix Mail

This guide takes you from an empty server to a working Awesonix Mail. Follow the steps in order. Each step tells you what to do, what you should see, and what to do if it does not work.

You do not need to be an IT expert. You must be able to open a web page on your server or NAS and copy text into forms.

## What you get

Awesonix Mail runs as one container on your own server. It keeps a copy of your mail, calendars, contacts, and tasks in a MariaDB database and in its own storage folders. You open it in a web browser, and you can also use the Android or iOS app.

## Before you start

Make sure that you have these items:

- A server or NAS that can run Docker containers. Unraid, Synology DSM 7.2, and Docker on Linux, Windows, or macOS are supported.
- A 64-bit Intel or AMD processor (amd64). Check this first, because some NAS models use ARM processors.
- At least 2 GB of free memory and 2 processor cores for Awesonix Mail.
- Free disk space for your mail. Plan about twice the size of the mailboxes that you connect.
- An SSD for the database, if possible. A database on an SSD is much faster.
- A computer with a modern web browser on the same network.
- The address and password of at least one mail account (IMAP).

The installation takes about 30 to 60 minutes.

## The steps

1. [Prepare](1-prepare.md): choose your platform and write down the values that you need.
2. [Create the database](2-database.md): install MariaDB and create an empty database for Awesonix Mail.
3. [Install Awesonix Mail](3-install.md): install the container and connect it to the database.
4. [Run the setup wizard](4-setup-wizard.md): create the first administrator and the basic settings.
5. [Connect your mailbox](5-mailbox.md): add your first mail account.
6. [Connect your phone](6-phone.md): install the app and pair it with your server.
7. [Make it safe](7-secure.md): set up HTTPS and backups.

## If you get stuck

Each step has a "If it does not work" section. The [troubleshooting guide](../troubleshooting/README.md) has more help.

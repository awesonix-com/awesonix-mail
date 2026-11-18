# Step 1: Prepare

In this step, you choose your platform and write down the values that you need in the next steps.

## Choose your platform

Find your platform in this table. Use the same platform in all later steps.

| Your server | Platform in this guide | How you install containers |
|---|---|---|
| Unraid server | **Unraid** | The **Docker** and **Apps** tabs in the Unraid web interface |
| Synology NAS with DSM 7.2 | **Synology** | **Container Manager** |
| Linux, Windows, or macOS computer with Docker | **Docker** | Docker Compose in a terminal |

Awesonix Mail runs on 64-bit Intel and AMD processors (amd64). A Mac with Apple silicon or another ARM computer can run it only with emulation. Emulation works, but Awesonix Mail is slower. Step 3 shows the extra line for this case.

## Know the parts

Awesonix Mail uses two containers:

- **MariaDB** stores the data, for example users, settings, and the list of your messages.
- **Awesonix Mail** is the application. It stores mail files, its secret key, and its logs in three folders.

The two containers talk to each other over your network. Awesonix Mail needs the address of MariaDB, a database name, a user name, and a password.

## Plan a domain name

A domain name is an address such as `mail.example.com`. You do not need it to start, but plan for it now if you want to use the phone app.

With a domain name, these things work:

- The phone app reaches your server at home and on the road.
- You can get a trusted HTTPS certificate. Certificates need a domain name.
- The address stays the same when the IP address of your internet connection changes.

From version 1.0, the phone app connects only over HTTPS. [Step 7](7-secure.md) shows how to get a domain name, also free of charge.

## Write down your values

Copy this table into a text file or onto paper. Fill it in during steps 2 and 3. Keep it in a safe place because it contains passwords.

| Value | Example | Your value |
|---|---|---|
| Server address (IP address of your server or NAS) | `192.168.1.10` | |
| Database root password | a long random password | |
| Database name | `awesonix_mail` | |
| Database user name | `awesonix` | |
| Database user password | a different long random password | |
| Awesonix Mail web address | `http://192.168.1.10:8180` | |
| Domain name, if you have one | `mail.example.com` | |

> **Warning:** Use long random passwords, for example 24 characters or more. A password manager can make them for you. Do not use the same password twice.

To find the IP address of your server:

- **Unraid:** The address shows at the top of the Unraid web interface and in your browser address bar.
- **Synology:** Open **Control Panel > Network > Network Interface**.
- **Docker on a computer:** Use the address of that computer on your network. If you use only this computer, use `localhost`.

## Next step

Continue with [Step 2: Create the database](2-database.md).

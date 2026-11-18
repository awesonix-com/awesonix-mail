# Step 7: Make it safe

Your installation works now. In this last step, you protect the connection and your data.

## Use HTTPS

Without HTTPS, your password and your mail travel unencrypted over the network. Use HTTPS if you open Awesonix Mail from outside your home network or from a phone.

### Get a domain name

HTTPS needs a domain name. Choose one of these ways. Free plans change, so check the current terms on the website of the provider.

| Way | Examples | Cost | Notes |
|---|---|---|---|
| Free dynamic DNS name | DuckDNS (`duckdns.org`), Dynu, deSEC (`dedyn.io`), FreeDNS (`afraid.org`), No-IP | Free | You get a name such as `myhome.duckdns.org`. The free No-IP plan must be confirmed every 30 days. |
| Name from your NAS or router | Synology DDNS (`synology.me`), FRITZ!Box MyFRITZ!, ASUS DDNS | Free | Your device keeps the name up to date by itself. |
| Your own domain | any domain registrar, with a DNS provider such as Cloudflare | About the price of a domain per year | Most flexible. A small tool or container updates the address when it changes. |
| No open ports: Cloudflare Tunnel | Cloudflare Zero Trust | Free | Needs your own domain at Cloudflare. Cloudflare decrypts the traffic on its servers. |
| No open ports: private network | Tailscale | Free for personal use | Works only on devices that run the Tailscale app, also on the phone. Tailscale gives HTTPS names that end in `ts.net`. |

A dynamic DNS name points to the IP address of your internet connection. Your router or NAS updates it when that address changes. With dynamic DNS or your own domain, forward port `443` on your router to the reverse proxy. Do not forward port `8180` directly.

### Set up the reverse proxy

A reverse proxy receives HTTPS requests and forwards them to Awesonix Mail. Examples are Nginx Proxy Manager and the Synology reverse proxy. With Cloudflare Tunnel or Tailscale, the tunnel takes this role.

1. Install a reverse proxy and give it a domain name and a certificate, for example from Let's Encrypt.
2. Forward the domain to the Awesonix Mail address, for example `http://192.168.1.10:8180`.
3. Turn on HTTP/2 and compression for text files.

If you use Nginx Proxy Manager, add these lines in the **Advanced** tab of the proxy host. Without them, the application loads about three times more data.

```nginx
gzip on;
gzip_vary on;
gzip_min_length 1024;
gzip_types text/css application/javascript text/javascript application/json image/svg+xml;
```

## Keep the diagnostics port closed

The diagnostics listener is off by default on every platform, and no template publishes port `8182`. You do not need it for daily use.

If you installed an earlier version with Docker or Synology, close the port:

1. Open `docker-compose.yml`.
2. Delete the line `- "8182:8182"`.
3. Start the project again.

For a short diagnostic session, the [deployment reference](../deployment/README.md) explains how to turn the listener on. Turn it off again after the session.

## Back up your data

Back up all of these parts together. A backup is complete only if all parts are from the same time.

| Part | Where it is | Why it is important |
|---|---|---|
| Database | the MariaDB data folder, or a database export | users, settings, and the list of your messages |
| `secrets` folder | the Awesonix Mail storage | the key that encrypts your passwords; without it, a backup cannot be used |
| `mail` folder | the Awesonix Mail storage | your downloaded mail and attachments |
| `logs` folder | the Awesonix Mail storage | help for troubleshooting |

> **Warning:** Keep backups outside the folders that Awesonix Mail uses. Keep the `secrets` folder safe, because it unlocks your stored passwords.

The easiest backup is to stop both containers and copy all four parts. On Unraid, the **Appdata Backup** app can do this on a schedule.

Test a restore at least once. The [operations guide](../operations/README.md) explains backup and restore in detail.

## You are done

Awesonix Mail is installed and safe. Next, read the [user guide](../user-guide/README.md) to learn how to use mail, calendar, contacts, and tasks.

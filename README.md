# oneroa.systems

The website for Oneroa Systems: four static pages, one stylesheet and two self-hosted fonts. There's no build step, no JavaScript, and nothing loads from a third party.

## Pages

| File | Page |
|---|---|
| `site/index.html` | Home: what Oneroa Systems is, with links to Continuity and Labs |
| `site/continuity.html` | The Continuity product: directories and sites kept online for twenty years, billed once |
| `site/labs.html` | Research projects; the inference co-op, coming soon |
| `site/contact.html` | Rich's photo, bio and email address |

The header and footer are repeated in each page; change all four together.

## Preview

```
cd site && python3 -m http.server 8765
```

Then open http://127.0.0.1:8765/.

## Archive photographs

Every page has a black-and-white archive photo, credited underneath, as on mountainviewsmartcontracts.com. Continuity's is still a placeholder. To add or change one:

1. Find a photo that may be used commercially, since this is a business's site. [DigitalNZ](https://digitalnz.org) searches most New Zealand archives at once; filter by usage "Use commercially" (CC BY, or no known copyright). natlib.govt.nz blocks scripts, but DigitalNZ's API (`api.digitalnz.org/v3/records.json`) doesn't.
2. Put the file in `site/images/` (JPEG, about 1000–1200px on the long side).
3. In the page, put `<img src="images/FILE.jpg" alt="what the photo shows">` inside `<figure class="archive">`, in place of the placeholder `<div>` if there is one.
4. Fill in the caption: the photo's title and year, then the credit and licence the archive asks for, linked to the archive's record.

The stylesheet shows every archive photo in black and white, whatever the file is, and crops it to 4:5, so a portrait photo works best. For a landscape photo, add the class `wide` to the figure (`<figure class="archive wide">`) to show it at 3:2 instead.

## Design

Mid-century New Zealand: Crown Lynn and 1950s travel-poster colours, with the archive photos in black and white.

| Colour | Hex | Used for |
|---|---|---|
| Kōwhai | `#e0a526` | Header and footer bands, the sun |
| Harbour | `#1f3a3d` | Text |
| Lichen | `#eef0e6` | Background |
| Turquoise | `#2f8c86` (`#1f6e69` for links) | Links, the Labs rule and status tag |
| Pōhutukawa | `#b23a2e` | Keyboard focus |
| Mānuka | `#707d3c` | Step numbers |

Headings use Jost, a Futura-style geometric sans; body text uses Source Serif 4. Both are under the SIL Open Font License (`site/fonts/OFL-*.txt`). The home page's one loud moment is the kōwhai sun behind the archive photo; the dot in "oneroa.systems" is the same sun, small.

## Hosting

GitHub Pages serves the site, Cloudflare runs the DNS and the mail forwarding, and Namecheap is the registrar. `.github/workflows/pages.yml` publishes `site/` on every push to `main`. The custom domain is set in the repo's Settings → Pages, not in a `CNAME` file.

### Moving the DNS to Cloudflare

1. In Cloudflare, Add a domain → `oneroa.systems` → Free plan. Cloudflare copies the records Namecheap has now.
2. Delete the copied parking and forwarding records: the `@` record pointing at 162.255.119.189, `www` → parkingpage.namecheap.com, the five `eforward` MX records, and the TXT record starting `v=spf1 include:spf.efwd`.
3. Add the records in the table below.
4. Copy the two nameservers Cloudflare shows on this domain's Overview page (both end in `.ns.cloudflare.com`). Cloudflare gives each domain its own pair, even within one account, so don't reuse another domain's: socialnetwork.health's pair refuses to answer for oneroa.systems. In Namecheap, Domain List → oneroa.systems → Manage → Nameservers → Custom DNS, paste both, and save. Namecheap's own mail forwarding stops here.
5. Verify the domain with GitHub. GitHub doesn't send the code; you ask for it. In Rich's account settings (github.com/settings/pages, not the repo's), click Add a domain and enter `oneroa.systems`. GitHub shows a TXT record; add it in Cloudflare, then click Verify. Verify only passes once the new nameservers have spread, so it can take a few tries. Verifying stops any other GitHub account from serving a site on the domain.
6. GitHub issues the certificate within about an hour of the DNS pointing at it. Then tick Enforce HTTPS in the repo's Settings → Pages. If no certificate starts (the repo's Settings → Pages shows no progress), the custom domain was probably saved before the DNS was ready: remove it, save, add it back, and save.
7. Set up mail (below).

`scripts/dns-status oneroa.systems` in Rich's `rbops` repo shows how far a nameserver change has spread.

### DNS records

Set every website record to **DNS only** (grey cloud), not Proxied. Cloudflare proxies copied records by default, and GitHub can't issue a certificate through the proxy.

| Type | Name | Content | Proxy |
|---|---|---|---|
| A | `@` | four records: `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153` | DNS only |
| AAAA | `@` | four records: `2606:50c0:8000::153`, `2606:50c0:8001::153`, `2606:50c0:8002::153`, `2606:50c0:8003::153` | DNS only |
| CNAME | `www` | `richbodo.github.io` | DNS only |
| TXT | `_github-pages-challenge-richbodo` | the code GitHub shows when you add the domain (step 5) | |
| MX, TXT | `@` and others | added by Cloudflare Email Routing (below) | |

Cloudflare adds the domain to each Name, so `www` means www.oneroa.systems. `richbodo.github.io` is GitHub's address for all of Rich's Pages sites; GitHub picks this repo because its custom domain is `oneroa.systems`, and it redirects www to the bare domain.

### Mail

Mail to rich@oneroa.systems goes through Cloudflare Email Routing to Rich's Gmail, so Gmail does the spam filtering. Replies go out through Gmail as rich@oneroa.systems.

To receive:

1. In Cloudflare, oneroa.systems → Email → Email Routing → Get started.
2. Under Destination addresses, add Rich's Gmail address, then click the link in the email Cloudflare sends there.
3. Under Routing rules, add the custom address `rich`, with the action Send to an email and the Gmail address as the destination. The catch-all rule forwards every other address too, but it also forwards spam sent to made-up names, so leave it off unless it's needed.
4. Cloudflare adds its records itself, without asking: three MX records (`route1`, `route2` and `route3.mx.cloudflare.net`), an SPF TXT on `@` (`v=spf1 include:_spf.mx.cloudflare.net ~all`), and a DKIM TXT at `cf2024-1._domainkey`. It won't turn on while other MX records exist, so the `eforward` ones must be gone.

To send as rich@oneroa.systems from Gmail:

1. Turn on 2-Step Verification for the Google account, then make an app password at myaccount.google.com/apppasswords.
2. In Gmail, Settings → See all settings → Accounts and Import → Send mail as → Add another email address. Use the name "Rich Bodo" and the address rich@oneroa.systems, and leave "Treat as an alias" ticked.
3. For the SMTP server, use `smtp.gmail.com` (not smtp.google.com), port 587, TLS. The username is the Gmail address, and the password is the app password; its spaces don't matter. Gmail clears the password after a failed try, and "Show error details" gives the reason: "Application-specific password required" means it got the account password instead of the app password, and "Couldn't connect to the server" means the server name is wrong.
4. Gmail sends a confirmation code to rich@oneroa.systems. It arrives back in Gmail through Cloudflare. Enter it.
5. In Cloudflare, edit the SPF TXT record on `@` so it covers Gmail too: `v=spf1 include:_spf.mx.cloudflare.net include:_spf.google.com ~all`. Edit the existing record rather than adding a second one, because a domain may have only one SPF record.

Test from a different account, not from the Gmail itself. Gmail hides mail you send yourself through a forward, so it shows only in Sent. Some recipients see "via gmail.com" beside the address, because mail sent this way isn't signed by oneroa.systems. For the same reason, if a DMARC record is ever added, keep it at `p=none`.

### mountainviewsmartcontracts.com

The same Cloudflare account runs mountainviewsmartcontracts.com until it has a repo of its own. The domain is moving into Rich's Namecheap account. Set Cloudflare's nameservers there once it arrives, as in step 4 above.

Delete the two copied `@` records pointing at 192.30.252.153 and 192.30.252.154. They're GitHub's old addresses, and they can't get a certificate. Then add:

| Type | Name | Content | Proxy |
|---|---|---|---|
| A | `@` | the same four as oneroa.systems | DNS only |
| AAAA | `@` | the same four as oneroa.systems | DNS only |
| CNAME | `www` | `mvscorg.github.io` | DNS only |
| TXT | `_github-pages-challenge-mvscorg` | the code from github.com/organizations/mvscorg/settings/pages → Add a domain | |

Its MX records point at a Google Workspace. Keep them, along with the Google SPF record (`v=spf1 include:_spf.google.com ~all`) and the `google-site-verification` TXT, while anyone still uses those addresses. To switch to Email Routing instead, delete the Google MX records, then follow the mail steps above.

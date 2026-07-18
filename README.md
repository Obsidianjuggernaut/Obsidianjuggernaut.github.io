# NSBC Communion Delivery dashboard

Mobile-first dashboard for New Salem Baptist Church's monthly communion delivery. Deacons open one link, see the month's visits grouped by team, and get tap-to-call and tap-to-navigate for every stop.

Live site: https://obsidianjuggernaut.github.io/

## How a deacon posts the monthly list

1. Open the site and tap "Post a new month".
2. Upload the Excel spreadsheet from the church office.
3. Check the summary (service date, member count, month) and enter the ministry code.
4. Tap "Post the list & get the link", then Copy / Text / Email the short link.

No JSON, no git, no file naming. The month is read from the spreadsheet itself.

## How it works

- **This repo is the site.** GitHub Pages serves `index.html` and the published lists in `lists/` (one `<mmmyyyy>.json` per month, plus `manifest.json` naming every published month).
- **Publishing** goes through a Cloudflare Worker. The page POSTs the parsed list and the ministry code; the Worker verifies the code, commits `lists/<id>.json` and the updated manifest with a repo-scoped token, and returns the short link `?list=<id>`.
- **Loading**: `?list=<id>` opens that month. With no parameter, the page opens the current month, or falls back to the newest published month (via the manifest) with a note. Old `#data=` links still render.
- **Privacy**: the page is `noindex` and `robots.txt` disallows crawling — the lists contain member addresses and phone numbers.

## Monthly data format

`lists/<mmmyyyy>.json`:

```json
{
  "members": [{ "firstName": "", "lastName": "", "fullAddress": "", "phone": "",
                "pocFirst": "", "pocLast": "", "pocPhone": "", "pocEmail": "",
                "deaconTeam": "", "notes": "" }],
  "svcDate": "Sunday, July 5, 2026",
  "onDeck": [], "unavail": []
}
```

## Spreadsheet expectations

Row 1-4: church name, address, service date (weekday name triggers detection). Header row contains "First Name"; columns are matched by name (Address, City, State, Zip, Phone, Point of Contact fields, Deacon(s) Team Assigned, Notes). A "Deacon Teams on Deck" section at the bottom, with an "Unavailable" subsection, is parsed into chips.

## Backup publish path (if the Worker is down)

The `update-communion` skill on Sedric's Mac copies a month JSON into `lists/`, updates the manifest, commits, and pushes. Same result, no Worker involved.

## History

The dashboard previously lived at `/nsbc-communion-delivery/`; that address now redirects here, forwarding `?list=` and `#data=` links. The v1 flow (Save JSON, manual git push, `#data=` long links) was retired 2026-07-17.

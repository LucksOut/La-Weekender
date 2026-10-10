# LA Weekender — Daily Auto Updates

This project preserves the supplied LA Weekender card layout and filters. The browser now fetches `events.json` each visit; GitHub Actions refreshes it daily and deploys to GitHub Pages.

## Deploy

1. Create a **public** GitHub repository named `la-weekender` and upload the contents of this folder (including the hidden `.github/workflows/daily.yml` file) to its **main** branch.
2. Go to **Settings → Pages → Build and deployment → Source → GitHub Actions**.
3. Get a free Discovery API key from [Ticketmaster Developer](https://developer.ticketmaster.com/products-and-docs/apis/discovery-api/v2/). In the repository, open **Settings → Secrets and variables → Actions → Secrets → New repository secret**. Name it `TICKETMASTER_API_KEY` and paste the key.
4. Optional: For public city/venue event calendars exposing `.ics` feeds, add **Actions → Variables → New repository variable** named `ICAL_FEED_URLS`. Enter one HTTPS iCalendar feed URL per line. Use only feeds you have permission to access; the importer intentionally skips recurring RRULE entries until a recurrence expander is added.
5. Open **Actions → Refresh and publish LA Weekender → Run workflow** to run the first refresh. View logs for source counts or errors.
6. Once deployed, visit `https://YOUR-USERNAME.github.io/la-weekender/`.

The scheduled run is **daily at 8:17 a.m. America/Los_Angeles**. GitHub may delay or occasionally drop scheduled jobs; inactive public repositories can have schedules disabled after 60 days. Manual runs remain available. The workflow also runs on pushes to `main`.

## Data policy

- Your original 61 listings are preserved initially; expired dated entries are pruned on successful configured-source refreshes.
- Ticketmaster Discovery API imports dated events within 65 miles of Downtown LA for the next 90 days, with source links and basic classification. It does **not** cover every LA event, free activity, market, or restaurant. Expand coverage by adding authorized public ICS feeds or new adapters.
- The importer never fabricates prices, free admission, venue details, or dates. Unknown prices stay blank; iCalendar entries default to `free: false` (unknown, not confirmed paid).
- Duplicate key: normalized title + start date + venue. Different venue spellings can still yield duplicates.
- If no source is configured, the workflow leaves the original data and update timestamp unchanged. If all configured sources fail, the refresh fails rather than falsely claiming fresh data.
- A partially failed refresh records the failures in `meta.source_errors`. Review Actions logs.
- Existing standing listings remain, but **are not reverified** by this importer. Check organizer links before attending.
- The `la-weekender.html` file is kept as an archival Claude-specific version, not used for the deployed site.

## Local test

Run `python -m http.server 8000` in the project folder, then visit `http://localhost:8000/`. Opening `index.html` directly via `file://` won't load JSON in many browsers.

Run `python scripts/update_events.py` to test imports (set `TICKETMASTER_API_KEY` first). Python 3.12+, standard library only.

## Security and limitations

Never commit API keys to the repository. Do not scrape sites against their terms. Github Pages is static; event retrieval happens in GitHub Actions, not in a server running continuously. A configured API can impose rate limits. For more coverage, add more authorized feed adapters and quality checks.

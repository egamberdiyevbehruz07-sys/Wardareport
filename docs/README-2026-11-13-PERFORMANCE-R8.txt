STOCK BOOK — PERFORMANCE IMPROVEMENTS r8
Release: 2026-11-13-stockbook-performance-r8

SCOPE
- Telegram Mini App only: first open now requests its default daily report together with selector metadata, removing one sequential API round trip.
- The first response can show the cached report immediately while a background BILLZ refresh continues.
- Polling while a report is building is reduced from 6 seconds to 4.5 seconds.
- Render Node server compresses text-based static assets with gzip when the client supports it.
- Telegram Mini App HTML can be cached briefly by the browser (60 seconds, stale-while-revalidate 5 minutes); the main Stock Book page remains no-cache so app updates are not hidden.
- Existing Firebase/BILLZ/Supabase data, report calculations, management analytics, and environment variables are unchanged.

IMPORTANT HOSTING NOTE
Render Free web services spin down after 15 minutes without inbound traffic. The next request wakes the service and can take about one minute. This platform limitation cannot be fixed by changing the Mini App JavaScript. For consistently fast boss access, use an always-on paid Render web service, or host the static UI on Netlify and keep a warm backend API. Do not rely on an uptime-pinger workaround for a production management system.

DEPLOYMENT
1. Back up the current repository.
2. Upload the project root contents, preserving netlify/ and tg/ folders.
3. On Render use the existing build/start settings: npm install and npm start.
4. Keep every existing environment variable unchanged.
5. After deploy, open the Mini App once and compare first-open timing with a second open. Verify that the default day appears immediately from cache when available, then updates when the background build completes.
6. Check Render Logs for server startup errors and the dashboard background worker completion message.

LIMITATIONS
- A first-ever report with no Supabase cache still requires BILLZ to return and aggregate the source data; this cannot be made instant without prebuilding/warming the cache or reducing source API work.
- This release removes one frontend request and compresses static files; it does not claim to eliminate Render Free cold starts.

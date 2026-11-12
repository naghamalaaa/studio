# Nagham Content Studio

A restaurant creative workspace built with React, TypeScript and the Sites Vinext starter. This is a functioning first release, not completion of every phase in the supplied product specification.

## Implemented

- ChatGPT sign-in for private application data. The site landing/demo is public; API records and media require authenticated, scoped access.
- Client creation, resumable onboarding draft, editable website-import suggestions with sources, brand records, project records, campaigns, and settings stored in D1.
- Per-client owner/editor/reviewer authorization; reviewer access grants are scoped by authenticated email and client. Site sharing remains an additional gate. Saving a membership does not send an invitation or expand site access.
- Protected R2 uploads/downloads, byte-range media responses, streaming size limits, MIME allowlists and basic file-signature checks, SHA-256 duplicate detection, immutable original object keys. Current upload limit: **25 MB** per file to remain inside Worker memory limits. Interrupted uploads can be restarted; they are **not multipart-resumable**.
- Browser video decoding, source dimensions/duration, representative shot samples, edge-detail scores, perceptual hashes, thumbnails, metadata, tags and rights notes. Samples are not semantic scene-boundary detection or validated aesthetic scores.
- Full-resolution still extraction into a new image asset.
- Structured video projects: source clips, in/out points, ordering, aspect ratio, independent headline/subtitle text, uploaded voice/music tracks, music level. Version snapshots and restore.
- Deterministic local revision commands remove subtitles without removing headlines or regenerating audio, remove exact repeated segments, and lower music volume. Overlapping segments are blocked at export. Automated cross-shot semantic replacement is not implemented.
- Browser-rendered PNG image export at 1080 px width. Browser video export at 720 px width, up to 90 seconds, using canvas/MediaRecorder. Codec is selected from browser-supported WebM/MP4. Original source audio is muted; uploaded voice/music are mixed. Keep the tab visible. The native editor preview shows a source clip; Render preview produces the full composition using the export renderer.
- Exports saved as media plus deliverables with project version and source lineage. Approval records, synchronized deliverable status, and timestamped feedback. Exports always require manual review; there is no claim of automatic full QC.
- Month/agenda calendar, editable captions, drag rescheduling, locked and approved post protection during dragging, draft weekly slots, basic file-count coverage warning. Calendar saves contain local date/time, IANA timezone, and UTC timestamp. No social publication is simulated.
- OpenRouter text-planning API adapter with configurable requested model, chat-catalogue connection check, request cap, no automatic retries, and client-scoped context. No model substitution. Needs the server-side OPENROUTER_API_KEY secret and actual model access.
- Light/dark themes, responsive layouts, keyboard search, modal focus trapping, and partial Finnish navigation. Content language is separate. Full Finnish interface localization remains pending.

## Deliberate platform choices

Sites provides authentication, D1, R2, and a Cloudflare Worker runtime. The requested email/password bootstrap and PostgreSQL/FFmpeg service stack were not silently simulated. Password authentication is not used. The account profile defaults to info@naghamalaa.com as contact information; user ownership is based on the stable authenticated Sites user ID.

D1 migration: `drizzle/0000_magenta_omega_red.sql`. Bindings: `.openai/hosting.json` (`DB`, `BUCKET`). Secret names only: `.env.example`.

## Setup

1. Deploy with the Sites workflow. Sites provisions D1/R2 and applies the schema migration.
2. Open the site and continue with ChatGPT to access saved work.
3. Add a restaurant. Confirm all imported facts. Upload real logos and footage; sample imagery is illustrative and never used as uploaded client media.
4. If AI text planning is needed, configure `OPENROUTER_API_KEY` as a **server-side secret** through Sites environment settings and redeploy. Save the desired model in Settings and test catalogue availability. The current preferred ID is user-specified and not guaranteed to exist.
5. To collaborate, grant the person a scoped editor/reviewer membership in the app and separately authorize site access through Sites sharing. No emails are sent by this app.

## Commands

- `npm test` runs the rule tests and 29 API integration checks.
- `npm run typecheck` checks TypeScript.
- `.github/workflows/verify.yml` runs tests, type checks and a build when pushed to GitHub.

- `npm run db:generate` after changing the Drizzle schema.
- `node node_modules/typescript/bin/tsc --noEmit` for type checks.
- `node --experimental-strip-types tests/rules.test.mjs` for timeline, permissions, host, and timezone rules.
- `node tests/run-api.mjs` runs the actual API in an isolated Miniflare Worker with temporary D1/R2 and fixture media. It does not need the browser preview or production credentials.
- `npm run build` builds the Worker/client bundle. The Sites plugin build helper is preferred in Work Mode.

Local migrations and previews follow the Sites starter workflow. Local and production databases are independent. The test harness never operates on production state.

## Not yet complete

The entire five-phase specification is larger than this release. Remaining work includes:

- Verified TTS, original music, and image-edit adapters; voice access tests, pronunciation dictionaries, transcript verification, true image refinement, billing budgets and cost ledger.
- Dedicated background FFmpeg/Remotion workers, durable job queue, idempotent paid generation, full-quality MP4 encoding, retries, cancellation and render recovery. Browser export is the implemented fallback, not a server worker.
- Resumable uploads, object-retention/deletion controls, backup/restore automation, deep media validation, malware scanning, and signed expiring review/media links. Media access currently uses authenticated same-origin endpoints, not presigned URLs.
- Automatic semantic shot indexing, robust visual similarity groups/replacement, audio loudness and ducking, transitions, speed/stabilization/color controls, draggable multilayer image editing, logo rendering, and continuous synchronized live timeline preview.
- Complete branch-specific records, references/font uploads/license management, factual offer windows, advanced playbook modules, three-view calendar, complete localization, analytics and actual social provider publishing.
- Full automated export QC (blank frames, codecs, duration, overflow, contrast, clipping). Current tests establish basic rendering and API behavior; manual export review is required.
- Production operational review (SSRF egress hardening, rate limits across every route, large-scale uploads, user management, and backup policy) before opening the studio to a broader client audience.

The requested voice/music/image model IDs are saved as preferences only. Unavailable capabilities remain identified in Settings. Connecting a key alone does not finish unimplemented adapters.

## Sources and licensing

- `https://ks-sushi.fi/` was reviewed as the user-supplied workflow example; no restaurant offers, prices, or dietary claims were seeded.
- Illustrative dashboard photos are from Unsplash image URLs listed in `docs/asset-sources.md`; they are not represented as the client’s uploaded photography.
- The requested GitHub repositories (`remotion-dev/skills`, `anthropics/skills`, `vercel-labs/agent-skills`) were inspected for relevance. No code or skill packages from them were incorporated; their individual licensing was not adopted or inferred.
- Runtime creative rules are implemented in this repository, not executed as model-generated code.

# Copilot instructions — Shalini & Kartik wedding site

This is a small, static wedding website with a single dynamic piece: the RSVP flow (client-side) backed by Supabase. The guidance below highlights the project's architecture, common patterns, and concrete examples so an AI coding agent can be immediately productive.

1) Big picture
- **Type:** Static HTML site (no build step). Edit `index.html` and `rsvp.html` directly.
- **Hosting:** Netlify (see `netlify.toml`). Pushes to `main` auto-deploy.
- **Dynamic data:** Supabase REST API used directly from `rsvp.html` via a small `supa()` fetch helper.

2) Key files to inspect
- `index.html` — landing page and invitation UI.
- `rsvp.html` — full RSVP UX: lookup by phone, render events, and POST responses to Supabase. See the embedded `supa()` helper and usage.
- `netlify.toml` — Netlify redirect config; treat as immutable unless you know Netlify.
- `assets/sketches/` — images referenced from pages.
- `README.md` — has project-level notes (Supabase project URL, which DB tables to edit via Supabase dashboard).

3) Data model & flows (exact, discoverable patterns)
- Parties lookup: `parties?phone_last4=eq.<last4>&select=*` (used in gate suggestions).
- Party by invite code: `parties?code=eq.<code>&select=*` (used for ?c= links).
- Group → events: `groups?tag=eq.<group_tag>&select=*` then `events?id=in.(<ids>)&is_active=eq.true&order=sort_order.asc&select=*`.
- Responses upsert: `responses?on_conflict=party_id` via POST with header `Prefer: resolution=merge-duplicates,return=representation`.

4) Example snippets (copy/paste safe)
- Helper: `supa(path, opts)` is defined at the top of the `<script>` in `rsvp.html` and sets `apikey` and `Authorization` headers for Supabase REST.
- Lookup example: `supa('parties?phone_last4=eq.' + last4 + '&select=*')`.
- Upsert example:
  ```js
  supa('responses?on_conflict=party_id', {
    method: 'POST',
    headers: { 'Prefer': 'resolution=merge-duplicates,return=representation' },
    body: JSON.stringify({ party_id: party.id, attending: true, guests: [...] })
  })
  ```

5) Conventions and patterns
- No bundler: JS and CSS are inlined into HTML. Keep changes minimal and localized.
- Styling: single-file styles in each HTML head; CSS variables used for theme tokens.
- Interactivity: vanilla DOM APIs and fetch. Look for `document.getElementById` patterns and `reveal` class for scroll animations.
- Images: stored under `assets/sketches/`; prefer relative paths.

6) Developer workflows (practical commands)
- Edit files locally, then commit & push. Netlify auto-deploys after push.
- Quick local preview (works because site is static):
  ```bash
  # from repo root
  python3 -m http.server 8000
  # then open http://localhost:8000/index.html
  ```
- If you have Netlify CLI installed: `netlify dev` may provide a closer dev environment.

7) Safety & secrets (what to avoid)
- `rsvp.html` contains a Supabase URL and anon key used by the client. That key is part of the repo; do not expose it further in PR descriptions, public gists, or logs. If you need server-side changes, ask the repository owner to rotate the key and supply new credentials.
- Do not replace the client-side supa() pattern with server-side calls unless requested — many UX flows rely on immediate client-side reads/writes.

8) When you make changes
- Keep changes minimal and focused to the target file (`index.html` or `rsvp.html`) unless asked to refactor.
- If you add JS behavior that touches Supabase, reuse the existing `supa()` helper for consistent headers and error handling.
- Update `README.md` when adding new integration points or changing how invite codes map to `parties`.

9) What an agent should do first
- Inspect `rsvp.html` first (script portion) to understand current Supabase queries and response shape.
- Use the examples above when suggesting edits or new endpoints.

If any of the Supabase table names, columns, or allowed guest-count logic change, tell me and I will update these instructions to match. Want me to add short developer steps for rotating the Supabase key or scaffolding local environment tooling? Reply with which you'd prefer.

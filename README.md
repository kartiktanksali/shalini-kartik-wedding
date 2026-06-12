# Shalini & Kartik — Wedding Website

## Structure
```
wedding-site/
  index.html              ← Landing page (edit HTML/CSS directly)
  rsvp.html               ← RSVP page (Supabase-connected)
  netlify.toml            ← Netlify config (don't touch)
  assets/
    sketches/
      hero-angkor.jpg     ← Hero background sketch
      story-films.jpg     ← "How it began" moment sketch
      story-maui.jpg      ← "The distance" moment sketch
      story-scuba.jpg     ← "The adventures" moment sketch
      venue-map.jpg       ← Sequoia site map
  README.md
```

## How to edit
- **Page copy, fonts, colors** → edit `index.html` or `rsvp.html` in any text editor
- **Events (name, time, description)** → Supabase table editor → `events` table
- **Guest groups** → Supabase → `groups` table
- **Add a guest party** → Supabase → `parties` table
- **See RSVPs** → Supabase → `responses` table

## Deploy a change
1. Edit the file
2. `git add . && git commit -m "your message" && git push`
3. Netlify auto-deploys in ~30 seconds

## Guest invite links
Format: `https://your-site.netlify.app/index.html?c=PARTY_CODE`
Example: `https://your-site.netlify.app/index.html?c=sharma-fam`

The `?c=` code maps to a row in the `parties` table in Supabase.

## Supabase project
URL: https://fjsbnbpkhizyebaqcvto.supabase.co
Dashboard: https://supabase.com/dashboard/project/fjsbnbpkhizyebaqcvto

# NSEA Swim Boards

Static, self-contained HTML boards for nseaswimteam.com, hosted here via
GitHub Pages and embedded on the live site as `<iframe>`s. Each board's
*design* (this HTML/CSS/JS) is meant to stay put — only the small JSON file
it loads from `/data/` should change when new results come in.

## One-time setup

1. **Create the repo.** On GitHub, create a new repository (public — GitHub
   Pages is free for public repos; if you have GitHub Pro/a paid org you can
   use a private repo instead). Suggested name: `nsea-swim-boards`.
2. **Upload these files**, keeping the folder structure:
   ```
   index.html
   top10-scy.html
   top10-lcm.html
   records.html
   data/
     top10-scy.json
     top10-lcm.json
     records.json
   ```
   Easiest way if you're not comfortable with git command line: on the
   repo's GitHub page, use **Add file → Upload files** and drag the whole
   folder in.
3. **Enable GitHub Pages.** In the repo, go to **Settings → Pages**. Under
   "Build and deployment", set Source to **Deploy from a branch**, branch
   `main`, folder `/ (root)`. Save.
4. GitHub will give you a URL like:
   ```
   https://<your-github-username>.github.io/nsea-swim-boards/
   ```
   It can take a minute or two to go live the first time. Visit
   `.../index.html` to confirm the three boards load (they'll show the
   placeholder "Example Swimmer" data until you drop in real data — see
   below).

## Embedding on nseaswimteam.com (BUILD)

On each corresponding page in the BUILD editor, add a Custom HTML widget
containing an iframe pointed at the matching board, e.g.:

```html
<iframe
  src="https://<your-github-username>.github.io/nsea-swim-boards/top10-scy.html"
  style="width:100%; border:0; min-height:1400px;"
  loading="lazy">
</iframe>
```

Do this once per page:
- **Top Times - SCY** → `top10-scy.html`
- **Top Times - LCM** → `top10-lcm.html`
- **Team Records - SCY** and **Team Records - LCM** → `records.html`
  (or split into two record files later if you want them separated by course)

A fixed `min-height` avoids a tiny/scrolling box; adjust it if a board looks
cramped after real data loads. If BUILD's widget strips the `style`
attribute, add `width="100%" height="1400" frameborder="0"` instead.

## Updating for a new season / new results

You should almost never need to touch the `.html` files again. To publish
new results:

1. Ask Claude to rebuild the board using the **nsea-swim-boards** skill,
   same as always — just tell it the output should be the JSON shape below
   instead of a full HTML file.
2. Replace the relevant file in `data/` (`top10-scy.json`, `top10-lcm.json`,
   or `records.json`) with the new content, and push/upload it to GitHub.
3. That's it — the iframe on nseaswimteam.com picks up the new data
   automatically next time someone loads the page. No BUILD editing.

### `data/top10-scy.json` / `data/top10-lcm.json` shape

```json
{
  "cutoff_iso": "2025-09-01",
  "cutoff_label": "9/1/2025",
  "disclaimer": "...",
  "footer": "...",
  "age_order": ["10 & Under", "11-12", "13-14", "15-16", "17-18"],
  "data": {
    "<Age Group>": {
      "Female": {
        "<Event Name>": [
          {"rank": 1, "time": "22.15", "first": "Jane", "last": "Smith",
           "age": 12, "meet": "Meet Name", "date": "2025-10-04", "points": 512}
        ]
      },
      "Male": { "...": "..." }
    }
  }
}
```

### `data/records.json` shape

```json
{
  "cutoff_iso": "2025-09-01",
  "cutoff_label": "9/1/2025",
  "subtitle": "...",
  "disclaimer": "...",
  "footer": "...",
  "swimmers": [
    {
      "name": "Jane Smith",
      "records": [
        {"course": "SCY", "event": "50 Free", "time": "22.15",
         "date": "2025-10-04", "meet": "Meet Name"}
      ]
    }
  ]
}
```

`date` fields in both shapes should be ISO format (`YYYY-MM-DD`) — the
pages compare them directly against the cutoff date in the browser.

## Why this setup

- The BUILD Custom HTML widget only needs to hold a two-line iframe, so
  there's no risk of its rich-text editor mangling a large pasted
  `<script>` block.
- Updating a board is a data-file swap, not a full rebuild-and-repaste —
  faster and much lower-risk mid-season.
- Everything here is plain static files; no build step, server, or paid
  hosting required.

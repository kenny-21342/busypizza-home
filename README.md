# busypizza.bond — landing page

Static single-file site. No build step, no dependencies. `index.html` is the whole thing.

## Plan for the domain

One Vercel project per tool, each on its own subdomain:

| Host | Project | Status |
|---|---|---|
| `busypizza.bond` | this repo | deployed |
| `www.busypizza.bond` | this repo (redirect to apex) | deployed |
| `study.busypizza.bond` | `smart-edu` (YKPao Study) | live |
| `tts.busypizza.bond` | `tts-site` (Shanghainese TTS) | live |
| `graphviz.busypizza.bond` | `graphviz-generator` | live |
| `tikz.busypizza.bond` | `tikz-generator` | live |

YKPao Study is the featured item on the page. Its link goes to the site root, which
is a NextAuth login wall for anyone not signed in — student work stays behind that
login, and nothing beyond the product name is exposed by listing it.

`speech.busypizza.bond` was the planned TTS host and was never used; the tool
shipped on `tts.busypizza.bond` instead. That host 404s.

## Setup, in order

### 1. Move DNS to Vercel

At NicNames, replace the nameservers:

```
ns10.nicnames.com  →  ns1.vercel-dns.com
ns11.nicnames.com  →  ns2.vercel-dns.com
ns12.nicnames.com  →  (remove)
```

Safe to do: `dig MX busypizza.bond` returns nothing, so no email breaks.

Two notes:
- This discards the `ENS1 0x...` TXT record NicNames added by default. Only matters
  if you actually use ENS/blockchain naming — re-add it in Vercel's DNS tab if so.
- Propagation is usually under an hour but can take up to 48.

**Alternative** — keep DNS at NicNames and add records per host instead. Use the exact
values Vercel shows you in *Project → Settings → Domains*; typically an `A` record at
the apex and a `CNAME` to `cname.vercel-dns.com` for each subdomain. More manual work,
one record per new tool.

### 2. Attach the subdomain to the existing Graphviz project

Vercel → `graphviz-generator` → Settings → Domains → add `graphviz.busypizza.bond`.
It's already live at `graphviz-generator.vercel.app`; this just gives it the real name.

### 3. Deploy this repo

Either push it to GitHub and import in the Vercel dashboard, or from this directory:

```bash
npx vercel --prod
```

Then add `busypizza.bond` and `www.busypizza.bond` in that project's domain settings.
Vercel issues the TLS certificate automatically once DNS resolves.

## Adding a tool later

One edit in `index.html`: copy an existing `<li class="tool">` block, paste it into
`<ul class="tools">`, and change four things — the `href`, the `.t-name`, the
`.t-desc` text, and the `.t-host` line.

To make a tool the featured one instead, move it into the `<a class="featured">`
block near the top and demote the current occupant to a list item.

## Notes

- `.bond` is a newer, cheap TLD. Some corporate and school network filters treat these
  more suspiciously than `.com`/`.dev`. Fine for personal and portfolio use; worth
  knowing if you ever share a link somewhere it silently fails to load.
- The domain expires **2027-05-11**. Turn on auto-renew at NicNames.

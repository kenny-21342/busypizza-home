# busypizza.bond — landing page

Static single-file site. No build step, no dependencies. `index.html` is the whole thing.

## Plan for the domain

One Vercel project per tool, each on its own subdomain:

| Host | Project | Status |
|---|---|---|
| `busypizza.bond` | this repo | to deploy |
| `www.busypizza.bond` | this repo (redirect to apex) | to deploy |
| `graphviz.busypizza.bond` | `graphviz-generator` | already deployed, needs the domain attached |
| `tikz.busypizza.bond` | `tikz-generator` | after 6 Sep |
| `speech.busypizza.bond` | TTS site | after 22 Sep |

YKPao Study is deliberately **not** listed here. It holds student work behind auth;
share its URL with the cohort directly rather than advertising the login page publicly.

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

Two edits in `index.html`:

1. The `<svg class="dg">` block — change that node's `class="n-box soon"` to
   `n-box live`, its `SOON` label to `LIVE`, and wrap the `<g>` in
   `<a class="node" href="https://…">`.
2. The matching `<li class="tool pending">` — drop `pending`, swap the `chip soon`
   span for `chip live`, and change the `<div class="tool-in">` to
   `<a class="tool-in" href="…">`.

## Notes

- `.bond` is a newer, cheap TLD. Some corporate and school network filters treat these
  more suspiciously than `.com`/`.dev`. Fine for personal and portfolio use; worth
  knowing if you ever share a link somewhere it silently fails to load.
- The domain expires **2027-05-11**. Turn on auto-renew at NicNames.

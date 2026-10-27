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

Every page has a black-and-white archive photo of Oneroa, credited underneath, as on mountainviewsmartcontracts.com. They're placeholders for now. To add one:

1. Put the file in `site/images/` (JPEG, about 1200px on the long side).
2. In the page, replace the `<div class="placeholder" …>` inside `<figure class="archive">` with `<img src="images/FILE.jpg" alt="what the photo shows">`. A comment marks each spot.
3. Fill in the caption: the photo's title and year, then the credit and permission line the archive asks for.

The stylesheet shows every archive photo in black and white, whatever the file is. The home page's photo is cropped to 4:5, so a portrait crop works best there.

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

GitHub Pages. `.github/workflows/pages.yml` publishes `site/` on every push to `main`; the custom domain is set in the repo's Settings → Pages, not in a `CNAME` file. The domain is registered at Namecheap, and its DNS points at GitHub Pages:

| Type | Host | Value |
|---|---|---|
| A | @ | 185.199.108.153, 185.199.109.153, 185.199.110.153, 185.199.111.153 |
| AAAA | @ | 2606:50c0:8000::153, 2606:50c0:8001::153, 2606:50c0:8002::153, 2606:50c0:8003::153 |
| CNAME | www | richbodo.github.io. |
| TXT | \_github-pages-challenge-richbodo | the code GitHub gives when verifying the domain |

## Not decided yet

- The email address on the contact page.

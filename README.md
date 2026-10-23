# gamenightly-site

The public website for **GameNightly**: the homepage, Privacy Policy, Terms of Service and logo. Google needs these to verify the OAuth consent screen for the bot's Google Calendar access (see [ryanhockstad/gamenight](https://github.com/ryanhockstad/gamenight) issues #2–#6).

Live: https://ryanhockstad.github.io/gamenightly-site/

| Page | URL |
|---|---|
| Homepage | https://ryanhockstad.github.io/gamenightly-site/ |
| Privacy Policy | https://ryanhockstad.github.io/gamenightly-site/privacy.html |
| Terms of Service | https://ryanhockstad.github.io/gamenightly-site/terms.html |
| Logo for the consent screen (120×120 PNG) | `assets/logo-120.png` |

## Files

- `index.html`, `privacy.html`, `terms.html`: plain HTML. The privacy and terms pages share one header and footer, so a nav change has to be made in both.
- `_config.yml`: site-wide settings (the contact email). GitHub Pages runs Jekyll, which fills them into the privacy and terms pages through `_includes/` (that's why those two pages start with an empty `---` front matter).
- `styles.css`: brand tokens copied from `Gamenightly/web/src/styles.css`, plus the legal-page styles.
- `home.css`: homepage only (hero, scroll-driven core features, product cards). The homepage has its own header and footer.
- `assets/logo.svg`: the master logo. `logo-120.png` (Google consent screen) and `logo-512.png` (Discord app icon / social) are exported from it.

## Keep in sync with the code

The privacy policy and the Google Calendar card on the homepage describe exactly what the bot does. When any of these change, update both:
- `SCOPES` in `src/oauth_server/src/oauth_server/google.py`
- what's stored in Redis (`storage.py`, `services/store.py`)
- retention: `SESSION_TTL_SECONDS` in `services/sessions.py` (14 days), and the web app's 30-day session expiry

## Run locally

```bash
python3 -m http.server 8000
```

Then open http://localhost:8000. This server doesn't run Jekyll, so the privacy and terms pages show the raw `{% include %}` tag where the contact email goes; the live site fills it in.

## Deploy

GitHub Pages serves `main` from the repo root. Pushing to `main` publishes the site within about a minute.

## Re-export the logo PNGs

```bash
for n in 120 512; do
  printf '<html><body style="margin:0;background:transparent"><img src="file://%s/assets/logo.svg" width="%s" height="%s" style="display:block"></body></html>' "$PWD" $n $n > /tmp/r$n.html
  "/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" --headless=new --hide-scrollbars --force-device-scale-factor=1 \
    --default-background-color=00000000 --allow-file-access-from-files --window-size=$n,$n \
    --screenshot="$PWD/assets/logo-$n.png" "file:///tmp/r$n.html"
done
```

## Open TODOs

- Fill in `[STATE — TODO]` (governing law, in the terms).
- Google usually wants the homepage on a domain you own and verify in Search Console. Plan to buy one (e.g. `gamenightly.app`) and point Pages at it before submitting for verification.
- These policies were drafted from the code as of 2026-10-06. They aren't legal advice; have them reviewed before launch.

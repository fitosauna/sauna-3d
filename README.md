# Fitosauna – 3D / AR page

Standalone static page that shows the Fitosauna round sauna in 3D and lets
visitors place it in their room in AR (iPhone: AR Quick Look, Android: Scene Viewer).
No price or cart.

## Structure

- `index.html` – the whole page (markup, styles, script)
- `.htaccess` – MIME types (and CORS) for the 3D files, needed on SiteGround/Apache
- `assets/` – optional: local copies of the model files (see below)

Libraries load from jsDelivr: `@google/model-viewer@4.2.0` and `qrcode-generator@1.4.4`.
Google Tag Manager (`GTM-P8QW4LPF`) is included, same as the other landing pages.
The page pushes `3d_model_loaded` and `ar_open` (with `ar_mode`, `platform`) to the dataLayer.

## Model files

The paths are set in the `MODELS` block at the bottom of `index.html`:

| Key | Used for | Current file |
|---|---|---|
| `web` | 3D viewer in the browser | `assets/sauna-web.glb` (a copy of `Sauna-21_12-Medium-texture-max-compressed-open-Android-1-web-2048.glb`) |
| `webFallback` | 3D viewer if the local copy is missing | the same file on fitosauna.com |
| `compressed.ios` | iPhone AR, quick load | `Sauna-opt-compressed-3.usdz` |
| `compressed.android` | Android AR, quick load | `Sauna-21_12-Medium-texture-max-compressed-Android.glb` |
| `ultra.ios` | iPhone AR, ultra-real (~49 MB) | `Sauna-full-3.usdz` |
| `ultra.android` | Android AR, ultra-real | `Sauna-21_12-Medium-texture-Without-compressed-Android.glb` |

By default they point at the files already uploaded to fitosauna.com
(`/wp-content/uploads/2026/05/`). AR works from any domain this way.

**The in-browser 3D viewer needs one of these two things** if this page is hosted
on a different domain/subdomain than fitosauna.com:

1. **Copy the files into `assets/`** (recommended) and change the paths in `MODELS`
   to e.g. `assets/sauna-web.glb`. The included `.htaccess` sets the right MIME types.
2. **Or allow cross-origin loading on fitosauna.com** – add this to the WordPress
   site's `.htaccess` (SiteGround → Site Tools → File Manager → `public_html/.htaccess`):

   ```apache
   <FilesMatch "\.(glb|gltf|usdz)$">
     Header set Access-Control-Allow-Origin "*"
   </FilesMatch>
   ```

GitHub rejects files over 100 MB; all current model files are under that.

## Preview locally

```bash
python3 -m http.server 8080
```

Then open `http://localhost:8080`. AR only works on a real phone over HTTPS.

## Hosting (SiteGround)

Upload `index.html`, `.htaccess` and `assets/` (if used) to the target folder,
e.g. `public_html/3d/` or a subdomain's root. Keep file names unchanged.

Test after publishing:
- Desktop: the model loads and rotates; the QR code opens the page on a phone.
- iPhone (Safari): both buttons open AR Quick Look.
- Android (Chrome): both buttons open Google Scene Viewer in AR.

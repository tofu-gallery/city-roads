# city-roads on Tofu

city-roads draws every road in a selected city using OpenStreetMap data and WebGL. People can search for a city, choose its boundaries, customize the colors and label, and export the result as a PNG or SVG image. The upstream name, appearance, author links, and OpenStreetMap attribution are retained.

## Run on Tofu

- Framework preset: `vite` (Vue 3, browser-only application).
- Repository/site root: `.`; source entry point: `index.html`.
- Package manager: npm, using the upstream `package-lock.json` unchanged.
- Install: `npm ci`.
- Production build: `npm run build`.
- Output directory: `dist/`; serve `dist/index.html` at `/`.
- Runtime: static hosting. No Node server is required after building.
- Environment variables: none.
- Database, sign-in, API keys, or Tofu-owned service accounts: none.
- Local build verification used Node.js `v22.22.3` and npm `10.9.8`.

## Changes and reasons

- Removed Google Analytics from `index.html`, its global error-reporting hook from `src/main.js`, and its outbound-click event code from `src/App.vue`, so copied apps do not report visitors to the upstream analytics account.
- Removed the Zazzle mug purchase interface, its state and event handlers, and `src/lib/getZazzleLink.js`. That feature uploaded the user's picture to the upstream author's AWS endpoint and linked to an affiliate purchase. PNG and SVG export remain entirely in the browser.
- Added this operating guide. No build tooling, dependencies, lockfile, branding assets, or license text was changed.

All adaptations are on the `tofu` branch. `main` remains the upstream mirror. The gallery version is tagged `pin/2026-10-23`.

## Public services and practical limits

The browser needs network access to public services; this is not an offline map database:

- City names and boundaries come from `https://nominatim.openstreetmap.org/`. Search is submitted by the person, not queried as they type. Respect the public service's usage policy and rate limits; do not turn it into a bulk geocoding service.
- The upstream city cache is `https://d2uf7yjjctyxf.cloudfront.net/nov-02-2020`. This is a public, historical roads snapshot maintained by the upstream author, not a Tofu account or a guarantee of current map data. A cache miss or failed request falls back to public Overpass services. `?cache=0` bypasses the cache for a fresh request.
- Overpass endpoints are listed in `src/lib/postData.js`, starting with `https://overpass-api.de/api/interpreter`. They may be slow, rate-limited, or unavailable. This fork does not provision or operate those services.
- The original Google Fonts stylesheet remains. The social preview and the no-WebGL illustration reference the upstream public image on Imgur. No analytics or advertising script remains.
- The optional developer console API can load modules through `d3-require` when explicitly requested by a developer; normal city search and rendering do not use it.

WebGL must be available. Large cities can transfer more than 200 MB and exhaust browser or GPU memory, especially on phones. Preserve the OpenStreetMap credit on displayed and exported maps and comply with the data's ODbL 1.0 terms. The bundled Vite 2 toolchain is retained from upstream; publish only its built static output, not its development server.

## Verification

Verified locally on 2026-10-23; no app was deployed.

```sh
npm --prefix /private/tmp/gallery-batch2/city-roads ci --no-audit --no-fund
npm --prefix /private/tmp/gallery-batch2/city-roads run build
python3 -m http.server 4187 --bind 127.0.0.1 --directory /private/tmp/gallery-batch2/city-roads/dist
curl --fail --silent --show-error --output /dev/null --write-out 'HTTP %{http_code}\n' http://127.0.0.1:4187/
```

Installation succeeded with the existing lockfile (260 packages). Vite 2.9.18 built 192 modules into `dist/`; the entry request returned HTTP 200. A scan of the built output found no Google Analytics/tag-manager, Zazzle, or removed AWS upload endpoint references.

A real-browser pass through the gstack `/browse` skill and Aside searched for `Oxford, England`, selected its boundary, and rendered the roads using the public Nominatim service and upstream city cache. The export menu contained PNG and SVG and no mug purchase. Both export buttons generated download names `Oxford.png` and `Oxford.svg`; browser instrumentation inspected a 686,128-byte PNG with the PNG signature and a 3,702,685-byte SVG. The test intercepted the final download click to inspect generated blobs without writing into the person's Downloads folder. Console errors were `[]` in both the search/render and export passes.

The public Overpass fallback and every city were not independently exercised. Passing local build and browser checks does not guarantee public service availability or constitute a dependency security audit.

## Upstream and license

- Upstream repository: https://github.com/anvaka/city-roads
- Upstream branch: `main`
- Base upstream commit: `35d6497db70debf4508b4d75f17d80c4367d66a6`
- Gallery fork: https://github.com/tofu-gallery/city-roads
- Source license: MIT, copyright (c) 2020-2026 Andrei Kashcha; see the unchanged root `LICENSE`.
- Vendored color-picker license: unchanged at `src/components/vue3-color/LICENSE`.
- Map data: OpenStreetMap contributors, ODbL 1.0; https://www.openstreetmap.org/copyright.

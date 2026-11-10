# <img alt="Outline Manager Logo" src="../docs/resources/logo_manager.png" title="Outline Manager" width="32">&nbsp;&nbsp;Outline Manager

![Build and Test](https://github.com/OutlineFoundation/outline-apps/actions/workflows/build_and_test_debug_manager.yml/badge.svg?branch=master)

## Running

To run the Outline Manager Electron app:

```
npm run action server_manager/electron/start ${PLATFORM}
```

To run the Outline Manager Electron app with a development build (code not minified):

```
BUILD_ENV=development npm run action server_manager/electron/start ${PLATFORM}
```

Where `${PLATFORM}` is one of `linux`, `macos`, `windows`.

## Development Server

To run the Outline Manager as a web app on the browser and listen for changes:

```
npm run action server_manager/www/start
```

## Updating Cloud Locations

When new cloud regions/zones appear (especially for GCP), update all of the
following so the location picker shows proper city and country data.

1. Add geolocation constants in `server_manager/model/location.ts`.
2. Map cloud region IDs to `GeoLocation` in `server_manager/model/gcp.ts`
   (`Zone.LOCATION_MAP`).
3. Add localized city message keys:
   - Source messages: `server_manager/messages/master_messages.json` using
     `geo_*` keys.
   - Runtime English messages: `server_manager/messages/en.json` and
     `server_manager/messages/en-GB.json` using `geo-*` keys.
4. Keep all `geo_*` / `geo-*` message keys alphabetically sorted.
5. Update tests:
   - `server_manager/model/gcp.spec.ts` region coverage list and assertions.
   - `server_manager/www/location_formatting.spec.ts` if formatting/sorting
     behavior changes.
6. Verify:

```bash
npx tsc -p server_manager --outDir output/build/js/server_manager --module commonjs
npx jasmine output/build/js/server_manager/model/gcp.spec.js output/build/js/server_manager/www/location_formatting.spec.js
npm run action server_manager/www/test
```

Notes:
- Missing entries in `Zone.LOCATION_MAP` cause unknown location cards (`?` and
  raw zone IDs) in the picker.
- Region discovery can be checked programmatically with:

```bash
gcloud compute regions list --format="value(name)"
gcloud compute zones list --format="value(name)"
```

## Debug an existing binary

You can run an existing binary in debug mode by setting `OUTLINE_DEBUG=true`.
This will enable the Developer menu on the application window.

## Packaging

To build the app binary:

```
npm run action server_manager/electron/package ${PLATFORM} -- --buildMode=[debug,release]
```

Where `${PLATFORM}` is one of `linux`, `macos`, `windows`.

The per-platform standalone apps will be at `output/build/server_manager/electron/static/dist`.

- Windows: An `.exe` file. Only generated if you have [wine](https://www.winehq.org/download) installed.
- Linux: An `.AppImage` file.
- macOS: A `.dmg` and a `.zip` file as [required by auto update](https://www.electron.build/mac#target).

> NOTE: If you are building for macOS, you may need to run `security unlock-keychain login.keychain` so electron-builder has access to your certificates.

## Testing

```
npm run action server_manager/test
```

## Error reporting

To enable error reporting through [Sentry](https://sentry.io/) for local builds, run:

```bash
export SENTRY_DSN=[Sentry DSN URL]
npm run action server_manager/electron/start ${PLATFORM}
```

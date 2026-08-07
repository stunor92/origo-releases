# OriGo — desktop releases

Native desktop installers for **OriGo**, the organiser client for orienteering
events. This repository holds nothing but distribution: the installers, their
checksums, and the signed manifest the installed app reads to find updates.
Application source lives elsewhere and is not published here.

## Download

Grab the latest installer from the [releases page](https://github.com/stunor92/origo-releases/releases/latest):

| Platform | File |
|---|---|
| macOS (Apple Silicon) | `OriGo-<version>.dmg` |
| Windows | `OriGo-<version>.msi` |
| Linux (Debian/Ubuntu) | `origo_<version>_amd64.deb` |

The app checks for updates on its own after that, and offers to install them.

### Notes per platform

- **macOS** is Apple Silicon only for now. The build carries an arm64 runtime, so
  it does not run on Intel Macs. It is signed with a Developer ID certificate and
  notarised by Apple, so it opens without a Gatekeeper warning.
- **Windows** installers are not yet code signed, so SmartScreen shows a warning
  on first install. Choose *More info* → *Run anyway*. Verify the checksum below
  if you want certainty about what you downloaded.
- **Linux** ships as a `.deb`. Install with `sudo apt install ./origo_<version>_amd64.deb`.

## Verifying a download

Every installer has a `.sha256` file beside it:

```bash
sha256sum -c OriGo-<version>.dmg.sha256
```

## How updates work

`latest.json` on the newest release describes the current version and, per
platform, the download URL, file name and SHA-256 of its installer. It is served
from a stable URL that always points at the newest release:

```
https://github.com/stunor92/origo-releases/releases/latest/download/latest.json
```

Beside it, `latest.json.sig` holds a detached Ed25519 signature over the exact
bytes of that file. The desktop app verifies the signature against a public key
compiled into the application before it parses the manifest or follows any URL
from it, so a substituted or modified manifest is rejected rather than trusted.
The SHA-256 is then checked against the downloaded installer, and on macOS the
disk image additionally has to pass Gatekeeper and match the installed app's team
identifier before it replaces anything.

There is no server in that path and no credential involved — the app talks
directly to these release assets.

## Contributing

This repository does not take issues or pull requests. Its workflow runs with
Apple code signing credentials, so it is kept closed to outside changes. Report
problems with OriGo itself through the usual channels.

Installers are built, signed and published automatically by
`.github/workflows/package-desktop.yml`, which is triggered from the application
repository when a release is cut. That workflow file is maintained there and
copied here — edit it at the source, not in this repository.

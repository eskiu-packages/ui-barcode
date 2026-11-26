<p align="center">
  <img src="https://raw.githubusercontent.com/eskiu-lang/eskiu/main/assets/logo.png" alt="Eskiu, a kitten with a ball of yarn" width="140">
</p>

<h2 align="center">barcode</h2>
<p align="center">QR codes and barcodes read live from the camera, for Eskiu ui apps.</p>
<p align="center"><sub>Part of the <a href="https://github.com/eskiu-lang">Eskiu</a> project</sub></p>

---

```eskiu
import <ui>;
import <barcode>;

void Scan() {
    *StringState found = use_string("");
    BarcodeScanner(Style{grow: 1}, "qr", found.get()[0] == 0, void(Barcode code) {
        if (code.status == ScanCode) { found.set(code.value); }
    });
}
```

`BarcodeScanner(style, formats, scanning, on_code)` shows the back camera filling its box and
hands each code it reads to `on_code` while `scanning`: the same code again only after a
second and a half, so a code held in front of the camera is one call. `formats` is a comma
separated list of `qr`, `aztec`, `datamatrix`, `pdf417`, `ean13`, `ean8`, `upca`, `upce`,
`code128`, `code39`, `code93`, `itf`, `codabar`, or `"all"`.

`Barcode` has a `status` (`ScanCode`, `ScanDenied`, `ScanUnavailable`), the `format` read and
the `value`, borrowed for the call. On `ScanDenied`, `ui_open_settings()` takes the user to
the app's settings to allow the camera.

| Platform | Reads codes with | `barcode_supported()` |
|---|---|---|
| iOS | AVFoundation's metadata output (no extra library) | true with a camera; false on a simulator |
| Android | ML Kit's barcode scanner, bundled with the app (`com.google.mlkit:barcode-scanning`, through the ui tool's Gradle build); no network, no Google Play services | true with a camera (`FEATURE_CAMERA_ANY`) |
| Web | the browser's `BarcodeDetector` (Chrome on Android, macOS and ChromeOS, Safari on iOS 17+) | true where the browser has `BarcodeDetector` and a camera API; false in Firefox, desktop Safari and Chrome on Windows and Linux |
| macOS, Linux, Windows | none | false |

`barcode_supported()` answers before a scanner is mounted, so an app can show another way
to enter a code instead. Where it is false a mounted scanner draws nothing and reports
`ScanUnavailable` once; nothing crashes. True does not mean the camera is allowed: the
permission is asked when the first scanner mounts.

On the native desktop hosts (macOS, Linux, Windows) the package registers a `barcode` view that
shows nothing and hands `on_code` one `ScanUnavailable`, so an app builds and runs there. It
never reports a code. The desktop's `--webview` builds use the web's `BarcodeDetector` where
the webview has it.

The first scanner mounted asks for the camera permission (`NSCameraUsageDescription`, which
an app's own `ui.toml` can word).

`examples/scan` reads a QR code and pauses until "Scan again" (`ui run ios` or
`ui run android`, on a device: simulators have no camera).


## Status

ui-barcode is in Preview, as ui's device packages are (ui's
[docs/stability.md](https://github.com/eskiu-packages/ui/blob/main/docs/stability.md)): `eskiuc
test` checks it against stand-ins for the platform, not on devices, and its API may still change
in a minor release.

It reads codes on iOS and Android, and on the web in a browser with `BarcodeDetector`.
Without `BarcodeDetector` (Firefox, Safari on the Mac) or without a camera,
`barcode_supported()` is false and a mounted scanner reports `ScanUnavailable` once; it never
fails silently. A browser whose `BarcodeDetector` has no formats to read (it can only say so
once asked, after `barcode_supported()` answered true) reports `ScanUnavailable` from the
mounted scanner too. The native macOS, Linux and Windows hosts read no codes (`ScanUnavailable`).
Their `--webview` builds would read codes only where the webview has `BarcodeDetector`, and
none of WKWebView, WebKitGTK and WebView2 is known to have it today (Chromium ships it on
Android, macOS and ChromeOS), so plan on `ScanUnavailable` on the desktops and offer another
way to enter a code.

## Documentation

Every public function and type, with its documentation, is in the [API reference](docs/api.md).

## Install

In an app made with `ui new`:

```bash
eskiuc add barcode --git https://github.com/eskiu-packages/ui-barcode --rev v0.1.4
```
## Tests

`eskiuc test` drives a scanner with the events a platform sends: codes of two formats, a value
with spaces, denied and unavailable, and pausing.

## License

MIT

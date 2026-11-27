# Changelog

All notable changes to barcode are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [0.1.5] - 2026-11-26

### Changed

- Depends on ui 0.8.0.

### Fixed

- Web: a browser whose `BarcodeDetector` reads no formats, or refuses the formats asked for,
  now reports `ScanUnavailable` from the mounted scanner; before, it showed the camera and
  read nothing.

### Changed

- The README says which platforms read codes, what happens without `BarcodeDetector` (and in
  the desktops' webview builds), and that the package is in Preview, tested against stand-ins.

## [0.1.4]

### Added

- `barcode_supported()`: whether a scanner can read codes here, before one is mounted. False
  on a device without a camera (a simulator), in a browser without `BarcodeDetector` and on
  the desktop. The README's platform table says what each platform does.
- CI builds the example on the native desktop hosts (`tools/ci_desktop.sh`).

### Fixed

- On the native desktop hosts (macOS, Linux, Windows) a `BarcodeScanner` now reports
  `ScanUnavailable` once it is mounted. Before, no `barcode` view was registered there, so the
  box stayed empty and the handler was never called.

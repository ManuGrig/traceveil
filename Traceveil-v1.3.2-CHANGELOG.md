# Traceveil Changelog

All notable changes to Traceveil are documented in this file.

## [1.3.2] - 2026-07-11

### Fixed

- Fixed a Chrome error in the Canvas `getImageData()` wrapper caused when a webpage shadows or modifies a captured method's `.apply()` or `.call()` property.
- Protected native browser APIs are now invoked through an early-captured `Reflect.apply` reference rather than mutable page-visible invocation properties.
- Canvas and OffscreenCanvas pixel perturbation now falls back to the unmodified native result if Traceveil's post-processing step fails.
- Genuine native browser exceptions are still preserved rather than silently suppressed.

### Security and hardening

- Applied the safer native-invocation mechanism consistently across protected Canvas, WebGL, audio, timezone, Client Hints, font-probing, property-getter, and function-wrapper code.
- Reduced the ability of webpages to interfere with Traceveil's protected API wrappers by modifying JavaScript invocation helpers.

### Testing

- Added a regression test that deliberately poisons `getImageData.apply` and `getImageData.call`; Canvas protection must continue to work without throwing.
- Confirmed the fix in real Chromium using Canvas `getImageData()` and `toDataURL()` while both invocation properties were poisoned.
- Confirmed that Canvas perturbation remains active after the hardening change.
- Re-ran package, service-worker, and main-world validation tests successfully.

### Maintenance

- Replaced environment-specific package-lock registry URLs with public npm registry URLs so `npm install` remains portable.

## [1.3.1] - 2026-07-11

### Added

- Added consistent `devicePixelRatio = 1` normalization alongside the 1920 × 1080 × 24 screen profile.
- Added DST-correct `America/New_York` behavior across:
  - Default `Intl.DateTimeFormat`
  - `Date.getTimezoneOffset()`
  - `Date.toString()`
  - `Date.toDateString()`
  - `Date.toTimeString()`
  - `Date.toLocaleString()`
  - `Date.toLocaleDateString()`
  - `Date.toLocaleTimeString()`
- Preserved explicitly requested timezones such as `Europe/London`.
- Added a dependency-free main-world validation harness.
- Added an expanded real-Chrome Puppeteer harness covering:
  - Fixed normalized targets
  - Canvas, WebGL, and audio interference
  - Early-script stability
  - Reload stability
  - First-party separation
  - Per-site exclusions
  - Session-seed leakage checks
- Added a pinned Puppeteer dependency and package lock for reproducible browser verification.

### Changed

- Strict-mode interference is now stable by first party and protection mode.
- Removed fresh random entropy generation on every navigation.
- Tracker status now reports zero active domains when tracker blocking or Traceveil itself is disabled, while still showing the number of bundled domains available.
- Documentation now distinguishes dependency-free validation from optional end-to-end Chrome verification.

### Security

- Rejected the experimental raw `chrome.storage.session` seed relay through `window.postMessage`.
- No browser-global seed or per-install identifier is exposed to webpages.
- Retained hardened installation and startup synchronization from v1.2.1.
- Retained failure isolation for optional Chrome privacy settings.
- Retained one-way iframe activation: page-visible messages may activate protection but cannot disable it.
- Retained `match_origin_as_fallback` for both the MAIN-world registration and isolated bridge so related `about:`, `data:`, `blob:`, and `filesystem:` frames remain covered.

---

Traceveil v1.3.2 is the current stable extension release. The later v1.3.3 experimental branch is not included in this changelog because its Balanced-mode WebGL changes exposed native GPU information and were not adopted as the stable release.

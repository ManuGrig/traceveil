# Changelog

## 1.4.0 — 2026-08-15

### Stable release

- Promoted the measured `1.4.0-rc.1` protection behavior to stable without changing fingerprint-output logic.
- Public release identity is now `1.4.0`; Chrome's monotonic numeric package version is `1.4.0.8` so installations can upgrade cleanly from the RC package.
- Retains the full v1.3.2 feature set, the alpha.6 UA-CH platform-coherence fix, deterministic first-party WebGL extension farbling, WebGPU adapter-info scrubbing, and coherent suppression of `WEBGL_debug_renderer_info`.
- Stable promotion was supported by real Chrome measurements: Cover Your Tracks again reported `WebGL Vendor & Renderer = None` (7.28 bits / about 1 in 155.69 browsers) while Canvas, WebGL pixels, and Audio remained first-party randomized; BrowserLeaks reported the unmasked GPU vendor/renderer as unavailable while WebGL/WebGL2 remained functional.
- Renamed the field test guide from `PRE_RELEASE_TESTING.md` to `RELEASE_TESTING.md`.
- Regenerated the final source-integrity manifest and reran the complete validation suite against both the source tree and a fresh extraction of the release ZIP.

### Why the renderer remains suppressed

The alpha.5/alpha.6 assumption that a generic `WebKit / WebKit WebGL` debug identity would blend better than an unavailable renderer was falsified by measured population data. The generic pair was far rarer than `None`, so v1.4.0 preserves the larger measured anonymity set rather than choosing a more aesthetically plausible but more identifying value. A fully coherent synthetic GPU persona remains deferred.

## 1.4.0-rc.1 — 2026-08-15

### Measured WebGL renderer strategy reversal

- Reverted the alpha.5/alpha.6 `WebKit` / `WebKit WebGL` renderer substitution and restored debug-renderer suppression.
- The earlier design assumed an empty/unavailable debug renderer would be unusually identifying. Measured Cover Your Tracks population data showed the opposite: the generic pair measured 12.27 bits / about 1 in 4,955.56 browsers, while the v1.3.2 unavailable/`None` result measured 7.13 bits / about 1 in 139.77 browsers. The measured anonymity set therefore overrides the aesthetic intuition that a generic-looking string is safer.
- `getExtension("WEBGL_debug_renderer_info")` now returns `null` when WebGL protection is enabled.
- `WEBGL_debug_renderer_info` is also removed from `getSupportedExtensions()` so Traceveil never advertises an extension that it then refuses to enable.
- Hard-coded unmasked renderer enum probes remain suppressed, while the native `getParameter()` path is invoked first to preserve Chrome's normal WebGL error-state side effects where applicable.
- `WEBGL_debug_shaders` suppression is unchanged.

### Preserved v1.4 GPU work

- Kept first-party WebGL pixel partitioning/interference unchanged.
- Kept deterministic first-party synthetic extension farbling and coherent synthetic `getExtension()` objects unchanged.
- Kept WebGPU adapter-info scrubbing unchanged.
- Deferred full synthetic GPU personas until renderer identity, extension support, shader/texture limits, framebuffer behavior, and rendered output can be made coherent as one profile.

### Regression tests and release integrity

- Added explicit `debugRendererInfoHidden` coverage.
- Added a regression check that `WEBGL_debug_renderer_info` is absent from `getSupportedExtensions()` whenever it is suppressed by `getExtension()`.
- Added hard-coded debug enum suppression coverage and native restoration checks when the WebGL module is disabled.
- Updated popup runtime verification to treat coherent renderer suppression as ACTIVE instead of LIMITED.
- Promoted package identity to `1.4.0-rc.1` / Chrome numeric version `1.4.0.7`.
- Regenerated `SOURCE_MANIFEST.sha256` from the final RC files only after tests and documentation were complete.

## 1.4.0-alpha.6 — 2026-08-15

### Persona coherence

- Fixed a direct same-JavaScript-layer contradiction on non-Windows hosts where `navigator.platform` was normalized to `Win32` while `navigator.userAgentData.platform` could still expose the native OS.
- Added Hardware-module normalization of `NavigatorUAData.prototype.platform` to `Windows`.
- Added coherent normalization of `NavigatorUAData.prototype.toJSON().platform` to `Windows`.
- Retained Strict-mode high-entropy `platformVersion` normalization at `10.0.0`, now covered by regression tests alongside the low-entropy platform surface.
- Disabling the Hardware module and per-site exclusion continue to restore native platform values.

### Tests and release integrity

- Added a `NavigatorUAData` test double so low-entropy and high-entropy UA-CH paths are exercised by the dependency-free main-world suite.
- Added real-Chrome harness assertions for `navigator.userAgentData.platform`, `toJSON().platform`, and exclusion restoration where UA-CH is available.
- Added package-level guards requiring both the UA-CH platform getter patch and the coherent `toJSON()` wrapper.
- Bumped all package identities to `1.4.0-alpha.6` / Chrome numeric version `1.4.0.6`.
- Regenerated `SOURCE_MANIFEST.sha256` only after final source, documentation, tests, and assets were complete.

## 1.4.0-alpha.5 — 2026-08-15

### Full merge

- Rebased the v1.4 work on the actual Traceveil v1.3.2 source tree instead of the standalone GPU Guard laboratory extension.
- Retained all v1.3.2 fingerprint, network, mode, exclusion, privacy-setting, bridge, and validation functionality.
- Replaced the temporary GPU-only popup with a full Traceveil interface containing every existing control.

### GPU protection

- Changed WebGL renderer protection from returning `null` for `WEBGL_debug_renderer_info` to preserving the real extension enablement flow and returning generic `WebKit` / `WebKit WebGL` values only after the debug extension is enabled.
- Added deterministic first-party synthetic WebGL extension farbling using Traceveil's existing public first-party/mode seed.
- Added coherent `getExtension()` behavior for the synthetic extension.
- Retained v1.3.2 WebGL `readPixels()` perturbation and legacy `WEBGL_debug_shaders` suppression.
- Changed WebGPU protection from hiding `navigator.gpu` to preserving WebGPU capability and scrubbing adapter `vendor`, `architecture`, `device`, and `description` fields where Chrome exposes them.
- Uses same-prototype WebGPU adapter-info snapshots to avoid Proxy invariant failures with immutable WebIDL properties.

### Interface

- Added the new Traceveil logo to the popup and toolbar icons.
- Added a Lens Guard-family dark/cyan interface with Overview, Protection, Network, Sites, and About sections.
- Preserved every v1.3.2 module toggle.
- Added live current-tab WebGL identity/farbling diagnostics.
- Added tracker-domain status to the Overview and Network views.
- Added a BrowserLeaks WebGL shortcut.
- Added a visible exclusion list and manual domain exclusion management.

### Integrity and tests

- Manifest identity is `Traceveil` with Chrome numeric version `1.4.0.5` and display version `1.4.0-alpha.5`.
- `package.json` and `package-lock.json` now agree on `1.4.0-alpha.5`.
- Updated package validation for the final v1.4 identity.
- Extended main-world validation to test WebGL renderer scrubbing, debug-extension semantics, synthetic extension stability, WebGL disable behavior, preserved WebGPU capability, immutable WebGPU adapter-info scrubbing, and WebGPU disable behavior.
- Added UI validation ensuring every configured v1.3.2 module remains represented in the redesigned popup.
- Added a test that verifies the duplicate GPU fake-extension pools in the popup and main-world code stay identical.
- Added SHA-256 source-manifest validation so stale release hashes fail `npm test`.

## 1.3.2

### Fixed

- Fixed a Chrome error in the Canvas `getImageData()` wrapper caused when a page shadows a captured method's `.apply()` or `.call()` property.
- Protected native browser APIs are invoked through an early captured `Reflect.apply` reference rather than mutable page-visible invocation properties.
- Canvas and OffscreenCanvas pixel perturbation fall back to the unmodified native result if only Traceveil's post-processing step fails. Native browser exceptions remain preserved.

### Tests

- Added a regression test that deliberately poisons `getImageData.apply` and `getImageData.call`; Canvas protection must continue to work without throwing.
- Confirmed the fix in real headless Chromium with Canvas `getImageData()` and `toDataURL()` while both invocation properties were poisoned.
- Replaced environment-specific package-lock registry URLs with public npm registry URLs.

## 1.3.1

### Added

- Consistent `devicePixelRatio = 1` normalization alongside the 1920 × 1080 screen profile.
- DST-correct `America/New_York` behavior across default `Intl.DateTimeFormat`, timezone offsets, common `Date` strings, and `toLocale*()` methods.
- Dependency-free main-world validation and expanded real-Chrome verification.

### Security

- Rejected the raw `chrome.storage.session` seed relay through `window.postMessage`.
- Retained one-way iframe activation and `match_origin_as_fallback` coverage.

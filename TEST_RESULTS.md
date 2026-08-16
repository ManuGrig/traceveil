# Test Results — Traceveil v1.4.0

Tested 2026-08-15 during stable-release assembly. The stable build promotes RC.1 protection behavior without functional changes.

## Dependency-free package suite

Command:

```bash
npm test
```

Final result: **PASS**

```text
Traceveil package validation passed
Traceveil service-worker validation passed
Traceveil main-world validation passed
Traceveil UI validation passed
Traceveil integrity validation passed (43 files)
```

Coverage includes:

- Manifest V3 identity, permissions, icon dimensions, package/version consistency, and source syntax.
- Persistent MAIN-world `document_start` registration, network-rule generation, tracker status, exclusions, and Chrome privacy-setting failure isolation.
- v1.3.2 hardware, screen/DPR, timezone, Canvas, first-party stability, and invocation-poisoning regressions.
- Low-entropy UA-CH platform coherence: `navigator.platform = Win32`, `navigator.userAgentData.platform = Windows`, and `toJSON().platform = Windows`.
- Hardware-module disable restoring native legacy and UA-CH platform values.
- Strict UA-CH high-entropy normalization including `platformVersion = 10.0.0` and reduced full-version values.
- `WEBGL_debug_renderer_info` suppression through `getExtension()`.
- `WEBGL_debug_renderer_info` removal from `getSupportedExtensions()` so discovery and enablement remain coherent.
- Hard-coded `UNMASKED_VENDOR_WEBGL` / `UNMASKED_RENDERER_WEBGL` enum suppression.
- `WEBGL_debug_shaders` suppression.
- Deterministic first-party synthetic WebGL extension insertion and stable synthetic `getExtension()` object behavior.
- WebGL pixel interference and WebGL module disable restoring native renderer/extension behavior.
- WebGPU capability preservation.
- Frozen/read-only WebGPU adapter-info scrubbing without Proxy invariant failures.
- Async `requestAdapterInfo()` scrubbing.
- WebGPU module disable restoring native adapter info.
- Popup coverage of every module in `defaults.js`.
- Popup/main-world synthetic-extension pool equality.
- Popup runtime WebGL status logic for renderer suppression rather than generic renderer substitution.
- SHA-256 validation of every packaged source/artwork file except the source manifest itself.

## Renderer-strategy regression target

v1.4 retains the measured reversal of the alpha.5/alpha.6 generic renderer substitution. The release decision is based on measured Cover Your Tracks population results supplied during testing:

```text
v1.3.2 unavailable/None renderer: 7.13 bits, about 1 in 139.77 browsers
alpha.6 WebKit / WebKit WebGL:    12.27 bits, about 1 in 4,955.56 browsers
```

The v1.4 target is therefore an unavailable debug renderer plus coherent removal of `WEBGL_debug_renderer_info` from the advertised extension list, while retaining synthetic extension farbling and first-party WebGL pixel interference.

## Real Chromium MAIN-world smoke test

Environment:

- Debian Chromium 144.0.7559.96
- Xvfb desktop session
- SwiftShader WebGL backend
- `defaults.js` and `main-world.js` injected before page code through Chrome DevTools Protocol for runtime smoke validation

Result: **PASS for WebGL 1 and WebGL 2**

Observed:

```text
platform: Win32
navigator.userAgentData: unavailable in this managed Chromium configuration

WebGL 1:
  vendor: null
  renderer: null
  WEBGL_debug_renderer_info getExtension: null
  WEBGL_debug_renderer_info advertised: false
  synthetic extension: EXT_image_residency
  synthetic getExtension object: present
  getExtension toString: function getExtension() { [native code] }
  getParameter toString: function getParameter() { [native code] }

WebGL 2:
  vendor: null
  renderer: null
  WEBGL_debug_renderer_info getExtension: null
  WEBGL_debug_renderer_info advertised: false
  synthetic extension: EXT_image_residency
  synthetic getExtension object: present
  getExtension toString: function getExtension() { [native code] }
  getParameter toString: function getParameter() { [native code] }
```

`navigator.userAgentData` was not exposed by this managed Chromium configuration, so UA-CH platform normalization remains **unit/regression tested but not marked as real-browser validated here**. Real-user Chrome testing on Windows/macOS/Linux remains documented in `RELEASE_TESTING.md`.

WebGPU was also not exposed by this managed Chromium configuration, so real-browser WebGPU remains **not marked as validated here**. The dependency-free suite covers both immutable `GPUAdapter.info` and async `requestAdapterInfo()` behavior.

## External real-browser validation used for stable promotion

A Chrome 151 / Windows hardware test supplied during release validation produced the following Cover Your Tracks results with the RC.1 behavior that is promoted unchanged into v1.4.0:

- Canvas fingerprint: `randomized by first party domain`, 1.01 bits / about 1 in 2.01 browsers.
- WebGL fingerprint: `randomized by first party domain`, 1.12 bits / about 1 in 2.18 browsers.
- WebGL Vendor & Renderer: `None`, 7.28 bits / about 1 in 155.69 browsers.
- AudioContext fingerprint: `randomized by first party domain`, 1.53 bits / about 1 in 2.88 browsers.
- Screen: `1920x1080x24`, 2.85 bits / about 1 in 7.23 browsers.
- Timezone: `America/New_York`, 3.18 bits / about 1 in 9.07 browsers.
- Platform: `Win32`, 1.46 bits / about 1 in 2.76 browsers.
- Hardware concurrency: `8`, 2.09 bits / about 1 in 4.26 browsers.
- Device memory: `8`, 2.71 bits / about 1 in 6.54 browsers.

A BrowserLeaks WebGL test on the same release candidate confirmed:

- WebGL and WebGL 2 remained available.
- Ordinary masked values remained `Vendor: WebKit` and `Renderer: WebKit WebGL`.
- `Unmasked Vendor` and `Unmasked Renderer` were unavailable/undefined.
- The supported-extension list contained a deterministic synthetic extension (`EXT_pipeline_residency` in that first-party test).
- The exact GPU model was not exposed through the debug-renderer path.

These external measurements are empirical validation of the release behavior, not a claim that every machine will produce identical population statistics.

## Full unpacked-extension browser harness

The included `npm run verify` harness remains available for a normal Chrome environment after `npm install`. The managed build environment is not being used as proof of successful unpacked-extension loading; release users can run the release verification plan on real Chrome hardware.

See `RELEASE_TESTING.md`.

## Stable package integrity gate

Result: **PASS**

The stable release was validated twice after the final version/documentation changes and SHA-256 manifest regeneration:

1. `npm test` against the final source tree: **PASS**.
2. A fresh extraction of `Traceveil-v1.4.0-Chrome.zip`, followed by `npm test` inside the extracted package: **PASS**.

The release ZIP was also inspected to confirm that `manifest.json` is directly at the archive root, avoiding the nested-folder load failure seen in an earlier experimental package.

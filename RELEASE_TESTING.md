# Release Verification — Traceveil v1.4.0

Thank you for testing Traceveil. Please report both successful hardware results and failures; hardware diversity remains important for validating a browser-fingerprinting defense across real machines.

## Before testing

- Chrome 119+ is the primary target.
- Disable other Traceveil builds and privacy extensions that patch WebGL/WebGPU while running A/B tests.
- After changing Traceveil settings, reload the page or open a new tab before measuring.

## Test 1 — WebGL GPU identity A/B

1. Turn Traceveil off.
2. Open a new `https://browserleaks.com/webgl` tab.
3. Record:
   - Unmasked Vendor
   - Unmasked Renderer
   - WebGL Report Hash
   - WebGL Image Hash
   - Supported WebGL Extensions
4. Turn Traceveil on with WebGL protection enabled.
5. Open a new BrowserLeaks WebGL tab and repeat.

Expected protected result:

```text
WEBGL_debug_renderer_info: unavailable / None
Unmasked Vendor: None / unavailable
Unmasked Renderer: None / unavailable
```

`WEBGL_debug_renderer_info` should also be absent from the supported-extension list. One Traceveil synthetic extension should still appear in that list. This combination is intentional: the real debug renderer is suppressed, but the separate site-scoped farbling experiment remains active.

## Test 1B — Renderer-extension coherence

On a normal page with WebGL available, run:

```javascript
const gl = document.createElement("canvas").getContext("webgl2") || document.createElement("canvas").getContext("webgl");
({
  debugRenderer: gl?.getExtension("WEBGL_debug_renderer_info"),
  advertised: (gl?.getSupportedExtensions() || []).includes("WEBGL_debug_renderer_info")
})
```

Expected with WebGL protection enabled:

```text
debugRenderer: null
advertised: false
```

If `debugRenderer` is null but `advertised` is true, report it as a coherence regression.

## Test 2 — Stability

With Traceveil enabled, reload the same WebGL test at least five times. Debug renderer info should remain unavailable and the selected synthetic extension should remain stable for that first party and protection mode.

Switch Balanced ↔ Strict and record whether the site-scoped output changes as expected.

## Test 3 — First-party separation

Test BrowserLeaks and another WebGL-reporting site. Synthetic-extension collisions are possible because the pool is finite, but results should not fluctuate on every navigation.

## Test 4 — WebGPU

If `navigator.gpu` exists, use DevTools:

```javascript
const adapter = await navigator.gpu?.requestAdapter();
adapter?.info
```

Where Chrome exposes adapter info through an interceptable surface, report the values before and after Traceveil. The protected build aims to clear `vendor`, `architecture`, `device`, and `description` while preserving WebGPU itself.

## Test 5 — Platform-persona coherence

On Chrome where `navigator.userAgentData` is available, run in DevTools with the Hardware module enabled:

```javascript
({
  legacy: navigator.platform,
  uaCH: navigator.userAgentData?.platform,
  json: navigator.userAgentData?.toJSON?.().platform
})
```

Expected protected result:

```text
legacy: Win32
uaCH: Windows
json: Windows
```

In Strict mode, also run:

```javascript
await navigator.userAgentData?.getHighEntropyValues(["platformVersion"])
```

Expected `platformVersion`: `10.0.0`. Disable the Hardware module and reload; the legacy and UA-CH platform values should return to the browser's native values.

## Test 6 — Full regression

Exercise the existing Traceveil controls:

- Canvas
- WebGL
- WebGPU
- Audio
- Hardware/platform
- Screen geometry
- Language
- Timezone
- Client Hints
- Font probing
- Battery
- WebRTC
- Tracker blocking
- Tracking parameters
- Privacy Sandbox controls
- Third-party cookies in Strict mode
- Network prediction in Strict mode
- Per-site exclusions
- Balanced / Strict switching

## Test 7 — Compatibility

Try normal GPU-heavy and device-sensitive applications:

- Google Maps / 3D Maps
- browser games
- Three.js/WebGL demos
- Figma or similar editors
- CAD/visualization tools
- video/image editors
- conferencing applications
- WebGPU demos
- authentication/payment flows

Report blank canvases, crashes, visual artifacts, missing controls, performance regressions, or behavior that immediately recovers after disabling Traceveil and reloading.

## GitHub issue template

```text
Traceveil version: 1.4.0
Chrome version:
Operating system:
GPU:
GPU driver version (if known):

Website:
Protection mode: Balanced / Strict
Relevant modules enabled:

Expected behavior:
Actual behavior:

WebGL Unmasked Vendor:
WebGL Unmasked Renderer:
WebGL Report Hash:
WebGL Image Hash:
Synthetic extension:

WebGPU available: yes/no
WebGPU adapter info:

Console / chrome://extensions errors:
Screenshot or reproduction steps:
Does disabling Traceveil + reloading fix it: yes/no
```

<p align="center">
  <img src="assets/traceveil-logo.png" alt="Traceveil logo" width="180">
</p>

# Traceveil

## Reduce what your browser reveals

**v1.4.0** is the first stable release of the full v1.4 line: the complete v1.3.2 protection stack plus measured WebGL debug-renderer suppression, site-scoped synthetic extension farbling, WebGPU adapter-info scrubbing, UA-CH platform-persona consistency, and the redesigned Traceveil interface inspired by the Roamer Lens Guard visual system.

Traceveil is a local-first Chrome privacy extension that reduces browser fingerprint linkability and limits common tracking signals. It combines browser-signal normalization, controlled first-party interference, GPU identity protection, lightweight tracker filtering, URL cleanup, and Chrome privacy controls in one configurable Manifest V3 extension.

Traceveil does not claim to make a browser anonymous. Its goal is to make selected high-entropy signals less useful for tracking while keeping related values coherent enough for normal websites to continue working.

## What is new in 1.4

### Platform-persona coherence

Traceveil's Balanced hardware profile reports a Windows-oriented persona. v1.4 closes a same-JavaScript-layer contradiction that could appear on macOS/Linux by keeping the modern low-entropy User-Agent Client Hints platform surface consistent with `navigator.platform`:

- `navigator.platform` → `Win32`
- `navigator.userAgentData.platform` → `Windows`
- `navigator.userAgentData.toJSON().platform` → `Windows`
- Strict `getHighEntropyValues(["platformVersion"])` → `10.0.0`

Disabling the Hardware module or excluding a site restores the native platform surfaces. Network-level User-Agent / Client Hint headers remain outside this JavaScript-layer normalization boundary and are documented as a limitation.

### GPU identity protection

Traceveil v1.4 keeps v1.3.2's first-party WebGL pixel interference and adds extension-surface farbling and WebGPU adapter scrubbing, and uses the renderer-identity strategy selected from measured population data.

The alpha.5/alpha.6 hypothesis was that returning generic `WebKit` / `WebKit WebGL` strings would be less anomalous than an unavailable debug renderer. Cover Your Tracks measurements falsified that assumption on the test population: the generic pair measured 12.27 bits / about 1 in 4,955.56 browsers, while the earlier unavailable/`None` result measured 7.13 bits / about 1 in 139.77 browsers. v1.4 therefore uses renderer-extension suppression instead of inventing a generic Chrome renderer identity.

When WebGL protection is enabled:

- `getExtension("WEBGL_debug_renderer_info")` returns `null`.
- `WEBGL_debug_renderer_info` is removed from `getSupportedExtensions()` so the advertised extension list remains coherent with `getExtension()`.
- Hard-coded `UNMASKED_VENDOR_WEBGL` / `UNMASKED_RENDERER_WEBGL` enum probes are suppressed; the native method is invoked first so Chrome can preserve its normal error-state behavior.
- `WEBGL_debug_shaders` remains suppressed.
- One deterministic first-party synthetic extension is added to `getSupportedExtensions()`.
- `getExtension()` returns a stable matching synthetic object for that extension.
- WebGL `readPixels()` output continues to receive first-party-stable controlled interference.

The synthetic extension uses the same deterministic first-party/mode seed already used by Traceveil. It does not introduce a per-install identifier, browser-global session secret, or per-navigation random value.

When WebGPU protection is enabled, Traceveil preserves `navigator.gpu`. Where Chrome exposes `GPUAdapter.info` or `requestAdapterInfo()`, Traceveil returns a same-prototype scrubbed view with `vendor`, `architecture`, `device`, and `description` cleared while leaving non-sensitive adapter fields intact.

This remains conceptually related to Brave's GPU-fingerprinting direction, but the architecture is different: Brave can implement protections inside Blink, while Traceveil must install MAIN-world JavaScript wrappers at `document_start`. Traceveil therefore does not claim browser-native equivalence.

A full synthetic GPU persona is deliberately deferred. Spoofing an ANGLE renderer string without also matching shader limits, texture limits, extension support, framebuffer behavior, and rendering characteristics can create a more detectable contradiction than suppressing the debug renderer entirely.

### Full Traceveil controls retained

v1.4 is built directly on v1.3.2. The previous protection modules remain available:

- Canvas and OffscreenCanvas output protection
- WebGL output protection
- WebGPU adapter protection
- Audio fingerprint interference
- CPU, memory, and platform normalization
- Screen geometry, color depth, and device-pixel-ratio normalization
- Language normalization
- DST-correct timezone normalization
- High-entropy Client Hint reduction
- Experimental font-probing protection in Strict mode
- Battery normalization in Strict mode
- WebRTC local-IP protection
- Local third-party tracker blocking
- Tracking-parameter removal
- Privacy Sandbox controls
- Strict-mode third-party cookie blocking
- Strict-mode network prediction disabling
- Balanced / Strict modes
- Per-site exclusions
- Strict-mode window standardization

### Redesigned interface

The popup now uses the Traceveil logo and a dark/cyan interface in the same visual family as Roamer Lens Guard. Controls are organized into Overview, Protection, Network, Sites, and About sections without removing the granular options from v1.3.2.

The Overview page also performs a local current-tab WebGL check and reports whether debug-renderer suppression and the synthetic extension are active on the loaded page.

## Install locally

1. Extract the release ZIP.
2. Open `chrome://extensions`.
3. Enable **Developer mode**.
4. Click **Load unpacked**.
5. Select the extracted folder that directly contains `manifest.json`.
6. Pin Traceveil if you want quick access to the popup.
7. Reload already-open websites after changing protection settings.

Do not run multiple Traceveil versions simultaneously; overlapping API wrappers make test results ambiguous.

## Balanced and Strict modes

**Balanced** keeps the v1.3.2 compatibility-focused defaults. Canvas, WebGL, and audio interference are deterministic by first party and protection mode. Hardware, screen, language, and timezone values are normalized to a common Windows/Chrome-oriented profile.

**Strict** increases perturbation density and activates additional protections such as high-entropy Client Hint reduction, optional font defenses, battery normalization, third-party cookie blocking, and network prediction disabling. Strict mode can also standardize the Chrome window to 1600 × 900.

Strict mode can break graphics-heavy sites, editors, authentication flows, media applications, or applications that depend on precise device capability detection. Use per-site exclusions when compatibility matters more.

## Tracker controls

Traceveil includes a bundled local tracker seed and browser-level request controls. There is no remote tracker-list download in this release.

The network layer can:

- block known third-party tracker domains;
- block third-party `ping` requests and disable hyperlink auditing;
- remove common tracking parameters such as `utm_*`, `gclid`, `fbclid`, and `msclkid` from top-level navigations;
- reduce WebRTC local-IP exposure;
- disable supported Privacy Sandbox APIs;
- block third-party cookies in Strict mode;
- disable network prediction in Strict mode.

Traceveil is not intended to replace a full ad blocker.

## Privacy model

Traceveil has:

- no account;
- no telemetry;
- no advertising system;
- no behavioral profile;
- no remote configuration service;
- no per-install fingerprint seed;
- no page-visible browser-session secret.

Settings are stored locally with `chrome.storage.local`. The tracker seed ships in the extension package.

## Testing

Run the dependency-free validation suite:

```bash
npm test
```

It validates package/version consistency, service-worker registration and rule generation, old v1.3.2 protections, the new WebGL/WebGPU behavior, popup coverage of every configured module, duplicate GPU-pool consistency, and the SHA-256 source manifest.

For optional real-Chrome verification:

```bash
npm install
npm run verify
```

See [RELEASE_TESTING.md](RELEASE_TESTING.md) for real-user verification instructions, including BrowserLeaks A/B testing and compatibility reports.

## Honest limitations

Traceveil improves resistance to several browser-level tracking techniques, but it does not:

- hide or change your public IP address;
- replace a VPN or anonymity network;
- control TLS, JA3/JA4, HTTP/2, HTTP/3, QUIC, DNS, or OS network-stack fingerprints;
- create isolated browser identities or profiles;
- prevent every form of first-party tracking;
- guarantee that MAIN-world wrappers are impossible to detect;
- normalize every WebGL/WebGPU capability limit or every rendered GPU behavior;
- cover every worker/worklet execution path from an extension;
- guarantee anonymity or uniqueness-test success.

See [KNOWN_LIMITATIONS.md](KNOWN_LIMITATIONS.md) for the detailed v1.4 threat and compatibility boundary.

## License

Traceveil is released under the [Zero-Clause BSD license](LICENSE). See [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md) for bundled-data and artwork notices.

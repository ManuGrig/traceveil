# Known Limitations — Traceveil v1.4.0

Traceveil v1.4.0 is a stable extension release, but it remains an extension-layer privacy tool rather than a browser-engine anonymity system. The limitations below are part of its explicit threat and compatibility boundary.

## GPU protection boundary

Traceveil suppresses `WEBGL_debug_renderer_info`, removes it from the advertised extension list, and perturbs the remaining supported-extension surface while retaining v1.3.2 WebGL readback interference. It does not normalize every WebGL/WebGPU capability, shader limit, texture limit, framebuffer characteristic, timing behavior, or rendered output path.

A sophisticated tracker can still combine remaining native GPU capability signals.

## Renderer suppression is not a complete GPU persona

RC.1 intentionally prefers an unavailable debug renderer over invented generic renderer strings because measured population data showed a substantially larger anonymity set for the unavailable result. This does not mean the GPU is fully hidden: WebGL/WebGPU limits, extension combinations, shader precision, framebuffer behavior, timing, and rendered characteristics can still contribute to a GPU fingerprint.

A future full GPU persona must make these surfaces agree. v1.4 deliberately does not spoof an Intel/NVIDIA/AMD ANGLE identity without that broader coherence.

## Extension architecture

Traceveil operates through Manifest V3 MAIN-world JavaScript wrappers. Brave can implement comparable GPU changes inside Blink. A browser-native implementation has stronger control over realms and is harder for page code to distinguish from the underlying browser.

Traceveil therefore does not claim Brave-equivalent resistance to wrapper detection.

## Workers and special realms

The current Chrome-extension design covers normal page frames and related fallback frames through `match_origin_as_fallback`, but it does not guarantee equivalent protection in every Worker, SharedWorker, ServiceWorker, Worklet, or browser-internal execution realm.

## Browser/network fingerprints

Traceveil cannot directly control TLS ClientHello, JA3/JA4, HTTP/2 SETTINGS, QUIC/HTTP/3 transport parameters, TCP behavior, DNS patterns, or OS microarchitectural side channels.

## Persona consistency

The JavaScript profile remains intentionally Windows/Chrome-oriented. v1.4 keeps `navigator.platform`, `navigator.userAgentData.platform`, `NavigatorUAData.toJSON().platform`, and Strict high-entropy `platformVersion` internally consistent in page JavaScript.

On a non-Windows host, network-level signals can still contradict that persona, including the reduced User-Agent platform token and UA Client Hint request headers such as `Sec-CH-UA-Platform`. Worker-only UA-CH surfaces are also not guaranteed to receive the same MAIN-world patch. This is an extension-architecture limitation, not a claim of a complete operating-system persona.

## Compatibility

Strict mode and GPU/API wrapping can affect graphics-heavy applications, authentication flows, media services, editors, conferencing, games, and device-capability detection. Use per-site exclusions when necessary and report reproducible breakage.

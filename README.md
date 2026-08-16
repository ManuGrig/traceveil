Traceveil

Reduce what your browser reveals.

Traceveil is a local-first Chrome privacy extension designed to reduce browser fingerprint linkability and common tracking signals without requiring an account, telemetry service, or remote profile.

It combines:

first-party Canvas, WebGL, and Audio protection;
GPU identity suppression;
experimental WebGPU adapter protection;
hardware and browser-profile normalization;
lightweight tracker blocking;
tracking-parameter cleanup;
browser privacy controls;
Balanced and Strict protection modes;
per-site exclusions.

Current stable release: v1.4.0

What changed in Traceveil 1.4

Traceveil 1.4 keeps the full v1.3.2 protection stack and adds a stronger GPU privacy layer, better browser-persona consistency, and a redesigned interface.

GPU identity suppression

WebGL can expose the exact graphics hardware behind the browser.

On the development test system, unprotected Chrome exposed an NVIDIA RTX 5070 Laptop GPU.

Traceveil 1.4 suppresses WEBGL_debug_renderer_info, preventing websites from directly retrieving the unmasked GPU vendor and model while leaving WebGL and WebGL2 available.

The suppression is kept internally coherent:

getExtension("WEBGL_debug_renderer_info") → null

and:

getSupportedExtensions()

does not advertise the suppressed extension.

Site-scoped WebGL farbling

Traceveil also adds a deterministic synthetic WebGL extension scoped to the first-party website.

The signal remains stable for the site but can differ between unrelated sites, reducing its usefulness as a reusable cross-site identifier.

If the synthetic extension is advertised, getExtension() returns a corresponding synthetic object instead of creating an obvious API contradiction.

Experimental WebGPU protection

Where Chrome exposes interceptable WebGPU adapter information, Traceveil scrubs identifying fields such as:

vendor
architecture
device
description

WebGPU itself remains available.

This protection remains experimental because a Manifest V3 extension cannot control the browser renderer as deeply as a browser-native implementation can.

Better platform consistency

Traceveil normalizes related JavaScript-visible platform signals together.

For example, the Windows-oriented hardware profile keeps:

navigator.platform
navigator.userAgentData.platform
navigator.userAgentData.toJSON().platform

consistent instead of exposing contradictory operating-system identities through different APIs.

Preserve capability. Remove identity.

Traceveil generally avoids disabling useful browser APIs.

Canvas still works.

WebGL still renders.

WebGL2 still renders.

Audio still works.

WebGPU remains available where supported.

The goal is to reduce the identifying value of information exposed through those capabilities rather than simply disabling the Web.

Fingerprint protection

Traceveil currently protects or normalizes selected surfaces including:

Canvas and OffscreenCanvas output
WebGL rendered output
WebGL GPU identity
WebGL extension surface
WebGPU adapter identity
AudioContext output
CPU concurrency
device memory
platform identity
screen geometry and color depth
language
timezone
high-entropy User-Agent Client Hints
battery information in stricter modes
optional experimental font probing

Traceveil captures browser intrinsics early and uses hardened invocation paths to reduce simple page-level attempts to bypass or detect modified APIs.

Balanced and Strict modes
Balanced

Balanced mode prioritizes compatibility.

High-entropy Canvas, WebGL, and Audio outputs remain deterministic within the same first-party context while differing across unrelated first parties.

Related normalized browser values remain stable instead of changing during normal navigation.

Strict

Strict mode increases protection strength without introducing per-navigation identity churn.

It can apply stronger rendering and audio interference, reduce additional Client Hints, normalize battery information, block third-party cookies, disable network prediction, and standardize selected window characteristics.

Strict mode may affect graphics-heavy applications, authentication flows, video services, editors, or sites that rely heavily on device detection.

Per-site exclusions are available when compatibility is more important.

Tracker controls

Traceveil includes lightweight local controls for common advertising, analytics, attribution, session-replay, and fingerprinting infrastructure.

Current controls include:

bundled third-party tracker-domain blocking
ping and hyperlink-auditing protection
removal of common tracking parameters such as utm_*, gclid, fbclid, and msclkid
WebRTC local-IP protection
Privacy Sandbox controls
Strict-mode third-party cookie blocking
network-prediction controls
per-site exclusions

Traceveil is not intended to replace a full ad blocker.

Fingerprint resistance and reduction of reusable browser signals remain the primary focus.

Measured results

Testing was performed on the same Chrome/Windows system using EFF Cover Your Tracks.

<table width="700" data-start="180" data-end="1000">
<tbody>
<tr data-start="180" data-end="241">
<td width="160">Metric</td>
<td width="160">Without Traceveil</td>
<td width="220">With Traceveil v1.4</td>
<td width="100">Result</td>
</tr>
<tr data-start="260" data-end="378">
<td data-start="260" data-end="296" data-col-size="sm">HTTP Accept / language header</td>
<td data-col-size="sm" data-start="296" data-end="325">11.17 bits / 1 in 2,307.87</td>
<td data-col-size="md" data-start="325" data-end="353">1.52 bits / 1 in 2.88</td>
<td data-col-size="sm" data-start="353" data-end="378">~801× more common</td>
</tr>
<tr data-start="379" data-end="511">
<td data-start="379" data-end="399" data-col-size="sm">Timezone name</td>
<td data-col-size="sm" data-start="399" data-end="440">America/Toronto, 6.23 bits / 1 in 75.3</td>
<td data-col-size="md" data-start="440" data-end="486">America/New_York, 3.18 bits / 1 in 9.07</td>
<td data-col-size="sm" data-start="486" data-end="511">~8.3× more common</td>
</tr>
<tr data-start="512" data-end="632">
<td data-start="512" data-end="525" data-col-size="sm">Screen</td>
<td data-col-size="sm" data-start="525" data-end="565">2752×1152×32, 11.80 bits / 1 in 3,554</td>
<td data-col-size="md" data-start="565" data-end="607">1920×1080×24, 2.85 bits / 1 in 7.23</td>
<td data-col-size="sm" data-start="607" data-end="632">~492× more common</td>
</tr>
<tr data-start="633" data-end="771">
<td data-start="633" data-end="646" data-col-size="sm">Canvas</td>
<td data-start="646" data-end="684" data-col-size="sm">Fixed hash, 10.56 bits / 1 in 1,512</td>
<td data-col-size="md" data-start="684" data-end="746">Randomized by first-party domain, 1.01 bits / 1 in 2.01</td>
<td data-col-size="sm" data-start="746" data-end="771">~752× more common</td>
</tr>
<tr data-start="772" data-end="917">
<td data-start="772" data-end="789" data-col-size="sm">WebGL hash</td>
<td data-start="789" data-end="828" data-col-size="sm">Fixed hash, 14.12 bits / 1 in 17,770</td>
<td data-col-size="md" data-start="828" data-end="890">Randomized by first-party domain, 1.12 bits / 1 in 2.18</td>
<td data-col-size="sm" data-start="890" data-end="917">~8,151× more common</td>
</tr>
<tr data-start="918" data-end="1044">
<td data-start="918" data-end="948" data-col-size="sm">WebGL vendor &amp; renderer</td>
<td data-col-size="sm" data-start="948" data-end="984">RTX 5070, 12.95 bits / 1 in 7,898</td>
<td data-col-size="md" data-start="984" data-end="1020">None, 7.28 bits / 1 in 155.88</td>
<td data-col-size="sm" data-start="1020" data-end="1044">~51× more common</td>
</tr>
<tr data-start="1045" data-end="1181">
<td data-start="1045" data-end="1057" data-col-size="sm">Audio</td>
<td data-start="1057" data-end="1094" data-col-size="sm">Fixed value, 2.92 bits / 1 in 7.59</td>
<td data-col-size="md" data-start="1094" data-end="1156">Randomized by first-party domain, 1.52 bits / 1 in 2.88</td>
<td data-col-size="sm" data-start="1156" data-end="1181">~2.6× more common</td>
</tr>
<tr data-start="1182" data-end="1283">
<td data-start="1182" data-end="1198" data-col-size="sm">CPU cores</td>
<td data-col-size="sm" data-start="1198" data-end="1228">24, 7.09 bits / 1 in 136.49</td>
<td data-col-size="md" data-start="1228" data-end="1259">8, 2.09 bits / 1 in 4.26</td>
<td data-col-size="sm" data-start="1259" data-end="1283">~32× more common</td>
</tr>
<tr data-start="1284" data-end="1395">
<td data-start="1284" data-end="1304" data-col-size="sm">Device memory</td>
<td data-col-size="sm" data-start="1304" data-end="1336">32 GB, 3.88 bits / 1 in 14.76</td>
<td data-col-size="md" data-start="1336" data-end="1370">8 GB, 2.71 bits / 1 in 6.54</td>
<td data-col-size="sm" data-start="1370" data-end="1395">~2.3× more common</td>
</tr>
</tbody>
</table>


“More common” describes the observed anonymity-set change for that individual signal in EFF's tested population. It does not mean overall privacy improves by the same multiplier.

Canvas, WebGL, and Audio are additionally partitioned by first-party context so unrelated sites do not simply receive the same protected high-entropy output.

A complete browser fingerprint may still be unique.

Traceveil reduces linkability. It does not promise anonymity.

Why Traceveil does not spoof everything

A fake value can be more identifying than the real one.

During development of v1.4, Traceveil temporarily replaced the GPU debug renderer with generic-looking:

WebKit
WebKit WebGL

Measured population data showed that this combination was much rarer than suppressing the identifying renderer entirely.

Traceveil therefore reverted to coherent debug-renderer suppression.

The lesson is simple:

Privacy decisions should be based on measured anonymity sets and internally coherent behavior, not on whether a fake value looks generic.

Similar goal, different architecture

Browser-native privacy systems can modify Chromium before identifying information reaches page JavaScript.

Traceveil operates as a Manifest V3 Chrome extension.

It therefore uses early MAIN-world interception and browser extension APIs rather than modifying Blink, the GPU process, or the network stack itself.

That allows meaningful protection for existing Chrome users, but also creates limits.

Honest limitations

Traceveil does not:

hide or change your public IP address;
replace a VPN or anonymity network;
control TLS, JA3, JA4, HTTP/2, HTTP/3, QUIC, or the full DNS/network stack;
guarantee that every JavaScript modification is undetectable;
control every Worker or Worklet execution context;
normalize every GPU capability or timing surface;
create isolated browser identities;
prevent all first-party tracking;
defeat every current or future fingerprinting technique.

Some privacy defenses can be implemented effectively as an extension.

Others ultimately require control of the browser engine itself.

Privacy

Traceveil runs locally.

There is:

no Traceveil account;
no browsing telemetry;
no analytics endpoint;
no remote behavioral profile;
no installation identifier sent to Traceveil;
no server receiving browsing history.

Settings are stored locally in Chrome.

Install Traceveil

Download the latest release from the GitHub Releases page.

Then:

Extract the release ZIP.
Open chrome://extensions.
Enable Developer mode.
Select Load unpacked.
Choose the extracted folder containing manifest.json.
Reload already-open tabs.

Do not run multiple Traceveil versions at the same time.

Testing

Useful external tests include:

EFF Cover Your Tracks
BrowserLeaks WebGL
Canvas fingerprint tests
Audio fingerprint tests
WebGL-heavy applications
Google Maps
Figma
browser games and 3D applications
WebGPU demos

Compatibility regressions are as important as improved privacy scores.

A privacy extension that wins a benchmark but breaks normal browsing is not a successful privacy tool.

Feedback

Bug reports and hardware-specific results are welcome.

Useful reports include:

Traceveil version:
Chrome version:
Operating system:
GPU:

Website:
Balanced / Strict:
Changed protection settings:

Expected behavior:
Actual behavior:

Console errors:
Screenshots:
Project

Website: https://roamer.one/traceveil/

Releases: https://github.com/ManuGrig/traceveil/releases

Issues: https://github.com/ManuGrig/traceveil/issues

License

Open source. See LICENSE for the license applicable to this repository.

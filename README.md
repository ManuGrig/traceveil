# traceveil
Traceveil is a local-first Chrome extension that reduces browser fingerprint linkability by normalizing noisy signals and adding controlled canvas, WebGL, WebGPU, and audio interference. It includes lightweight tracker controls, no account, and no telemetry.

Traceveil is a privacy extension for Chrome that reduces browser fingerprinting and limits common tracking signals. It combines fingerprint normalization, controlled interference, lightweight tracker filtering, and browser privacy controls in one transparent, configurable extension.
Instead of pretending to make your browser invisible, Traceveil makes selected browser signals less reliable and less identifying while preserving enough functionality for ordinary websites to work. Protection is applied before page scripts run and operates across frames, helping prevent trackers from collecting the original high-entropy values first.
Fingerprint protection
Modern tracking does not depend entirely on cookies. Websites can combine dozens of browser and hardware characteristics to recognize a device, including screen geometry, graphics hardware, processor count, memory, language, timezone, canvas rendering, WebGL output, WebGPU support, and audio processing.
Traceveil reduces the usefulness of these signals by presenting a more standardized browser profile and interfering with high-entropy rendering outputs.
Protection includes:
•	Canvas output perturbation
•	WebGL output protection and GPU identity suppression
•	WebGPU adapter protection
•	Audio fingerprint interference
•	CPU, memory, and platform normalization
•	Screen geometry and color-depth normalization
•	Language and timezone normalization
•	High-entropy User-Agent Client Hint reduction
•	Battery normalization in stricter protection modes
•	Optional experimental font-probing defenses
•	Basic resistance to scripts inspecting modified JavaScript functions
Traceveil does not randomly invent a completely different identity for every browser API. Its protections are designed to keep related values internally consistent and avoid obvious contradictions that could make the browser even more unusual.
Balanced and Strict modes
Balanced mode provides stable first-party fingerprint protection with a focus on compatibility. Canvas, WebGL, and audio interference remain consistent within each first-party domain, reducing cross-site correlation without causing values to fluctuate during normal page use.
Strict mode adds stronger defenses for more privacy-sensitive browsing. It generates fresh private interference entropy for each navigation, applies denser rendering and audio perturbation, reduces additional Client Hints, normalizes battery information, blocks third-party cookies, disables network prediction, and can standardize the browser window.
Strict mode may affect graphics applications, editors, authentication systems, video services, or websites that depend heavily on device detection. Individual sites can be excluded when compatibility is more important.
Tracker controls
Traceveil includes lightweight local tracker controls for common advertising, analytics, attribution, session-replay, and fingerprinting infrastructure.
This local-only build does not download or bundle restricted third-party tracker datasets. Instead, it uses a small bundled local seed list and browser-level request controls to reduce common tracking channels while keeping the project simple to audit and suitable for unrestricted open-source publication.
Tracker controls include:
•	Basic third-party tracker-domain blocking
•	Third-party ping and hyperlink-auditing protection
•	Removal of common tracking parameters such as utm_*, gclid, fbclid, and msclkid
•	WebRTC local-IP protection
•	Privacy Sandbox API controls
•	Strict-mode third-party cookie blocking
•	Per-site exceptions for compatibility
Traceveil is not intended to replace a full ad blocker. Its primary purpose is fingerprint resistance and signal reduction. The tracker controls are a secondary layer designed to reduce common tracking paths without adding remote telemetry, account systems, or proprietary update services.
Clear, local controls
Traceveil gives you direct control over each protection category. You can enable or disable protection globally, exclude a specific website, switch between Balanced and Strict modes, and independently control fingerprint and network modules.
No account is required. There is no cloud dashboard, behavioral profile, advertising system, or remote configuration service. Settings are stored locally in the browser.
Measurable protection
In comparative Cover Your Tracks testing, Traceveil substantially reduced the identifying value of several high-entropy browser signals. Canvas, WebGL, and audio results were reported as randomized by first-party domain, while exposed screen, timezone, language, processor, memory, and GPU characteristics became significantly more common than the original hardware profile.
A browser may still be reported as unique because fingerprint tests combine many attributes and are limited by the size of their datasets. The important measurement is not only the headline result, but how much identifying information each protected signal continues to reveal.
Traceveil is designed to reduce linkability. It does not promise anonymity.
Honest limitations
Traceveil improves tracking resistance, but it does not make a browser anonymous.
It does not:
•	Hide or change your public IP address
•	Replace a VPN or anonymity network
•	Control TLS, JA3, JA4, HTTP/2, HTTP/3, QUIC, DNS, or network-stack fingerprints
•	Create isolated browser profiles
•	Prevent every form of first-party tracking
•	Guarantee that modified APIs are undetectable
•	Defeat every current or future fingerprinting technique
For stronger identity separation, use dedicated Chrome profiles or separate browser installations alongside appropriate network-level privacy tools.
Privacy without pretending to be invisible
Traceveil is designed to reduce linkability, suppress unusually revealing hardware characteristics, and make browser fingerprints less reliable while remaining honest about the limits of a Chrome extension.
It does not make you disappear.
It gives websites less dependable information to work with.

Direct comparison - Test done with https://coveryourtracks.eff.org/
Metric	Without extension	With extension	Result
HTTP Accept / language header	11.17 bits / 1 in 2,307.87	1.49 bits / 1 in 2.81	~821× more common
Timezone name	America/Toronto, 6.23 bits / 1 in 75.3	America/New_York, 3.60 bits / 1 in 12.1	~6.2× more common
Screen	2752×1152×32, 11.80 bits / 1 in 3,554	1920×1080×24, 3.43 bits / 1 in 10.79	~329× more common
Canvas	fixed hash, 10.56 bits / 1 in 1,512	randomized by first party	~768× more common
WebGL hash	fixed hash, 14.12 bits / 1 in 17,770	randomized by first party	~7,969× more common
WebGL renderer	RTX 5070, 12.95 bits / 1 in 7,898	None, 7.13 bits / 1 in 139.77	~56× more common
Audio	fixed value, 2.92 bits / 1 in 7.59	randomized by first party	~2.8× more common
CPU cores	24, 7.09 bits / 1 in 136.49	8, 2.08 bits / 1 in 4.24	~32× more common
Device memory	32 GB, 3.88 bits / 1 in 14.76	8 GB, 2.61 bits / 1 in 6.12	~2.4× more common



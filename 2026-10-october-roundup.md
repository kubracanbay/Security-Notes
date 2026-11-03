# October 2026: when trusted tools became the way in

*Kübra Nur Canbay, 3 November 2026*

October was a heavy month for security. Looking back, what connects the biggest incidents is not one clever exploit. It is trust: attackers got in through release pipelines, vendor apps and runtimes that people were right to rely on.

Below are four incidents that are confirmed by the affected projects or vendors themselves.

## 1. LiteLLM 1.107.0: a poisoned release (21 October)

LiteLLM, an open-source gateway many teams use to reach AI model providers, says its release pipeline was compromised and used to publish a malicious `litellm==1.107.0`. The package was on PyPI for about two hours (11:39 to 13:23 UTC), and the Docker tags `v1.107.0`, `latest`, `main-latest` and `main-stable` also pointed to a malicious image. According to reports, the attackers used stolen GitHub CLI tokens belonging to LiteLLM engineers.

The malicious code ran whenever Python started, even if LiteLLM was never imported, and collected cloud tokens, private keys and environment variables. LiteLLM's advice is clear: if you installed or ran 1.107.0 in that window, assume the system is compromised. Rotate credentials from a clean machine and rebuild from a verified release. LiteLLM says customers on its private enterprise release were not affected.

## 2. ConnectWise Cloud Backup: a vendor app turned against its customers (16 October)

ConnectWise disclosed unauthorized access to the application identities of its Cloud Backup service (formerly SkyKick). These apps already had consent to read customers' Microsoft 365 tenants, so whoever controlled them could reach those tenants. Microsoft later attributed the activity to Storm-0940, a China state-aligned group.

If you used the service, review your Entra ID service principals, app sign-ins, role assignments, client secrets and OAuth consents from mid-October onward.

## 3. PHP2Shell: critical flaws in PHP itself (21 October)

PHP shipped emergency fixes for two flaws rated 9.2 that allow remote code execution without authentication. Details and fixed versions are in my earlier note: [PHP and Node.js security releases](2026-10-21-php-and-nodejs.md).

## 4. Node.js: remote code execution in the runtime (21 October)

On the same day, Node.js released scheduled fixes for ten vulnerabilities, including a Brotli decoder flaw that can lead to remote code execution. Same note as above.

## The pattern

None of these victims did anything unusual. They installed an official package, trusted an established vendor, or ran a widely used runtime. That is exactly why these attacks worked.

A few habits would have reduced the damage in every case:

- Pin dependencies by version and hash, and container images by digest, so a new "latest" never reaches production on its own.
- Review the third-party apps that hold consent to your cloud and Microsoft 365 tenants, and remove the ones you no longer need.
- Keep an inventory of the runtimes and versions you run, so patching day starts with a list instead of a search.
- When credentials may have leaked, rotate them from a clean environment, not from the machine that may be compromised.

## Sources

- [LiteLLM: Contained: Oct 21, 2026 LiteLLM release v1.107.0 compromised](https://docs.litellm.ai/blog/security-incident-oct-21-2026)
- [LiteLLM: What we are changing after October 21](https://docs.litellm.ai/blog/security-changes-after-oct-21-2026)
- [Zentera: Threat Briefing, October 2026 (ConnectWise and Storm-0940)](https://www.zentera.net/news/threat-briefing-october-2026)
- [Cyber Attacks October 2026: The biggest incidents and what to patch](https://znovastudio.com/insights/cyber-attacks-october-2026)
- [PHP security advisory, 21 October 2026](https://www.php.net/archive/2026.php#2026-10-21-5)
- [Node.js: Wednesday, October 21, 2026 Security Releases](https://nodejs.org/en/blog/vulnerability/october-2026-security-releases)

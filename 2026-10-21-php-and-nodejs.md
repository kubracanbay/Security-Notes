# PHP and Node.js security releases, 21 October 2026

*Written 23 October 2026 by [Kübra Nur Canbay](https://github.com/kubracanbay)*

## TL;DR

- **Two separate events landed on the same day.** PHP fixed two critical remote code execution flaws, known together as PHP2Shell. Node.js shipped its scheduled security releases, fixing ten vulnerabilities. The two are unrelated, even though some posts on social media mixed them up.
- **PHP:** upgrade to 8.5.12, 8.4.27, 8.3.36 or 8.2.35. There is no official release for PHP 8.1 or older.
- **Node.js:** upgrade to 22.23.4, 24.22.1 or 26.12.1. End-of-life lines, including Node.js 20, get no fix.

## PHP: PHP2Shell (CVE-2026-109618, CVE-2026-109619)

**What happened.** Both flaws are rated 9.2 (critical) under CVSS v4.0, and both let an attacker run code on the server without logging in, with the privileges of the web worker.

- **CVE-2026-109618:** memory corruption when a request runs out of memory.
- **CVE-2026-109619:** stale internal state that leads to a use-after-free, memory disclosure and remote code execution.

**Who is affected.** Any PHP installation that serves web requests: PHP-FPM, Apache with mod_php, php-cgi, FrankenPHP and LiteSpeed. The flaws were reported by researchers from Calif, Wiz and OpenAI.

| PHP branch | Fixed in |
| --- | --- |
| 8.5 | 8.5.12 |
| 8.4 | 8.4.27 |
| 8.3 | 8.3.36 |
| 8.2 | 8.2.35 |
| 8.1 and older | No official release. The PHP security team published minimal source backports for 7.4, 8.0 and 8.1 for people who build PHP from source. |

**Worth knowing:**

- The advisory lists interim mitigations, but they only reduce some attack paths. Upgrading is the only complete fix.
- Linux distributions often backport fixes without changing the version number. Check your vendor's advisory instead of relying on `php -v` alone.
- PHP announced the release a day in advance, on 20 October. A public proof-of-concept was claimed on the day of the release, so treat this as urgent.
- PHP 8.2 reaches end of life on 31 December 2026. Patch now, and plan the upgrade to a newer branch separately.

## Node.js: October 2026 security releases

**What happened.** Node.js fixed ten vulnerabilities across its supported lines (22.x, 24.x and 26.x). Four are rated high:

- **CVE-2026-111497:** an out-of-bounds write in the Brotli decoder, which Node.js says permits remote code execution. Services that decompress Brotli data from untrusted sources are most exposed, for example HTTP clients and proxies that accept `Content-Encoding: br`, or services that handle uploaded compressed files.
- **CVE-2026-106069:** a TLS certificate hostname check that can be bypassed when a host loosely resolves to an IP address.
- **CVE-2026-105483:** a TLS certificate hostname check that can be bypassed with an IP address written in fullwidth digits (24.x and 26.x only).
- **CVE-2026-105482:** `dns.resolve` truncating names that contain a NUL character.

**Fixed in:** 22.23.4, 24.22.1 and 26.12.1. End-of-life versions are always affected and are not patched.

**Worth knowing:** this was a scheduled release, announced a week in advance on 14 October.

## Why they got mixed up

Both stories broke on the same day under "critical security update" headlines. But PHP's release was an out-of-band emergency, while Node.js's was planned in advance. Different runtimes, different bugs, different fixes.

That said, many stacks run both: a PHP backend with Node.js in the frontend build, for example. If that is you, both apply.

## What to do

1. **Find every place each runtime runs.** Servers, containers, serverless layers, and CI and build agents. Start with `php -v` and `node -v`.
2. **Upgrade to the fixed versions above.** For distribution packages, confirm the patch status in your vendor's advisory.
3. **Rebuild your containers.** Pull the patched base image, rebuild and redeploy. A running container keeps its old runtime until you do.
4. **If you cannot upgrade PHP today,** apply the interim mitigations from the advisory and consider limiting access to the service until you can. Treat both as temporary.
5. **Track end-of-life dates.** PHP 8.1 and older and Node.js 20 and older received nothing this week. PHP 8.2 joins them on 31 December 2026.

## From the field

At Magicorn I work with a portfolio of around 25 clients. When these releases landed, we patched the affected environments and sent our clients clear guidance on what to update.

The lesson is the one weeks like this keep teaching: you can only patch what you know you run. Knowing every runtime version across hosts, containers and build pipelines, and when each one reaches end of life, is what turns a day like this from a crisis into a checklist.

## Sources

**PHP**
- [PHP security advisory, 21 October 2026](https://www.php.net/archive/2026.php#2026-10-21-5)
- Release announcements: [8.5.12](https://www.php.net/releases/8_5_12.php) · [8.4.27](https://www.php.net/releases/8_4_27.php) · [8.3.36](https://www.php.net/releases/8_3_36.php) · [8.2.35](https://www.php.net/releases/8_2_35.php)
- [PHP 8.4.27 release on GitHub (php-src)](https://github.com/php/php-src/releases/tag/php-8.4.27)
- [Canadian Centre for Cyber Security, Alert AL26-026](https://cyber.gc.ca/en/alerts-advisories/al26-026-critical-vulnerabilities-impacting-php-cve-2026-109618-cve-2026-109619)
- [BleepingComputer: PHP ships emergency patches for critical PHP2Shell flaws](https://bleepingcomputer.com/news/security/php-ships-emergency-patches-for-critical-php2shell-flaws)

**Node.js**
- [Node.js: Wednesday, October 21, 2026 Security Releases](https://nodejs.org/en/blog/vulnerability/october-2026-security-releases)
- [Qualys: Node.js patches Brotli remote code execution flaw](https://blog.qualys.com/product-tech/2026/10/23/node-js-patches-brotli-remote-code-execution-flaw-find-vulnerable-runtimes-before-attackers-do)

*These notes reflect my own view and do not represent my employer. Details may change as maintainers publish more information; always check the official advisories.*

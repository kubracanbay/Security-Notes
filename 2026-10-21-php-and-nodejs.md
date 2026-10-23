# PHP and Node.js security releases, 21 October 2026

*Kübra Nur Canbay, 23 October 2026*

On 21 October, PHP and Node.js both shipped security fixes. The timing made it look like one story, and some posts online treated it that way. It wasn't. These are two separate runtimes with separate bugs, so if you only run one of them, only one of these applies to you.

## PHP

PHP fixed two critical flaws, CVE-2026-109618 and CVE-2026-109619, which have been nicknamed PHP2Shell. Both are rated 9.2 under CVSS v4.0, and both allow remote code execution without authentication. The first comes from memory corruption when a request runs out of memory. The second comes from internal state that outlives the memory it points to, a use-after-free.

Anything that serves web requests with PHP is in scope: PHP-FPM, Apache with mod_php, php-cgi, FrankenPHP and LiteSpeed.

| Branch | Fixed in |
| --- | --- |
| 8.5 | 8.5.12 |
| 8.4 | 8.4.27 |
| 8.3 | 8.3.36 |
| 8.2 | 8.2.35 |
| 8.1 and older | No official release (source-only backports exist for 7.4, 8.0 and 8.1) |

The advisory lists interim mitigations, but they don't cover every attack path, so upgrading is the real fix. If you install PHP from a Linux distribution, the version number may not change after a patch, so check your vendor's advisory too. And PHP 8.2 reaches end of life on 31 December 2026, which makes this a good moment to plan that upgrade as well.

## Node.js

Node.js fixed ten vulnerabilities across 22.x, 24.x and 26.x, four of them rated high. The one that stands out is CVE-2026-111497, an out-of-bounds write in the Brotli decoder that Node.js says can lead to remote code execution. Services that decompress Brotli data from outside are the most exposed, such as an HTTP client or proxy that accepts `Content-Encoding: br`, or an upload handler. The other three high-severity issues (CVE-2026-106069, CVE-2026-105483 and CVE-2026-105482) affect TLS hostname checks and DNS resolution.

The fixed versions are 22.23.4, 24.22.1 and 26.12.1. Node.js 20 and other end-of-life lines are not patched.

Unlike PHP's, this release was planned. Node.js announced it a week in advance.

## What to do

- Find where each runtime actually runs: servers, containers, serverless layers and build agents. `php -v` and `node -v` are a start.
- Upgrade to the fixed versions, and rebuild your container images so they pick up the patched runtime.
- If you can't upgrade PHP right away, apply the advisory's mitigations and limit access to the service in the meantime.

## My take

There have been a lot of security incidents lately, and I keep wondering about the ones that haven't surfaced yet. With Node.js in particular, a remote code execution flaw in the runtime itself feels like a serious turning point.

PHP shipped its fixes within a day of announcing them, which is impressive. What I don't know is how long this pace can last. The people who maintain these projects, many of them volunteers, are being asked to respond faster and faster.

At Magicorn, we updated the affected client environments and told our clients what to change. We have also recently launched a security product for our clients, and I'm curious to see where it goes.

My honest view: with AI in the picture, security vulnerabilities will be one of the biggest problems of the years ahead.

## Sources

- [PHP security advisory, 21 October 2026](https://www.php.net/archive/2026.php#2026-10-21-5)
- PHP release announcements: [8.5.12](https://www.php.net/releases/8_5_12.php), [8.4.27](https://www.php.net/releases/8_4_27.php), [8.3.36](https://www.php.net/releases/8_3_36.php), [8.2.35](https://www.php.net/releases/8_2_35.php)
- [Canadian Centre for Cyber Security, Alert AL26-026](https://cyber.gc.ca/en/alerts-advisories/al26-026-critical-vulnerabilities-impacting-php-cve-2026-109618-cve-2026-109619)
- [Node.js: Wednesday, October 21, 2026 Security Releases](https://nodejs.org/en/blog/vulnerability/october-2026-security-releases)
- [Qualys: Node.js patches Brotli remote code execution flaw](https://blog.qualys.com/product-tech/2026/10/23/node-js-patches-brotli-remote-code-execution-flaw-find-vulnerable-runtimes-before-attackers-do)

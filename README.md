# Subdomain Takeover PoC — `resources.atlas.wooga.com`

Authorized security research conducted under the [Playtika HackerOne Bug Bounty Program](https://hackerone.com/playtika).

## Purpose

This repository exists **only** to prove a subdomain takeover vulnerability against `resources.atlas.wooga.com`, which is a dangling CNAME pointing to GitHub Pages edge IPs (`185.199.108-111.153`).

## Scope

- Affected asset: `resources.atlas.wooga.com`
- HackerOne program scope: `*.wooga.com` (in-scope wildcard)

## Contents

- `CNAME` — tells GitHub Pages which custom domain to bind (`resources.atlas.wooga.com`)
- `index.html` — static security-research notice with no JavaScript, no redirects, no cookies, no forms

## Remediation will remove this PoC

This repository will be deleted as soon as Playtika / Wooga triages the report and confirms the finding has been remediated.

## Contact

- HackerOne report: (linked in the private H1 submission)
- Playtika Security: security@playtika.com

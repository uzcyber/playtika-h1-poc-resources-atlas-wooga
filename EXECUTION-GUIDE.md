# Execution Guide — Live Takeover Demonstration

## Pre-flight checklist

- [ ] Your HackerOne report for `resources.atlas.wooga.com` is already drafted (see `reports-round4/report-1-github-pages-takeover-atlas-wooga.md`)
- [ ] You have a GitHub account
- [ ] You have `git` installed locally
- [ ] You understand this is a live PoC on an in-scope asset — follow responsible disclosure

---

## Step 1 — Edit `index.html` with your H1 handle

Already done — the handle `@cybersecuz` is embedded in `index.html`.

Verify:
```bash
grep -n "cybersecuz" index.html
```

---

## Step 2 — Create the GitHub repository

1. Go to https://github.com/new
2. Repository name: `playtika-h1-poc-resources-atlas-wooga` (or similar — descriptive name makes intent clear to anyone who looks)
3. **Make it PUBLIC** (GitHub Pages custom domains on the free tier require public repos, and public repos are auditable by Playtika)
4. Do NOT initialize with README (we have one)
5. Click "Create repository"
6. Note the repo URL, e.g. `https://github.com/YOUR_USERNAME/playtika-h1-poc-resources-atlas-wooga`

---

## Step 3 — Push the PoC files

```bash
cd /Users/cybersecuz/Desktop/claude2/playtika/takeover-poc
git init
git add CNAME index.html README.md
git commit -m "PoC: subdomain takeover proof for authorized HackerOne report against Playtika"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/playtika-h1-poc-resources-atlas-wooga.git
git push -u origin main
```

---

## Step 4 — Enable GitHub Pages on the repo

1. Go to your repo → **Settings** → **Pages**
2. Under "Build and deployment":
   - Source: **Deploy from a branch**
   - Branch: **main** / **/ (root)**
3. Click **Save**
4. GitHub will read the `CNAME` file and bind the custom domain
5. Wait 1-5 minutes for:
   - DNS propagation check
   - Let's Encrypt certificate issuance (you'll see "DNS check successful" → "Ready" in the Pages UI)
6. Check "Enforce HTTPS" once it becomes available

---

## Step 5 — Verify the takeover

From a terminal (not your browser — browsers cache aggressively):

```bash
# Should return 200 OK with your PoC HTML
curl -sI https://resources.atlas.wooga.com/

# Confirm the cert chain is GitHub's Let's Encrypt
curl -v https://resources.atlas.wooga.com/ 2>&1 | grep -E "subject:|issuer:"

# Confirm your PoC HTML is served
curl -s https://resources.atlas.wooga.com/ | grep -i "Subdomain Takeover PoC"
```

Expected output:
- `HTTP/2 200`
- `server: GitHub.com`
- Your PoC HTML page content
- Certificate issued by Let's Encrypt / GitHub

---

## Step 6 — Capture evidence

Take screenshots of:

1. Browser visiting `https://resources.atlas.wooga.com/` showing:
   - Your PoC page rendered
   - Padlock icon (valid TLS cert)
   - URL bar showing the hijacked domain
2. Click the padlock → View certificate → screenshot showing the Let's Encrypt cert for `resources.atlas.wooga.com`
3. Terminal output of the curl commands above
4. GitHub repo Settings → Pages showing your custom domain bound

Save screenshots as `evidence-1-browser.png`, `evidence-2-cert.png`, `evidence-3-curl.png`, `evidence-4-pages-settings.png`.

---

## Step 7 — Update the HackerOne report

Add a new section to the report titled **"Live Takeover Demonstration"**:

```markdown
## Live Takeover Demonstration (YYYY-MM-DD HH:MM UTC)

As of [TIMESTAMP], I have successfully claimed the dangling CNAME using a public GitHub repository at:

- Repo: https://github.com/YOUR_USERNAME/playtika-h1-poc-resources-atlas-wooga
- Live PoC URL: https://resources.atlas.wooga.com/

The PoC serves only a static HTML notice clearly identifying this as authorized security research.
No malicious content is hosted. The repository and takeover will be removed immediately upon
Playtika's confirmation of remediation.

Evidence attached:
- evidence-1-browser.png (browser rendering + padlock)
- evidence-2-cert.png (Let's Encrypt TLS certificate detail)
- evidence-3-curl.png (curl output showing server: GitHub.com)
- evidence-4-pages-settings.png (GitHub Pages settings confirming custom domain binding)
```

Upload screenshots as attachments to the HackerOne report.

---

## Step 8 — Notify Playtika immediately

- Submit/update the HackerOne report with "Live Takeover Demonstration" section
- Optionally: send a brief email to `security@playtika.com` pointing at the H1 report ID
- State that you will take the PoC down immediately on request

---

## Step 9 — Take down the PoC once triaged

Once Playtika confirms the finding (or sooner if they request):

1. GitHub repo → Settings → Pages → Remove custom domain
2. Delete the repository entirely
3. Verify takedown:
   ```bash
   curl -sI https://resources.atlas.wooga.com/
   # Should now return 404 from GitHub Pages or start resolving to whatever they reconfigure
   ```
4. Update the HackerOne report with a "PoC taken down" note and timestamp.

---

## Do NOT do any of the following

- ❌ Add JavaScript that fetches external resources, reads cookies, or interacts with other `*.wooga.com` origins
- ❌ Add redirects anywhere (including `<meta http-equiv=refresh>`)
- ❌ Add forms, input fields, or anything resembling credential capture
- ❌ Leave the takeover live longer than necessary for triage
- ❌ Advertise the live URL on social media or public forums before triage completes
- ❌ Claim other dangling subdomains in the same session — one PoC is sufficient to prove the vulnerability class

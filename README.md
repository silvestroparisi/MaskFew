# MaskFew

**Privacy-first data anonymizer — runs entirely in your browser, open source, for everyone.**

MaskFew is part of the **Few** toolkit, alongside [FirstFew](https://github.com/silvestroparisi/FirstFew) and [FixFew](https://github.com/silvestroparisi/FixFew).
It removes personal data, secrets and identifiers from your files — locally — so you're free to use cloud tools and AI without leaking what matters.

It's a single, self-contained web page. No backend, no build step, no upload, no tracking.

🔗 **Live:** https://silvestroparisi.github.io/MaskFew/

> Strip out the sensitive bits. Then use AI and cloud freely.

## Why

You want to paste that spreadsheet into ChatGPT, upload a contract to a cloud tool, or send logs to a vendor — but each of those can leak names, codici fiscali, IBANs, API keys. The value is real, and so is the exposure.

There's only one place a sanitizer makes sense: **locally**. You can't upload a file to a website *to remove* its sensitive data without first handing that data over. MaskFew does the detection and redaction inside the page itself and gives you back the **same file format**, cleaned.

## Private by default

This is the core design choice:

- Your file is read, scanned and rewritten **entirely in your browser's memory**. Nothing is uploaded, there is no server and no account.
- The only network requests are the ones that load this page and its two libraries (a spreadsheet reader and a zip reader) from a CDN — **never your file**.
- Don't trust the claim? You don't have to — read every line in `index.html`.

## What it detects

Deterministic recognizers, high precision on structured data:

| type | notes |
|---|---|
| **Email** | standard addresses |
| **Phone** | international / Italian formats, 8–13 digits |
| **IBAN** | validated with the mod-97 checksum |
| **Codice fiscale** | 16-character Italian tax code |
| **Partita IVA** | 11-digit Italian VAT, checksum-validated |
| **Credit cards** | 13–19 digits, Luhn-validated |
| **IP addresses** | IPv4 (each octet ≤ 255) |
| **Secrets** | AWS keys, JWTs, private-key blocks |

Each category is shown with a count and can be toggled on/off before you redact.

## The three modes

- **Placeholder** — replace each value with a tag like `[EMAIL]`, `[IBAN]`. Simplest; fully removes the value.
- **Consistent pseudonym** — replace each unique value with a stable, searchable label (`[MASK_EMAIL_1]`, `[MASK_IP_1]`…). Same value → same label everywhere, and you can find every replacement by searching `[MASK`. **Best before feeding a file to an AI:** the document keeps its internal logic while the real identities are gone.
- **Mask** — keep the last few characters (`••••6789`) when you still need a partial reference.

## Supported formats

**TXT · CSV · JSON · LOG** (and other plain-text: `tsv`, `md`, `xml`, `yaml`, `ini`) · **XLSX** · **DOCX**

The cleaned file comes back in the same format. For XLSX and DOCX the document structure is preserved; redaction happens on the text content (and, for DOCX, in headers, footers, comments and notes).

**Not in this version (deliberately):** PDF and images (which need OCR for safe redaction), IPv6, and free-text names/addresses (which need NLP, not patterns).

## How to use

1. Open the page (or host your own copy — see below).
2. Drop in a file. It's read and scanned locally; you get a summary of what was found.
3. Choose which categories to remove and a replacement mode.
4. Click **Anonymize & download** — the cleaned file downloads in the same format.

Once the page has loaded, everything works offline.

## Important

MaskFew is **best-effort**, never a guarantee. Deterministic detection reliably catches structured identifiers, but it does **not** catch everything — free-text names are not covered, and complex DOCX/XLSX (charts, macros, text split mid-word across runs) may slip through. It **reduces** risk; it does not certify a file as clean. **Always review the summary, and the file, before sharing it.**

## Run it yourself

It's one file. Either open `index.html` locally, or host it for free:

1. Create a repository named `MaskFew`.
2. Add `index.html` to the repository root.
3. Enable **GitHub Pages** (deploy from branch `main`, folder `/root`).

Works in any modern browser. No build, no dependencies to install.

## Tech

Vanilla HTML / CSS / JavaScript, no framework, no build.
Spreadsheet read/write: [SheetJS](https://sheetjs.com/). DOCX (zip) handling: [JSZip](https://stuk.github.io/jszip/). Both loaded from CDN; your data never touches them remotely.

## The Few toolkit

- [**FirstFew**](https://github.com/silvestroparisi/FirstFew) — prioritize the few that matter
- [**FixFew**](https://github.com/silvestroparisi/FixFew) — verify and remediate
- **MaskFew** — anonymize before you share

## License

[MIT](LICENSE) © 2026 Silvestro Parisi

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
- The only network requests are the ones that load this page and its libraries from a CDN — a spreadsheet reader, a zip reader, and (only when you scan an image or PDF) an OCR engine and PDF tools. **Never your file.**
- Don't trust the claim? You don't have to — it's open source. Open your browser's DevTools → Network and watch: your file never leaves your device.

## What it detects

Deterministic recognizers, high precision on structured data:

| type | notes |
|---|---|
| **Email** | standard addresses |
| **Phone** | Italian / international formats, 9–13 digits; skips dates and document numbers |
| **Address** | Italian street lines (`Via`, `Piazza`, `Corso`… + civic number) — heuristic |
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

**TXT · CSV · JSON · LOG** (and other plain-text: `tsv`, `md`, `xml`, `yaml`, `ini`) · **XLSX** · **DOCX** · **Images** (PNG, JPG, WEBP, GIF, BMP) · **PDF**

The cleaned file comes back in the same format. For XLSX and DOCX the document structure is preserved; redaction happens on the text content (and, for DOCX, in headers, footers, comments and notes).

For **images** and **PDF** the flow is visual: MaskFew runs OCR **in your browser** to read the text, then draws redaction boxes over the sensitive regions for you to review. You can add boxes by hand (drag), remove any box (click it), switch between solid black and blur, and rotate. PDFs add page-by-page navigation and are scanned across **all** pages. Images export as PNG (EXIF metadata stripped); PDFs are rebuilt into a fresh, rasterized PDF (original metadata dropped). Note: a rasterized PDF loses its selectable text layer — that's deliberate, so nothing hidden in the text layer can slip through.

**Not covered (deliberately):** free-text **names** — automatic name detection in prose is too false-positive-prone, so for name-heavy documents (legal letters, contracts) use the manual box tool. Also out of scope: **IPv6**, and address detection is **heuristic** (it catches typical Italian street lines, not every format).

## How to use

1. Open the page (or host your own copy — see below).
2. Drop in a file. It's read and scanned locally; you get a summary of what was found.
3. Choose which categories to remove and a replacement mode.
4. Click **Anonymize & download** — the cleaned file downloads in the same format.

Once the page has loaded, everything works offline.

For **images and PDFs** the steps are visual instead: click **Scan** to OCR the file and auto-box the sensitive regions, review them (drag to add a box, click a box to remove it), pick **box** or **blur**, then **Anonymize & download**. For PDFs, page through with ◀ ▶ before exporting.

## Important

MaskFew is **best-effort**, never a guarantee. Deterministic detection reliably catches structured identifiers, but it does **not** catch everything — free-text names are not covered, address detection is heuristic, OCR on images and PDFs depends on scan quality, and complex DOCX/XLSX (charts, macros, text split mid-word across runs) may slip through. It **reduces** risk; it does not certify a file as clean. The visual review for images and PDFs — checking, adding and removing boxes by hand — is part of the intended workflow, not a fallback. **Always review the summary (or the boxes), and the file, before sharing it.**

## Run it yourself

It's one file. Either open `index.html` locally, or host it for free:

1. Create a repository named `MaskFew`.
2. Add `index.html` to the repository root.
3. Enable **GitHub Pages** (deploy from branch `main`, folder `/root`).

Works in any modern browser. No build, no dependencies to install.

## Tech

Vanilla HTML / CSS / JavaScript, no framework, no build.
Spreadsheet read/write: [SheetJS](https://sheetjs.com/). DOCX (zip) handling: [JSZip](https://stuk.github.io/jszip/). OCR: [Tesseract.js](https://tesseract.projectnaptha.com/). PDF rendering: [pdf.js](https://mozilla.github.io/pdf.js/); PDF assembly: [pdf-lib](https://pdf-lib.js.org/). All loaded from CDN, on demand (OCR and PDF tools only when you open an image or PDF); your data never touches them remotely.

## The Few toolkit

- [**FirstFew**](https://silvestroparisi.github.io/FirstFew/) — prioritize the few that matter
- [**FixFew**](https://silvestroparisi.github.io/FixFew/) — verify and remediate
- **MaskFew** — anonymize a file before you share it
- [**ScrubFew**](https://silvestroparisi.github.io/ScrubFew/) — strip hidden metadata before you share
- [**AskFew**](https://silvestroparisi.github.io/AskFew/) — a private AI that runs in your browser

## License

[MIT](LICENSE) © 2026 Silvestro Parisi

# fileshift

> Convert anything to anything. Read everything. 100% free, 100% yours.

**PDF → EPUB. DOCX → PDF. MP4 → MP3. HEIC → PNG. 200+ format combinations.**  
Your GitHub Actions runner is the backend. Your browser is the frontend. Nothing goes to any third-party server.

---

## How it works

```
You drop a file
    → Frontend base64-encodes it
    → Calls GitHub API to trigger a workflow_dispatch
    → GitHub Actions runner installs Pandoc, LibreOffice, FFmpeg, Calibre
    → Converts the file
    → Uploads result as an artifact
    → Frontend polls, downloads the artifact ZIP, extracts the file
    → You download it or save it to your in-browser library
```

**Cost: $0.** GitHub Actions gives you 2,000 free minutes/month on private repos, unlimited on public repos. Each conversion takes ~30–90 seconds of runner time.

---

## Setup (5 minutes)

### 1. Fork / create the repo

Create a new GitHub repo (can be private). It just needs one file:

```
.github/
  workflows/
    convert.yml    ← the backend
app/
  index.html       ← the frontend (deploy this with GitHub Pages)
```

### 2. Enable GitHub Pages

Go to your repo → **Settings → Pages → Source: Deploy from branch → `main` → `/app`**

Your site will be at `https://yourusername.github.io/fileshift/`

### 3. Create a Personal Access Token

Go to **GitHub → Settings → Developer settings → Personal access tokens → Fine-grained tokens**

Grant it:
- **Repository permissions:** `Actions` (read & write), `Contents` (read)

Copy the token — you'll only see it once.

### 4. Configure the app

Open your GitHub Pages URL, click **⚙ Configure** in the top right, and enter:
- Your token: `ghp_xxxxxxxxxxxx`
- Your repo: `yourusername/fileshift`

These are stored in your browser's `localStorage` only — never sent anywhere except directly to `api.github.com`.

---

## Supported conversions

### Documents
| From | To |
|------|----|
| PDF  | EPUB, DOCX, TXT, HTML, Markdown |
| DOCX / DOC | PDF, EPUB, ODT, HTML, Markdown, TXT |
| EPUB | PDF, DOCX, MOBI, TXT |
| Markdown | PDF, DOCX, HTML, EPUB |
| HTML | PDF, DOCX, Markdown |
| ODT, RTF | PDF, DOCX, HTML |
| PowerPoint (PPTX) | PDF |
| Excel (XLSX) | CSV, ODS, PDF |

### Images
PNG, JPEG, WebP, GIF, BMP, TIFF, ICO, SVG, HEIC, AVIF → any of the above

### Audio
MP3, WAV, OGG, FLAC, AAC, M4A, OPUS, WMA, AIFF → any of the above (via FFmpeg)

### Video
MP4, MOV, AVI, MKV, WebM → MP4, WebM, GIF, MP3 (extract audio)

### Data
JSON, CSV, TSV, XML, YAML → any of the above

---

## File size limits

GitHub Actions artifacts have a **500MB limit per artifact**. For the base64 encoding step (in the API call body), there's a soft limit of around **50–70MB** before the GitHub API starts rejecting the payload.

For larger files, a future improvement would be to upload to a temp storage (e.g., a GitHub Release asset) and have the action download it from there.

---

## Reader

Files saved to your library open in a built-in reader:

| Format | Reader |
|--------|--------|
| PDF | Page-by-page canvas render via pdf.js |
| EPUB | Chapter navigation, adjustable font size |
| DOCX | Rendered via mammoth.js |
| Markdown | Rendered via marked.js |
| TXT / HTML | Scrollable text view |
| Images | Zoomable image viewer |

All library files are stored in **IndexedDB** — they persist across browser sessions with no size limits beyond your local storage.

---

## Privacy

- Your files are sent to **GitHub's API** (same as pushing code to GitHub)
- Artifacts are deleted after **1 day** (configured in the workflow)
- Your token is stored in `localStorage` and only ever sent to `api.github.com`
- No analytics, no tracking, no third parties

---

## Ideas for what to add next

See the **Ideas** tab in the app for a full list. Top picks:

- **OCR** — Tesseract in the action, scanned PDF → searchable PDF
- **Video → GIF** — already in the workflow, just add the UI button
- **Highlights & annotations** — save to IndexedDB keyed by file hash
- **Text-to-speech** — Web Speech API, zero dependencies
- **Batch convert** — queue multiple files, download as ZIP
- **Metadata editor** — ExifTool is available in the action runner

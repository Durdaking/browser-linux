# 🐧 Browser Linux

Run Alpine Linux entirely in your browser via WebAssembly — embeddable in Google Apps Script as an iframe. Includes a **Download Data** button that snapshots the entire VM state so you can restore it later.

No server, no Docker, no installs. Just a browser.

---

## How It Works

| Layer | Technology |
|-------|-----------|
| CPU emulation | [v86](https://github.com/copy/v86) (x86 → WebAssembly) |
| OS | Alpine Linux (≈50 MB ISO) |
| Hosting | GitHub Pages (static, free) |
| Embed | Google Apps Script iframe |
| Data export | `emulator.save_state()` → `.bin` snapshot |

---

## Quick Start

### Step 1 — Fork or clone this repo

```bash
git clone https://github.com/YOUR_USERNAME/browser-linux.git
cd browser-linux
```

### Step 2 — Set up Git LFS (for large files)

```bash
git lfs install
```

> Git LFS is required for the `.iso`, `.wasm`, and `.bin` files. GitHub gives you 1 GB free.

### Step 3 — Download v86 library files

1. Go to https://github.com/copy/v86/releases/latest
2. Download the `.zip` source asset and extract it
3. Copy these files into your repo:

```
v86/libv86.js        ← from build/libv86.js
v86/v86.wasm         ← from build/v86.wasm
bios/seabios.bin     ← from bios/seabios.bin
bios/vgabios.bin     ← from bios/vgabios.bin
```

### Step 4 — Download Alpine Linux ISO

1. Go to https://alpinelinux.org/downloads/
2. Choose **Virtual → x86_64** (smallest, ~50 MB)
3. Rename the file to `alpine.iso` and place it at `images/alpine.iso`

### Step 5 — Commit and push

```bash
git add .
git commit -m "Add v86 library, BIOS, and Alpine Linux ISO"
git push origin main
```

### Step 6 — Enable GitHub Pages

1. Go to your repo on GitHub → **Settings → Pages**
2. Source: **Deploy from a branch**
3. Branch: `main` / root `/`
4. Click **Save**

Your site will be live at:
```
https://YOUR_USERNAME.github.io/browser-linux/
```

Wait ~60 seconds, then open that URL — Linux should boot in your browser!

---

## Google Apps Script Setup

### Step 1 — Create a new Apps Script project

1. Go to https://script.google.com
2. Click **New project**
3. Rename it to "Browser Linux"

### Step 2 — Add the files

**Code.gs** (replaces the default `Code.gs`):
- Copy the contents of `google-apps-script/Code.gs`
- Set `GITHUB_PAGES_URL` to your GitHub Pages URL

**Page.html** (new file):
- Click **File → New → HTML file**
- Name it exactly `Page` (no extension)
- Copy the contents of `google-apps-script/Page.html`

### Step 3 — Deploy as a web app

1. Click **Deploy → New deployment**
2. Type: **Web app**
3. Execute as: **Me**
4. Who has access: **Anyone** (or restrict as needed)
5. Click **Deploy** → copy the web app URL

Open the URL — you'll see the Linux emulator embedded in your Google Apps Script app!

---

## Using the Download Button

Click **⬇ Download Data** any time after boot. This saves a full VM state snapshot (`.bin` file) to your computer.

To **restore** from a snapshot, pass it as `initial_state` in `index.html`:

```javascript
emulator = new V86({
  // ... other options ...
  initial_state: { url: "your-snapshot.bin" },
  autostart: true,
});
```

Rename your `.bin` file, place it in the repo, and update the URL above.

---

## Tips

- **Login**: Alpine Linux boots to a `localhost login:` prompt. Username: `root` (no password by default)
- **Install packages**: `apk add nano git curl python3`
- **Persistence**: The ISO boots fresh each time. For persistent storage, create a disk image (`.img`) and use `hda` instead of `cdrom` in `index.html`
- **Memory**: Edit `memory_size` in `index.html` to give Linux more RAM (max depends on browser)
- **Slow first load**: The ISO is ~50 MB — subsequent loads are cached by the browser

---

## Folder Structure

```
browser-linux/
├── index.html              # Main emulator page (hosted on GitHub Pages)
├── .gitattributes          # Git LFS config for large files
├── README.md
├── bios/
│   ├── seabios.bin         # ← download (see Step 3)
│   └── vgabios.bin         # ← download (see Step 3)
├── images/
│   └── alpine.iso          # ← download (see Step 4)
├── v86/
│   ├── libv86.js           # ← download (see Step 3)
│   └── v86.wasm            # ← download (see Step 3)
└── google-apps-script/
    ├── Code.gs             # Apps Script backend
    └── Page.html           # Apps Script HTML template
```

---

## License

The code in this repo is MIT licensed.  
v86 is licensed under BSD 2-Clause — see https://github.com/copy/v86/blob/master/LICENSE  
Alpine Linux is MIT/GPL — see https://alpinelinux.org
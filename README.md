# Menampilkan Website dari Repo ini (GitHub Pages / Deploy)

README ini menjelaskan langkah-langkah umum supaya website di repo ini tampil (GitHub Pages atau deploy statis). Pilih bagian yang sesuai dengan stack/project Anda (HTML plain, Create React App, Vue CLI, Next.js, atau Static Site Generator).

---

## ⚙️ Prasyarat
- Akun GitHub dan repository yang sudah ada.
- Node.js + npm/yarn (jika menggunakan bundler seperti React/Vue/Next).
- Akses untuk mengubah Settings → Pages di repo (atau ability untuk push branch `gh-pages`).

---

## 1) Jika project adalah plain HTML/CSS/JS
1. Letakkan file `index.html` dan aset (css/js/img) di root repository, atau di folder `docs/`.
2. Di GitHub: Settings → Pages → Source:
   - Pilih branch `main` (atau `master`) dan folder `/ (root)` atau `/docs`.
3. Save → GitHub akan memberikan URL: `https://<username>.github.io/<repo>/` atau jika pakai root repo khusus user/organization bisa `https://<username>.github.io`.

Tips: Jika pakai folder `docs/`, pastikan `index.html` berada di sana.

---

## 2) Jika project dibuat dengan Create React App
1. Tambahkan `homepage` ke `package.json`:
   {
     "homepage": "https://<username>.github.io/<repo>"
   }
2. Install paket `gh-pages`:
   ```bash
   npm install --save-dev gh-pages
   ```
3. Tambahkan scripts di `package.json`:
   ```json
   "scripts": {
     "predeploy": "npm run build",
     "deploy": "gh-pages -d build"
   }
   ```
4. Deploy:
   ```bash
   npm run deploy
   ```
5. Di GitHub → Settings → Pages: biasanya akan otomatis menggunakan branch `gh-pages` yang dibuat oleh paket.

---

## 3) Jika menggunakan Vue CLI
1. Buat/ubah `vue.config.js`:
   ```js
   module.exports = {
     publicPath: process.env.NODE_ENV === 'production'
       ? '/<repo>/'
       : '/'
   }
   ```
2. Build & deploy dengan `gh-pages` atau action (sama pola seperti CRA).

---

## 4) Jika menggunakan Next.js (static export)
1. Untuk GitHub Pages lebih mudah menggunakan `next export`:
   - Set `next.config.js` jika repo bukan root:
     ```js
     module.exports = {
       basePath: '/<repo>',
       assetPrefix: '/<repo>/'
     }
     ```
2. Build + export:
   ```bash
   npm run build
   npm run export
   ```
   Hasil `out/` bisa diletakkan ke `docs/` atau branch `gh-pages` dan di-push.

Untuk Next.js seringkali lebih gampang deploy ke Vercel (otomatis).

---

## 5) GitHub Actions: contoh workflow sederhana (deploy ke gh-pages)
Contoh menggunakan peaceiris/actions-gh-pages (bisa ditaruh di `.github/workflows/deploy.yml`):
```yaml
name: Deploy to GitHub Pages
on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: 18
      - name: Install and build
        run: |
          npm install
          npm run build
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./build  # ganti sesuai folder hasil build
```

---

## 6) Preview lokal cepat
- Untuk folder statis (`build`, `out`, atau `docs`):
  ```bash
  npx http-server ./build -p 5000
  ```
- Atau:
  ```bash
  npx serve build
  ```

---

## 7) Masalah umum & solusi cepat
- 404 setelah deploy: Pastikan `homepage` / `publicPath` / `basePath` sesuai `/<repo>/`.
- Aset (CSS/JS) tidak ditemukan: Periksa path relatif vs absolut dan konfigurasi assetPrefix/publicPath.
- HTTPS / CNAME: Jika pakai custom domain, tambahkan file `CNAME` di root dan periksa DNS.

---

## Checklist singkat untuk tampilkan website
1. Tentukan target deploy: GitHub Pages (root/docs/gh-pages) atau hosting lain (Vercel/Netlify).
2. Konfig `homepage`/`publicPath`/`basePath` bila repo bukan root (`<username>.github.io/<repo>`).
3. Build project (npm run build / npm run export).
4. Pindahkan hasil build ke `docs/` atau branch `gh-pages` atau pakai GitHub Action otomatis.
5. Buka Settings → Pages untuk memastikan source benar dan cek URL yang diberikan.

---

Butuh bantuan lebih lanjut?
- Beri tahu saya: framework yang Anda pakai (plain HTML, CRA, Vue, Next, Hugo, dsb.) dan nama repo atau username GitHub Anda. Saya bisa buatkan file konfigurasi deploy (contoh `package.json` changes, `vue.config.js`, `next.config.js`), atau langsung buatkan workflow GitHub Actions yang siap pakai.

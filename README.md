# 🌸 bloom

> A cute aesthetic task manager to beat procrastination — with voice input & real-time sync across devices.

---

## ✨ what it does

bloom is a lightweight personal productivity app that lives on your phone and laptop at the same time. no accounts, no subscriptions, just a pretty little app that keeps you on top of things without stressing you out.

- ✅ **add tasks fast** — type or just tap the mic and speak it
- 🎤 **voice input** — say your task out loud, hands-free
- 🏷️ **categories** — email, reply, post, errand, reminder
- 🌸 **mood tracker** — set your vibe for the day
- 📊 **progress bar** — see your daily bloom grow as you tick things off
- 🔥 **streak counter** — shows up every day to keep your streak alive
- 💾 **real-time sync** — tasks sync across all your devices via Google Sheets
- 🎉 **confetti** — little burst of joy every time you check something off

---

## 🛠️ how it's built

| thing | what's used |
|---|---|
| frontend | vanilla HTML + CSS + JS |
| storage & sync | Google Sheets (via Apps Script) |
| voice input | Web Speech API |
| hosting | GitHub Pages |
| fonts | Nunito + Quicksand (Google Fonts) |

no frameworks, no dependencies, no build step. just one `index.html` file.

---

## 🚀 setup (if you want your own version)

### 1. google sheets backend

- create a new Google Sheet with these headers in row 1:
  ```
  id | text | cat | done | ts
  ```
- go to **Extensions → Apps Script** and paste the backend script (see `apps-script.js` below)
- deploy as a **Web App** (execute as: me, access: anyone)
- copy the Web App URL

### 2. update the api url

in `index.html`, find this line near the bottom:
```js
const API = 'your-web-app-url-here';
```
replace it with your own Apps Script URL.

### 3. host on github pages

- push `index.html` to a public GitHub repo
- go to **Settings → Pages → Deploy from branch (main)**
- your app will be live at `https://yourusername.github.io/your-repo-name`

### 4. add to devices

**android:** open the link in Chrome → tap 3 dots → Add to Home Screen

**windows:** open in Chrome → 3 dots → More tools → Create shortcut → tick "Open as window"

---

## 📋 apps-script.js

paste this into your Google Apps Script editor:

```javascript
function doGet(e) {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  const rows = sheet.getDataRange().getValues();
  const headers = rows[0];
  const tasks = rows.slice(1).map(row => {
    const obj = {};
    headers.forEach((h, i) => obj[h] = row[i]);
    return obj;
  });
  return ContentService
    .createTextOutput(JSON.stringify({ tasks }))
    .setMimeType(ContentService.MimeType.JSON);
}

function doPost(e) {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  const data = JSON.parse(e.postData.contents);

  if (data.action === 'save') {
    const rows = sheet.getDataRange().getValues();
    const headers = rows[0];
    const existing = rows.slice(1).findIndex(r => r[0] == data.task.id);
    const rowData = headers.map(h => data.task[h] ?? '');
    if (existing >= 0) {
      sheet.getRange(existing + 2, 1, 1, rowData.length).setValues([rowData]);
    } else {
      sheet.appendRow(rowData);
    }
  }

  if (data.action === 'delete') {
    const rows = sheet.getDataRange().getValues();
    const idx = rows.findIndex(r => r[0] == data.id);
    if (idx > 0) sheet.deleteRow(idx + 1);
  }

  return ContentService
    .createTextOutput(JSON.stringify({ ok: true }))
    .setMimeType(ContentService.MimeType.JSON);
}
```

---

## 📱 screenshots

coming soon 🌸

---

*made with love and a desperate need to stop procrastinating* 💕

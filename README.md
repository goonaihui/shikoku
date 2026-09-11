# Shared trip itinerary

A small mobile-first web app for our family trip. Both of us can edit it from our phones, attach screenshots (booking confirmations, tickets), and add links to Google Drive files. There is no server: the page runs on GitHub Pages and saves everything to a private GitHub repo.

**What's public and what isn't:** the public site is only an empty app with a login screen. `index.html` contains no trip information. The itinerary and screenshots live in a private repo and can only be loaded with a GitHub token. On top of that, each phone can set a number PIN that locks the app when it's opened.

## How it's set up

The app and the data live in two separate repos:

| Repo | Visibility | Contains |
|---|---|---|
| `trip` | **Public** (GitHub Pages needs this on a free account) | `index.html`, `sw.js`, this README. No trip data. |
| `trip-data` | **Private** | `data.json` (the itinerary) and `attachments/` (screenshots, created by the app). |

Repo names are up to you. Neutral names like these are a good idea because the public repo name appears in the site address.

Keeping the data private matters: the itinerary shows when the family is away from home, and booking screenshots usually contain names, phone numbers, and reservation codes.

## Setup (about 10 minutes, done once)

### 1. Create the private data repo and upload the itinerary

1. On GitHub, **New repository** → name it `trip-data`.
2. Choose **Private**.
3. Tick **Add a README file**.
4. **Create repository**.
5. **Add file → Upload files** → upload `data.json` → **Commit changes**.

Only upload `data.json` once. After that the app keeps it updated, and uploading it again would overwrite your edits.

### 2. Create the public app repo and turn on GitHub Pages

1. **New repository** → name it `trip` → **Public** → create.
2. **Add file → Upload files** → upload `index.html`, `sw.js`, and `README.md` → **Commit changes**.
3. **Settings → Pages** → under *Build and deployment*, choose **Deploy from a branch**, branch `main`, folder `/ (root)` → **Save**.
4. After a minute the site is at `https://<your-username>.github.io/trip/`.

Don't upload `data.json` to this repo.

### 3. Create your access token

A token is like a password that lets the app read and save the itinerary.

1. github.com → profile picture → **Settings → Developer settings → Personal access tokens → Fine-grained tokens → Generate new token**.
2. Name: `trip`. Expiration: **Custom** → about a month after you get back.
3. Repository access: **Only select repositories** → `trip-data`.
4. Permissions → Repository permissions → **Contents: Read and write**.
5. **Generate token** and copy it (starts with `github_pat_`).

Use a **fine-grained** token like this, not a classic one. It can only touch `trip-data`, and the app refuses to put classic tokens into invite links.

### 4. Log in and set a PIN (you)

1. Open the site on your phone.
2. Tap **用 GitHub token 登入（設定的人用）**, then fill in your name (e.g. 姊姊), your GitHub username, `trip-data`, and the token.
3. Tap **用 token 登入**.
4. Set a **解鎖密碼** (4–8 digits), or skip it for now.

### 5. Invite your sister

Your sister doesn't need a GitHub account.

1. In the app: gear icon → **邀請家人** → check the name → **產生邀請連結**.
2. Tap **傳送邀請** and pick LINE (or **複製邀請連結** and paste it into a chat). **Send it in a private chat only**: anyone with the link can view and edit the itinerary.
3. She taps the link, checks her name, taps **開始使用**, and sets her own PIN. That's it.

**If the link opens inside LINE:** the page suggests opening it in Safari or Chrome instead (LINE's ⋯ menu → open in browser). It still works inside LINE, but the browser is better for everyday use.

**Optional: a separate token for her.** By default the link contains your token. If you'd like to be able to cut off her access without affecting yours, create a second fine-grained token the same way (step 3) and paste it under **另外給她一個 token** before generating the link.

**If the link ever leaks:** on GitHub, delete the token (Settings → Developer settings → Personal access tokens), make a new one, log in again, and send a new link.

### 6. Add it to the home screen

- **Android (Chrome):** ⋮ menu → **加到主畫面**. You stay logged in.
- **iPhone (Safari):** Share button → **加入主畫面**. On iPhone, the home screen version keeps its own separate storage, so the first time you open it from the home screen it asks you to log in again. Copy the invite link from the chat and tap **貼上邀請連結**. For yourself, generate an invite link with your own name and use it the same way.

Once she's set up on her home screen, you can both delete (收回) the invite message.

## Using it

- **Switch days:** tap the day chips, the arrows on the blue sign, or swipe left/right.
- **Edit the place name or tonight's hotel:** tap the sign or the hotel row.
- **Add, edit, or delete a stop:** tap **新增**, or tap any stop.
- **Bookings:** set 預約 to 需預約 and the stop shows up in the **待預約** tab. Tap **已訂好** once it's booked.
- **Screenshots:** in the editor, tap **加入截圖**. Images are shrunk before upload (text stays readable) and stored in `attachments/` in the data repo.
- **Google Drive files:** add them under 連結. For PDFs and other files, Drive links work better than screenshots. Set Drive sharing so your sister's Google account can open them.

## The PIN, honestly

- The PIN is saved separately on each phone (and separately for the iPhone home screen version). Your sister's PIN and yours are independent.
- It stops someone who picks up an unlocked phone from opening the itinerary. It is not what keeps strangers out; that's the private repo and the token.
- After 5 wrong tries it makes you wait 30 seconds, and the wait doubles after every further 5.
- **Forgot it?** Tap 忘記密碼 on the lock screen. That logs this phone out; log back in with an invite link (or your token). The itinerary itself isn't affected.

## How syncing works

- Every save re-reads the latest `data.json` from GitHub, applies your change on top, and saves again. If you and your sister edit **different stops** at the same moment, both changes are kept. If you both edit **the same stop**, the later save wins for that stop.
- The app checks for your sister's changes when you open it, when you come back to the tab, and about once a minute while it's open. The sync pill in the top bar also refreshes when tapped.
- **No signal?** The app still opens (it keeps a copy of the page and the last synced itinerary on the phone, still behind the PIN). Edits made offline show as 「N 項未儲存」 and upload automatically when you're back online. Don't close the page until they're saved. Screenshots need a connection to upload and to load.
- **History:** every change is a commit in `trip-data`, labelled with who made it (設定 → 在 GitHub 看修改紀錄). To undo a mistake, you can restore an older version of `data.json` from there.
- **Backup:** 設定 → 下載備份（JSON）.

## Editing data.json by hand

You can also edit `data.json` directly on GitHub. The structure:

```json
{
  "title": "家族旅行",
  "days": [
    {
      "id": "d1",
      "date": "2025-01-01",
      "area": "市區",
      "hotel": { "name": "飯店名稱", "url": "" },
      "items": [
        {
          "id": "i001", "order": 1,
          "time": "06:40", "end": "",
          "title": "到機場",
          "type": "transport",
          "booking": "",
          "notes": "",
          "links": [{ "label": "", "url": "https://..." }],
          "files": [{ "path": "attachments/d1/xxxx.jpg", "name": "IMG_0001.png" }]
        }
      ]
    }
  ]
}
```

- `type`: `transport`, `sight`, `meal`, `stay`, `other`
- `booking`: `""` (not needed), `needed`, `done`
- `time` / `end`: `HH:MM`, or `""` for 時間未定
- To add or remove whole days, edit the `days` list here. The app doesn't have a button for that.

## Troubleshooting

| Message | Fix |
|---|---|
| Token 無效或已過期 | Create a new token (step 3) and paste it in Settings → 更換 Token. |
| 找不到 repo | Check the GitHub username and repo name, and that the token was created with `trip-data` selected. |
| 這個邀請已經失效 | The token in the link was deleted or expired. Create a token, then send a new invite link. |
| 這個 token 沒有 repo 的寫入權限 | The fine-grained token needs **Contents: Read and write** on `trip-data`. |
| 已連上…但裡面沒有 data.json | Upload `data.json` to the private data repo (step 1). |
| 找不到分支 | The data repo is empty. Add a README to it (step 1). |
| Page shows old version after updating `index.html` | Fully close the app (swipe it away) and open it again. |

## Privacy notes

- Anyone who finds the site address sees only the login screen. Without a token that can read `trip-data`, the page can't load anything.
- Tokens stay on each phone's browser, and the invite link is removed from the address bar as soon as it's opened. The link itself still sits in the chat, which is why it should only go in a private chat and be deleted afterwards.
- On a shared or borrowed device, use **在這台裝置登出** in Settings when you're done.
- If you uploaded an earlier version of `index.html` that had the itinerary built in, that old version is still in the public repo's history. To remove it completely, delete the public repo (**Settings → Danger Zone → Delete this repository**), create it again, and upload the new files.

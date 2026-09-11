# Shared trip itinerary

A small mobile-first web app for our family trip. Both of us can edit it from our phones, attach screenshots (booking confirmations, tickets), and add links to Google Drive files. There is no server: the page runs on GitHub Pages and saves everything to a private GitHub repo.

**What's public and what isn't:** the public site is only an empty app with a login screen. `index.html` contains no trip information. The itinerary and screenshots live in a private repo and can only be loaded with a GitHub token. Once a phone is logged in, it stays logged in: next time, the app opens straight to the itinerary.

## How it's set up

The app and the data live in two separate repos:

| Repo | Visibility | Contains |
|---|---|---|
| `trip` | **Public** (GitHub Pages needs this on a free account) | `index.html`, `sw.js`, this README. No trip data. |
| `trip-data` | **Private** | `data.json` (itinerary and costs) and `attachments/` (photos, PDFs, receipts and other files, created by the app). |

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

### 4. Log in (you)

1. Open the site on your phone.
2. Tap **用 GitHub token 登入（設定的人用）**, then fill in your name (e.g. 姊姊), your GitHub username, `trip-data`, and the token.
3. Tap **用 token 登入**.

### 5. Invite your sister

Your sister doesn't need a GitHub account.

1. In the app: gear icon → **邀請家人** → keep **家人** selected, check the name → **產生邀請連結**.
2. Tap **傳送邀請** and pick LINE (or **複製邀請連結** and paste it into a chat). **Send it in a private chat only**: anyone with the link can view and edit the itinerary.
3. She taps the link, checks her name, and taps **開始使用**. That's it: from then on the app opens straight to the itinerary on that phone.

**If the link opens inside LINE:** the page suggests opening it in Safari or Chrome instead (LINE's ⋯ menu → open in browser). It still works inside LINE, but the browser is better for everyday use.

**Optional: a separate token for her.** By default the link contains your token. If you'd like to be able to cut off her access without affecting yours, create a second fine-grained token the same way (step 3) and paste it under **另外給她一個 token** before generating the link.

**If the link ever leaks:** on GitHub, delete the token (Settings → Developer settings → Personal access tokens), make a new one, log in again, and send a new link.

### 6. Add it to the home screen

- **Android (Chrome):** ⋮ menu → **加到主畫面**. You stay logged in.
- **iPhone (Safari):** Share button → **加入主畫面**. On iPhone, the home screen version keeps its own separate storage, so the first time you open it from the home screen it asks you to log in again. Copy the invite link from the chat and tap **貼上邀請連結**. For yourself, generate an invite link with your own name and use it the same way.

Once she's set up on her home screen, you can both delete (收回) the invite message.

## Using it

- **Switch days:** tap the day chips, the arrows on the blue sign, or swipe left/right.
- **Edit the place name or tonight's hotel:** tap the sign or the hotel name. The hotel can have an address or map link (used for 路線 and travel estimates), any number of links (booking confirmation, hotel site), and attachments (booking screenshots, receipts). If you stay several nights in a row at the same hotel, tick 「這 N 晚一起更新」 to update all of them at once.
- **Add, edit, or delete a stop:** tap **新增**, or tap any stop.
- **To-dos:** every stop has a 狀態: 無, 待處理, 待確認, or 已完成.
  - **待處理** is for things to do, like booking, buying, or reserving. They're listed in the **待處理** tab; tap **完成** when done.
  - **待確認** is for things not decided yet, like whether to buy a pass. They're listed in the **待確認** tab; tap **確認了** once decided.
  - Each tab keeps its finished items at the bottom. A dot on the date chips shows which days still have open items (orange for 待處理, purple for 待確認).
- **Attachments:** in the editor, tap **加入附件** and pick as many files as you like at once: screenshots, photos, PDF tickets, or anything else, up to 20 MB each. Photos are shrunk before upload (text stays readable). Tap an attachment to open it: images and PDFs open inside the app, other files can be downloaded. Everything is stored in `attachments/` in the data repo.
- **Google Drive files:** for anything bigger than 20 MB, add a Drive link under 連結. Set Drive sharing so your sister's Google account can open it.
- **Places:** fill in **地點** on a stop (a place name, an address, or a pasted Google Maps link). The stop then shows a place button that opens Google Maps and a **路線** button with public-transport directions from where you are. The hotel row has a **路線** button too, for getting back.
- **Transport stops (交通):** set the type to 交通 and the place field splits into **出發地** and **抵達地**. Tap **從標題帶入出發地和抵達地** to fill them from a title like 「客運 甲地 → 乙地」 (words like 客運 or 渡輪 are dropped). The stop then shows both places and a **路線** button for that leg, and the travel suggestions use the departure place for getting there and the arrival place for what comes next.
- **Getting between stops:** when two stops in a row both have a 地點, a small row appears between them suggesting how to get there, e.g. 「步行約 12 分鐘」 or 「電車或巴士，粗估 35 分鐘，計程車約 15 分鐘」, with a **路線** button that opens Google Maps for that exact leg. Each day also starts with a suggestion from last night's hotel and ends with one back to tonight's hotel. If the time you left between two stops looks too short, the row shows an orange warning.
  - The times are rough estimates based on distance, not real timetables. Use **路線** for actual trains and buses.
  - To estimate distances, the app looks up each place name once on OpenStreetMap and remembers it on the phone. It understands Traditional Chinese spellings like 道後溫泉. If a place can't be found, write it more fully (add the station or an address). For hotels, pasting a desktop Google Maps link (one containing @latitude,longitude) into the hotel's 連結 field lets it estimate the trip back.
  - You can turn the estimates off in Settings. Only the place names are sent, never dates or other trip details.
- **地圖 tab:** shows your Google My Maps map. The first time, the admin taps **加入地圖** and pastes the map's share link (in My Maps, set sharing to "anyone with the link can view"). Below the map is the list of that day's places, each with **地圖** and **路線**.

## 行前注意事項 (before the trip)

- A shared checklist of things to prepare. Tap the square to tick an item off; the progress bar shows how much is done.
- The first time, tap **加入建議清單** for a common list for a trip from Taiwan to Japan (passport, Visit Japan Web, eSIM, cash, medicine, power bank rules, and so on), then add, edit, or delete items with **新增** or by tapping an item.
- Each item has a category, an optional note (links in it are tappable), and optionally who's responsible.
- Everyone can edit this list, including members.

## Admin and members

- **Admin** (you): a device logged in with the GitHub token, or with an invite link made for **我自己的其他裝置**. Only admin devices see the gear icon, which holds inviting, account settings, the map link, the costs setup, backups, and logout.
- **Members** (your sister): devices that joined with a normal **家人** invite link. They can view and edit the itinerary, attachments, to-dos and costs, but have no gear icon.
- **Your own other devices:** in 邀請家人, choose **我自己的其他裝置** before generating the link. That link gives admin access, so only send it to yourself.
- **Getting admin back on a device:** open the site address with `#admin` at the end (for example `https://<your-username>.github.io/trip/#admin`) and paste your GitHub token. Devices that were logged in before this update start as members, so do this once on your own phone.
- This hides settings from members; it isn't a security wall. Anyone with an invite link can still edit the trip data, which is why links go in private chats only.

## Costs (花費)

- **Set up once (admin):** open the **花費** tab → **開始記帳**. The list starts with 爸、媽、培、菲; edit it if needed, pick the currency to settle in, and set the exchange rate (1 日圓 = ? 台幣). Use the rate you actually exchanged at if you like.
- **Cost of a stop:** open any stop in the itinerary and fill in **花費**: the amount (日圓 or 台幣), who paid, and, under 怎麼分, how to split it (everyone shares equally by default). The stop then shows e.g. 「妹妹付 ¥1,000」, and the cost is added to the **花費** tab and the final settlement. Clear the amount to remove it; deleting the stop removes its cost too.
- **Other costs:** in the **花費** tab, tap **記一筆**. Enter what it was, the amount in 日圓 or 台幣, who paid, and how to split it. Everyone gets 1 share by default. Change the shares to split unevenly: for example, if 姊姊 covers the parents' portion, give 姊姊 more shares; 0 shares means that person isn't part of it. You can attach receipt photos.
- **See who owes whom:** the top card shows the total, what each person paid and owes, and the fewest payments that settle everything, e.g. 「妹妹 給 姊姊 NT$1,160」.
- **Settle up:** when someone pays the other back, tap **記錄已給** next to that line (or **記錄還款**). The balances update right away.
- Costs paid before the trip (flights, hotels) go under **出發前／行程外**.
- **Who's using this phone:** if a phone's name doesn't match anyone in the list, the 花費 tab asks 「這台手機是誰在用？」. Picking a name makes that person the default payer and labels their edits.
- Several people can add costs at the same time; entries never overwrite each other.

## Staying logged in

- A phone stays logged in until the admin taps **在這台裝置登出** in Settings on that phone, or until the token expires. Set the token's expiration to after the trip so nobody gets logged out in Japan.
- On iPhone, Safari and the home screen icon count as two separate places, so each needs one login (see step 6).

## How syncing works

- Every save re-reads the latest `data.json` from GitHub, applies your change on top, and saves again. If you and your sister edit **different stops** at the same moment, both changes are kept. If you both edit **the same stop**, the later save wins for that stop.
- The app checks for your sister's changes when you open it, when you come back to the tab, and about once a minute while it's open. The sync pill in the top bar also refreshes when tapped.
- **No signal?** The app still opens (it keeps a copy of the page and the last synced itinerary on the phone). Edits made offline show as 「N 項未儲存」 and upload automatically when you're back online. Don't close the page until they're saved. Attachments need a connection to upload and to open.
- **History:** every change is a commit in `trip-data`, labelled with who made it (設定 → 在 GitHub 看修改紀錄). To undo a mistake, you can restore an older version of `data.json` from there.
- **Backup:** 設定 → 下載備份（JSON）.

## Editing data.json by hand

You can also edit `data.json` directly on GitHub. The structure:

```json
{
  "title": "家族旅行",
  "mapUrl": "",
  "prep": { "items": [{ "id": "x1", "section": "證件", "text": "護照", "detail": "", "owner": "", "done": false }] },
  "split": {
    "people": [{ "id": "p1", "name": "姊姊" }, { "id": "p2", "name": "妹妹" }],
    "currency": "TWD",
    "rate": 0.205,
    "expenses": [
      { "id": "e1", "kind": "expense", "title": "晚餐", "amount": 6000, "currency": "JPY",
        "paidBy": "p1", "shares": { "p1": 1, "p2": 1 }, "dayId": "d1", "category": "food", "note": "", "files": [] }
    ]
  },
  "days": [
    {
      "id": "d1",
      "date": "2025-01-01",
      "area": "市區",
      "hotel": { "name": "飯店名稱", "location": "", "links": [], "files": [] },
      "items": [
        {
          "id": "i001", "order": 1,
          "time": "06:40", "end": "",
          "title": "到機場",
          "type": "transport",
          "booking": "",
          "notes": "",
          "location": "",
          "locationTo": "",
          "links": [{ "label": "", "url": "https://..." }],
          "files": [{ "path": "attachments/d1/xxxx.pdf", "name": "ticket.pdf", "type": "application/pdf", "size": 182044 }]
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

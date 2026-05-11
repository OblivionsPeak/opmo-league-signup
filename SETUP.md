# Google Sheets Backend Setup

This lets all driver registrations from any device accumulate in one Google Sheet. You only export when you're ready.

## One-time setup (~5 minutes)

### 1. Create the Google Sheet

1. Go to [sheets.google.com](https://sheets.google.com) and create a new blank sheet
2. Name it something like **OpMo League Registrations**

### 2. Open Apps Script

1. In the sheet, click **Extensions → Apps Script**
2. Delete any existing code in the editor
3. Paste the following:

```javascript
function doPost(e) {
  const ss    = SpreadsheetApp.getActiveSpreadsheet();
  const sheet = ss.getActiveSheet();

  // Write header row if the sheet is empty
  if (sheet.getLastRow() === 0) {
    sheet.appendRow([
      'Season','First Name','Last Name','Driver ID',
      'Car Number','Driver Class','Car Class','Car','Submitted'
    ]);
    sheet.getRange(1,1,1,9).setFontWeight('bold')
      .setBackground('#c4a87a').setFontColor('#0d1117');
    sheet.setFrozenRows(1);
  }

  const d = e.parameter;
  sheet.appendRow([
    d.season      || '',
    d.firstName   || '',
    d.lastName    || '',
    d.driverId    || '',
    d.carNum      || '',
    d.driverClass || '',
    d.carClass    || '',
    d.car         || '',
    new Date().toLocaleString('en-US'),
  ]);

  return ContentService
    .createTextOutput(JSON.stringify({ status: 'ok' }))
    .setMimeType(ContentService.MimeType.JSON);
}
```

4. Click **Save** (disk icon), name the project **OpMo Registration**

### 3. Deploy as a Web App

1. Click **Deploy → New deployment**
2. Click the gear icon next to "Select type" → choose **Web app**
3. Set:
   - **Description**: OpMo Registration Backend
   - **Execute as**: Me
   - **Who has access**: Anyone
4. Click **Deploy**
5. Authorise the permissions when prompted (Google will warn it's an unverified app — click "Advanced → Go to OpMo Registration")
6. **Copy the Web App URL** — it looks like `https://script.google.com/macros/s/ABC.../exec`

### 4. Add the URL to the form

Open `index.html` and find this line near the top of the `<script>` block:

```javascript
const APPS_SCRIPT_URL = '';
```

Replace it with your URL:

```javascript
const APPS_SCRIPT_URL = 'https://script.google.com/macros/s/YOUR_ID_HERE/exec';
```

Commit and push. Done — every submission now goes straight into the sheet.

---

## Season management

- In the **Admin Panel** on the form page, click **Change Season** to set the season label (e.g. *2026 Season 2*). This label is saved in the browser and stamped on every registration.
- The Google Sheet accumulates all seasons in one sheet. Filter column A by season when you want to see a specific season.
- To export as Excel: in Google Sheets go to **File → Download → Microsoft Excel (.xlsx)**.
- To start a new season: just change the season label. Old entries stay in the sheet untouched.

---

## Exporting

You have two options:

| Method | When to use |
|--------|-------------|
| **Google Sheet → File → Download → Excel** | Easiest — always has every submission from all devices |
| **Admin Panel → Export → Excel** | Exports only entries recorded on your current device/browser |

The Google Sheet method is recommended for official records.

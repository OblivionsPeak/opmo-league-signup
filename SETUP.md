# Google Sheets Backend Setup

Every registration from any driver on any device flows automatically into one Google Sheet. Data persists forever, exports to Excel any time, and costs nothing.

**Requires:** A Google account (Gmail)

---

## One-time setup (~5 minutes)

### Step 1 — Create the Google Sheet

1. Go to [sheets.google.com](https://sheets.google.com) and create a new blank spreadsheet
2. Name it **OpMo League Registrations**

### Step 2 — Open Apps Script

1. In the spreadsheet, click **Extensions → Apps Script**
2. Delete any existing code in the editor
3. Paste the following script:

```javascript
function doPost(e) {
  const ss    = SpreadsheetApp.getActiveSpreadsheet();
  const sheet = ss.getActiveSheet();

  if (sheet.getLastRow() === 0) {
    sheet.appendRow([
      'Season','First Name','Last Name','Driver ID',
      'Car Number','Driver Class','Car Class','Car','Submitted'
    ]);
    sheet.getRange(1,1,1,9)
      .setFontWeight('bold')
      .setBackground('#c4a87a')
      .setFontColor('#0d1117');
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

4. Click **Save** (disk icon) and name the project **OpMo Registration**

### Step 3 — Deploy as a Web App

1. Click **Deploy → New deployment**
2. Click the gear icon next to "Select type" → choose **Web app**
3. Set:
   - **Execute as**: Me
   - **Who has access**: Anyone
4. Click **Deploy**
5. When prompted, click **Authorise access** → sign in with your Google account → click **Advanced → Go to OpMo Registration (unsafe)** → **Allow**
6. **Copy the Web App URL** — it looks like:
   `https://script.google.com/macros/s/ABC.../exec`

### Step 4 — Add the URL to the form

Open `index.html` and find this line near the top of the `<script>` block:

```javascript
const APPS_SCRIPT_URL = '';
```

Paste your URL inside the quotes:

```javascript
const APPS_SCRIPT_URL = 'https://script.google.com/macros/s/YOUR_ID_HERE/exec';
```

Commit and push — every submission now writes a permanent row to your Google Sheet.

---

## Season management

- In the **Admin Panel**, click **Change Season** to set the label (e.g. *Season 14*)
- Or update the default in `index.html`: `return localStorage.getItem(SEASON_KEY) || 'Season 14';`
- All seasons accumulate in the same sheet — filter column A to isolate a specific season
- Old entries are never deleted or overwritten

## Exporting to Excel

In Google Sheets go to **File → Download → Microsoft Excel (.xlsx)** — done.

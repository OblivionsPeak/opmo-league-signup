# Power Automate + Excel Setup Guide

Every registration from any driver on any device flows automatically into one Excel file in your OneDrive. Export or open it any time — no extra steps needed.

**Requires:** Microsoft 365 (any plan that includes OneDrive and Power Automate)

---

## One-time setup (~10 minutes)

### Step 1 — Create the Excel file in OneDrive

1. Open [onedrive.live.com](https://onedrive.live.com) (or OneDrive for Business)
2. Create a new Excel workbook — name it **OpMo League Registrations**
3. In cell **A1**, type the following headers across the row:

   | A | B | C | D | E | F | G | H | I |
   |---|---|---|---|---|---|---|---|---|
   | Season | First Name | Last Name | Driver ID | Car Number | Driver Class | Car Class | Car | Submitted |

4. Select cells **A1 through I1**, then on the **Insert** tab click **Table**
   - Check "My table has headers" → OK
5. Name the table **Registrations**:
   - Click anywhere in the table → **Table Design** tab → change "Table Name" from `Table1` to `Registrations`
6. Save the file

---

### Step 2 — Create the Power Automate flow

1. Go to [make.powerautomate.com](https://make.powerautomate.com) and sign in
2. Click **+ Create** → **Instant cloud flow**
3. Name it **OpMo Registration**, select **When a HTTP request is received** as the trigger → **Create**

---

### Step 3 — Configure the trigger

1. Click the **When a HTTP request is received** trigger card to expand it
2. In **Request Body JSON Schema**, paste the following:

```json
{
  "type": "object",
  "properties": {
    "season":      { "type": "string" },
    "firstName":   { "type": "string" },
    "lastName":    { "type": "string" },
    "driverId":    { "type": "string" },
    "carNum":      { "type": "string" },
    "driverClass": { "type": "string" },
    "carClass":    { "type": "string" },
    "car":         { "type": "string" },
    "timestamp":   { "type": "string" }
  }
}
```

> **Note:** The HTTP URL won't appear until after you save for the first time in Step 5.

---

### Step 4 — Add the Excel action

1. Click **+ New step**
2. Search for **Excel Online (Business)** → select **Add a row into a table**
3. Fill in the fields:
   - **Location**: OneDrive for Business *(or OneDrive if personal)*
   - **Document Library**: OneDrive
   - **File**: Browse to your **OpMo League Registrations.xlsx**
   - **Table**: Registrations
4. Map each column using dynamic content (click the field, then pick from the list):

   | Column | Dynamic content value |
   |--------|-----------------------|
   | Season | `season` |
   | First Name | `firstName` |
   | Last Name | `lastName` |
   | Driver ID | `driverId` |
   | Car Number | `carNum` |
   | Driver Class | `driverClass` |
   | Car Class | `carClass` |
   | Car | `car` |
   | Submitted | `timestamp` |

---

### Step 5 — Save and copy the URL

1. Click **Save** (top right)
2. Click back on the **When a HTTP request is received** trigger card
3. Copy the **HTTP POST URL** — it looks like:
   `https://prod-xx.westus.logic.azure.com:443/workflows/abc.../triggers/manual/paths/invoke?...`

---

### Step 6 — Add the URL to the form

Open `index.html` and find this line near the top of the `<script>` block:

```javascript
const POWER_AUTOMATE_URL = '';
```

Paste your URL:

```javascript
const POWER_AUTOMATE_URL = 'https://prod-xx.westus.logic.azure.com:443/workflows/...';
```

Commit and push — every submission now writes a row to your Excel file automatically.

---

## Season management

- In the **Admin Panel**, click **Change Season** to update the season label (e.g. *Season 14*)
- Or update the default in `index.html`: `return localStorage.getItem(SEASON_KEY) || 'Season 14';`
- All seasons accumulate in the same Excel table — filter column A by season name to isolate a specific season
- To start a new season just update the label — old entries are untouched

## Exporting

Open **OpMo League Registrations.xlsx** in OneDrive any time — it's already Excel. Download it, share it, or work in it directly.

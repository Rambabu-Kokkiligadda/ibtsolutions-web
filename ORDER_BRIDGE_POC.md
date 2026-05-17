# Order Bridge – POC Review Document
**IBT Solutions · Internal Tool · May 2026**

---

## What Is Order Bridge?

Order Bridge is an AI-powered automation tool that reads retailer purchase order documents (PDFs, scanned images, Excel files) and extracts all order data into a ready-to-upload OTC ingest Excel template — automatically.

**The problem it solves:** Customer service staff currently re-type purchase orders manually from PDFs sent by retailers. A single batch from one retailer (e.g. Renaud-Bray / Archambault) can contain 40+ individual store orders across 44 pages. This takes hours and is error-prone.

**With Order Bridge:** Upload the PDF → AI reads every page → Download the completed OTC Excel file. Under 2 minutes.

---

## How to Access

The tool is embedded in the IBT Solutions staff portal and protected by login.

| | |
|---|---|
| **URL** | `https://ibtsolutions.co.in/staff/order-bridge.html` |
| **Username** | `order-bridge-user` |
| **Password** | *(shared separately)* |

> Access credentials are shared separately for security. The page is not publicly listed on the website.

---

## How to Use — Step by Step

**1. Open the URL above** — your browser will prompt for username and password.

**2. Select an AI Provider**
   - **Anthropic Claude** — best accuracy, recommended for complex/scanned PDFs
   - **GCP Gemini** — fast, strong on structured documents
   - **Azure OpenAI** — enterprise option (coming soon — quota pending)

**3. Upload your document**
   - Click the upload zone or drag and drop your file
   - Supported: PDF, PNG, JPG, TIFF, XLSX/XLS
   - Max file size: 50 MB

**4. Click "Extract Orders"**
   - A progress bar shows the 5 stages: Upload → Prepare → AI Extraction → Parse → Generate
   - AI extraction typically takes 1–3 minutes depending on file size and provider

**5. Review the results**
   - Summary shows: number of Purchase Orders, Line Items, Total Qty, Total Cost
   - A confidence score (%) indicates how certain the AI is about the extraction
   - Expand individual POs to verify line items before downloading
   - Any orders the AI could not fully parse are flagged in red for manual review

**6. Download the OTC Template**
   - Click **"Download OTC Template"**
   - File is named automatically: `OTC_Orders_YYYYMMDD_HHMM_Provider_NPOs.xlsx`
   - The Excel file has two sheets: Line Items and PO Summary — ready for OTC upload

---

## Supported Document Types

| Type | Notes |
|---|---|
| Digital PDF | Best results — text is directly readable |
| Scanned PDF | AI vision reads the scan; accuracy depends on scan quality |
| Handwritten orders | Supported via AI vision |
| Images (PNG, JPG, TIFF) | Single-page scanned orders |
| Excel / CSV | Retailer-formatted order spreadsheets |

---

## Real-World Test Results

Tested with a Librairie Renaud-Bray / Groupe Archambault batch:

| | |
|---|---|
| **Document** | 44-page PDF with orders for 43 store locations |
| **Anthropic Claude** | 43 of 43 orders extracted · ~90 sec |
| **GCP Gemini** | 43 of 43 orders extracted · ~75 sec |
| **Confidence score** | 88–94% across test runs |

---

## Technical Architecture

```
ibtsolutions.co.in/staff/order-bridge.html   ← Staff web page (GoDaddy hosting)
        │  (HTTPS fetch with API token)
        ▼
order-bridge-496517.uc.r.appspot.com         ← Flask API backend (Google Cloud)
        │
        ├── Anthropic Claude API  (claude-sonnet-4-6)
        ├── Google Gemini API     (gemini-2.5-flash)
        └── Azure OpenAI          (pending quota)
```

**Security layers:**
- Staff page protected by HTTP Basic Auth (server-side, credentials not in code)
- GCP backend URL is API-only — direct browser access returns 403
- All API calls require a shared secret token
- AI provider API keys stored server-side only — never exposed to the browser
- CORS restricted to ibtsolutions.co.in only

---

## Current Status & Known Limitations

| Item | Status |
|---|---|
| Anthropic Claude extraction | Working |
| GCP Gemini extraction | Working |
| Azure OpenAI | Pending — quota increase request needed for East US region |
| Multi-order PDFs (40+ POs) | Working |
| Scanned / handwritten PDFs | Working |
| Excel input files | Working |
| OTC Excel output | Working — matches OTC ingest template format |
| Dark mode UI | Working |
| Mobile / tablet | Partially supported — desktop recommended for review |

**Known limitations:**
- Occasional parse errors on very low quality scans (flagged in UI for manual review)
- In-memory task storage — if the server restarts during extraction, the task is lost
- One extraction at a time per server instance (POC scale — not production-ready for concurrent users)

---

## Suggested Next Steps

1. **Pilot with CS team** — test with real retailer batches across 2–3 weeks, collect accuracy feedback
2. **Azure OpenAI** — submit quota increase request for East US (1–2 business days); enables a third provider comparison
3. **OTC template validation** — confirm the output Excel columns exactly match the live OTC system's import format
4. **Error correction UI** — allow CS staff to edit extracted values in-browser before downloading
5. **Production hardening** — persistent task storage, multi-user support, audit logging
6. **Direct OTC API upload** — if OTC exposes an API, skip the Excel step entirely

---

## Contact

Built by IBT Solutions engineering team.  
For questions or feedback contact: **sujatha@ibtsolutions.in**

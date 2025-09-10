# WooCommerce Novitus Receipts

A WordPress/WooCommerce plugin that connects directly to **Novitus printers via API** to print **fiscal and non-fiscal receipts** (with or without NIP) straight from WooCommerce orders.  
The plugin provides its own **UX panel**, **print queue**, **logs**, **security features**, and **settings**. All operations are executed through dedicated endpoints and stored for auditing.  

> **Status:** In progress — production-ready, but screenshots and extended documentation are still being prepared.

---

## ✨ Features

- 🔌 **Direct Novitus API** integration (printer configuration & connectivity).
- 🧾 **Fiscal & non-fiscal receipts** (with/without VAT ID / NIP).
- 🛠️ **WooCommerce Admin UX**:
  - Extra order actions: *Print Receipt*, *Re-print*, *Void/Cancel*.
  - Dedicated menu with searchable receipt list, filters, and downloads.
- ⏳ **Print Queue** with retries, prioritization, and error handling.
- ⚙️ **Settings**:
  - API endpoints, keys, and printer parameters.
  - Auto-print rules (e.g. on order status change).
  - Timeouts and retry policies.
- 🔐 **Security**:
  - Capability checks (`manage_wnr`, `view_wnr_receipts`).
  - Nonce validation and optional signed requests.
- 🗂️ **Logs**:
  - Full request/response history with redacted sensitive data.
  - Operator, timestamps, printer status.
- 📥 **Download Receipts**:
  - Export as PDF, HTML, or JSON (per order or global archive).
- 🧹 **Maintenance**:
  - Queue cleanup, log rotation, printer test ping.

---

## 🧱 Tech Stack

- WordPress + WooCommerce hooks & admin UI  
- PHP 7.4+ (tested on 8.x)  
- REST API endpoints (`/wp-json/wnr/v1/...`)  
- Novitus Printer API client  
- Custom DB tables for receipts & print queue  

---

## 📦 Installation (Dev)

1. Copy the plugin folder `woocommerce-novitus-receipts/` into `wp-content/plugins/`.
2. Activate in **WordPress Admin → Plugins**.
3. Go to **WooCommerce → Novitus Receipts** (new menu item).
4. Configure:
   - Novitus Base URL / IP
   - API Key / Token
   - Printer model & options
   - Auto-print rules  

> **Screenshots:** coming soon (Admin UI, queue view, settings).

---

## ⚙️ How it works

1. **Trigger** — order action (manual) or automatic rule (e.g. order status *completed*).  
2. **Queue Job** — plugin creates a new print job in its database.  
3. **Payload Builder** — prepare JSON payload with items, totals, VAT, NIP.  
4. **Novitus API Call** — send request to fiscal/non-fiscal endpoint.  
5. **Result Handling**:
   - On success → store receipt ID, generate file, attach to order.  
   - On error → retry or mark failed, log details.  
6. **Admin Panel** — operators can re-print, download, or void receipts.  

---

## 🔌 REST API (Internal)

- **Print Receipt**
  ```http
  POST /wp-json/wnr/v1/print
  ```
  ```json
  {
    "order_id": 12345,
    "type": "fiscal",
    "nip": "PL1234567890"
  }
  ```

- **Check Status**
  ```http
  GET /wp-json/wnr/v1/status?job_id=987
  ```
  ```json
  {
    "job_id": 987,
    "status": "queued",
    "order_id": 12345
  }
  ```

- **Retry Failed Job**
  ```http
  POST /wp-json/wnr/v1/retry
  ```

- **Cancel Job**
  ```http
  POST /wp-json/wnr/v1/cancel
  ```

---

## 🗄️ Data Model

**Table** `wp_wnr_receipts`  
- `id`, `order_id`, `type`, `nip`, `payload_json`, `novitus_id`, `status`, `file_path`, `created_at`, `updated_at`

**Table** `wp_wnr_queue`  
- `id`, `order_id`, `operation`, `attempts`, `status`, `last_error`, `created_at`, `updated_at`

---

## 🧭 Architecture Diagram

```mermaid
flowchart TD
  A[Order action / Auto rule] --> B[Create queue job]
  B --> C[Worker picks job]
  C --> D[Build payload (items, totals, VAT, NIP)]
  D --> E[Call Novitus API (fiscal / non-fiscal)]
  E -->|OK| F[Store receipt ID + file]
  E -->|Error| G[Retry / Log failure]
  F --> H[Attach to order + Admin panel]
  H --> I[Download / Re-print / Void (if supported)]
```

---

## 🗺️ Roadmap / In Progress

- 📸 Add public **screenshots** (Admin UI, queue, settings).  
- 🔄 Support **refund / return flows** (fiscal return).  
- 🌐 **Multi-printer support**.  
- ✅ 2-step approvals for re-print actions.  

---

## 📄 License

GPL-2.0-or-later  

---

## 👤 Author

Jakub Róg  
📍 Poland / Remote  
🔗 [LinkedIn](https://www.linkedin.com/in/jakub-róg-a15152380/)  

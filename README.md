# COREMERCH
### Core Infrastructure For Digital Commerce

> *Commerce does not need a platform. It never did.*

Built in Zimbabwe 🇿🇼 — Deployed everywhere.

---

## What This Is

COREMERCH is a sovereign commerce infrastructure store. It is a fully functional digital product business built entirely on free tools — GitHub Pages, Google Apps Script, Cloudflare Workers, Supabase, and NOWPayments — with automatic cryptocurrency payment verification and instant product delivery.

This repository contains the complete storefront. Every file is standalone HTML with embedded CSS and JavaScript. No build process. No npm. No dependencies. Upload and it works.

---

## Live Store

```
https://coremerch.io
```

---

## File Structure

```
/
├── index.html          — Homepage
├── products.html       — Full product catalogue (20 products)
├── freebies.html       — Free resources + stack selector quiz
├── payment.html        — Crypto checkout (NOWPayments integrated)
├── success.html        — Post-payment confirmation + confetti
├── posts.html          — Blog index (links to /posts/ folder)
├── custom-job.html     — Custom commission enquiry + brief form
├── legal.html          — Privacy, Terms, Refund, Cookie policies
├── contact.html        — Contact form with subject routing
├── README.md           — This file
│
└── posts/              — Individual blog post HTML files
    ├── why-stripe-doesnt-work-in-africa.html
    ├── how-nowpayments-webhook-works.html
    ├── github-pages-digital-store.html
    └── [slug].html     — Add new posts here
```

---

## Tech Stack

| Layer | Tool | Cost |
|-------|------|------|
| Storefront hosting | GitHub Pages | $0/month |
| Backend automation | Google Apps Script | $0/month |
| Database | Google Sheets or Supabase | $0/month |
| File storage | Google Drive or Cloudflare R2 | $0/month |
| Email delivery | Gmail or Resend | $0/month |
| Payment processing | NOWPayments | 0.5% per transaction |
| DNS and CDN (optional) | Cloudflare | $0/month |
| **Total monthly cost** | | **$0.00** |

---

## Supported Cryptocurrencies

Configured out of the box via NOWPayments:

| Symbol | Name | Network | Fee Level |
|--------|------|---------|-----------|
| USDT | Tether USD | BSC | 🟢 Low |
| USDT | Tether USD | Solana | 🟢 Low |
| USDT | Tether USD | TRON | 🟢 Low |
| BNB | BNB | BSC | 🟢 Low |
| SOL | Solana | Solana | 🟢 Low |
| MATIC | Polygon | Polygon | 🟢 Low |
| LTC | Litecoin | Litecoin | 🟢 Low |
| BTC | Bitcoin | Bitcoin | 🟡 Medium |
| ETH | Ethereum | Ethereum | 🔴 High |

NOWPayments supports 300+ cryptocurrencies total. Add any coin by updating the `COINS` array in `payment.html`.

---

## Setup Guide

### Prerequisites

- GitHub account (free)
- Google account (free)
- NOWPayments account (free — sign up at nowpayments.io)
- Your cryptocurrency wallet addresses

---

### Step 1 — Fork or Clone This Repository

```bash
git clone https://github.com/yourusername/coremerch-store.git
cd coremerch-store
```

Or fork directly on GitHub and enable GitHub Pages in Settings → Pages → Source: main branch / root.

Your store will be live at:
```
https://yourusername.github.io/coremerch-store
```

For a custom domain, add a `CNAME` file containing your domain:
```
coremerch.io
```

Then configure your DNS with a CNAME record pointing to `yourusername.github.io`.

---

### Step 2 — Configure NOWPayments

1. Create a free account at [nowpayments.io](https://nowpayments.io)
2. Generate an API key in your dashboard
3. Open `payment.html`
4. Replace `YOUR_NOWPAYMENTS_API_KEY` with your actual API key:

```javascript
const NP_API_KEY = 'your-actual-api-key-here';
```

---

### Step 3 — Add Your Wallet Addresses

In `payment.html`, update the `COINS` array with your wallet addresses:

```javascript
const COINS = [
  {
    id:      'usdtbsc',
    symbol:  'USDT',
    name:    'Tether USD',
    network: 'BSC',
    emoji:   '💵',
    wallet:  'YOUR_BEP20_WALLET_ADDRESS',  // ← Replace this
    lowFee:  true,
    npCurr:  'usdtbsc'
  },
  {
    id:      'usdttrc',
    symbol:  'USDT',
    name:    'Tether USD',
    network: 'TRON',
    emoji:   '💵',
    wallet:  'YOUR_TRC20_WALLET_ADDRESS',  // ← Replace this
    lowFee:  true,
    npCurr:  'usdttrc20'
  },
  // Add or remove coins as needed
];
```

---

### Step 4 — Set Up Google Apps Script Backend

The backend handles:
- Receiving order submissions
- Logging orders to Google Sheets
- Sending product delivery emails
- Sending you sale notifications
- Processing freebie form submissions
- Processing contact form submissions
- Processing custom brief submissions
- Processing newsletter subscriptions

#### 4a — Create the Google Sheet

1. Go to [sheets.google.com](https://sheets.google.com)
2. Create a new spreadsheet named `COREMERCH Orders`
3. Create the following tabs (sheets):

| Tab Name | Purpose |
|----------|---------|
| `Orders` | All purchase orders |
| `Leads` | Freebie email submissions |
| `Contacts` | Contact form submissions |
| `Briefs` | Custom job briefs |
| `Newsletter` | Newsletter subscriptions |
| `Products` | Product configuration |

#### Orders tab columns (Row 1 headers):
```
Timestamp | Email | Product ID | Product Name | Price USD | Coin | Network | TXID | NP Payment ID | NP Confirmed | Wallet Used | Delivered | Notes
```

#### Leads tab columns:
```
Timestamp | Email | Resource ID | Resource Name | Source
```

#### Contacts tab columns:
```
Timestamp | Name | Email | Subject | Message | Order ID | Product | Priority | Source
```

#### Briefs tab columns:
```
Timestamp | Name | Email | Project Type | Timeline | Description | Features | Budget | References | Notes | Source
```

#### Newsletter tab columns:
```
Timestamp | Email | Source
```

---

#### 4b — Create the Apps Script

1. In your Google Sheet, go to **Extensions → Apps Script**
2. Delete any existing code
3. Paste the following:

```javascript
// ═══════════════════════════════════════════════════
// COREMERCH — Google Apps Script Backend
// ═══════════════════════════════════════════════════

const SHEET_ID        = SpreadsheetApp.getActiveSpreadsheet().getId();
const DELIVERY_EMAIL  = 'your@gmail.com'; // Your Gmail address
const NOTIFY_EMAIL    = 'your@gmail.com'; // Where to send sale alerts

// Product file IDs from Google Drive
// Get the file ID from the URL:
// drive.google.com/file/d/FILE_ID_HERE/view
const PRODUCTS = {
  'sovereign-starter-stack':    'GOOGLE_DRIVE_FILE_ID',
  'edge-commerce-stack':        'GOOGLE_DRIVE_FILE_ID',
  'full-business-build':        'GOOGLE_DRIVE_FILE_ID',
  'sovereign-blueprint':        'GOOGLE_DRIVE_FILE_ID',
  'nowpayments-masterclass':    'GOOGLE_DRIVE_FILE_ID',
  'cloudflare-commerce-guide':  'GOOGLE_DRIVE_FILE_ID',
  'email-deliverability-playbook': 'GOOGLE_DRIVE_FILE_ID',
  'affiliate-system-blueprint': 'GOOGLE_DRIVE_FILE_ID',
  'scaling-playbook':           'GOOGLE_DRIVE_FILE_ID',
  'storefront-template-pack':   'GOOGLE_DRIVE_FILE_ID',
  'crypto-payment-page-kit':    'GOOGLE_DRIVE_FILE_ID',
  'apps-script-bundle':         'GOOGLE_DRIVE_FILE_ID',
  'workers-commerce-bundle':    'GOOGLE_DRIVE_FILE_ID',
  'admin-dashboard-template':   'GOOGLE_DRIVE_FILE_ID',
  'freebie-funnel-kit':         'GOOGLE_DRIVE_FILE_ID',
  'legal-pack':                 'GOOGLE_DRIVE_FILE_ID',
  'complete-builder-kit':       'GOOGLE_DRIVE_FILE_ID',
};

// Freebie file IDs
const FREEBIES = {
  'e1': 'GOOGLE_DRIVE_FILE_ID', // Infrastructure Map
  'e2': 'GOOGLE_DRIVE_FILE_ID', // Fee Cheatsheet
  'e3': 'GOOGLE_DRIVE_FILE_ID', // Platform Restriction Database
  'e4': 'GOOGLE_DRIVE_FILE_ID', // NOWPayments Quick Start
};

// ── Main entry point ─────────────────────────────────
function doPost(e) {
  try {
    const data = JSON.parse(e.postData.contents);
    const action = data.action;

    if (action === 'new_order') {
      return handleOrder(data);
    }
    if (action === 'freebie') {
      return handleFreebie(data);
    }
    if (action === 'contact') {
      return handleContact(data);
    }
    if (action === 'custom_brief') {
      return handleBrief(data);
    }
    if (action === 'newsletter_subscribe') {
      return handleNewsletter(data);
    }
    if (action === 'nowpayments_webhook') {
      return handleNOWPaymentsWebhook(data);
    }

    return response({ status: 'unknown_action' });
  } catch (err) {
    return response({ status: 'error', message: err.toString() });
  }
}

function doGet(e) {
  return ContentService
    .createTextOutput('COREMERCH Backend — OK')
    .setMimeType(ContentService.MimeType.TEXT);
}

// ── Handle new order ─────────────────────────────────
function handleOrder(data) {
  const ss    = SpreadsheetApp.openById(SHEET_ID);
  const sheet = ss.getSheetByName('Orders');

  sheet.appendRow([
    data.timestamp,
    data.email,
    data.productId,
    data.productName,
    data.priceUSD,
    data.coin,
    data.network,
    data.txid,
    data.npPaymentId,
    data.npConfirmed ? 'YES' : 'PENDING',
    data.walletUsed,
    'PENDING',
    ''
  ]);

  // Notify seller
  notifySeller(data);

  // Deliver product if payment confirmed
  if (data.npConfirmed) {
    deliverProduct(data.email, data.productName,
      data.productId, data.txid);
  }

  return response({ status: 'ok' });
}

// ── Deliver product ───────────────────────────────────
function deliverProduct(email, productName,
                         productId, txid) {
  const fileId = PRODUCTS[productId];

  if (!fileId || fileId === 'GOOGLE_DRIVE_FILE_ID') {
    // No file configured — send manual delivery notice
    MailApp.sendEmail({
      to:      NOTIFY_EMAIL,
      subject: '⚠️ Manual delivery needed: ' + productName,
      body:    'Order for ' + productName +
               ' from ' + email +
               ' needs manual delivery.\nTXID: ' + txid
    });
    return;
  }

  try {
    const file = DriveApp.getFileById(fileId);
    const blob = file.getBlob();

    MailApp.sendEmail({
      to:          email,
      subject:     '✅ Your COREMERCH Purchase: ' + productName,
      htmlBody:    buildDeliveryEmail(productName, email),
      attachments: [blob]
    });

    // Mark as delivered in sheet
    markDelivered(txid);

  } catch (err) {
    // Log delivery failure
    MailApp.sendEmail({
      to:      NOTIFY_EMAIL,
      subject: '❌ Delivery failed: ' + productName,
      body:    'Delivery failed for ' + email +
               '\nProduct: ' + productName +
               '\nTXID: ' + txid +
               '\nError: ' + err.toString()
    });
  }
}

// ── NOWPayments webhook ───────────────────────────────
function handleNOWPaymentsWebhook(data) {
  const confirmedStatuses =
    ['finished', 'confirmed', 'partially_paid'];

  if (confirmedStatuses.includes(data.payment_status)) {
    const ss    = SpreadsheetApp.openById(SHEET_ID);
    const sheet = ss.getSheetByName('Orders');
    const rows  = sheet.getDataRange().getValues();

    // Find matching order by NP payment ID
    for (var i = 1; i < rows.length; i++) {
      if (rows[i][8] === data.payment_id) {
        // Update confirmed status
        sheet.getRange(i + 1, 10).setValue('YES');

        // Trigger delivery if not yet delivered
        if (rows[i][11] !== 'DELIVERED') {
          deliverProduct(
            rows[i][1],  // email
            rows[i][3],  // product name
            rows[i][2],  // product id
            rows[i][7]   // txid
          );
        }
        break;
      }
    }
  }

  return response({ status: 'ok' });
}

// ── Handle freebie ────────────────────────────────────
function handleFreebie(data) {
  const ss    = SpreadsheetApp.openById(SHEET_ID);
  const sheet = ss.getSheetByName('Leads');

  sheet.appendRow([
    data.timestamp,
    data.email,
    data.resourceId,
    data.resourceName,
    data.source
  ]);

  // Deliver freebie
  const fileId = FREEBIES[data.resourceId];

  if (fileId && fileId !== 'GOOGLE_DRIVE_FILE_ID') {
    try {
      const file = DriveApp.getFileById(fileId);
      const blob = file.getBlob();

      MailApp.sendEmail({
        to:          data.email,
        subject:     '📦 Your Free Resource: ' + data.resourceName,
        htmlBody:    buildFreebieEmail(data.resourceName, data.email),
        attachments: [blob]
      });
    } catch (err) {
      MailApp.sendEmail({
        to:      NOTIFY_EMAIL,
        subject: '⚠️ Freebie delivery failed: ' + data.resourceName,
        body:    'Email: ' + data.email + '\nError: ' + err.toString()
      });
    }
  }

  return response({ status: 'ok' });
}

// ── Handle contact ────────────────────────────────────
function handleContact(data) {
  const ss    = SpreadsheetApp.openById(SHEET_ID);
  const sheet = ss.getSheetByName('Contacts');

  sheet.appendRow([
    data.timestamp,
    data.name,
    data.email,
    data.subject,
    data.message,
    data.orderId || '',
    data.product || '',
    data.priority ? 'YES' : 'NO',
    data.source
  ]);

  // Notify seller
  MailApp.sendEmail({
    to:      NOTIFY_EMAIL,
    subject: (data.priority ? '🔴 PRIORITY ' : '') +
             'Contact: ' + data.subject + ' — ' + data.name,
    body:    'From: ' + data.name + ' <' + data.email + '>\n' +
             'Subject: ' + data.subject + '\n' +
             'Priority: ' + (data.priority ? 'YES' : 'NO') + '\n' +
             'Order ID: ' + (data.orderId || 'N/A') + '\n' +
             'Product: ' + (data.product || 'N/A') + '\n\n' +
             'Message:\n' + data.message
  });

  return response({ status: 'ok' });
}

// ── Handle custom brief ───────────────────────────────
function handleBrief(data) {
  const ss    = SpreadsheetApp.openById(SHEET_ID);
  const sheet = ss.getSheetByName('Briefs');

  sheet.appendRow([
    data.timestamp,
    data.name,
    data.email,
    data.type,
    data.timeline,
    data.description,
    (data.features || []).join(', '),
    data.budget,
    data.references || '',
    data.notes || '',
    data.source
  ]);

  // Notify seller
  MailApp.sendEmail({
    to:      NOTIFY_EMAIL,
    subject: '🏗️ New Custom Brief: ' + data.type + ' — ' + data.name,
    body:    'From: ' + data.name + ' <' + data.email + '>\n' +
             'Type: ' + data.type + '\n' +
             'Budget: ' + data.budget + '\n' +
             'Timeline: ' + data.timeline + '\n\n' +
             'Description:\n' + data.description + '\n\n' +
             'Features: ' + (data.features || []).join(', ') + '\n' +
             'References: ' + (data.references || 'None') + '\n' +
             'Notes: ' + (data.notes || 'None')
  });

  return response({ status: 'ok' });
}

// ── Handle newsletter ─────────────────────────────────
function handleNewsletter(data) {
  const ss    = SpreadsheetApp.openById(SHEET_ID);
  const sheet = ss.getSheetByName('Newsletter');

  // Check for duplicates
  const emails = sheet.getRange(
    1, 2, sheet.getLastRow(), 1
  ).getValues().flat();

  if (!emails.includes(data.email)) {
    sheet.appendRow([
      data.timestamp,
      data.email,
      data.source
    ]);
  }

  return response({ status: 'ok' });
}

// ── Notify seller of sale ─────────────────────────────
function notifySeller(data) {
  MailApp.sendEmail({
    to:      NOTIFY_EMAIL,
    subject: '💰 New Sale: ' + data.productName +
             ' — ' + data.priceUSD + ' USD',
    body:    'Product: ' + data.productName + '\n' +
             'Price: $' + data.priceUSD + ' USD\n' +
             'Buyer: ' + data.email + '\n' +
             'Coin: ' + data.coin + ' on ' + data.network + '\n' +
             'TXID: ' + data.txid + '\n' +
             'NP Payment ID: ' + data.npPaymentId + '\n' +
             'NP Confirmed: ' + (data.npConfirmed ? 'YES' : 'PENDING') + '\n' +
             'Timestamp: ' + data.timestamp
  });
}

// ── Mark order as delivered ───────────────────────────
function markDelivered(txid) {
  const ss    = SpreadsheetApp.openById(SHEET_ID);
  const sheet = ss.getSheetByName('Orders');
  const rows  = sheet.getDataRange().getValues();

  for (var i = 1; i < rows.length; i++) {
    if (rows[i][7] === txid) {
      sheet.getRange(i + 1, 12).setValue('DELIVERED');
      break;
    }
  }
}

// ── Email templates ───────────────────────────────────
function buildDeliveryEmail(productName, email) {
  return `
    <div style="font-family:sans-serif;max-width:560px;
                margin:0 auto;background:#07080F;
                color:#F4F6FA;padding:40px;
                border-radius:12px;">
      <div style="font-size:1.4rem;font-weight:700;
                  color:#F4F6FA;margin-bottom:8px;">
        CORE<span style="color:#00BDB3;">MERCH</span>
      </div>
      <hr style="border:none;border-top:1px solid
                 rgba(255,255,255,0.07);margin:16px 0;" />
      <h2 style="color:#22C55E;margin-bottom:16px;">
        ✅ Your Purchase Is Attached
      </h2>
      <p style="color:#8591A3;line-height:1.7;">
        Thank you for your purchase of
        <strong style="color:#F4F6FA;">
          ${productName}
        </strong>.
        Your product is attached to this email.
      </p>
      <div style="background:#0C1220;border:1px solid
                  rgba(255,255,255,0.07);border-radius:8px;
                  padding:16px;margin:20px 0;">
        <p style="color:#8591A3;font-size:.85rem;
                  margin:0;line-height:1.6;">
          📁 <strong style="color:#F4F6FA;">
            Your file is attached above.
          </strong>
          Save it somewhere safe. This email is your
          permanent receipt and access method.
        </p>
      </div>
      <p style="color:#8591A3;font-size:.85rem;
                line-height:1.7;">
        Questions? Reply to this email or visit
        <a href="https://coremerch.io/contact.html"
           style="color:#00BDB3;">
          coremerch.io/contact
        </a>.
      </p>
      <hr style="border:none;border-top:1px solid
                 rgba(255,255,255,0.07);margin:24px 0;" />
      <p style="color:#47566A;font-size:.75rem;
                margin:0;line-height:1.6;">
        COREMERCH — Core infrastructure for digital commerce.
        Built in Zimbabwe 🇿🇼
      </p>
    </div>
  `;
}

function buildFreebieEmail(resourceName, email) {
  return `
    <div style="font-family:sans-serif;max-width:560px;
                margin:0 auto;background:#07080F;
                color:#F4F6FA;padding:40px;
                border-radius:12px;">
      <div style="font-size:1.4rem;font-weight:700;
                  color:#F4F6FA;margin-bottom:8px;">
        CORE<span style="color:#00BDB3;">MERCH</span>
      </div>
      <hr style="border:none;border-top:1px solid
                 rgba(255,255,255,0.07);margin:16px 0;" />
      <h2 style="color:#00BDB3;margin-bottom:16px;">
        📦 Your Free Resource Is Attached
      </h2>
      <p style="color:#8591A3;line-height:1.7;">
        Here is your free copy of
        <strong style="color:#F4F6FA;">
          ${resourceName}
        </strong>.
        It is attached to this email.
      </p>
      <div style="background:#0C1220;border:1px solid
                  rgba(0,189,179,0.2);border-radius:8px;
                  padding:16px;margin:20px 0;">
        <p style="color:#8591A3;font-size:.85rem;
                  margin:0;line-height:1.6;">
          When you are ready to go further,
          our full product catalogue is at
          <a href="https://coremerch.io/products.html"
             style="color:#00BDB3;">
            coremerch.io/products
          </a>.
        </p>
      </div>
      <hr style="border:none;border-top:1px solid
                 rgba(255,255,255,0.07);margin:24px 0;" />
      <p style="color:#47566A;font-size:.75rem;
                margin:0;line-height:1.6;">
        COREMERCH — Core infrastructure for digital commerce.
        Built in Zimbabwe 🇿🇼
        <br>You received this because you requested
        this free resource. No further emails unless
        you purchase or subscribe.
      </p>
    </div>
  `;
}

// ── Helper ────────────────────────────────────────────
function response(obj) {
  return ContentService
    .createTextOutput(JSON.stringify(obj))
    .setMimeType(ContentService.MimeType.JSON);
}
```

---

#### 4c — Deploy the Web App

1. In Apps Script, click **Deploy → New Deployment**
2. Type: **Web App**
3. Execute as: **Me**
4. Who has access: **Anyone**
5. Click **Deploy**
6. Copy the **Web App URL**

---

### Step 5 — Connect Frontend to Backend

Replace `YOUR_GOOGLE_APPS_SCRIPT_WEBAPP_URL` in every HTML file with your Web App URL:

Files to update:
- `freebies.html` — freebie form submissions
- `payment.html` — order submissions and NOWPayments webhook
- `posts.html` — newsletter subscriptions
- `custom-job.html` — custom brief submissions
- `contact.html` — contact form submissions

Search for:
```javascript
const SCRIPT_URL = 'YOUR_GOOGLE_APPS_SCRIPT_WEBAPP_URL';
```

Replace with:
```javascript
const SCRIPT_URL = 'https://script.google.com/macros/s/YOUR_DEPLOYMENT_ID/exec';
```

---

### Step 6 — Upload Product Files to Google Drive

1. Upload your product files to Google Drive
2. For each file, right-click → **Get link** → **Anyone with the link can view**
3. Copy the file ID from the URL:
   - URL: `drive.google.com/file/d/1BxiMVs0XRA5nFMdKvBdBZjgmUUqptlbs/view`
   - File ID: `1BxiMVs0XRA5nFMdKvBdBZjgmUUqptlbs`
4. Update the `PRODUCTS` object in the Apps Script with your file IDs

---

### Step 7 — Push to GitHub

```bash
git add .
git commit -m "Initial COREMERCH store deployment"
git push origin main
```

Your store is live.

---

## Adding New Products

### In the store (products.html)

Add a new card to the appropriate category grid following the existing card structure. Link to:
```
payment.html?product=your-product-slug&price=XX&id=XX
```

### In the backend (Apps Script)

Add the product slug and Google Drive file ID to the `PRODUCTS` object:
```javascript
const PRODUCTS = {
  // existing products...
  'your-new-product': 'GOOGLE_DRIVE_FILE_ID',
};
```

---

## Adding New Blog Posts

1. Create a new HTML file in the `/posts/` folder named `your-post-slug.html`
2. Add an entry to the `POSTS` array in `posts.html`:

```javascript
{
  slug:       'your-post-slug',
  title:      'Your Post Title',
  excerpt:    'A short description of the post.',
  category:   'infrastructure', // infrastructure | crypto | strategy | tutorials | origin
  date:       '2025-02-01',
  readTime:   '7 min read',
  author:     'CM',
  authorName: 'COREMERCH',
  emoji:      '🔧'
}
```

The post index updates automatically. Your GitHub content bot publishes the HTML file to `/posts/`. The `posts.html` index links to it.

---

## Scaling Beyond The Free Tier

| When | What to upgrade | To |
|------|-----------------|----|
| 100+ email deliveries/day | Gmail → Resend or Postmark | $0–$15/month |
| 50,000+ rows in Sheets | Google Sheets → Supabase | $0 (500MB free) |
| Higher backend performance | Apps Script → Cloudflare Workers | $0 (100k req/day free) |
| Better CDN performance | GitHub Pages → Cloudflare Pages | $0 |
| Large product files (25MB+) | Gmail attachment → Drive/R2 link | $0 |
| Production database | Supabase free → Supabase Pro | $25/month |

Full scaling guide in Product B6 — The Scaling Playbook.

---

## Environment Variables Reference

All configuration lives directly in the HTML files. There is no `.env` file. Replace these placeholders before deploying:

| File | Placeholder | Replace With |
|------|-------------|-------------|
| `payment.html` | `YOUR_NOWPAYMENTS_API_KEY` | Your NOWPayments API key |
| `payment.html` | `YOUR_GOOGLE_APPS_SCRIPT_WEBAPP_URL` | Your Apps Script Web App URL |
| `payment.html` | `YOUR_BEP20_WALLET_ADDRESS` | Your BSC wallet address |
| `payment.html` | `YOUR_TRC20_WALLET_ADDRESS` | Your TRON wallet address |
| `payment.html` | `YOUR_SOLANA_WALLET_ADDRESS` | Your Solana wallet address |
| `payment.html` | `YOUR_BTC_WALLET_ADDRESS` | Your Bitcoin wallet address |
| `payment.html` | `YOUR_ETH_WALLET_ADDRESS` | Your Ethereum wallet address |
| `freebies.html` | `YOUR_GOOGLE_APPS_SCRIPT_WEBAPP_URL` | Your Apps Script Web App URL |
| `posts.html` | `YOUR_GOOGLE_APPS_SCRIPT_WEBAPP_URL` | Your Apps Script Web App URL |
| `custom-job.html` | `YOUR_GOOGLE_APPS_SCRIPT_WEBAPP_URL` | Your Apps Script Web App URL |
| `contact.html` | `YOUR_GOOGLE_APPS_SCRIPT_WEBAPP_URL` | Your Apps Script Web App URL |
| `Apps Script` | `your@gmail.com` (DELIVERY_EMAIL) | Your Gmail address |
| `Apps Script` | `your@gmail.com` (NOTIFY_EMAIL) | Your notification email |

---

## Security Notes

- **Never commit your NOWPayments API key** to a public repository. If this repository is public, consider using a backend proxy or environment-level secret management.
- **Wallet addresses** are safe to include in frontend code — they are public by design.
- **Google Apps Script Web App URLs** are safe to include in frontend code — they are authenticated by your Google account on the backend.
- **NOWPayments IPN webhooks** should be validated using the IPN secret key. Add validation to the `handleNOWPaymentsWebhook` function in production.

---

## NOWPayments Webhook Security (Production)

Add IPN secret validation to your webhook handler:

```javascript
function handleNOWPaymentsWebhook(data, headers) {
  const IPN_SECRET = 'your-nowpayments-ipn-secret';

  // Validate HMAC signature
  const receivedSig = headers['x-nowpayments-sig'];
  const sortedData  = JSON.stringify(
    Object.keys(data).sort().reduce(function(acc, key) {
      acc[key] = data[key];
      return acc;
    }, {})
  );

  const expectedSig = Utilities.computeHmacSha512Signature(
    sortedData, IPN_SECRET
  ).map(function(b) {
    return ('0' + (b & 0xFF).toString(16)).slice(-2);
  }).join('');

  if (receivedSig !== expectedSig) {
    return response({ status: 'invalid_signature' });
  }

  // Process confirmed payment...
}
```

---

## Known Limitations

| Limitation | Detail | Workaround |
|------------|--------|------------|
| Gmail 100/day limit | Apps Script can send 100 emails/day on free tier | Upgrade to Resend or Postmark |
| 25MB email attachment limit | Gmail cannot attach files over 25MB | Use Google Drive shared link for large files |
| Apps Script quotas | Daily execution time limits apply | Monitor in Apps Script dashboard; upgrade to Cloudflare Workers for higher volume |
| Manual fallback | If NOWPayments API is unavailable, payment page falls back to static wallet addresses | NOWPayments has 99.9%+ uptime; manual fallback is for rare edge cases |
| QR code visual | The built-in QR generator is a visual approximation | Replace `generateQR()` in `payment.html` with a proper QR library like qrcode.js for production |

---

## Products in This Store

| ID | Product | Price |
|----|---------|-------|
| A1 | Sovereign Starter Stack | $97 |
| A2 | Edge Commerce Stack — Done For You | $197 |
| A3 | Full Business Build | $497 |
| A4 | Ghost Stack Deployment | $247 |
| A5 | Multi-Store Bundle | $397 |
| A6 | Growth Partnership | $1,500 |
| B1 | The Sovereign Blueprint | $47 |
| B2 | The NOWPayments Masterclass | $27 |
| B3 | The Cloudflare Commerce Guide | $37 |
| B4 | The Email Deliverability Playbook | $27 |
| B5 | The Affiliate System Blueprint | $37 |
| B6 | The Scaling Playbook | $47 |
| C1 | Complete Storefront Template Pack | $67 |
| C2 | Crypto Payment Page Kit | $37 |
| C3 | Google Apps Script Commerce Bundle | $47 |
| C4 | Cloudflare Workers Commerce Bundle | $47 |
| C5 | Admin Dashboard Template | $27 |
| C6 | Freebie Funnel Kit | $37 |
| C7 | The Legal Pack | $17 |
| BUNDLE | Complete Builder Kit (B1–B6 + C1–C7) | $197 |
| D1 | COREMERCH Pro | $27/month |
| D2 | Platform Founding Member | $29 deposit |

---

## Support

- **General enquiries:** [coremerch.io/contact.html](https://coremerch.io/contact.html)
- **Delivery issues:** Response within 4 hours
- **Technical support:** Response within 12 hours
- **Custom work:** [coremerch.io/custom-job.html](https://coremerch.io/custom-job.html)

---

## License

The storefront HTML, CSS, and JavaScript in this repository is licensed for use by COREMERCH customers under the terms described in the purchased product. Unauthorized reproduction or redistribution of this codebase is not permitted.

Individual products purchased from COREMERCH carry their own licence terms as described in the product listing and in the Terms of Service at [coremerch.io/legal.html](https://coremerch.io/legal.html).

---

## Built In Zimbabwe

COREMERCH was built in Zimbabwe by people who needed sovereign commerce infrastructure and could not find it anywhere else.

If you can build this from inside one of the most financially isolated economies on earth — and make it work, and make it scale — then you have built something that works anywhere.

That is what we built. That is what you now own.

---

*Your store. Your stack. Your sovereignty.*

**[coremerch.io](https://coremerch.io)**

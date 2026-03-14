# Trivkin Website — Setup & Deployment Guide

## What's Included
A single-file (`index.html`) e-commerce website with:
- Full product catalog with cart
- Razorpay payment integration
- WhatsApp ordering (works immediately)
- Mobile-responsive design
- SEO meta tags & OG cards for marketing

---

## 🚀 Step 1: Configure Your Details (Required)
Open `index.html` and find the `CONFIG` block near the bottom (search `CONFIG =`):

```javascript
const CONFIG = {
  razorpayKeyId: 'rzp_test_XXXXXXXXXXXXXXXXXX',  // ← Your Razorpay Key
  whatsappNumber: '919999999999',                 // ← Your WhatsApp number
  businessName:  'Trivkin',
  businessEmail: 'hello@trivkin.in',
  freeShipMin: 1500,                              // ← Free shipping threshold
};
```

### Get Razorpay Key:
1. Sign up at https://razorpay.com
2. Dashboard → Settings → API Keys → Generate Key
3. Copy the Key ID (starts with `rzp_live_...`)
4. Paste into `razorpayKeyId`

### Set WhatsApp Number:
- Format: country code + number (no + or spaces)
- Example for India: `919876543210`

---

## 🛍️ Step 2: Add Your Products
Find `const PRODUCTS = [` and update with your actual items:

```javascript
{
  id: 1,
  name: 'Your Shade Name',
  category: 'pendant',   // pendant | table | floor
  price: 1299,           // selling price in ₹
  mrp: 1799,             // original price in ₹
  tag: 'Bestseller',
  isNew: false,
  desc: 'Your product description here.',
  color: ['#3D4A2E', '#C07A30', '#EFE6D8'],  // [shade, accent, background]
},
```

**To add real product photos**, replace the `lamp-placeholder` div in `renderProducts()` with:
```javascript
<img src="your-image.jpg" class="product-card__img" alt="${p.name}"/>
```

---

## 🌐 Step 3: Deploy (Choose One)

### Option A — Netlify (Recommended, Free)
1. Go to https://app.netlify.com
2. Drag and drop your `index.html` file onto the Netlify dashboard
3. Done! You get a live URL instantly.
4. Add a custom domain in Site Settings → Domain Management

### Option B — Vercel (Free)
1. Go to https://vercel.com
2. Click "Add New → Project"
3. Upload your file or connect GitHub
4. Deploy with one click

### Option C — Any Web Host (cPanel, GoDaddy, etc.)
1. Upload `index.html` to your `public_html` folder
2. Point your domain to the folder
3. Done.

---

## 💳 Payment: Razorpay Backend (For Full Production)

The current setup uses Razorpay's client-side checkout. For production, Razorpay recommends creating an `order_id` from a backend to prevent order tampering.

### Quick Backend with Netlify Functions:
Create file `netlify/functions/create-order.js`:

```javascript
const Razorpay = require('razorpay');

exports.handler = async (event) => {
  const { amount } = JSON.parse(event.body);
  const razorpay = new Razorpay({
    key_id: process.env.RAZORPAY_KEY_ID,
    key_secret: process.env.RAZORPAY_KEY_SECRET,
  });
  const order = await razorpay.orders.create({
    amount: amount,
    currency: 'INR',
    receipt: 'order_' + Date.now(),
  });
  return { statusCode: 200, body: JSON.stringify({ order_id: order.id }) };
};
```

Set environment variables in Netlify:
- `RAZORPAY_KEY_ID` = your key ID
- `RAZORPAY_KEY_SECRET` = your key secret

Then update the `initiatePayment` function to call `/.netlify/functions/create-order` before opening Razorpay.

---

## 📣 Marketing Tips

### Instagram/Facebook Ads
The site has full Open Graph tags. Update these in `<head>`:
```html
<meta property="og:url" content="https://yourdomain.com" />
<meta property="og:image" content="https://yourdomain.com/og-image.jpg" />
```
Create a 1200×630px banner image for `og-image.jpg`.

### WhatsApp Marketing
The site has a floating WhatsApp button. Update the link in the HTML:
```html
<a href="https://wa.me/91XXXXXXXXXX?text=Hi%20Trivkin...">
```

### Google Analytics
Add before `</head>`:
```html
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXXXXXXXXX');
</script>
```

### SEO
- Update meta title and description with your keywords
- Submit your domain to Google Search Console
- Add your sitemap

---

## 📞 Support
For any customizations, contact: hello@trivkin.in
WhatsApp: +91 99999 99999

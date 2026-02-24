# Razorpay Setup Guide

## Quick Start

### Step 1: Add Missing Environment Variables

You already have `RAZORPAY_KEY_ID = rzp_live_S8vENUyAOwaGhw`

Add these to your Vercel project environment variables:

\`\`\`
RAZORPAY_KEY_SECRET=<your_razorpay_secret_key>
RAZORPAY_WEBHOOK_SECRET=<your_webhook_secret>
\`\`\`

**How to find these:**
1. Go to https://dashboard.razorpay.com
2. Click Settings → API Keys
3. Copy "Key Secret"
4. For Webhook Secret: Go to Settings → Webhooks → Create webhook
5. Copy the secret

### Step 2: Verify Razorpay Key ID

The key `rzp_live_S8vENUyAOwaGhw` is already configured. This is your live key ID.

### Step 3: Database is Ready ✅

The `payments` table has been created in Supabase with:
- order_id (unique)
- payment_id
- amount
- currency
- status (pending/completed/failed)
- email, phone, name
- service details
- signature verification data

### Step 4: Testing

**Test Cards for Development** (if needed):
- Success: 4111 1111 1111 1111
- Failure: 4000 0000 0000 0002

**Note**: Since you're using live keys, real payments will be processed.

### Step 5: Production Checklist

- [ ] Add RAZORPAY_KEY_SECRET to environment
- [ ] Add RAZORPAY_WEBHOOK_SECRET to environment
- [ ] Test a real ₹1 payment
- [ ] Verify receipt email
- [ ] Check database for payment record
- [ ] Monitor Razorpay dashboard for transactions

## Payment Flow Architecture

\`\`\`
User → Contact Form
   ↓
OTP Verification
   ↓
Click "Pay Fee" Button
   ↓
Payment Modal Opens
   ↓
Select Service OR Custom Amount
   ↓
Backend: Create Razorpay Order
   ↓
Razorpay Checkout Modal
   ↓
Complete Payment
   ↓
Backend: Verify Signature
   ↓
Update Database (status = completed)
   ↓
Send Receipt Email via Resend
   ↓
Show Success Message
\`\`\`

## API Endpoints

All endpoints expect JSON and return JSON.

### Create Order
\`\`\`
POST /api/razorpay/create-order
Body: {
  amount: 500,
  email: "user@example.com",
  phone: "+919999999999",
  name: "John Doe",
  service: "Consultation",
  orderId: "ORDER_12345"
}
\`\`\`

### Verify Payment
\`\`\`
POST /api/razorpay/verify-payment
Body: {
  razorpay_order_id: "order_xxxxx",
  razorpay_payment_id: "pay_xxxxx",
  razorpay_signature: "sig_xxxxx"
}
\`\`\`

### Send Receipt
\`\`\`
POST /api/send-payment-receipt
Body: {
  email: "user@example.com",
  name: "John Doe",
  amount: 500,
  service: "Consultation",
  payment_id: "pay_xxxxx",
  order_id: "order_xxxxx",
  contact_message: "Optional message"
}
\`\`\`

## Troubleshooting

### "Payment verification failed"
- Verify RAZORPAY_KEY_SECRET is correct
- Check if signature matches
- Ensure database is accessible

### Receipt email not sending
- Verify RESEND_API_KEY is set
- Check email address is valid
- Review Resend logs

### Order creation fails
- Check Supabase connection
- Verify payments table exists
- Ensure amount >= ₹100

## Important Notes

1. **Live Mode**: Using live Razorpay keys means real money will be charged
2. **Email Verification**: Resend will verify domain if using custom email
3. **Webhook**: Optional but recommended for async payment updates
4. **Database**: Payments are tracked for reporting and reconciliation

## Support

For Razorpay issues: https://razorpay.com/docs
For Resend issues: https://resend.com/docs
For Supabase issues: https://supabase.com/docs

---

**Ready to Deploy!** All code is production-ready. Just add the missing environment variables.

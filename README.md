# GTN Binary Options

## Run locally

1. Copy `.env.example` to `.env` and set a strong `JWT_SECRET`.
2. Configure password-reset email in `.env` with `RESEND_API_KEY` and a verified `RESEND_FROM_EMAIL`. Set `FRONTEND_URL` to the public site URL so reset links open the right page. Signup and login use email and password without OTP.
3. Configure PostgreSQL with `DATABASE_URL` and apply `database/database/schema.sql`. For an existing database, apply `database/migrations/001_auth_otp.sql`.
4. Configure the Pesapal environment and credentials in `.env`. For live payments, `PESAPAL_CALLBACK_URL` must be publicly reachable over HTTPS.
5. Register `https://YOUR-PUBLIC-DOMAIN/api/payments/pesapal/ipn` with Pesapal as a POST notification URL and put the returned ID in `PESAPAL_IPN_ID`. Set `FRONTEND_URL` to the URL where customers use the app.
6. Run `npm install`, then `npm start`, and open the frontend URL.

Deposits are credited only after the backend verifies the payment with Pesapal. The callback and IPN update the deposit state; customer status checks read the database and do not repeatedly call Pesapal.

Signup and login use password-only authentication and do not verify the email or phone at account creation. Password recovery uses a single-use link delivered to the account email; the link expires after 15 minutes, allows at most five invalid attempts, and is rate-limited. Keep the Resend credentials on the server and use a strong `JWT_SECRET`.

Withdrawals are currently manual M-Pesa payouts. Requests reserve funds and are queued for admin review; the admin queue prioritizes the oldest pending request. Faster automatic payouts require an approved payout provider integration and its credentials.

Never commit `.env` or put provider credentials in the frontend.

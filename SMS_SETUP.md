# SMS OTP Integration Setup Guide

This guide explains how to set up SMS OTP verification for the SafeVitals application.

## Overview

The application now supports both Email OTP and SMS OTP verification. Users can choose their preferred verification method during signup.

## Local Development Setup

For local development, Supabase uses a test OTP feature that doesn't require an external SMS provider.

### 1. Configure Test OTP (Local Development)

Edit `supabase/config.toml` and add test phone numbers:

```toml
[auth.sms.test_otp]
"15551234567" = "123456"  # Phone number without + or spaces = OTP code
"919876543210" = "654321"  # Example for India (+91)
```

**Note:** Phone numbers should be in E.164 format without the `+` sign and without spaces.

### 2. Test SMS OTP Locally

1. Use a test phone number configured in `test_otp`
2. The OTP will always be the value you set (e.g., "123456")
3. No actual SMS will be sent

## Production Setup

For production, you need to configure an SMS provider. Supabase supports:

- **Twilio** (Recommended)
- Twilio Verify
- MessageBird
- TextLocal
- Vonage

### Option 1: Twilio Setup (Recommended)

1. **Create a Twilio Account**
   - Sign up at https://www.twilio.com
   - Get your Account SID and Auth Token

2. **Get a Phone Number**
   - Purchase a phone number from Twilio
   - Or use Twilio Verify service (recommended for OTP)

3. **Configure Supabase**

   For **Twilio Verify** (Recommended):
   ```toml
   [auth.sms.twilio]
   enabled = true
   account_sid = "env(SUPABASE_AUTH_SMS_TWILIO_ACCOUNT_SID)"
   message_service_sid = "env(SUPABASE_AUTH_SMS_TWILIO_MESSAGE_SERVICE_SID)"
   auth_token = "env(SUPABASE_AUTH_SMS_TWILIO_AUTH_TOKEN)"
   ```

   For **Regular Twilio**:
   ```toml
   [auth.sms.twilio]
   enabled = true
   account_sid = "env(SUPABASE_AUTH_SMS_TWILIO_ACCOUNT_SID)"
   auth_token = "env(SUPABASE_AUTH_SMS_TWILIO_AUTH_TOKEN)"
   ```

4. **Set Environment Variables**

   In your Supabase project settings or `.env` file:
   ```
   SUPABASE_AUTH_SMS_TWILIO_ACCOUNT_SID=your_account_sid
   SUPABASE_AUTH_SMS_TWILIO_MESSAGE_SERVICE_SID=your_service_sid  # For Verify
   SUPABASE_AUTH_SMS_TWILIO_AUTH_TOKEN=your_auth_token
   ```

5. **Enable SMS in Supabase Config**

   ```toml
   [auth.sms]
   enable_signup = true
   enable_confirmations = true
   template = "Your SafeVitals verification code is {{ .Code }}"
   ```

### Option 2: Other SMS Providers

See Supabase documentation for configuring:
- MessageBird
- TextLocal
- Vonage

## How It Works

### User Flow

1. **Sign Up**
   - User fills out signup form with email, password, full name, and mobile number
   - User selects verification method: Email OTP or SMS OTP

2. **Email OTP Flow**
   - Account is created
   - OTP is sent to user's email automatically
   - User enters OTP on verification page
   - Account is verified

3. **SMS OTP Flow**
   - Account is created
   - SMS OTP is sent to user's phone number
   - User enters OTP on verification page
   - Account is verified

### Code Structure

- **AuthContext** (`src/contexts/AuthContext.tsx`)
  - `sendSMSOTP(phone)` - Sends OTP to phone number
  - `verifySMSOTP(phone, token)` - Verifies SMS OTP
  - `resendSMSOTP(phone)` - Resends SMS OTP

- **SignUp Page** (`src/pages/SignUp.tsx`)
  - Allows user to choose between Email and SMS OTP
  - Handles account creation and OTP sending

- **VerifyOTP Page** (`src/pages/VerifyOTP.tsx`)
  - Supports both email and SMS OTP verification
  - Shows appropriate messaging based on method

## Testing

### Local Testing

1. Add test phone numbers to `supabase/config.toml`:
   ```toml
   [auth.sms.test_otp]
   "15551234567" = "123456"
   ```

2. Restart Supabase:
   ```powershell
   npx supabase stop
   npx supabase start
   ```

3. Sign up with the test phone number
4. Use the OTP code you configured (e.g., "123456")

### Production Testing

1. Configure your SMS provider (Twilio, etc.)
2. Use a real phone number
3. Receive actual SMS with OTP code

## Troubleshooting

### SMS Not Sending

1. **Check Supabase Config**
   - Ensure `[auth.sms]` is enabled
   - Verify SMS provider configuration

2. **Check Environment Variables**
   - Ensure all required env vars are set
   - Verify credentials are correct

3. **Check Phone Number Format**
   - Must be in E.164 format: `+[country code][number]`
   - Example: `+15551234567` or `+919876543210`

4. **Check Twilio Account**
   - Verify account is active
   - Check account balance
   - Verify phone number is verified (for trial accounts)

### OTP Verification Failing

1. **Check OTP Code**
   - Ensure 6 digits
   - No spaces or special characters

2. **Check Expiry**
   - OTP expires after 1 hour (configurable)
   - Request a new OTP if expired

3. **Check Phone Number**
   - Must match exactly (including country code)
   - No formatting differences

## Cost Considerations

- **Twilio**: ~$0.0075 per SMS in US, varies by country
- **Twilio Verify**: ~$0.05 per verification (includes sending)
- **MessageBird**: Varies by country
- **TextLocal**: India-focused, competitive rates

## Security Notes

1. **Rate Limiting**: Configured in `supabase/config.toml`
   - `max_frequency = "5s"` - Minimum time between SMS sends
   - `sms_sent = 30` - Max SMS per hour

2. **OTP Expiry**: Default 1 hour (3600 seconds)

3. **Phone Verification**: Required before account is fully activated

## Next Steps

1. Choose your SMS provider
2. Set up account and get credentials
3. Configure environment variables
4. Update `supabase/config.toml`
5. Test with real phone numbers
6. Deploy to production

For more details, see [Supabase SMS Documentation](https://supabase.com/docs/guides/auth/phone-login)




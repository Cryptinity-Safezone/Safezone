# Invite-Only Access Configuration

The application is now configured for private beta with invite-only access.

## How It Works

- Only whitelisted email addresses can create new accounts
- Non-whitelisted users see: "This is a private beta. Access is invite-only."
- Existing users can still log in normally

## Managing the Whitelist

Edit `/src/lib/invite-whitelist.ts` to add or remove emails:

```typescript
const WHITELISTED_EMAILS = [
  'admin@cryptinity.com',
  'test@example.com',
  // Add new emails here
];
```

After updating the whitelist, rebuild and redeploy:

```bash
npm run build
```

## Testing

1. Try signing up with a non-whitelisted email - should see the beta message
2. Try signing up with a whitelisted email - should succeed
3. Existing users can still log in regardless of whitelist status

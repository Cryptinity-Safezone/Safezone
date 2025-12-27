# Admin Panel Setup Guide

## Overview
The admin panel provides testing functionality for granting CTY to accounts during development. This feature is hidden from regular users and only accessible to designated admin accounts.

## Configuration

### Admin Emails
Admin accounts are configured in two locations:

1. **Frontend**: `src/lib/admin-config.ts`
2. **Backend**: `supabase/functions/cty-manager/index.ts`

Default admin emails:
- `admin@cryptinity.com`
- `developer@cryptinity.com`

### Feature Flag
To enable/disable admin features, edit `src/lib/admin-config.ts`:

```typescript
export const ADMIN_CONFIG = {
  enableAdminFeatures: true,  // Set to false before production launch
  adminEmails: [
    'admin@cryptinity.com',
    'developer@cryptinity.com',
  ],
};
```

## Usage

### Accessing the Admin Panel

1. Sign up or log in with an admin email address
2. Navigate to Settings
3. Select "Admin Panel" from the sidebar (only visible to admins)

### Granting Test CTY

The admin panel offers three preset amounts:
- **100 CTY**: Quick testing
- **500 CTY**: Medium testing
- **1000 CTY**: Extended testing

Click any button to grant that amount to your account. The balance updates automatically.

### Transaction Logging

All admin grants are logged in the `cty_transactions` table with:
- `transaction_type`: `admin_grant`
- `description`: `Admin test grant`
- `metadata`: `{ reason: 'admin_test_grant' }`

## Testing AI Image Generation

After granting CTY:

1. Navigate to the Create view
2. Enter a prompt (minimum 10 characters)
3. Select style and quality
4. Click "Create Image" (costs 25 CTY for standard, 40 CTY for high quality)
5. View your generated image in the history

## Security Notes

- Admin emails are hardcoded in both frontend and backend
- The backend validates admin status before processing grants
- Maximum grant amount is capped at 10,000 CTY
- All grants require authentication

## Production Deployment

**IMPORTANT**: Before launching to production:

1. Set `enableAdminFeatures: false` in `src/lib/admin-config.ts`
2. Update admin emails to real project owner emails
3. Consider removing or commenting out the admin section in `SettingsView.tsx`
4. Verify all test grants in database can be identified via `admin_test_grant` reason

## Files Modified

- `src/lib/admin-config.ts` - Admin configuration
- `src/hooks/useAdmin.ts` - Admin detection hook
- `src/hooks/useAdminGrant.ts` - CTY grant functionality
- `src/views/SettingsView.tsx` - Admin panel UI
- `supabase/functions/cty-manager/index.ts` - Admin grant endpoint

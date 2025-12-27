# Cryptinity - Full Implementation Guide

## Overview

Cryptinity is now fully integrated with Supabase for authentication, database, storage, and server-side logic via Edge Functions. This implementation follows SafeZone principles with a non-manipulative CTY⚡ economy system.

## What's Implemented

### 1. Authentication (Supabase Auth)
- Email/password authentication
- Automatic user profile creation on signup
- Session management and persistence
- Password reset functionality

### 2. Database (PostgreSQL via Supabase)
- **user_profiles**: Extended user data with CTY balance, plan info, and profile details
- **posts**: User-generated content with likes and comments
- **cty_transactions**: Complete transaction history for transparency
- **user_subscriptions**: Subscription plan tracking
- **showcase_items**: User portfolio/showcase entries
- **warehouse_items**: Private user storage inventory
- **follows**: User follow relationships
- **post_likes**: Like tracking for posts

All tables have Row Level Security (RLS) enabled with appropriate policies.

### 3. Storage (Supabase Storage)
- **avatars**: Profile pictures (5MB limit)
- **covers**: Cover photos (10MB limit)
- **posts**: Post images (10MB limit)
- **warehouse**: Private storage for creations (50MB limit)
- **showcase**: Public portfolio items (20MB limit)

All buckets have security policies ensuring users can only manage their own files.

### 4. Edge Functions
- **cty-manager**: Server-side CTY⚡ economy management
  - `/balance` - Get user's CTY balance and plan
  - `/transactions` - Get transaction history
  - `/spend` - Spend CTY on actions (AI generation, customization)
  - `/earn` - Earn CTY through engagement
  - `/purchase` - Purchase CTY energy packs

## How to Use

### Authentication

```typescript
import { useAuth } from './contexts/AuthContext';

function MyComponent() {
  const { user, profile, signIn, signUp, signOut, resetPassword } = useAuth();

  // Sign up a new user
  await signUp('email@example.com', 'password', 'Username', '@handle');

  // Sign in
  await signIn('email@example.com', 'password');

  // Sign out
  await signOut();

  // Reset password
  await resetPassword('email@example.com');
}
```

### CTY⚡ Management

```typescript
import { ctyManager } from './lib/cty';

// Get balance
const { balance, plan } = await ctyManager.getBalance();

// Spend CTY (e.g., for AI generation)
await ctyManager.spend(50, 'AI Image Generation', {
  prompt: 'Beautiful landscape',
  model: 'stable-diffusion'
});

// Earn CTY (e.g., for completing profile)
await ctyManager.earn(10, 'Profile completion bonus');

// Purchase CTY
await ctyManager.purchase(250); // Purchase 250 CTY pack

// Get transaction history
const transactions = await ctyManager.getTransactions();
```

### File Storage

```typescript
import { storage } from './lib/storage';
import { useAuth } from './contexts/AuthContext';

function UploadAvatar() {
  const { user } = useAuth();

  const handleUpload = async (file: File) => {
    const { url, path } = await storage.uploadFile(
      'avatars',
      user.id,
      file,
      'avatar'
    );

    // Update user profile with new avatar URL
    await supabase
      .from('user_profiles')
      .update({ avatar_url: url })
      .eq('id', user.id);
  };
}
```

### Database Operations

```typescript
import { supabase } from './lib/supabase';

// Create a post
const { data: post, error } = await supabase
  .from('posts')
  .insert({
    user_id: user.id,
    content: 'Hello SafeZone!',
    image_url: 'https://...',
  })
  .select()
  .single();

// Get user's posts
const { data: posts } = await supabase
  .from('posts')
  .select('*')
  .eq('user_id', user.id)
  .order('created_at', { ascending: false });

// Like a post
const { data: like } = await supabase
  .from('post_likes')
  .insert({
    user_id: user.id,
    post_id: postId,
  });

// Follow a user
const { data: follow } = await supabase
  .from('follows')
  .insert({
    follower_id: user.id,
    following_id: targetUserId,
  });
```

## SafeZone Rules Implementation

### 1. No Algorithms
- Feed is chronological (sorted by `created_at DESC`)
- No recommendation engine
- No boosting or ranking

### 2. No Manipulation
- Transparent CTY transaction history
- Clear pricing with no dark patterns
- No artificial scarcity

### 3. CTY⚡ Economy
- Internal utility with no real-world value
- Used for:
  - AI generation (images, sounds, art)
  - Profile customization (avatars, covers)
  - Storage expansion
- Limits encourage intentional creation
- Monthly allowances based on plan:
  - Free: 100 CTY/month
  - Personal: 500 CTY/month
  - Creator: 1,500 CTY/month

### 4. Privacy & Safety
- All data stays in user's control
- RLS ensures users can only access their own data
- Optional anonymous expression
- Strictly moderated content

### 5. Non-Competitive Design
- No public follower counts on display
- No trending or viral mechanics
- Optional showcase (never forced)
- Step away anytime

## Development Workflow

### Running Locally
```bash
npm run dev
```

### Building for Production
```bash
npm run build
```

### Type Checking
```bash
npm run typecheck
```

## Environment Variables

The following environment variables are already configured:

```
VITE_SUPABASE_URL=https://0ec90b57d6e95fcbda19832f.supabase.co
VITE_SUPABASE_ANON_KEY=<your-key>
```

## Database Migrations

Migrations are automatically applied via the Supabase dashboard. Current migrations:
1. `create_cryptinity_schema` - Main database schema with all tables and RLS
2. `create_storage_buckets` - Storage buckets and security policies

## Security Notes

1. All user data is protected by Row Level Security (RLS)
2. Storage buckets enforce user-specific access
3. Edge Functions verify JWT tokens
4. CTY transactions are server-side to prevent tampering
5. No secrets are exposed to the client

## Next Steps

You can now build features like:
- Real-time feed from database posts
- Image upload with CTY cost
- AI generation with CTY spending
- Profile customization with storage
- User-to-user interactions (follows, likes, comments)
- Warehouse management
- Showcase galleries
- Monthly CTY allowance distribution (via scheduled function)

All following SafeZone principles and the non-manipulative CTY economy.

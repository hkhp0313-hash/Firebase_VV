# Local Supabase Development Setup Guide

## Prerequisites

### 1. Install Docker Desktop
Docker Desktop is required to run Supabase services locally.

**Download and Install:**
1. Visit: https://docs.docker.com/desktop/install/windows-install/
2. Download Docker Desktop for Windows
3. Run the installer and follow the setup wizard
4. Restart your computer if prompted
5. Launch Docker Desktop and ensure it's running (you'll see the Docker icon in your system tray)

**Verify Installation:**
```powershell
docker --version
```

### 2. Supabase CLI
✅ Already available via `npx supabase` (no installation needed)

### 3. Project Configuration
✅ Supabase config initialized in `supabase/config.toml`
✅ Helper scripts created in `scripts/` directory

## Setup Steps

Once Docker Desktop is installed and running:

### Option A: Using Helper Scripts (Recommended)

#### Step 1: Start Local Supabase Services
```powershell
cd D:\VitalVerse\vitals-vr-fusion
.\scripts\start-local.ps1
```

This script will:
- Check if Docker is running
- Start all Supabase services locally
- Download Docker images (first time only, may take a few minutes)

#### Step 2: Update Environment Variables Automatically
```powershell
.\scripts\setup-local-env.ps1
```

This script will:
- Read local Supabase configuration
- Update your `.env` file with local URLs and keys
- Create a backup of your original `.env` file

### Option B: Manual Setup

#### Step 1: Start Local Supabase Services
```powershell
cd D:\VitalVerse\vitals-vr-fusion
npx supabase start
```

This will:
- Download Docker images (first time only, may take a few minutes)
- Start all Supabase services locally (database, auth, storage, etc.)
- Display local URLs and API keys

#### Step 2: Update Environment Variables
After `supabase start` completes, it will show output like:
```
API URL: http://localhost:54321
GraphQL URL: http://localhost:54321/graphql/v1
DB URL: postgresql://postgres:postgres@localhost:54322/postgres
Studio URL: http://localhost:54323
anon key: ...
service_role key: ...
```

Update your `.env` file with these local values:
- `VITE_SUPABASE_URL=http://localhost:54321`
- `VITE_SUPABASE_PUBLISHABLE_KEY=<anon key from output>`

### Step 3: Start Frontend Development Server
```powershell
npm run dev
```

The frontend will start on **http://localhost:5173** (configured in `vite.config.ts`)

### Step 4: Verify Setup
1. Visit Supabase Studio: http://localhost:54323 (manage your local database)
2. Visit Frontend: http://localhost:5173
3. Your app should now connect to local Supabase instead of cloud

## Useful Commands

### Using Helper Scripts
```powershell
# Start Supabase services (with Docker check)
.\scripts\start-local.ps1

# Stop Supabase services
.\scripts\stop-local.ps1

# Update .env file for local development
.\scripts\setup-local-env.ps1
```

### Direct Supabase CLI Commands
```powershell
# Start Supabase services
npx supabase start

# Stop Supabase services
npx supabase stop

# View status
npx supabase status

# Reset local database
npx supabase db reset

# View logs
npx supabase logs
```

## Access Points

Once everything is running:
- **Frontend**: http://localhost:5173 (configured in `vite.config.ts`)
- **Supabase Studio**: http://localhost:54323
- **API**: http://localhost:54321
- **Database**: postgresql://postgres:postgres@localhost:54322/postgres

## Troubleshooting

- **Docker not starting**: Ensure Docker Desktop is running and WSL 2 is installed (Windows 11) or Hyper-V is enabled (Windows 10)
- **Port conflicts**: 
  - Supabase ports (54321-54323) can be changed in `supabase/config.toml`
  - Frontend port (5173) can be changed in `vite.config.ts`
- **Out of memory**: Docker Desktop may need more RAM allocated in settings

## Switching Between Local and Cloud

- **Local Development**: Use `.env` with local URLs
- **Production/Cloud**: Use `.env` with cloud URLs (current setup)

You can create separate environment files:
- `.env.local` - for local development
- `.env.production` - for cloud deployment


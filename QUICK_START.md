# Quick Start Guide - Local Development

## Prerequisites
1. **Docker Desktop** - [Download & Install](https://docs.docker.com/desktop/install/windows-install/)

## One-Time Setup

1. **Install Docker Desktop** (if not already installed)
   - Download from: https://docs.docker.com/desktop/install/windows-install/
   - Install and start Docker Desktop

2. **Start Local Supabase**
   ```powershell
   .\scripts\start-local.ps1
   ```

3. **Configure Environment**
   ```powershell
   .\scripts\setup-local-env.ps1
   ```

4. **Start Frontend** (in a separate terminal)
   ```powershell
   npm run dev
   ```

## Daily Development

### Start Both Services
```powershell
# Terminal 1: Start Supabase
.\scripts\start-local.ps1

# Terminal 2: Start Frontend
npm run dev
```

### Access Points
- **Frontend**: http://localhost:5173
- **Supabase Studio**: http://localhost:54323
- **API**: http://localhost:54321

### Stop Services
```powershell
# Stop Supabase
.\scripts\stop-local.ps1

# Stop Frontend: Press Ctrl+C in the terminal
```

## Switching Between Local and Cloud

### Use Local Supabase
Your `.env` file should have:
```
VITE_SUPABASE_URL=http://localhost:54321
VITE_SUPABASE_PUBLISHABLE_KEY=<local-anon-key>
```

### Use Cloud Supabase
Restore from backup:
```powershell
Copy-Item .env.backup .env -Force
```

## Troubleshooting

**Docker not running?**
- Start Docker Desktop from Start Menu
- Wait until Docker icon appears in system tray

**Port conflicts?**
- Edit `supabase/config.toml` to change Supabase ports
- Frontend port is in `vite.config.ts` (currently 5173)

**Need help?**
- See detailed guide: `LOCAL_SETUP.md`
- Check Supabase logs: `npx supabase logs`


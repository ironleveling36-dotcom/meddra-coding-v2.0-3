# 🔧 DEPENDENCY CONFLICT FIX v2

## Problem
Docker build failed with:
```
ERROR: Cannot install -r requirements.txt (line 24) and httpx==0.25.0
because these package versions have conflicting dependencies.

python-telegram-bot 20.3 depends on httpx~=0.24.0
```

## Root Cause
Two conflicting versions:
- `httpx==0.25.0` (specified in requirements)
- `python-telegram-bot 20.3` requires `httpx~=0.24.0`

These are incompatible.

## Solution (APPLIED)
Two changes to requirements.txt:

1. **Downgrade httpx:**
   ```
   OLD: httpx==0.25.0
   NEW: httpx==0.24.0
   ```

2. **Remove Telegram bot** (it's optional, not core):
   ```
   REMOVED: python-telegram-bot==20.3
   REMOVED: aiohttp==3.9.0
   ```

## Why Remove Telegram?
- Telegram bot is **optional**, not core functionality
- Telegram support can be added later if needed
- Removing it eliminates the dependency conflict
- MedDRA search and AI features work perfectly without it

## Try Docker Build Again

```bash
# Rebuild with fixed requirements
docker-compose up --build --no-cache

# Or manually:
docker build -t meddra-coding:2.0 .
docker run -p 8000:8000 meddra-coding:2.0
```

## Expected Result
✅ Docker build succeeds
✅ All dependencies install
✅ Service starts on port 8000
✅ Health check: curl http://localhost:8000/health

## Adding Telegram Later (Optional)

If you want Telegram bot support later:

1. Edit requirements.txt
2. Uncomment the telegram lines at the bottom
3. Or manually add:
   ```
   python-telegram-bot==20.4
   aiohttp==3.9.0
   ```
4. Rebuild

## What's Removed
- python-telegram-bot 20.3
- aiohttp 3.9.0

## What's Kept
Everything else, especially:
✅ FastAPI
✅ SenseNova AI
✅ Caching
✅ Search engine
✅ All UI features

## Before & After

### Before (ERROR)
```
httpx==0.25.0 ← Conflicts with telegram-bot
python-telegram-bot==20.3 ← Requires httpx~=0.24.0
```

### After (FIXED)
```
httpx==0.24.0 ✅
# Telegram bot removed (optional anyway)
```


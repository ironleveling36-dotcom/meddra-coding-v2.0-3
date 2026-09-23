# 🚀 GitHub to Render Deployment Guide

Complete guide for deploying MedDRA Coding Assistant from GitHub to Render.

---

## 📋 Quick Start (5 minutes)

### Step 1: Create GitHub Repository

```bash
# Initialize git repo
cd your-meddra-project
git init
git add .
git commit -m "Initial commit: MedDRA Coding Assistant v2.0"

# Create repo on GitHub.com
# → Go to github.com/new
# → Name: meddra-coding-assistant
# → Description: AI-powered MedDRA search with SenseNova integration
# → Choose: Public or Private
# → Create repository

# Connect local to GitHub
git remote add origin https://github.com/YOUR-USERNAME/meddra-coding-assistant.git
git branch -M main
git push -u origin main
```

### Step 2: Deploy to Render

1. **Go to**: https://render.com
2. **Sign up** with GitHub account (or create account)
3. **Connect GitHub**: Render → Dashboard → Connect GitHub Account
4. **Create Web Service**:
   - Click "New +" → "Web Service"
   - Select your `meddra-coding-assistant` repository
   - Name: `meddra-coding`
   - Environment: `Python`
   - Build Command: `pip install -r requirements.txt && python scripts/build_index.py`
   - Start Command: `gunicorn -w 4 -b 0.0.0.0:$PORT app.main:app`
   - Plan: `Free` (or `Starter` for production)
   - Create Service

### Step 3: Add Secrets

In Render Dashboard → Service Settings → Environment:

```
AI_API_KEY=sk_your_sensenova_key_here
AI_ENABLED=true
AI_API_BASE_URL=https://api.hcnsec.cn/v1
AI_MODEL=sensenova-6.8-flash-lite
```

### Step 4: Deploy

```bash
# Push to main branch to trigger deployment
git push origin main

# Watch deployment in Render dashboard
# URL will be: https://meddra-coding-xxxx.onrender.com
```

---

## 📂 Repository Structure

```
meddra-coding-assistant/
├── .github/
│   └── workflows/
│       └── deploy.yml              # CI/CD pipeline
├── app/
│   ├── __init__.py
│   ├── main.py                     # FastAPI app (optimized)
│   ├── engine.py                   # Search engine (optimized)
│   ├── config.py                   # Configuration (optimized)
│   ├── ai_service.py               # SenseNova integration (optimized)
│   ├── ime_service.py              # IME data loader (enhanced)
│   ├── telegram_bot.py             # Telegram bot (optional)
│   └── __pycache__/
├── data/
│   ├── meddra_terms.jsonl.gz       # MedDRA terms (binary)
│   ├── meddra_vectors.npz          # Embeddings (binary)
│   └── Important-medical-event-29_1.xlsx  # IME list
├── static/
│   ├── index.html                  # Web UI (enhanced)
│   ├── credits.html
│   └── style.css                   # (if separated)
├── scripts/
│   └── build_index.py              # Build search index
├── tests/
│   ├── test_engine.py
│   └── test_api.py
├── .github/
│   └── workflows/
│       └── deploy.yml              # GitHub Actions
├── .gitignore                       # Exclude from git
├── render.yaml                      # Render config
├── requirements.txt                 # Python dependencies
├── requirements-build.txt           # Build dependencies
├── Dockerfile                       # Docker image (optional)
├── railway.json                     # Railway config (optional)
├── README.md                        # Project documentation
└── LICENSE                          # MIT or similar
```

---

## 🔧 Configuration Files

### requirements.txt

```
fastapi==0.104.1
uvicorn==0.24.0
httpx==0.25.0
pydantic==2.5.0
python-multipart==0.0.6

# Data/ML
numpy==1.24.3
pandas==2.1.1
rapidfuzz==3.5.2
openpyxl==3.1.2

# Embeddings
fastembed==0.2.8
onnx==1.15.0
onnxruntime==1.17.0

# Production
gunicorn==21.2.0
python-dotenv==1.0.0

# Optional: Telegram bot
python-telegram-bot==20.3
aiohttp==3.9.0

# Development
pytest==7.4.3
pytest-asyncio==0.21.1
black==23.11.0
flake8==6.1.0
```

### render.yaml (Auto-deployment)

```yaml
services:
  - type: web
    name: meddra-coding
    env: python
    buildCommand: pip install -r requirements.txt && python scripts/build_index.py
    startCommand: gunicorn -w 4 -b 0.0.0.0:$PORT app.main:app
    healthCheckPath: /health
    autoDeploy: true
    branch: main
```

---

## 🔐 Setting Up Secrets

### Method 1: Render Dashboard

1. **Service** → **Environment**
2. **Add secret**:
   - Key: `AI_API_KEY`
   - Value: `sk_your_sensenova_key`
3. **Save** and service redeploys

### Method 2: GitHub Secrets (for Actions)

1. **Repository** → **Settings** → **Secrets and variables** → **Actions**
2. **New repository secret**:
   - Name: `RENDER_API_KEY`
   - Value: (from Render account settings)
3. **Add** → Used by workflow automatically

### Environment Variables

```
# .env.example (commit this, not actual values)
AI_ENABLED=true
AI_API_BASE_URL=https://api.hcnsec.cn/v1
AI_MODEL=sensenova-6.8-flash-lite
# AI_API_KEY=sk_xxxxx  (DO NOT COMMIT - set in Render dashboard)
```

---

## 📝 First Deployment Checklist

- [ ] **GitHub Repository Created**
  - Public or Private
  - Description set
  - README included

- [ ] **Render Account Ready**
  - Email verified
  - GitHub connected
  - Payment method added (for production)

- [ ] **Files in Repository**
  - [ ] `requirements.txt` with dependencies
  - [ ] `render.yaml` configuration
  - [ ] `.github/workflows/deploy.yml` (optional CI/CD)
  - [ ] `.gitignore` to exclude secrets
  - [ ] `README.md` documentation
  - [ ] `app/` folder with all files
  - [ ] `data/` folder with MedDRA files
  - [ ] `static/` folder with HTML/CSS

- [ ] **Secrets Configured**
  - [ ] `AI_API_KEY` set in Render
  - [ ] Other environment variables set

- [ ] **Initial Push**
  - [ ] `git push origin main` completed
  - [ ] Render webhook triggered

- [ ] **Deployment Success**
  - [ ] URL accessible: `https://meddra-coding-xxxx.onrender.com`
  - [ ] `/health` endpoint returns 200
  - [ ] `/api/code?q=bleeding` works

---

## 🔄 Continuous Integration/Deployment (CI/CD)

### GitHub Actions Workflow

Automatic testing and deployment on every push:

```bash
# Push to main
git add .
git commit -m "Feature: Add new IME handling"
git push origin main

# Automatically:
# 1. Run tests
# 2. Check code quality
# 3. Security scan
# 4. Deploy to Render
```

### Workflow Status

View at: `Repository → Actions` tab

Shows:
- ✅ Test results
- ✅ Deployment status
- ✅ Performance metrics

---

## 📊 Monitoring Deployment

### Render Dashboard

```
Service → Logs → View output

Key indicators:
✅ "Engine ready (18234 terms loaded)"
✅ "Uvicorn running on 0.0.0.0:8000"
❌ Watch for errors in initial load
```

### Health Checks

```bash
# Health endpoint
curl https://meddra-coding-xxxx.onrender.com/health
# Response: {"status": "healthy", "ready": true, ...}

# Stats
curl https://meddra-coding-xxxx.onrender.com/api/stats

# Test search
curl "https://meddra-coding-xxxx.onrender.com/api/code?q=bleeding"
```

### Performance Monitoring

Render includes:
- CPU usage
- Memory usage
- Request count
- Error rate

Access in: **Service** → **Metrics**

---

## 🚀 Production Deployment Tips

### For Production (not just hobby)

1. **Upgrade Plan**
   - Render Free: Spins down after 15 min inactivity
   - Render Starter ($7/mo): Always on, better performance

   ```bash
   # In Render Dashboard:
   Service → Settings → Plan → Choose "Starter"
   ```

2. **Enable Auto-Scaling**
   - Render → Service → Instance Count
   - Set to 1-2 for reliability

3. **Custom Domain**
   - Render → Service → Settings → Custom Domain
   - Add: `meddra.yourdomain.com`

4. **Environment Optimization**
   - Reduce cache sizes for memory efficiency
   - Enable response compression
   - Use CDN for static files

### Cost Optimization

```
Free Plan: $0 (hobby)
Starter: $7/month × 1 instance
Standard: $25/month × 1 instance

Estimates:
- ~100 searches/day: Starter sufficient
- ~10K searches/day: Standard recommended
- ~100K+ searches/day: Multiple instances
```

---

## 🔍 Debugging Deployment Issues

### Issue: "Build failed"

```bash
# Check: requirements.txt syntax
pip install -r requirements.txt

# Check: build script
python scripts/build_index.py

# Check: missing dependencies
grep "import" app/*.py | awk '{print $2}' | sort | uniq
```

### Issue: "Service won't start"

```bash
# Check: Python version compatibility
python --version  # 3.10+ required

# Check: FastAPI imports
python -c "from app.main import app; print('OK')"

# Check: logs in Render dashboard
```

### Issue: "AI not working"

```bash
# Verify API key set
curl https://meddra-coding-xxxx.onrender.com/api/ai-health

# Check logs for:
grep -i "sensenova\|api\|error" logs/render.log
```

---

## 📦 Updating Production

### Safe Deployment Workflow

```bash
# 1. Create feature branch
git checkout -b feature/my-improvement

# 2. Make changes
# ... edit files ...

# 3. Test locally
python -m uvicorn app.main:app --reload

# 4. Commit
git add .
git commit -m "Feature: [description]"

# 5. Push to feature branch
git push origin feature/my-improvement

# 6. Create Pull Request
# GitHub → Compare & Pull Request

# 7. Review and tests run automatically
# If all green, merge to main

# 8. Automatic deployment to Render
# Service restarts with new code
```

### Rollback if Needed

```bash
# View commit history
git log --oneline -20

# Revert to previous version
git revert <commit-hash>
git push origin main

# Render automatically deploys the revert
```

---

## 🆘 Support Resources

### Documentation
- **Render Docs**: https://render.com/docs
- **GitHub Docs**: https://docs.github.com
- **FastAPI Docs**: https://fastapi.tiangolo.com

### Troubleshooting
1. Check **Render Logs** (Service → Logs)
2. Check **GitHub Actions** (Repository → Actions)
3. Run locally: `python -m uvicorn app.main:app --reload`
4. Review **error messages** for specific hints

### Community
- Render Community: https://render.com/community
- Stack Overflow: Search "render.com" + issue
- GitHub Issues: Ask in repository

---

## 📝 Project Files to Commit

### Essential Files

```bash
# Core application
git add app/
git add static/
git add scripts/

# Configuration
git add render.yaml
git add .github/workflows/
git add .gitignore

# Documentation
git add README.md
git add DEPLOYMENT_GUIDE.md

# Dependencies
git add requirements.txt
```

### Do NOT Commit

```bash
# Secrets
DO NOT commit .env files
DO NOT commit API keys
DO NOT commit credentials

# Large files
DO NOT commit data/*.npz (add to .gitignore)
DO NOT commit data/*.gz (add to .gitignore)

# Generated
DO NOT commit __pycache__
DO NOT commit .pytest_cache
DO NOT commit venv/
```

---

## ✅ Success Criteria

After deployment, verify:

```
✅ Service deployed successfully
✅ URL accessible and responsive
✅ /health endpoint works
✅ /api/code?q=test returns results
✅ AI analysis working (if enabled)
✅ No errors in logs
✅ Performance acceptable (<500ms)
```

---

## 🎉 You're Live!

Your MedDRA Coding Assistant is now running on Render:

```
🌐 https://meddra-coding-xxxx.onrender.com
📊 Metrics: Service → Metrics
📝 Logs: Service → Logs
⚙️ Settings: Service → Settings
```

**Total setup time:** ~15 minutes  
**Cost:** $0-7/month depending on plan  
**Uptime:** 99.9% with Starter plan

---

*Version: 2.0 | Provider: Render.com | Status: Production-Ready ✅*

# Render Environment Variables Checklist

## ✅ AUTO-CONFIGURED (Working Automatically)

These are set automatically by Render via `render.yaml`:

### Database (PostgreSQL) - `verifywise-database`
- ✅ `DB_HOST` - Auto-set from database
- ✅ `DB_USER` - Auto-set from database  
- ✅ `DB_PASSWORD` - Auto-set from database
- ✅ `DB_PORT` - Auto-set from database
- ✅ `DB_NAME` - Auto-set from database
- ✅ `DB_SSL` - Set to `true`

### Redis - `verifywise-redis`
- ✅ `REDIS_URL` - Auto-set from Redis service

### Backend - `verifywise-backend`
- ✅ `NODE_ENV` - Set to `production`
- ✅ `PORT` - Set to `10000`
- ✅ `HOST` - Set to `0.0.0.0`
- ✅ `DB_HOST`, `DB_USER`, `DB_PASSWORD`, `DB_PORT`, `DB_NAME` - Auto-set from database
- ✅ `DB_SSL` - Set to `true`
- ✅ `REJECT_UNAUTHORIZED` - Set to `false`
- ✅ `REDIS_URL` - Auto-set from Redis
- ✅ `JWT_SECRET` - Auto-generated
- ✅ `MOCK_DATA_ON` - Set to `false`

### Bias & Fairness Backend - `verifywise-bias-fairness`
- ✅ `PORT` - Set to `10000`
- ✅ `DB_HOST`, `DB_USER`, `DB_PASSWORD`, `DB_PORT`, `DB_NAME` - Auto-set from database
- ✅ `REDIS_URL` - Auto-set from Redis

### Frontend - `accordant-frontend` (or `verifywise-frontend`)
- ✅ `VITE_IS_DEMO_APP` - Set to `false`
- ✅ `VITE_IS_MULTI_TENANT` - Set to `true`
- ✅ `VITE_SLACK_URL` - Set to `https://slack.com/oauth/v2/authorize`
- ✅ `VITE_IS_SLACK_VISIBLE` - Set to `true`

---

## 🔴 CRITICAL - MUST SET MANUALLY (Required for Login/Register)

### Frontend Service - `accordant-frontend` (or `verifywise-frontend`)

**🔴 `VITE_APP_API_BASE_URL`** - **REQUIRED FOR LOGIN/REGISTER TO WORK**
- **What to set:** `https://verifywise-backend.onrender.com`
- **Where:** Render Dashboard → `accordant-frontend` service → Environment tab
- **Why:** Without this, the frontend can't connect to the backend API
- **⚠️ IMPORTANT:** After setting this, you MUST rebuild the frontend (VITE_* vars are baked at build time)
  - Go to "Manual Deploy" → "Deploy latest commit"

### Backend Service - `verifywise-backend`

**🔴 `ALLOWED_ORIGINS`** - **REQUIRED FOR CORS (Login/Register will fail without this)**
- **What to set:** Your frontend URL(s), comma-separated if multiple
  - Example: `https://accordant-frontend-XXXX.onrender.com`
  - Or if renamed: `https://verifywise-frontend-n4bv.onrender.com`
- **Where:** Render Dashboard → `verifywise-backend` service → Environment tab
- **Why:** Backend blocks requests from unknown origins (CORS). Without this, frontend requests will be rejected
- **After setting:** Restart the backend service

---

## 🟡 RECOMMENDED - Should Set (For Full Functionality)

### Backend Service - `verifywise-backend`

**🟡 `FAIRNESS_AND_BIAS_URL`** - Optional but recommended
- **What to set:** `https://verifywise-bias-fairness.onrender.com`
- **Where:** Render Dashboard → `verifywise-backend` service → Environment tab
- **Why:** Needed if you use the bias/fairness features

**🟡 Email Configuration** - Required for email features (password reset, notifications)
- Choose ONE option:

**Option A: Resend (Recommended)**
```
EMAIL_PROVIDER=resend
RESEND_API_KEY=re_your_api_key_here
EMAIL_ID=noreply@yourdomain.com
```

**Option B: SMTP**
```
EMAIL_PROVIDER=smtp
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your-email@gmail.com
SMTP_PASS=your-app-password
SMTP_SECURE=true
EMAIL_ID=noreply@yourdomain.com
```

### Bias & Fairness Backend - `verifywise-bias-fairness`

**🟡 `BACKEND_URL`** - Recommended
- **What to set:** `https://verifywise-backend.onrender.com`
- **Where:** Render Dashboard → `verifywise-bias-fairness` service → Environment tab

### Frontend Service - `accordant-frontend`

**🟡 `VITE_SLACK_CLIENT_ID`** - Optional (only if using Slack integration)
- **What to set:** Your Slack app client ID
- **Where:** Render Dashboard → `accordant-frontend` service → Environment tab

**🟡 `VITE_BRAND_NAME`** - Should update to "AccordantAI"
- **Current:** `VerifyWise` (in render.yaml)
- **Should be:** `AccordantAI`
- **Where:** Update in `render.yaml` OR set in Render Dashboard

---

## 📋 QUICK FIX CHECKLIST

To get login/register working immediately:

1. ✅ **Set Frontend API URL:**
   - Go to Render Dashboard → Frontend service → Environment
   - Add: `VITE_APP_API_BASE_URL` = `https://verifywise-backend.onrender.com`
   - Save → This triggers a rebuild (wait for it to complete)

2. ✅ **Set Backend CORS:**
   - Go to Render Dashboard → Backend service → Environment
   - Add: `ALLOWED_ORIGINS` = Your frontend URL (e.g., `https://accordant-frontend-XXXX.onrender.com`)
   - Save → Restart the service

3. ✅ **Verify Backend is Running:**
   - Visit: `https://verifywise-backend.onrender.com/api/docs`
   - Should show Swagger documentation

4. ✅ **Test Login:**
   - Go to your frontend
   - Try logging in or registering
   - Check browser console (F12) for any errors

---

## 🔍 HOW TO CHECK WHAT'S SET IN RENDER

1. Go to Render Dashboard
2. Click on each service
3. Go to "Environment" tab
4. Review all environment variables listed there

---

## 🐛 TROUBLESHOOTING

**Login/Register doesn't work:**
1. ✅ Check `VITE_APP_API_BASE_URL` is set in frontend (must rebuild after setting)
2. ✅ Check `ALLOWED_ORIGINS` is set in backend (must restart after setting)
3. ✅ Check backend service is running (check logs)
4. ✅ Check browser console for errors (F12 → Console tab)
5. ✅ Check network tab (F12 → Network tab) to see if API calls are being made

**Frontend shows "Cannot connect to API":**
- `VITE_APP_API_BASE_URL` is not set or incorrect
- Frontend was not rebuilt after setting the variable

**Backend returns CORS errors:**
- `ALLOWED_ORIGINS` is not set or doesn't match your frontend URL exactly
- Backend was not restarted after setting the variable


# Deployment Guide

## Railway Deployment

### Environment Variables Required

```env
POSTGRES_HOST=containers-us-west-xxx.railway.app
POSTGRES_DB=railway
POSTGRES_USER=postgres
POSTGRES_PASSWORD=your_password_here
FIREBASE_CREDENTIALS={"type":"service_account","project_id":"..."}
```

### Get DATABASE_URL

Railway Dashboard → PostgreSQL → Variables → Copy `DATABASE_URL`

**Format:**
```
postgresql://postgres:password@host:5432/railway
```

**Extract values:**
- `POSTGRES_HOST` = host part
- `POSTGRES_USER` = postgres
- `POSTGRES_PASSWORD` = password part
- `POSTGRES_DB` = railway

### Deploy Steps

1. **Initialize database:**
   ```bash
   psql "DATABASE_URL" < init.sql
   ```

2. **Push to GitHub:**
   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git remote add origin https://github.com/YOUR_USERNAME/frizzly-railway-sync.git
   git push -u origin main
   ```

3. **Deploy on Railway:**
   - New → GitHub Repo
   - Select repository
   - Add environment variables
   - Deploy

4. **Verify:**
   - Check logs for "✅ Sync service running!"
   - Test by creating order in Firebase

### Troubleshooting

**Connection refused:**
- Check POSTGRES_HOST is correct
- Verify PostgreSQL is running

**Permission denied:**
- Check FIREBASE_CREDENTIALS is valid JSON
- Verify Firestore permissions

**No data syncing:**
- Check Firebase collections exist
- Verify initial sync completed in logs

### Cost

- Sync Service: ~$0.50/month
- PostgreSQL: ~$0.67/month (separate service)
- **Total: ~$1.17/month**

Free for 4+ months with $5 Railway credit.

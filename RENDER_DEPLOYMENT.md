# Render Deployment Guide

This guide will help you deploy your Next.js application to Render.

## Changes Made for Render Deployment

### 1. Fixed MongoDB Version Conflict
- Downgraded `mongodb` from `^7.0.0` to `^5.9.2` to be compatible with `@next-auth/mongodb-adapter@1.1.3`
- Downgraded `react` and `react-dom` from `^19` to `^18.2.0` to be compatible with Next.js 14.2.25

### 2. Custom Server Configuration
- Created `server.js` to ensure the app binds to `0.0.0.0` instead of `localhost`
- The server now uses the `PORT` environment variable provided by Render
- Updated `package.json` scripts to use the custom server

### 3. Node.js Version Specification
- Created `.node-version` and `.nvmrc` files specifying Node.js `22.16.0`
- Created `render.yaml` for automated deployment configuration

## Environment Variables

Make sure to set these environment variables in your Render dashboard:

### Required Variables:
```bash
NODE_ENV=production
NEXTAUTH_URL=https://your-app-name.onrender.com
NEXTAUTH_SECRET=your-secure-secret-here
MONGO_URI=your-mongodb-connection-string
GOOGLE_CLIENT_ID=your-google-client-id
GOOGLE_CLIENT_SECRET=your-google-client-secret
YOUTUBE_CLIENT_ID=your-youtube-client-id
YOUTUBE_CLIENT_SECRET=your-youtube-client-secret
YOUTUBE_API_KEY=your-youtube-api-key
JWT_SECRET=your-jwt-secret
```

### Important Notes:
- **NEXTAUTH_URL**: Update this to your Render app URL (e.g., `https://yt-ai.onrender.com`)
- **NEXTAUTH_SECRET**: Generate a secure random string (you can use `openssl rand -base64 32`)
- **JWT_SECRET**: Should be a secure random string
- Don't include `PORT` - Render sets this automatically

## Deployment Steps

### Option 1: Using Render Dashboard (Recommended)

1. **Create a New Web Service**
   - Go to [Render Dashboard](https://dashboard.render.com/)
   - Click "New +" → "Web Service"
   - Connect your GitHub repository

2. **Configure the Service**
   - **Name**: `yt-ai` (or your preferred name)
   - **Region**: Choose the closest to your users
   - **Branch**: `main`
   - **Root Directory**: Leave empty
   - **Environment**: `Node`
   - **Build Command**: `npm install && npm run build`
   - **Start Command**: `npm start`
   - **Plan**: Choose your plan (Free tier available)

3. **Set Environment Variables**
   - Go to "Environment" tab
   - Add all the environment variables listed above
   - **Important**: Update `NEXTAUTH_URL` to your Render URL after deployment

4. **Deploy**
   - Click "Create Web Service"
   - Wait for the build and deployment to complete
   - Your app will be available at `https://your-app-name.onrender.com`

### Option 2: Using render.yaml (Infrastructure as Code)

1. **Push the render.yaml file to your repository**
   ```bash
   git add render.yaml .node-version .nvmrc server.js
   git commit -m "Add Render deployment configuration"
   git push
   ```

2. **Create a Blueprint Instance**
   - Go to [Render Dashboard](https://dashboard.render.com/)
   - Click "New +" → "Blueprint"
   - Connect your repository
   - Render will automatically use the `render.yaml` configuration

3. **Add Environment Variables**
   - After creation, go to your web service
   - Navigate to "Environment" tab
   - Add all required environment variables

## Post-Deployment

### 1. Update Google OAuth Redirect URIs
Add your Render URL to Google Cloud Console:
- Authorized JavaScript origins: `https://your-app-name.onrender.com`
- Authorized redirect URIs: `https://your-app-name.onrender.com/api/auth/callback/google`

### 2. Update YouTube API Redirect URIs
Add your Render URL to Google Cloud Console:
- Authorized redirect URIs: `https://your-app-name.onrender.com/api/youtube/auth`

### 3. Test the Deployment
- Visit your app URL
- Test authentication flows
- Check MongoDB connections
- Verify all features work correctly

## Troubleshooting

### Build Fails with Dependency Errors
- Check that you're using Node.js 22.16.0
- Clear build cache in Render dashboard
- Verify all dependencies in `package.json` are correct

### "No open HTTP ports detected" Error
- This should now be fixed with the custom `server.js`
- The server binds to `0.0.0.0` and uses Render's PORT variable
- Check deployment logs for any startup errors

### Authentication Issues
- Verify `NEXTAUTH_URL` matches your Render app URL
- Ensure `NEXTAUTH_SECRET` and `JWT_SECRET` are set
- Check Google OAuth redirect URIs are correctly configured

### MongoDB Connection Issues
- Verify `MONGO_URI` is correct
- Ensure MongoDB Atlas allows connections from Render's IP (use 0.0.0.0/0 or Render's IPs)
- Check MongoDB Atlas user permissions

## Logs and Monitoring

Access logs in Render Dashboard:
1. Go to your web service
2. Click "Logs" tab
3. View real-time logs for debugging

## Free Tier Limitations

Render's free tier:
- Spins down after 15 minutes of inactivity
- Takes ~30 seconds to spin up on first request
- 750 hours/month free (enough for one always-on service)

Consider upgrading to a paid plan for production apps.

## Updates and Redeployment

Render automatically redeploys when you push to the connected branch:
```bash
git add .
git commit -m "Your changes"
git push
```

Manual redeploy:
- Go to Render Dashboard → Your service → "Manual Deploy" → "Deploy latest commit"

---

## Quick Checklist

- [ ] MongoDB version downgraded to 5.9.2
- [ ] React version downgraded to 18.2.0
- [ ] Custom server.js created
- [ ] Environment variables set in Render
- [ ] NEXTAUTH_URL updated to Render URL
- [ ] Google OAuth redirect URIs updated
- [ ] YouTube API redirect URIs updated
- [ ] MongoDB Atlas IP whitelist updated
- [ ] Deployment successful
- [ ] Authentication tested
- [ ] All features working

---

For more help, check [Render's Node.js documentation](https://render.com/docs/deploy-node-express-app).

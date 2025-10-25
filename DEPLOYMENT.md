# Deployment Guide

## Frontend Deployment (Vercel/Netlify)

### Vercel
1. Connect GitHub repository to Vercel
2. Set build settings:
   - **Build Command**: `cd client && npm run build`
   - **Output Directory**: `client/dist`
3. Add environment variable:
   - `VITE_API_URL`: Your backend API URL

### Netlify
1. Connect GitHub repository to Netlify
2. Set build settings:
   - **Build Command**: `cd client && npm run build`
   - **Publish Directory**: `client/dist`
3. Add environment variable:
   - `VITE_API_URL`: Your backend API URL

## Backend Deployment (Railway/Render/Heroku)

### Railway
1. Connect GitHub repository to Railway
2. Set environment variables:
   ```
   PORT=5000
   MONGODB_URI=your_mongodb_atlas_connection_string
   JWT_SECRET=your_secure_jwt_secret
   NODE_ENV=production
   ```
3. Railway will auto-detect Node.js and deploy

### Render
1. Connect GitHub repository to Render
2. Set build settings:
   - **Build Command**: `cd server && npm install`
   - **Start Command**: `cd server && npm start`
3. Add environment variables (same as above)

### Heroku
1. Install Heroku CLI
2. Create Heroku app: `heroku create your-app-name`
3. Set environment variables:
   ```bash
   heroku config:set MONGODB_URI=your_connection_string
   heroku config:set JWT_SECRET=your_secret
   heroku config:set NODE_ENV=production
   ```
4. Deploy: `git push heroku main`

## Database Setup (MongoDB Atlas)

1. Create MongoDB Atlas account
2. Create cluster (free tier available)
3. Create database user
4. Get connection string
5. Update MONGODB_URI in environment variables

## Environment Variables

### Production Frontend
```
VITE_API_URL=https://your-backend-url.com/api
```

### Production Backend
```
PORT=5000
MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/hybrid-tracker
JWT_SECRET=your_very_secure_jwt_secret_key_for_production
NODE_ENV=production
```

## Post-Deployment

1. Run seed script to create demo users:
   ```bash
   npm run seed
   ```

2. Test with demo credentials:
   - Admin: admin@demo.com / admin123
   - Bot: bot@demo.com / bot123
   - Applicant: user@demo.com / user123

## CORS Configuration

Make sure your backend allows requests from your frontend domain in production.
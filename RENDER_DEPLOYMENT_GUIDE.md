# 🚀 Render Deployment Guide for CPF Project

This guide will help you deploy your MBA Career Assessment Platform to Render with both backend and frontend services.

## 📋 Prerequisites

- A [Render account](https://render.com) (free tier available)
- A [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) account (free tier available)
- Your project code pushed to a Git repository (GitHub, GitLab, or Bitbucket)

---

## 🔧 Pre-Deployment Setup

### 1. Update Backend for Environment Variables

Your backend is currently using hardcoded values. Update `backend/server.js`:

**Replace:**
```javascript
const JWT_SECRET = 'choosekonguengineeringcollegeforbestfuture';
const MONGODB_URI = 'mongodb+srv://nisar:nisar%402004@cluster0.7q9px.mongodb.net/CPF?appName=Cluster0';
```

**With:**
```javascript
const JWT_SECRET = process.env.JWT_SECRET || 'choosekonguengineeringcollegeforbestfuture';
const MONGODB_URI = process.env.MONGODB_URI || 'mongodb+srv://nisar:nisar%402004@cluster0.7q9px.mongodb.net/CPF?appName=Cluster0';
const PORT = process.env.PORT || 5000;
```

**And update the server listen section:**
```javascript
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

### 2. Update Frontend API URL

Update `frontend/src/App.js` to use environment variable:

**Replace:**
```javascript
const API_URL = 'https://cpf-topaz.vercel.app/api';
```

**With:**
```javascript
const API_URL = process.env.REACT_APP_API_URL || 'https://cpf-topaz.vercel.app/api';
```

### 3. Create Environment Files (Optional for Local Development)

Create a `.env` file in the root directory for local testing:
```
MONGODB_URI=your_mongodb_connection_string
JWT_SECRET=your_secret_key
PORT=5000
REACT_APP_API_URL=http://localhost:5000/api
```

**⚠️ Important:** Add `.env` to your `.gitignore` file to prevent committing sensitive data.

---

## 🌐 MongoDB Atlas Setup

1. Go to [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)
2. Create a free cluster (if you haven't already)
3. Go to **Database Access** → Create a database user with password
4. Go to **Network Access** → Add IP Address → **Allow Access from Anywhere** (0.0.0.0/0)
5. Go to **Database** → **Connect** → **Connect your application**
6. Copy the connection string (it looks like: `mongodb+srv://username:<password>@cluster.mongodb.net/`)
7. Replace `<password>` with your actual password
8. Add your database name at the end: `mongodb+srv://username:password@cluster.mongodb.net/CPF`

---

## 🚀 Deploying to Render

### Method 1: Using Blueprint (Recommended - Automatic Setup)

1. **Push to Git Repository**
   - Ensure all your code is committed and pushed to GitHub/GitLab/Bitbucket
   - Make sure `render.yaml` is in the root directory

2. **Create New Blueprint on Render**
   - Log in to [Render Dashboard](https://dashboard.render.com)
   - Click **"New +"** → **"Blueprint"**
   - Connect your Git repository
   - Render will automatically detect `render.yaml` and set up both services

3. **Configure Environment Variables**
   - After blueprint creation, go to each service
   - For **cpf-backend**:
     - Set `MONGODB_URI` to your MongoDB Atlas connection string
     - `JWT_SECRET` will be auto-generated (or set your own)
     - `PORT` is already set to 10000

4. **Deploy**
   - Render will automatically deploy both services
   - Wait for both builds to complete (5-10 minutes)

### Method 2: Manual Service Creation

#### A. Deploy Backend Service

1. **Create Web Service**
   - Go to [Render Dashboard](https://dashboard.render.com)
   - Click **"New +"** → **"Web Service"**
   - Connect your repository
   - Configure:
     - **Name:** `cpf-backend` (or your preferred name)
     - **Region:** Oregon (US West) or closest to you
     - **Branch:** `main` (or your default branch)
     - **Root Directory:** `backend`
     - **Environment:** `Node`
     - **Build Command:** `npm install`
     - **Start Command:** `npm start`
     - **Plan:** Free

2. **Add Environment Variables**
   - Click **"Advanced"** → **"Add Environment Variable"**
   - Add these variables:
     ```
     MONGODB_URI = your_mongodb_connection_string
     JWT_SECRET = your_secret_jwt_key_here
     PORT = 10000
     NODE_ENV = production
     ```

3. **Deploy Backend**
   - Click **"Create Web Service"**
   - Wait for deployment (5-10 minutes)
   - Copy the backend URL (e.g., `https://cpf-backend.onrender.com`)

#### B. Deploy Frontend Service

1. **Create Static Site**
   - Click **"New +"** → **"Static Site"**
   - Connect the same repository
   - Configure:
     - **Name:** `cpf-frontend`
     - **Branch:** `main`
     - **Root Directory:** `frontend`
     - **Build Command:** `npm install && npm run build`
     - **Publish Directory:** `build`

2. **Add Environment Variable**
   - Click **"Advanced"** → **"Add Environment Variable"**
   - Add:
     ```
     REACT_APP_API_URL = https://your-backend-url.onrender.com/api
     ```
     Replace with your actual backend URL from step A.3

3. **Configure Redirects/Rewrites**
   - Add a `_redirects` file in `frontend/public/`:
     ```
     /*    /index.html   200
     ```

4. **Deploy Frontend**
   - Click **"Create Static Site"**
   - Wait for build and deployment

---

## ✅ Post-Deployment Steps

### 1. Test Your Application

- Visit your frontend URL: `https://cpf-frontend.onrender.com`
- Try logging in with existing credentials
- Verify all features work correctly

### 2. Seed Database (If Needed)

If you need to seed questions:

1. Go to your backend service on Render
2. Click **"Shell"** tab
3. Run: `node seedQuestions.js`

### 3. Set Up Custom Domain (Optional)

1. Go to your frontend service settings
2. Click **"Custom Domain"**
3. Follow instructions to add your domain

---

## 🔄 Automatic Deployments

Render automatically redeploys your services when you push changes to your connected Git branch:

- Push to `main` branch → Automatic deployment
- View deployment logs in the Render dashboard

---

## 📊 Monitoring

### Check Service Health

- **Dashboard:** Monitor both services in the Render dashboard
- **Logs:** Click on each service to view real-time logs
- **Metrics:** View CPU, memory usage, and request metrics

### Free Tier Limitations

- Services spin down after 15 minutes of inactivity
- First request after spin down may take 30-60 seconds
- 750 hours/month free (enough for one service)

---

## 🐛 Troubleshooting

### Backend Won't Start

1. **Check Environment Variables**
   - Verify `MONGODB_URI` is correct
   - Ensure MongoDB Atlas allows connections from anywhere (0.0.0.0/0)

2. **Check Logs**
   - Go to backend service → **Logs**
   - Look for connection errors or missing dependencies

3. **Common Issues:**
   - MongoDB connection timeout → Check Network Access in Atlas
   - Port already in use → Render uses `PORT` env variable automatically
   - Missing dependencies → Clear cache and rebuild

### Frontend Build Fails

1. **Check Build Logs**
   - Look for npm install errors
   - Verify all dependencies are in `package.json`

2. **API URL Issues**
   - Ensure `REACT_APP_API_URL` is set correctly
   - Must include `/api` suffix
   - Must use `https://` for production

3. **Routing Issues**
   - Add `_redirects` file to `frontend/public/`
   - Content: `/*    /index.html   200`

### CORS Errors

If you see CORS errors in browser console:

1. Verify backend CORS configuration in `server.js`
2. Ensure frontend URL is allowed in CORS settings
3. Update CORS to allow your Render frontend domain

---

## 💰 Cost Estimate

**Free Tier Usage:**
- Backend: Free (with spin down)
- Frontend: Free
- MongoDB Atlas: Free (512MB storage)

**Paid Tier (Optional):**
- Backend: $7/month (always on)
- Frontend: Free
- MongoDB Atlas: Free tier is usually sufficient

---

## 🔐 Security Best Practices

1. ✅ Use environment variables for sensitive data
2. ✅ Never commit `.env` files to Git
3. ✅ Use strong JWT secrets (auto-generated on Render)
4. ✅ Enable MongoDB authentication
5. ✅ Restrict MongoDB network access when possible
6. ✅ Use HTTPS (automatic on Render)

---

## 📚 Additional Resources

- [Render Documentation](https://render.com/docs)
- [Render Node.js Guide](https://render.com/docs/deploy-node-express-app)
- [Render Static Sites](https://render.com/docs/static-sites)
- [MongoDB Atlas Docs](https://docs.atlas.mongodb.com/)

---

## 📞 Support

- **Render Support:** [https://render.com/docs/support](https://render.com/docs/support)
- **Community:** [Render Community Forum](https://community.render.com/)

---

## 🎉 Congratulations!

Your MBA Career Assessment Platform is now live on Render! 🚀

**Your URLs:**
- Frontend: `https://cpf-frontend.onrender.com`
- Backend: `https://cpf-backend.onrender.com`

Share your application and start helping students discover their ideal career paths!

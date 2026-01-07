# Pastebin - Text Sharing Application

A modern web application for sharing text snippets with customizable expiration options. Built with Next.js, TypeScript, and MongoDB.

## Features Implemented

### Core Features
- **Text Sharing**: Share text content through unique, shareable links
- **Expiration by Views**: Set links to expire after a specific number of views
- **Expiration by Time**: Set links to expire after a certain number of hours
- **Auto-deletion**: Expired texts are automatically removed from the database
- **View Tracking**: Real-time tracking of how many times a link has been viewed
- **Copy to Clipboard**: One-click link copying functionality
- **Responsive Design**: Works seamlessly on desktop, tablet, and mobile devices

### Technical Features
- **Server-Side Rendering**: Built with Next.js 14 App Router
- **TypeScript**: Full type safety across the application
- **MongoDB Integration**: Efficient data storage with Mongoose ODM
- **RESTful API**: Clean API endpoints for creating and retrieving texts
- **Error Handling**: Comprehensive error handling for expired/missing content
- **Modern UI**: Clean, student-friendly interface with emojis and intuitive design

## Workflow

### Creating a Shared Text
1. User visits the home page
2. Pastes or types text into the input field
3. Selects expiration type:
   - **By Views**: Link expires after X number of views
   - **By Time**: Link expires after X hours
4. Clicks "Generate Link" button
5. Receives a unique shareable URL
6. Copies and shares the link with others

### Viewing a Shared Text
1. Recipient clicks on the shared link
2. System checks if the text is still valid:
   - Checks if expired by time
   - Checks if maximum views reached
3. If valid:
   - Displays the text content
   - Increments view counter
   - Shows remaining views (if applicable)
4. If expired:
   - Shows "Text Not Available" error
   - Automatically deletes the text from database

### Database Workflow
```
[User Input] → [API Route] → [MongoDB] → [Generate ID] → [Return Link]
                    ↓
            [Store Document with:
             - text content
             - expiration type
             - max views / expiry time
             - current view count]

[View Request] → [API Route] → [MongoDB Lookup] → [Check Expiration]
                                      ↓
                              [Increment Views]
                                      ↓
                              [Return Content or Error]
```

## 🛠️ Setup Guide

### Prerequisites
- Node.js 18+ installed
- MongoDB Atlas account (free tier) OR local MongoDB installation
- Git installed

### Step 1: Clone the Repository
```bash
git clone https://github.com/yourusername/pastebin.git
cd pastebin
```

### Step 2: Install Dependencies
```bash
npm install
```

### Step 3: Setup MongoDB

#### Option A: MongoDB Atlas (Recommended)
1. Go to [mongodb.com/cloud/atlas](https://www.mongodb.com/cloud/atlas)
2. Create a free account
3. Create a new cluster (choose free tier)
4. Click "Connect" → "Drivers"
5. Copy your connection string
6. Go to "Network Access" → "Add IP Address" → "Allow Access from Anywhere" (0.0.0.0/0)

### Step 4: Configure Environment Variables
Create a `.env.local` file in the root directory:

```env
# For MongoDB Atlas
MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/pastebin


Replace `username`, `password`, and `cluster` with your actual MongoDB credentials.

### Step 5: Create Required Files
Ensure you have `global.d.ts` in the root:

```typescript
declare global {
  var mongoose: {
    conn: any;
    promise: any;
  };
}

export {};
```

### Step 6: Run the Development Server
```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) in your browser.

### Step 7: Build for Production (Optional)
```bash
npm run build
npm start
```

## 📁 Project Structure

```
pastebin/
├── src/
│   ├── app/
│   │   ├── api/
│   │   │   └── text/
│   │   │       ├── route.ts           # POST endpoint for creating texts
│   │   │       └── [id]/
│   │   │           └── route.ts       # GET endpoint for retrieving texts
│   │   ├── view/
│   │   │   └── [id]/
│   │   │       └── page.tsx           # View page for shared texts
│   │   ├── globals.css                # Global styles
│   │   ├── layout.tsx                 # Root layout
│   │   └── page.tsx                   # Home page
│   ├── lib/
│   │   └── mongodb.ts                 # MongoDB connection logic
│   └── models/
│       └── Text.ts                    # Mongoose schema for texts
├── .env.local                         # Environment variables (create this)
├── global.d.ts                        # TypeScript global declarations
├── package.json                       # Dependencies
├── tsconfig.json                      # TypeScript configuration
├── next.config.js                     # Next.js configuration
├── tailwind.config.js                 # Tailwind CSS configuration
└── postcss.config.js                  # PostCSS configuration
```

## 🚀 Deployment

### Deploy to Vercel
1. Push your code to GitHub
2. Go to [vercel.com](https://vercel.com)
3. Import your GitHub repository
4. Add environment variable:
   - Key: `MONGODB_URI`
   - Value: Your MongoDB connection string
5. Deploy

### Deploy with Docker
1. Build the Docker image:
   ```bash
   docker build -t pastebin .
   ```

2. Run the container:
   ```bash
   docker run -p 3000:3000 -e MONGODB_URI="your_connection_string" pastebin
   ```

## 🔧 Technologies Used

- **Frontend**: Next.js 14, React 18, TypeScript
- **Styling**: Tailwind CSS
- **Backend**: Next.js API Routes
- **Database**: MongoDB with Mongoose ODM

## 📝 API Endpoints

### Create Text
```http
POST /api/text
Content-Type: application/json

{
  "text": "Your text content",
  "expirationType": "views" | "time",
  "expirationValue": 10
}

Response: { "id": "unique_text_id" }
```

### Get Text
```http
GET /api/text/[id]

Response: {
  "text": "Text content",
  "views": 5,
  "maxViews": 10,
  "expiresAt": "2026-01-08T10:00:00.000Z",
  "isLastView": false
}
```
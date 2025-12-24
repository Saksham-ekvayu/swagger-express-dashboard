# Migration Guide: From Integrated to Standalone Package

This guide will help you migrate from your current integrated Swagger implementation to the standalone `swagger-express-dashboard` package.

## 🔄 Migration Steps

### Step 1: Install the Package

```bash
npm install swagger-express-dashboard
```

### Step 2: Remove Old Implementation

Remove these files from your current project:

- `src/utils/listRoutes.js`
- `src/utils/routeRegistry.js`
- `public/api-dashboard.html`

### Step 3: Update Your Main Server File

**Before (your current implementation):**

```javascript
const express = require("express");
const path = require("path");
const { registerRoutes } = require("./src/utils/routeRegistry");
const listRoutes = require("./src/utils/listRoutes");

const app = express();

// Your routes
app.use("/api/auth", authRoutes);
registerRoutes("/api/auth", authRoutes);

app.use("/api/user", userRoutes);
registerRoutes("/api/user", userRoutes);

// Dashboard endpoints
app.get("/apis", (req, res) => {
  const routes = listRoutes(app);
  res.json({ apis: routes });
});

app.get("/", (req, res) => {
  res.sendFile(path.join(__dirname, "public", "api-dashboard.html"));
});
```

**After (using the package):**

```javascript
const express = require("express");
const SwaggerExpressDashboard = require("swagger-express-dashboard");

const app = express();

// Initialize dashboard
const dashboard = new SwaggerExpressDashboard({
  title: "Cypher Sentinel API",
  description: "Backend API Control Dashboard",
  version: "1.0.0",
  basePath: "/api-docs", // or keep '/' if you want it at root
  enableAuth: true,
  enableTesting: true,
  controllersPath: "./src/controllers",
});

// Your routes (no changes needed)
app.use("/api/auth", authRoutes);
app.use("/api/user", userRoutes);

// Register routes with dashboard for better documentation
dashboard.registerRoutes("/api/auth", authRoutes);
dashboard.registerRoutes("/api/user", userRoutes);

// Initialize dashboard (replaces your old endpoints)
dashboard.init(app);
```

### Step 4: Update Route Imports

**Before:**

```javascript
const { registerRoutes } = require("./src/utils/routeRegistry");
```

**After:**

```javascript
// No longer needed - remove this import
```

### Step 5: Update Package.json Scripts (Optional)

Add a script to easily run the example:

```json
{
  "scripts": {
    "dev": "nodemon index.js",
    "docs": "node examples/basic-usage.js"
  }
}
```

## 🎯 Configuration Mapping

Map your current configuration to the new package options:

| Current Feature  | New Configuration                      |
| ---------------- | -------------------------------------- |
| Dashboard title  | `title: 'Your Title'`                  |
| Auto-detection   | `autoDetectControllers: true`          |
| Controllers path | `controllersPath: './src/controllers'` |
| Authentication   | `enableAuth: true`                     |
| API testing      | `enableTesting: true`                  |
| Custom styling   | `customCSS: 'your-css'`                |

## 🔧 Advanced Configuration

### Custom Dashboard Path

If you want to keep the dashboard at the root path (`/`):

```javascript
const dashboard = new SwaggerExpressDashboard({
  basePath: "/", // Dashboard at root
  // ... other options
});
```

### Disable Features

If you want to disable certain features:

```javascript
const dashboard = new SwaggerExpressDashboard({
  enableTesting: false, // Disable API testing
  enableAuth: false, // Disable authentication features
  // ... other options
});
```

### Custom Styling

To maintain your current cyberpunk theme:

```javascript
const dashboard = new SwaggerExpressDashboard({
  theme: "dark",
  customCSS: `
    .title {
      background: linear-gradient(45deg, #00ff41, #00d4ff);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
    }
  `,
  // ... other options
});
```

## 📁 File Structure Changes

**Before:**

```
your-project/
├── src/
│   ├── utils/
│   │   ├── listRoutes.js      ❌ Remove
│   │   └── routeRegistry.js   ❌ Remove
│   └── controllers/
├── public/
│   └── api-dashboard.html     ❌ Remove
└── index.js
```

**After:**

```
your-project/
├── src/
│   └── controllers/           ✅ Keep (used for auto-detection)
├── node_modules/
│   └── swagger-express-dashboard/  ✅ New package
└── index.js                   ✅ Updated
```

## 🚀 Testing the Migration

1. **Start your server:**

   ```bash
   npm run dev
   ```

2. **Access the dashboard:**

   - If `basePath: '/api-docs'`: `http://localhost:3000/api-docs`
   - If `basePath: '/'`: `http://localhost:3000/`

3. **Verify functionality:**
   - ✅ All APIs are listed
   - ✅ API testing works
   - ✅ Authentication token management works
   - ✅ Auto-detection of request schemas works

## 🐛 Troubleshooting

### APIs Not Showing

**Problem:** No APIs appear in the dashboard.

**Solution:** Make sure you're calling `dashboard.registerRoutes()` for each router:

```javascript
// Register each router
dashboard.registerRoutes("/api/auth", authRoutes);
dashboard.registerRoutes("/api/user", userRoutes);

// Then initialize
dashboard.init(app);
```

### Auto-detection Not Working

**Problem:** Request body schemas are not auto-detected.

**Solution:** Check your `controllersPath` configuration:

```javascript
const dashboard = new SwaggerExpressDashboard({
  controllersPath: "./src/controllers", // Correct path to your controllers
  autoDetectControllers: true,
});
```

### Styling Issues

**Problem:** Dashboard doesn't look right.

**Solution:** Check for CSS conflicts and use custom CSS:

```javascript
const dashboard = new SwaggerExpressDashboard({
  customCSS: `
    /* Override any conflicting styles */
    .api-table { z-index: 1000; }
  `,
});
```

### Authentication Not Working

**Problem:** Token management doesn't work.

**Solution:** Ensure authentication is enabled:

```javascript
const dashboard = new SwaggerExpressDashboard({
  enableAuth: true, // Make sure this is true
});
```

## 📈 Benefits of Migration

✅ **Maintainability**: Package updates automatically  
✅ **Reusability**: Use in multiple projects  
✅ **Features**: New features added regularly  
✅ **Support**: Community support and documentation  
✅ **Testing**: Thoroughly tested package  
✅ **Performance**: Optimized implementation

## 🔄 Rollback Plan

If you need to rollback:

1. Keep your old files in a backup folder
2. Uninstall the package: `npm uninstall swagger-express-dashboard`
3. Restore your old files
4. Revert your `index.js` changes

## 📞 Need Help?

- Check the [README.md](./README.md) for detailed documentation
- Look at [examples/](./examples/) for implementation examples
- Open an issue on GitHub for specific problems

---

**Migration Complete!** 🎉

Your Swagger dashboard is now powered by a reusable, maintainable package that you can use across multiple projects.

# Frontend CI/CD Pipeline Documentation

This document explains the CI/CD pipeline setup for the ShowGo Merchant Frontend (Expo/React Native app).

## Overview

The CI/CD pipeline includes:
- **Linting & Type Checking**: ESLint + TypeScript validation on every push/PR
- **Automated Builds**: EAS Build for Android APK generation
- **Test Execution**: Jest or custom test runners
- **Build Artifacts**: Automated upload to GitHub Actions Artifacts
- **Notifications**: Build status reporting

## Workflows

### 1. **ci-cd.yml** - Main CI/CD Pipeline
Runs on every push to `main` and `develop` branches, plus all pull requests.

**Jobs:**
- `lint-and-type-check`: Validates code quality
- `build-preview`: Builds Android APK on `develop` branch
- `build-production`: Builds Android APK on `main` branch
- `test`: Runs unit/integration tests
- `notify`: Reports build status

### 2. **mobile-build.yml** - Mobile Build & Deployment
Manual workflow trigger for advanced builds. Supports both Android and iOS builds.

**Features:**
- Manual trigger with selectable build profile
- iOS build support (macOS runner)
- Artifact retention control
- Build health checks

## Setup Instructions

### Prerequisites

1. **Expo Account**: Create or log in at https://expo.dev
2. **EAS CLI**: Already configured in the project
3. **GitHub Secrets**: Add the following to your repository:

   ```
   EXPO_TOKEN: <your-expo-token>
   ```

### Step 1: Generate Expo Token

```bash
# Login to Expo
eas login

# Generate token (Settings > Personal > Authentication)
# Copy the token to GitHub Secrets
```

### Step 2: Add GitHub Secret

1. Go to **Settings** > **Secrets and variables** > **Actions**
2. Click **New repository secret**
3. Name: `EXPO_TOKEN`
4. Value: Your Expo token from Step 1
5. Click **Add secret**

### Step 3: Configure eas.json (Already Done)

Your `eas.json` already has profiles configured:
- `development`: For local testing
- `preview`: For testing builds on `develop` branch
- `production`: For release builds on `main` branch

Adjust API endpoints and build settings as needed:

```json
{
  "build": {
    "preview": {
      "env": {
        "EXPO_PUBLIC_API_URL": "http://13.222.131.179:8000"
      }
    },
    "production": {
      "autoIncrement": true,
      "env": {
        "EXPO_PUBLIC_API_URL": "http://13.222.131.179:8000"
      }
    }
  }
}
```

## Workflow Triggers

### Automatic Triggers
- **Push to `main`**: Runs linting, type check, production build
- **Push to `develop`**: Runs linting, type check, preview build
- **Pull Request**: Runs linting and type checks only

### Manual Triggers
- Go to **Actions** > **Mobile App Build & Deployment**
- Click **Run workflow**
- Select build profile: `development`, `preview`, or `production`

## Monitoring Builds

### View Build Status
1. Push code to GitHub
2. Go to **Actions** tab
3. Click on the workflow run
4. View real-time logs

### Download Artifacts
1. After workflow completes, click on the job
2. Scroll to **Artifacts** section
3. Download APK or iOS build files

## Build Profiles

### Development
- Fast builds for local testing
- Development client enabled
- Internal distribution

### Preview
- APK format for Android testing
- API points to staging backend
- Suitable for UAT and testing

### Production
- APK format optimized for Play Store
- API points to production backend
- Auto-incrementing version number
- Recommended for releases

## Troubleshooting

### Common Issues

#### 1. **Build Fails: "EXPO_TOKEN not found"**
- Ensure `EXPO_TOKEN` is added to GitHub Secrets
- Verify the token is not expired
- Re-generate token if needed

#### 2. **Build Fails: "node_modules not found"**
- Check `package.json` and `package-lock.json` are committed
- Verify npm cache is working: `npm install` locally first

#### 3. **Build Takes Too Long**
- EAS builds can take 5-15 minutes
- Check EAS dashboard: https://expo.dev/dashboard
- Use `develop` branch for faster preview builds

#### 4. **Type Errors on TypeScript Check**
Run locally and fix:
```bash
npx tsc --noEmit
npm run lint
```

## Performance Optimization

### 1. Enable Caching
The workflows already cache npm packages. To clear cache:
- Go to **Settings** > **Actions** > **General**
- Click **Clear all caches**

### 2. Concurrent Builds
By default:
- Multiple PRs can build simultaneously
- Main branch builds cancel previous runs via concurrency settings

### 3. Artifact Management
- Artifacts auto-delete after 7 days
- Adjust retention in workflow files if needed

## Next Steps

### 1. Test the Pipeline
```bash
# Make a small change and push
git add .
git commit -m "Test CI pipeline"
git push origin main
```

### 2. Monitor Build Status
- Check the Actions tab in GitHub
- Wait for build to complete

### 3. Download and Test APK
1. Go to Actions > Latest workflow
2. Download the Android APK artifact
3. Install on Android device: `adb install app.apk`

## Environment Variables

Configure API endpoints and other secrets in `eas.json`:

```json
{
  "build": {
    "production": {
      "env": {
        "EXPO_PUBLIC_API_URL": "https://api.production.com",
        "EXPO_PUBLIC_LOG_LEVEL": "info"
      }
    }
  }
}
```

**Note**: Variables prefixed with `EXPO_PUBLIC_` are accessible in the app. Store secrets differently.

## Useful Commands

```bash
# Trigger local EAS build
eas build --platform android --profile preview

# Check build status
eas build:list

# View build logs
eas build:log <build-id>

# Validate eas.json
eas config --scope eas.json
```

## Support & Resources

- [Expo Documentation](https://docs.expo.dev/)
- [EAS Build Documentation](https://docs.expo.dev/eas-update/introduction/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [TypeScript in React Native](https://docs.expo.dev/guides/typescript/)

---

**Last Updated:** March 2026

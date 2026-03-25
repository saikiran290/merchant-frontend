# CI/CD Pipeline Quick Start

Your frontend's CI/CD pipeline is now live! Here's what's happening:

## What's Included ✅

### 1. **Automated Linting & Type Checking**
- ESLint runs on every push/PR
- TypeScript type checking
- Catches code quality issues before merge

### 2. **Mobile Build Pipeline**
- **On `main` branch**: Builds production APK
- **On `develop` branch**: Builds preview APK
- Automatic artifact upload to GitHub

### 3. **Code Quality & Security**
- NPM audit for vulnerabilities
- Dependency consistency checks
- Outdated package detection

### 4. **Manual Build Trigger**
- Go to **Actions** tab in GitHub
- Select "Mobile App Build & Deployment"
- Choose build profile and run

## Required Setup

### 1. Add EXPO_TOKEN Secret (IMPORTANT!)

Your builds will fail without this:

```bash
# On your machine:
eas login
# Copy the token from Expo settings

# In GitHub:
1. Go to: Settings > Secrets and variables > Actions
2. New repository secret
3. Name: EXPO_TOKEN
4. Value: <paste your expo token>
5. Add secret
```

## Test the Pipeline

Push a test commit:

```bash
cd frontend
echo "# Test" >> README.md
git add README.md
git commit -m "Test CI pipeline"
git push origin main
```

Then:
1. Go to https://github.com/saikiran290/merchant-frontend/actions
2. Watch the workflow run in real-time
3. Download APK artifact when complete

## Workflows Explained

| Workflow | Trigger | Purpose |
|----------|---------|---------|
| **ci-cd.yml** | Every push/PR | Lint, type-check, build |
| **mobile-build.yml** | Manual trigger | Build specific profile |
| **code-quality.yml** | Every push/PR | Security & quality checks |

## File Locations

```
frontend/
├── .github/workflows/
│   ├── ci-cd.yml              # Main pipeline
│   ├── mobile-build.yml       # Mobile builds
│   └── code-quality.yml       # Security checks
├── CI-CD-SETUP.md             # Full documentation
└── eas.json                   # Build profiles
```

## Common Commands

```bash
# Build locally (instead of waiting for CI)
eas build --platform android --profile preview

# View build status
eas build:list

# Check logs
eas build:log <build-id>

# Test lint locally
npm run lint

# Type check locally
npx tsc --noEmit
```

## What Happens on Each Branch

### Main Branch
```
Push → Lint → Type Check → Build Production APK → Upload Artifact
```

### Develop Branch
```
Push → Lint → Type Check → Build Preview APK → Upload Artifact
```

### Pull Request
```
Submit PR → Lint → Type Check → Status Check Required
```

## Download Your First Build

1. Push changes to `main` or `develop`
2. Go to **Actions** tab
3. Click the workflow run
4. Scroll down to **Artifacts**
5. Download `android-apk-production` or `android-apk-preview`
6. Install on device: `adb install app.apk`

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Token not found" error | Add EXPO_TOKEN to GitHub Secrets |
| Build takes 15+ minutes | EAS cloud builds are normal, check https://expo.dev/dashboard |
| Type errors on main | Run `npx tsc --noEmit` locally and fix |
| Lint errors failing PR | Run `npm run lint` locally first |

## Next Steps

1. ✅ **Add EXPO_TOKEN secret** (critical!)
2. 🧪 Push a test commit to trigger workflow
3. 📱 Download and test the APK
4. 🔄 Create a PR to see linting checks
5. 🚀 Set up branch protection rules (optional)

## Branch Protection (Optional)

To prevent merging broken code:

1. Go to **Settings** > **Branches**
2. Add rule for `main`
3. Require CI checks to pass
4. Require pull request reviews

---

Your CI/CD pipeline is ready! Questions? Check [CI-CD-SETUP.md](./CI-CD-SETUP.md) for detailed docs.

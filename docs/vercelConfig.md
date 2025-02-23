# Vercel Configuration Guide

This document explains our Vercel configuration settings and deployment process.

## Our Configuration
Our `vercel.json` contains the following configuration:

```json
{
    "git": {
        "deploymentEnabled": {
            "main": true,
            "development": false,
            "feature/*": false
        }
    },
    "github": {
        "enabled": true,
        "silent": false,
        "autoAlias": true,
        "pullRequestPreviewsEnabled": true,
        "deploymentProtection": {
            "main": {
                "requiredApprovals": 2,
                "teamSlug": "tetsuo-ai"
            }
        }
    },
    "public": false,
    "cleanUrls": true,
    "trailingSlash": false,
    "buildCommand": "pnpm run build",
    "devCommand": "pnpm run dev",
    "installCommand": "pnpm install",
    "framework": "nextjs",
    "outputDirectory": ".next"
}
```

## Deployment Settings

### Branch Deployments
- `"main": true` - Enables deployments for main branch
- `"development": false` - Disables deployments for development branch
- `"feature/*": false` - Disables automatic deployments for feature branches
- This configuration means feature branches only deploy when a PR is opened

### Pull Request Previews
- `"pullRequestPreviewsEnabled": true` - Creates preview deployments when PRs are opened
- Updates preview automatically when new commits are pushed to PR
- More efficient as it only creates previews for code ready for review
- Preview URLs format: `your-project-pr-123-username.vercel.app`

### GitHub Integration
- `"silent": false` - Posts deployment statuses to:
  - PR Checks section
  - PR Comments (with preview URL)
  - Commit Status
  - GitHub Status Checks

### Auto-Aliasing
- `"autoAlias": true` - Automatically promotes successful main branch deployments to production
- When `true`, successful deployments to your production branch (usually `main`) automatically become your production deployment
- When `false`, you need to manually promote deployments to production through the Vercel dashboard
- Useful to set to `false` when you want manual control over what goes live
- Example flow with `true`:
  ```
  1. Deploy to main branch
  2. Build succeeds
  3. Automatically aliases to your-site.vercel.app
  ```

### Deployment Protection
Note: For organization accounts, you can add deployment protection with the following configuration:
```json
{
    "github": {
        "deploymentProtection": {
            "main": {
                "requiredApprovals": 2,
                "teamSlug": "your-team"
            }
        }
    }
}
```

This feature:
- Only available for team/organization accounts (not personal accounts)
- Requires specified number of approvals before deployment
- Team members must be part of the specified team slug
- Helps ensure code quality and review process
- Particularly useful for production deployments

## Build Configuration
- Using pnpm as package manager
- Build command: `pnpm run build`
- Dev command: `pnpm run dev`
- Install command: `pnpm install`
- Framework: Next.js
- Output directory: `.next`

Note: Vercel automatically detects these settings for Next.js projects, but we explicitly set them in `vercel.json` for clarity and to ensure consistent behavior. The automatic detection:
- Identifies the framework (Next.js)
- Determines the package manager (npm, yarn, or pnpm)
- Sets appropriate build/dev commands
- Configures the correct output directory

## URL Configuration

### Our Settings
- `public: false` - Deployments are private (changed from default)
- `cleanUrls: true` - URLs don't show file extensions (changed from default)
- `trailingSlash: false` - URLs don't have trailing slashes (default)

### Clean URLs Example
```
Before: /about.html
After:  /about
```

## Environment Variables

### Best Practices
- Use `vercel.json` for public configuration
- Use Vercel Dashboard for secrets and sensitive configuration
  - Encrypted at rest
  - Protected from unauthorized access
  - Can be team-specific
- Use `.env` files for local development only
  - Should be in `.gitignore`
  - Can include development-specific values
- Never commit sensitive data to version control
  - No secrets in `vercel.json`
  - No API keys in code
  - No tokens in configuration files

### Variable Precedence
Vercel merges environment variables from multiple sources in this order:
1. Vercel Dashboard Environment Variables (highest priority)
   - Production environment
   - Preview environment
   - Development environment
2. Environment Variables in `vercel.json`
   - Only for non-sensitive, public configuration

For Local Development Only:
3. `.env` files (only used locally, never on Vercel)
   When running locally, Next.js loads environment variables in this order:
   - `.env.development.local` - Local overrides when running `next dev`
   - `.env.local` - Local overrides for all environments
   - `.env.development` - Default development values
   - `.env` - Default values for all environments

   When running `next build` (production build locally):
   - `.env.production.local` - Local overrides for production builds
   - `.env.local` - Local overrides for all environments
   - `.env.production` - Default production values
   - `.env` - Default values for all environments

   Note: Files ending in `.local` are git-ignored and used for personal overrides.

   Example of how different .env files might be used:
   ```plaintext
   .env                    - DATABASE_URL=postgresql://localhost:5432/mydb
   .env.production        - DATABASE_URL=postgresql://prod-db.example.com
   .env.local            - Your personal API keys for all environments
   .env.production.local - Your personal production database credentials for local testing
   ```

   Remember: None of these files are used on Vercel - they're strictly for local development!

    When conflicts occur, values from higher priority sources override lower ones.

### Environment Types
Vercel supports different values for different environments:
- Production: Live deployment environment
- Preview: Deployment previews (PRs, branches)
- Development: Local development

You can set different values for each environment in the Vercel Dashboard:
```plaintext
DATABASE_URL
├─ Production: postgresql://prod-db.example.com
├─ Preview: postgresql://staging-db.example.com
└─ Development: postgresql://localhost:5432
```

### Built-in Variables
Vercel automatically provides these system environment variables:

#### Deployment Information
- `VERCEL`: "1" when running on Vercel
- `VERCEL_ENV`: Environment type
  - "production" for production deployments
  - "preview" for preview deployments
  - "development" for local development
- `VERCEL_URL`: The deployment URL
  - Format: `project-name-git-branch-team-name.vercel.app`
  - Also available as `NEXT_PUBLIC_VERCEL_URL`
- `VERCEL_REGION`: The deployment region (e.g., "cdg1")

#### Git Information
- `VERCEL_GIT_PROVIDER`: "github", "gitlab", or "bitbucket"
- `VERCEL_GIT_REPO_SLUG`: Repository name
- `VERCEL_GIT_REPO_OWNER`: Repository owner
- `VERCEL_GIT_REPO_ID`: Repository ID
- `VERCEL_GIT_COMMIT_REF`: Branch or tag name
- `VERCEL_GIT_COMMIT_SHA`: Commit hash
- `VERCEL_GIT_COMMIT_MESSAGE`: Commit message
- `VERCEL_GIT_COMMIT_AUTHOR_LOGIN`: Commit author username
- `VERCEL_GIT_COMMIT_AUTHOR_NAME`: Commit author name

#### Pull Request Information
Available for preview deployments:
- `VERCEL_GIT_PULL_REQUEST_ID`: PR number
- `VERCEL_GIT_PULL_REQUEST_NUMBER`: PR number (alternative)

#### Team Information
- `VERCEL_TEAM_ID`: Team ID if deployed to a team
- `VERCEL_TEAM_SLUG`: Team slug/name if deployed to a team

### Using Environment Variables

#### In Next.js
```javascript
// Server-side only
const dbUrl = process.env.DATABASE_URL;

// Exposed to client
// Must be prefixed with NEXT_PUBLIC_
const apiUrl = process.env.NEXT_PUBLIC_API_URL;
```

#### Environment Variable Tips
- Use `NEXT_PUBLIC_` prefix for client-side variables
- Keep sensitive data server-side only
- Use TypeScript for better type safety:
```typescript
if (!process.env.DATABASE_URL) {
  throw new Error('DATABASE_URL is not defined');
}
```

### Local Development
For local development:
1. Create a `.env` file
2. Use `vercel env pull` to download environment variables
3. Never commit `.env` files
4. Use `.env.example` for documentation

Example `.env.example`:
```plaintext
DATABASE_URL=postgresql://localhost:5432/mydb
NEXT_PUBLIC_API_URL=http://localhost:3000/api
# Add other variables with dummy values
```

## Security Headers

### Default Headers
Vercel sets these security headers by default:
```
Strict-Transport-Security: max-age=63072000
X-Frame-Options: DENY
X-Content-Type-Options: nosniff
Referrer-Policy: origin-when-cross-origin
```

### Custom Security Headers
There are two ways to add custom security headers, depending on your application type:

#### Option 1: For Next.js Applications (Recommended)
If you're using Next.js, configure headers in `next.config.js` for better integration with Next.js features:
```javascript
{
  async headers() {
    return [
      {
        source: '/:path*',
        headers: [
          {
            key: 'Content-Security-Policy',
            value: "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline';"
          },
          {
            key: 'Permissions-Policy',
            value: 'camera=(), microphone=(), geolocation=(), interest-cohort=()'
          },
          {
            key: 'X-XSS-Protection',
            value: '1; mode=block'
          }
        ]
      }
    ]
  }
}
```

#### Option 2: For Non-Next.js Applications
For other types of applications deployed on Vercel, configure headers in `vercel.json`:
```json
{
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        {
          "key": "Content-Security-Policy",
          "value": "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline';"
        },
        {
          "key": "Permissions-Policy",
          "value": "camera=(), microphone=(), geolocation=(), interest-cohort=()"
        },
        {
          "key": "X-XSS-Protection",
          "value": "1; mode=block"
        }
      ]
    }
  ]
}
```

#### Why Next.js Config is Preferred
Using `next.config.js` for Next.js applications offers several advantages:
- Better integration with Next.js development workflow
- More granular route-based control
- Consistent behavior across all environments (development, preview, production)
- Access to Next.js-specific features and optimizations

These custom headers enhance security by:
- `Content-Security-Policy`: Controls which resources the browser can load
- `Permissions-Policy`: Restricts which browser features your site can use
- `X-XSS-Protection`: Enables browser's XSS filtering capabilities

Note: Adjust the CSP values based on your application's needs, as the example above is quite restrictive.

## Regional Deployments

### Available Regions
Vercel allows you to specify deployment regions in `vercel.json`:
```json
{
    "regions": ["sfo1"]
}
```

Common regions include:
- `sfo1` (San Francisco)
- `iad1` (Washington DC)
- `hnd1` (Tokyo)
- `bru1` (Brussels)

If not specified, Vercel automatically deploys to optimal regions based on traffic.

## URL Management

### Redirects
Redirects allow you to send users from old URLs to new ones. Users will see the URL change in their browser:
```json
{
    "redirects": [
        {
            "source": "/old-page",
            "destination": "/new-page",
            "permanent": true  // 301 redirect
        }
    ]
}
```

### Rewrites
Rewrites allow you to map request paths to different destinations internally. Users won't see the destination URL:
```json
{
    "rewrites": [
        {
            "source": "/api/:path*",
            "destination": "/api/proxy/:path*"
        }
    ]
}
```

Common use cases:
- API proxying
- Clean URLs while maintaining complex internal routing
- A/B testing different pages
- Maintaining old URLs while restructuring your application

Note: Consider implementing URL management through Next.js configuration (`next.config.js`) instead of `vercel.json` when using Next.js, as it provides better integration with the framework's features.

## Additional Resources
- [Vercel Documentation](https://vercel.com/docs)
- [Next.js Documentation](https://nextjs.org/docs)

## CI/CD Integration

### Built-in CI/CD Features
Vercel provides comprehensive CI/CD functionality out of the box:

1. **Automated Deployments**
   - Every push triggers a new deployment
   - Production deployments from main branch
   - Preview deployments from PRs and branches
   - Automatic branch previews

2. **Build Pipeline**
   - Dependency installation
   - Build process execution
   - Asset optimization
   - Caching between builds
   - Environment variable injection

3. **Testing Integration**
   - Runs tests during build phase
   - Fails deployment on test failures
   - Provides detailed build and test logs
   - Notifies on GitHub about status

4. **Quality Checks**
   - Build validation
   - Framework-specific optimizations
   - Performance checks
   - Deployment previews

### When to Use GitHub Actions
While Vercel handles most deployment needs, GitHub Actions might still be useful for:

1. **Pre-deployment Tasks**
   - Advanced linting
   - Code formatting checks
   - Security scanning
   - Custom validation

2. **Additional Testing**
   - Integration tests
   - E2E tests
   - Performance testing
   - Cross-browser testing

3. **Custom Workflows**
   - Documentation generation
   - Asset processing
   - Database migrations
   - External service notifications

4. **Non-deployment Tasks**
   - Issue management
   - Release notes generation
   - Version bumping
   - Package publishing

Note: For most Next.js applications, Vercel's built-in CI/CD is sufficient. Add GitHub Actions only when you need specific functionality not covered by Vercel's deployment pipeline.

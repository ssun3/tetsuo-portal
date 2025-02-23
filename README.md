# tetsuo-portal

[![GitHub stars](https://img.shields.io/github/stars/tetsuo-ai/tetsuo-portal)](https://github.com/tetsuo-ai/tetsuo-portal/stargazers)
[![GitHub issues](https://img.shields.io/github/issues/tetsuo-ai/tetsuo-portal)](https://github.com/tetsuo-ai/tetsuo-portal/issues)
[![Vercel](https://therealsujitk-vercel-badge.vercel.app/?app=tetsuo-portal)](https://tetsuo-portal.vercel.app)

tetsuo-portal is a three.js powered portfolio landing page that serves as an interactive entry point for tetsuo's projects. Users can explore a 3D environment to interact with various components—integrated via iframes and WebAssembly—enabling demos and experiments that showcase the platform's capabilities.


```bash
+------------------------+
|    3D Navigation       |
|    (Interactive UI)    |
+------------------------+
          |
          v
+------------------------+
| Interactive Widgets    |
| (External Apps &       |
| WebAssembly Features)  |
+------------------------+
          |
          v
+------------------------+
|   Focused Views        |
| (Guided Experience)    |
+------------------------+
```

## star history

![Star History Chart](https://api.star-history.com/svg?repos=tetsuo-ai/tetsuo-portal&type=Date)

## development

tetsuo-portal is built with Next.js and uses pnpm for package management. To set up the project locally:

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/tetsuo-ai/tetsuo-portal.git
   cd tetsuo-portal
   ```

2. **Install Dependencies**:
   ```bash
   pnpm install
   ```

3. **Run the Development Server**:
   ```bash
   pnpm dev
   ```

4. **Access the Application**:
   Open your browser and navigate to `http://localhost:3000` to view the application.   

## deployment

tetsuo-portal is deployed to Vercel. To deploy the application:

1. **Push to the main branch**:
   - Pushing to `main` triggers automatic deployment
   - Preview deployments are created for Pull Requests
   - Feature branches (`feature/*`) only deploy when PRs are opened

2. **Deployment Protection** (Coming Soon):
   - Will require 2 approvals from the tetsuo-ai team
   - Will only apply to production deployments
   - Currently disabled - planned for future implementation

3. **Environment Variables**:
   - Managed through Vercel Dashboard
   - Different values for Production/Preview/Development
   - Local development uses `.env` files

For detailed configuration information and deployment settings, see [Vercel Configuration Guide](docs/vercelConfig.md).

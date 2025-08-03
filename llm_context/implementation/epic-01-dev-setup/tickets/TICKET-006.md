# TICKET-006: Set Up Frontend Commands

## Objective
Configure the frontend development environment with pnpm commands for local development, building, testing, and other common tasks.

## Requirements
- Development server with hot module replacement
- Production build command
- Linting and formatting commands
- Type checking
- Testing setup (optional for MVP)
- Preview production build locally

## Package.json Scripts

### frontend/package.json
```json
{
  "name": "reading-app-frontend",
  "private": true,
  "version": "0.1.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "lint": "eslint . --ext ts,tsx --report-unused-disable-directives --max-warnings 0",
    "lint:fix": "eslint . --ext ts,tsx --fix",
    "format": "prettier --write \"src/**/*.{ts,tsx,css,md}\"",
    "format:check": "prettier --check \"src/**/*.{ts,tsx,css,md}\"",
    "typecheck": "tsc --noEmit",
    "test": "vitest",
    "test:ui": "vitest --ui",
    "test:coverage": "vitest --coverage",
    "clean": "rm -rf dist node_modules",
    "prepare": "cd .. && husky install frontend/.husky"
  },
  "engines": {
    "node": ">=18.0.0",
    "pnpm": ">=8.0.0"
  }
}
```

## Configuration Files

### .eslintrc.cjs
```javascript
module.exports = {
  root: true,
  env: { browser: true, es2020: true },
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:react-hooks/recommended',
  ],
  ignorePatterns: ['dist', '.eslintrc.cjs'],
  parser: '@typescript-eslint/parser',
  plugins: ['react-refresh'],
  rules: {
    'react-refresh/only-export-components': [
      'warn',
      { allowConstantExport: true },
    ],
    '@typescript-eslint/no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
  },
}
```

### .prettierrc
```json
{
  "semi": false,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 80,
  "tabWidth": 2
}
```

### vite.config.ts (enhanced)
```typescript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import path from 'path'

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
  server: {
    port: 5173,
    host: true,
    proxy: {
      '/api': {
        target: 'http://localhost:8000',
        changeOrigin: true,
        secure: false,
      },
    },
  },
  build: {
    outDir: 'dist',
    sourcemap: true,
    rollupOptions: {
      output: {
        manualChunks: {
          'react-vendor': ['react', 'react-dom', 'react-router-dom'],
          'ui-vendor': ['@headlessui/react', '@heroicons/react'],
        },
      },
    },
  },
})
```

### postcss.config.js
```javascript
export default {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}
```

## Development Workflow Commands

### Initial Setup
```bash
# Navigate to frontend directory
cd frontend

# Install dependencies
pnpm install

# Start development server
pnpm dev
```

### Daily Development
```bash
# Start dev server (http://localhost:5173)
pnpm dev

# Run type checking in watch mode
pnpm typecheck --watch

# Run linting
pnpm lint

# Fix linting issues
pnpm lint:fix

# Format code
pnpm format
```

### Before Committing
```bash
# Run all checks
pnpm typecheck && pnpm lint && pnpm format:check

# Run tests
pnpm test
```

### Building for Production
```bash
# Create production build
pnpm build

# Preview production build locally
pnpm preview
```

## Environment Variables

### .env.example
```env
VITE_API_URL=http://localhost:8000
VITE_APP_NAME="Reading App"
VITE_APP_VERSION=$npm_package_version
```

### .env.development
```env
VITE_API_URL=http://localhost:8000
VITE_ENABLE_MOCK=false
```

### .env.production
```env
VITE_API_URL=/api
VITE_ENABLE_MOCK=false
```

## Git Hooks (Optional)

### .husky/pre-commit
```bash
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

cd frontend
pnpm typecheck && pnpm lint
```

## Notes
- The `dev` command starts Vite dev server with HMR
- The `build` command runs TypeScript checking before building
- API requests to `/api` are proxied to backend at `localhost:8000`
- Path aliases (`@/`) are configured for cleaner imports
- Manual chunks in build config optimize bundle splitting
- All commands should be run from the `frontend/` directory
# TICKET-002: Propose Frontend Dependencies

## Objective
Define the frontend technology stack and dependencies for the reading app MVP using React, TypeScript, and Vite.

## Core Dependencies

### Build Tools & Framework
- **vite**: ^5.0.0 - Fast build tool and dev server
- **react**: ^18.2.0 - UI library
- **react-dom**: ^18.2.0 - React DOM bindings
- **typescript**: ^5.3.0 - Type safety

### Routing & State Management
- **react-router-dom**: ^6.20.0 - Client-side routing
- **@tanstack/react-query**: ^5.0.0 - Server state management
- **zustand**: ^4.4.0 - Client state management

### UI & Styling
- **tailwindcss**: ^3.4.0 - Utility-first CSS framework
- **@headlessui/react**: ^1.7.0 - Unstyled UI components
- **@heroicons/react**: ^2.0.0 - Icon library
- **clsx**: ^2.0.0 - Conditional CSS classes

### Form Handling & Validation
- **react-hook-form**: ^7.48.0 - Form management
- **zod**: ^3.22.0 - Schema validation

### Development Dependencies
- **@types/react**: ^18.2.0
- **@types/react-dom**: ^18.2.0
- **@vitejs/plugin-react**: ^4.2.0
- **@typescript-eslint/eslint-plugin**: ^6.15.0
- **@typescript-eslint/parser**: ^6.15.0
- **eslint**: ^8.56.0
- **eslint-plugin-react-hooks**: ^4.6.0
- **prettier**: ^3.1.0
- **autoprefixer**: ^10.4.0
- **postcss**: ^8.4.0

### Testing (Optional for MVP)
- **vitest**: ^1.0.0 - Unit testing
- **@testing-library/react**: ^14.0.0 - React testing utilities
- **@testing-library/jest-dom**: ^6.1.0 - DOM assertions

## Installation Instructions

1. Navigate to the frontend directory:
   ```bash
   cd frontend
   ```

2. Initialize the project:
   ```bash
   pnpm init
   ```

3. Install core dependencies:
   ```bash
   pnpm add react react-dom react-router-dom @tanstack/react-query zustand
   pnpm add tailwindcss @headlessui/react @heroicons/react clsx
   pnpm add react-hook-form zod
   ```

4. Install development dependencies:
   ```bash
   pnpm add -D vite @vitejs/plugin-react typescript @types/react @types/react-dom
   pnpm add -D eslint @typescript-eslint/eslint-plugin @typescript-eslint/parser eslint-plugin-react-hooks
   pnpm add -D prettier autoprefixer postcss
   ```

5. Install testing dependencies (optional):
   ```bash
   pnpm add -D vitest @testing-library/react @testing-library/jest-dom
   ```

## Configuration Files Needed

### vite.config.ts
```typescript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  server: {
    port: 5173,
    proxy: {
      '/api': 'http://localhost:8000'
    }
  }
})
```

### tsconfig.json
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true
  },
  "include": ["src"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

### tailwind.config.js
```javascript
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

## Notes
- All versions are approximate and should use the latest stable versions
- The proxy configuration in Vite will forward API calls to the backend
- Additional dependencies can be added as features are implemented
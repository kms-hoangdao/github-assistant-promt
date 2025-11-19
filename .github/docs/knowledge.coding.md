# Veristat Frontend - Technical Knowledge Base

## Document Purpose

This document serves as a technical onboarding guide for developers working on the Veristat Frontend project. It provides comprehensive information about the project's architecture, design patterns, file structure, and development workflows.

## Table of Contents

- [1. Architectural Assessment](#1-architectural-assessment)
  - [1.1 Overall Architecture](#11-overall-architecture)
  - [1.2 Shared Code Conventions](#12-shared-code-conventions)
  - [1.3 Cross-Cutting Concerns](#13-cross-cutting-concerns)
- [2. Application-Level Design](#2-application-level-design)
- [3. Infrastructure and Data](#3-infrastructure-and-data)
  - [3.1 Data and Infrastructure](#31-data-and-infrastructure)
  - [3.2 Development Workflow](#32-development-workflow)

---

## 1. Architectural Assessment

### 1.1 Overall Architecture

**Architecture Type**: Single-Page Application (SPA) Monolith

This project is structured as a modern React SPA with a clear separation between application code (`src/`) and configuration/documentation. The application follows a **feature-based architecture** with centralized shared resources.

#### Top-Level Directory Structure

```
veristat-frontend/
├── .github/              # GitHub-specific files (workflows, docs, prompts)
├── .husky/               # Git hooks for pre-commit linting
├── .storybook/           # Storybook configuration for component documentation
├── docs/                 # Technical documentation (debugging, architecture, migration guides)
├── public/               # Static assets served directly (MSW worker, images)
├── src/                  # Application source code (features, components, pages)
├── Configuration files   # Root-level config (vite, tsconfig, tailwind, postcss)
```

**Key Characteristics**:

- **Build System**: Vite with React plugin, TypeScript compilation, and SCSS preprocessing
- **Module System**: ESNext with bundler resolution, path aliasing (`@/` → `src/`)
- **Deployment Model**: Static build output (`dist/`) optimized for CDN deployment with vendor chunking
- **Development Server**: Vite dev server on port 3000 with API proxy configuration

### 1.2 Shared Code Conventions

#### Location and Organization of Common Code

**1. Utilities (`src/utils/`)**

- **Purpose**: Pure functions, constants, helper utilities
- **Key Files**:
  - `constants.ts`: API base URL configuration, HTTP status codes
  - `common.ts`: Class name merging utility (`cn`) for Tailwind CSS
  - `routes.ts`: Application route definitions and path helpers
  - `index.ts`: Re-exports public utilities

**2. Components (`src/components/`)**

- **Purpose**: Reusable UI components shared across features
- **Structure**: Component-per-directory pattern with co-located tests, stories, and styles
- **Sub-directories**:
  - `ui/`: shadcn/ui components (Radix UI + Tailwind CSS primitives)
  - `Icons/`: Centralized icon library (Lucide React icons)
  - Feature-specific components: `Sidebar/`, `Table/`, `Toast/`, etc.
- **Pattern**: Each component directory contains:
  ```
  ComponentName/
  ├── ComponentName.tsx         # Main component
  ├── ComponentName.stories.tsx # Storybook documentation
  ├── ComponentName.test.tsx    # Unit tests
  ├── ComponentName.styled.tsx  # Styled components (if needed)
  └── index.ts                  # Public exports
  ```

**3. Hooks (`src/hooks/`)**

- **Purpose**: Reusable React Query hooks and custom React hooks
- **Pattern**: Domain-specific hooks (e.g., `useAuth.ts`, `useDashboard.ts`)
- **Convention**: React Query hooks follow naming pattern `use<Entity><Action>` (e.g., `useLogin`, `useCurrentUser`)

**4. Types (`src/types/`)**

- **Purpose**: Global TypeScript type definitions and module declarations
- **Key Files**:
  - `modules.d.ts`: Module declarations for non-TypeScript imports (CSS, images)

**5. Test Utilities (`src/test/`)**

- **Purpose**: Shared testing utilities, setup, and providers
- **Key Files**:
  - `setup.ts`: Global test setup (mocks for DOM APIs, environment variables)
  - `test-utils.tsx`: Custom render function with providers (React Query, Router, Theme, i18n)

**6. Library Configuration (`src/lib/`)**

- **Purpose**: Third-party library configurations
- **Key Files**:
  - `queryClient.ts`: React Query client with global defaults

**7. Internationalization (`src/i18n/`)**

- **Purpose**: i18next configuration and translation files
- **Structure**: `locales/<lang>/translation.json` pattern
- **Setup**: Browser language detection with localStorage caching

### 1.3 Cross-Cutting Concerns

**Configuration Management**:

- Environment variables via Vite (`import.meta.env`)
- Configuration loaded from `.env` file at build time
- Constants centralized in `src/utils/constants.ts`
- Base API URL configurable via `VITE_API_BASE_URL` environment variable

**Authentication & Authorization**:

- **Strategy**: JWT Bearer token authentication
- **Token Storage**: localStorage with key `token`
- **Implementation Location**: `src/features/auth/`
  - `authStore.ts`: Zustand store for auth state management
  - `authApi.ts`: API layer for authentication endpoints
  - `axios-config.ts`: Axios interceptors for automatic token attachment
  - `ProtectedRoute.tsx`: Route guard component for authenticated routes
- **Token Lifecycle**:
  - Request interceptor: Automatic Bearer token attachment from localStorage
  - Response interceptor: Global 401/403 handling with redirect to login
  - Token removal on logout or authentication errors

**HTTP Client & API Communication**:

- **Primary Client**: Axios with centralized configuration
- **Configuration File**: `src/features/auth/axios-config.ts`
- **Features**:
  - Base URL configuration from environment
  - Request interceptor for JWT token attachment
  - Response interceptor for global error handling (401/403 redirects)
  - Retry logic for network errors and 5xx server errors (configurable)
  - Request cancellation support via cancel tokens
  - Smart header merging (user-provided headers take precedence)
- **Retry Configuration**: Defaults to 0 retries for easier debugging, configurable via `RETRY_CONFIG`

**Server State Management**:

- **Library**: TanStack React Query (v5)
- **Configuration**: `src/lib/queryClient.ts`
- **Cache Settings**:
  - `staleTime`: 5 minutes (data considered fresh)
  - `gcTime`: 10 minutes (garbage collection time for unused queries)
  - `retry`: 1 attempt for queries, 0 for mutations
  - `refetchOnWindowFocus`: Disabled
- **Custom Hooks Pattern**: Domain-specific hooks in `src/hooks/` wrap useQuery/useMutation
- **DevTools**: React Query DevTools enabled in development mode

**Client State Management**:

- **Library**: Zustand with Redux DevTools integration
- **Implementation**: `src/features/auth/authStore.ts`
- **Features**:
  - Minimal boilerplate state management
  - Redux DevTools support via middleware
  - Persistent state via localStorage integration
  - Type-safe with TypeScript

**Error Handling**:

- **React Error Boundaries**: `src/components/ErrorBoundary.tsx`
  - Catches component tree errors
  - Displays user-friendly error UI
  - Provides retry mechanism
- **HTTP Error Handling**:
  - Global interceptors in axios-config for 401/403 errors
  - React Query error states exposed via hooks
  - Error logging to console (development mode)
- **No Centralized Logging Service**: Errors logged to browser console

**Internationalization (i18n)**:

- **Library**: react-i18next
- **Configuration**: `src/i18n/index.ts`
- **Translation Files**: `src/i18n/locales/{language}/translation.json`
- **Features**:
  - Browser language detection
  - localStorage caching of language preference
  - Fallback language: English (en)
  - Debug mode enabled in development

**Mock API (Development)**:

- **Library**: Mock Service Worker (MSW)
- **Configuration**: `src/msw/browser.ts`, `src/msw/handlers.ts`
- **Worker File**: `public/mockServiceWorker.js`
- **Purpose**: Development-only API mocking (separate from unit tests)
- **Scope**: Authentication endpoints and sample data endpoints

**Caching Strategy**:

- **No explicit application-level cache**: Relies on React Query's built-in caching
- **React Query Cache**: In-memory cache with configurable stale/GC times
- **localStorage**: Used only for JWT token and i18n language preference
- **HTTP Caching**: Relies on browser cache and server cache headers

**Testing Infrastructure**:

- **Unit Testing**: Vitest + Testing Library
- **Configuration**: `vite.config.ts` with test section
- **Test Environment**: jsdom (browser-like environment)
- **Coverage Tool**: v8 coverage provider
- **Coverage Exclusions**: Stories, MSW mocks, styled components, config files, icon components
- **Test Utilities**: Custom render with providers in `src/test/test-utils.tsx`
- **Component Documentation**: Storybook for isolated component development and testing

**Code Quality & Formatting**:

- **Linter**: ESLint with TypeScript, React, and React Hooks plugins
- **Formatter**: Prettier
- **Pre-commit Hooks**: Husky + lint-staged
  - Auto-fix ESLint issues
  - Auto-format with Prettier on staged files
- **Import Sorting**: eslint-plugin-simple-import-sort
- **Unused Imports**: eslint-plugin-unused-imports for cleanup

**Documentation Approach**:

- **Architecture Docs**: `/docs` directory with Markdown files
  - `REACT_QUERY.md`: React Query integration guide
  - `REACT_QUERY_ARCHITECTURE.md`: Detailed architecture diagrams
  - `ZUSTAND_MIGRATION_PLAN.md`: State management migration strategy
  - `DEBUGGING.md`: Debugging setup and best practices
  - `howToContribute.md`: Contribution guidelines
- **Component Documentation**: Storybook stories (`.stories.tsx` files)
- **Code Comments**: JSDoc comments for functions and complex logic
- **README.md**: Project overview and quick start guide

---

## 2. Application-Level Design

### Internal Design Pattern

The application follows a **Feature-Slice Design** with shared component architecture:

```
src/
├── features/              # Feature modules (vertical slices)
│   ├── auth/              # Authentication feature
│   ├── deliverable-set/   # Deliverable set management feature
│   └── sidebar/           # Sidebar navigation feature
├── components/            # Shared presentational components
├── pages/                 # Page-level components (route handlers)
├── hooks/                 # Shared React Query hooks
├── utils/                 # Pure utilities and constants
├── lib/                   # Library configurations
├── test/                  # Test infrastructure
├── msw/                   # API mocking (development only)
├── i18n/                  # Internationalization
└── styles/                # Global styles
```

### Primary Folder Responsibilities

#### **`src/features/`** - Feature Modules

Each feature is a self-contained module with its own:

- **API Layer**: Service functions using `customInstance` for HTTP requests
- **State**: Zustand stores for feature-specific client state
- **Hooks**: React Query hooks for server state (`useQuery`, `useMutation`)
- **Types**: TypeScript interfaces for domain entities
- **Components**: Feature-specific components (if needed)

**Example Structure** (`features/auth/`):

```
auth/
├── authApi.ts           # API functions (login, logout, getCurrentUser)
├── axios-config.ts      # Shared Axios configuration with interceptors
├── authStore.ts         # Zustand store for auth state
├── ProtectedRoute.tsx   # Route guard component
```

**Example Structure** (`features/deliverable-set/`):

```
deliverable-set/
├── services.ts          # API functions
├── hooks.ts             # React Query hooks
├── store.ts             # Zustand store for filters, pagination, sorting
├── types.ts             # Domain types (DeliverableSet, Company, Project, etc.)
├── columns.tsx          # Table column definitions
├── components/          # Feature-specific UI components
└── __tests__/           # Feature unit tests
```

#### **`src/components/`** - Shared UI Components

Reusable presentational components following **Atomic Design principles**:

- **Atoms**: Basic UI elements (`ui/button`, `ui/input`, `Icons/`)
- **Molecules**: Composite components (`CountPill/`, `Text/`)
- **Organisms**: Complex components (`Table/`, `Sidebar/`, `TableView/`)
- **Templates**: Layout components (`MainLayout/`, `PageDetailLayout/`)

**Component Pattern**:

- Co-located tests (`.test.tsx`) and stories (`.stories.tsx`)
- Barrel exports via `index.ts`
- Props interfaces exported for reusability
- Accessibility-first design (ARIA attributes, keyboard navigation)

#### **`src/pages/`** - Route Handlers

Page components mapped to routes in `App.tsx`:

- Thin wrappers that compose features and components
- Handle route-specific logic (URL params, navigation)
- Integrate with React Query for data fetching via hooks
- Examples: `LoginPage.tsx`, `CompanyPage.tsx`, `DeliverableSet/ListPage.tsx`

#### **`src/hooks/`** - Custom Hooks

Reusable hooks for cross-feature functionality:

- **React Query Hooks**: Domain-specific data fetching (e.g., `useAuth.ts`)
- **Pattern**: Each hook file contains related hooks (e.g., `useLogin`, `useLogout`, `useCurrentUser` in `useAuth.ts`)
- **Composition**: Hooks use React Query's `useQuery` and `useMutation` primitives

#### **`src/utils/`** - Utilities

Pure functions and constants with no side effects:

- **`constants.ts`**: API configuration, HTTP status codes
- **`common.ts`**: Tailwind class merging utility
- **`routes.ts`**: Route path definitions and helpers
- **No React dependencies** (pure JavaScript/TypeScript)

#### **`src/lib/`** - Third-Party Configuration

Isolated configuration for external libraries:

- **`queryClient.ts`**: React Query client with global defaults
- **Purpose**: Keep configuration separate from application logic

#### **`src/test/`** - Test Infrastructure

Shared testing utilities for unit tests:

- **`setup.ts`**: Global test setup (DOM mocks, environment variables)
- **`test-utils.tsx`**: Custom render function with all providers
- **Not used in production** (excluded from build)

#### **`src/msw/`** - Mock API (Development Only)

MSW handlers for API mocking during development:

- **`handlers.ts`**: Request handlers with mock data generators
- **`browser.ts`**: Worker setup with conditional initialization
- **Separation**: Development mocking separate from test mocking
- **Control**: Enabled via `VITE_ENABLE_MSW` environment variable

#### **`src/i18n/`** - Internationalization

i18next configuration and translation resources:

- **`index.ts`**: i18next initialization
- **`locales/<lang>/translation.json`**: Translation files per language
- **Detection**: Browser language detection with localStorage persistence

#### **`src/styles/`** - Global Styles

Application-wide styles and variables:

- **`globals.scss`**: Global CSS reset and base styles
- **`variables.scss`**: SCSS variables (colors, spacing, breakpoints)
- **`App.module.css`**: CSS modules example (component-specific styles)

---

## 3. Infrastructure and Data

### 3.1 Data and Infrastructure

#### Database Entities / Schema Definitions

The application interfaces with the following domain entities (defined in `src/features/deliverable-set/types.ts`):

1. **DeliverableSet**: Core entity representing deliverable set records
2. **Company**: Reference data for company information
3. **Project**: Reference data for project information
4. **Study**: Reference data for study information
5. **ReportingEvent**: Reference data for reporting event information

**Note**: These are TypeScript interfaces for API integration. The backend schema definitions are not part of this frontend codebase.

#### External Dependencies and Integrations

**1. Backend API Integration**

- **Base URL**: Configured via `VITE_API_BASE_URL` environment variable
- **Endpoints**:
  - `/identity/v1/login`: Authentication endpoint (Azure cloud gateway)
  - `/api/*`: API endpoints (proxied in development)
- **Authentication**: JWT Bearer token in `Authorization` header
- **CORS**: Handled via Vite proxy in development, credentials included in requests

**2. HTTP Client (Axios)**

- **Version**: ^1.6.2
- **Configuration**: `src/features/auth/axios-config.ts`
- **Features**: Request/response interceptors, retry logic, cancellation support
- **Export**: `AXIOS_INSTANCE` (simple requests), `customInstance` (with retry/cancellation)

**3. State Management (Zustand)**

- **Version**: ^5.0.8
- **Usage**: Client-side state (auth, filters, UI state)
- **DevTools**: Zustand devtools middleware in development

**4. Server State (React Query)**

- **Package**: @tanstack/react-query ^5.90.5
- **DevTools**: @tanstack/react-query-devtools ^5.90.2 (development only)
- **Configuration**: `src/lib/queryClient.ts`
- **Query Keys**: Defined per feature (e.g., `['auth', 'user']`, `['deliverable-set-list']`)

**5. UI Framework (MUI)**

- **Packages**:
  - @mui/material ^5.15.0
  - @mui/icons-material ^5.15.0
  - @emotion/react ^11.11.1
  - @emotion/styled ^11.11.0
- **Usage**: Theme provider, complex components (not primary styling system)

**6. Component Library (shadcn/ui)**

- **Based on**: Radix UI primitives + Tailwind CSS
- **Packages**:
  - @radix-ui/react-\* (multiple components)
  - lucide-react ^0.553.0 (icons)
- **Location**: `src/components/ui/`

**7. Router (React Router)**

- **Package**: react-router-dom ^6.20.1
- **Usage**: BrowserRouter with nested routes
- **Protected Routes**: `ProtectedRoute` component wraps authenticated routes

**8. Form Management**

- **Package**: react-hook-form ^7.66.0
- **Usage**: Form state management and validation

**9. Notifications**

- **Package**: react-toastify ^11.0.5
- **Usage**: Toast notifications for user feedback
- **Component**: `ToastContainer` in `App.tsx`

**10. Internationalization**

- **Packages**:
  - i18next ^23.7.6
  - react-i18next ^13.5.0
  - i18next-browser-languagedetector ^7.2.0
- **Setup**: `src/i18n/index.ts`

**11. Mock Service Worker (Development)**

- **Package**: msw ^2.0.11
- **Usage**: API mocking in development (not in tests)
- **Setup**: `src/msw/browser.ts`

**12. Testing**

- **Framework**: vitest ^1.1.0 + @vitest/ui ^1.1.0
- **Testing Library**: @testing-library/react ^14.1.2, @testing-library/user-event ^14.5.1
- **Assertions**: @testing-library/jest-dom ^6.1.5
- **Environment**: jsdom ^23.0.1
- **Coverage**: @vitest/coverage-v8 ^1.1.0

**13. Storybook**

- **Version**: ^7.6.5
- **Addons**: essentials, interactions, links, onboarding
- **Integration**: @storybook/react-vite

### 3.2 Development Workflow

#### Essential Commands

**Installation**

```bash
npm install
```

**Development Server**

```bash
npm run dev
# Starts Vite dev server on http://localhost:3000
# Includes HMR and API proxy for CORS-free development
```

**Production Build**

```bash
npm run build
# 1. TypeScript compilation check (tsc)
# 2. Vite production build with optimizations
# Output: dist/ directory with chunked assets
```

**Preview Production Build**

```bash
npm run preview
# Serves the production build locally for testing
```

**Testing**

```bash
# Run tests in watch mode (default)
npm run test

# Run tests once (CI mode)
npm run test -- --run

# Run tests with coverage report
npm run test:coverage

# Run tests with UI interface
npm run test:ui
```

**Linting**

```bash
# Lint TypeScript files
npm run lint

# Auto-fix linting issues
npm run lint:fix

# Strict linting (zero warnings)
npm run lint:strict
```

**Formatting**

```bash
# Format all files
npm run format

# Check formatting without changes
npm run format:check
```

**Storybook**

```bash
# Start Storybook development server
npm run storybook
# Runs on http://localhost:6006

# Build static Storybook site
npm run build-storybook
```

#### Pre-Commit Workflow

**Git Hooks (Husky + lint-staged)**

- **Hook**: Pre-commit
- **Trigger**: On `git commit`
- **Actions**:
  1. Run ESLint with auto-fix on staged `.ts`, `.tsx` files
  2. Run Prettier on staged files (JS, TS, JSON, CSS, SCSS, MD)
- **Configuration**: `.husky/pre-commit` + `package.json` `lint-staged` field

#### Environment Configuration

**Environment Variables** (`.env` file):

```bash
# API Base URL (defaults to localhost in development)
VITE_API_BASE_URL=http://localhost:3001/api

# Enable/disable MSW mocking in development
VITE_ENABLE_MSW=true
```

**Access in Code**:

```typescript
const apiUrl = import.meta.env.VITE_API_BASE_URL
const mswEnabled = import.meta.env.VITE_ENABLE_MSW === 'true'
const isDev = import.meta.env.DEV
```

#### Build Output Structure

```
dist/
├── assets/
│   ├── index-[hash].css       # Bundled styles
│   ├── index-[hash].js        # Main app chunk
│   ├── vendor-[hash].js       # React + React DOM
│   ├── zustand-[hash].js      # Zustand chunk
│   ├── mui-[hash].js          # Material-UI chunk
│   └── router-[hash].js       # React Router chunk
├── index.html                 # Entry HTML file
└── mockServiceWorker.js       # MSW worker (if needed)
```

**Optimization Features**:

- Source maps enabled for debugging
- Manual code-splitting for vendor libraries
- Tree-shaking for unused code elimination
- CSS extraction and minification

---

## Document Version

- **Created**: 2025-11-14
- **Last Updated**: 2025-11-14
- **Project Version**: 0.0.0
- **React Version**: 18.2.0
- **TypeScript Version**: 5.3.3

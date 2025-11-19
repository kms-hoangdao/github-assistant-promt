# Project: React 19.2 TypeScript Application

This is a modern React 19.2 application built with TypeScript and Vite, featuring:

## Tech Stack

- **Framework**: React 19.2 with TypeScript
- **Build Tool**: Vite
- **State Management**: Zustand
- **Authentication**: JWT Bearer tokens
- **UI Framework**: Shadcn UI
- **Styling**: Tailwind CSS, CSS Modules, SCSS support
- **Testing**: Vitest + Testing Library with coverage reports
- **Internationalization**: react-i18next
- **Component Documentation**: Storybook
- **Mock API**: MSW (Mock Service Worker)
- **HTTP Client**: Axios
- **Code Quality**: ESLint, Prettier, Husky, lint-staged

## Project Structure

```
my-app/
├─ .vscode/
├─ .husky/
├─ public/
├─ src/
│  ├─ app/store.ts
│  ├─ features/auth/
│  ├─ components/
│  ├─ pages/
│  ├─ i18n/
│  ├─ mocks/
│  ├─ styles/
│  └─ storybook/
├─ .env
└─ configuration files
```

## Development Guidelines

- Use TypeScript for all components and logic
- Follow React 19.2 best practices
- Implement proper error boundaries
- Use Redux Toolkit for state management
- Write unit tests for all components
- Document components with Storybook
- Use environment variables for configuration
- Follow ESLint and Prettier formatting rules

## Build & Deployment

- Static files optimized for CDN deployment
- Environment-specific configurations
- Coverage reports for testing

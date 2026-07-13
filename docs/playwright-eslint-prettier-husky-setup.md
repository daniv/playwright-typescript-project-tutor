# 🧰 Setting Up ESLint, Prettier, Husky, lint-staged, and TypeScript for a Playwright Project

This guide documents the complete setup of a professional **Playwright + TypeScript** automation project using:

- 🧹 ESLint
- 🎨 Prettier
- 🎭 Playwright ESLint rules
- 🪝 Husky
- 🚦 lint-staged
- 🧠 Type-aware TypeScript linting
- 🧱 Stronger TypeScript compiler checks
- 🧑‍💻 VS Code workspace configuration
- 🌍 Cross-platform line-ending consistency
- 📁 Git-tracked empty framework folders

The setup is intentionally performed in small, verifiable steps so configuration problems are caught early.

---

# 📋 Prerequisites

Verify Node.js, npm, and Git:

```bash
node -v
npm -v
git --version
```

Example versions used during this setup:

```text
Node.js: v24.18.0
npm: 11.7.0
Git: 2.55.0.windows.2
```

---

# 1. 🧹 Install ESLint and TypeScript ESLint

Install the modern ESLint flat-config packages:

```bash
npm install -D eslint @eslint/js typescript-eslint globals
```

Packages:

- `eslint` — static code analysis
- `@eslint/js` — official JavaScript recommended rules
- `typescript-eslint` — TypeScript parser, plugin, and presets
- `globals` — predefined Node.js and browser globals

---

# 2. 📄 Create `eslint.config.mjs`

Create the file:

```text
eslint.config.mjs
```

The final version developed later in this guide is:

```javascript
import js from "@eslint/js";
import { defineConfig } from "eslint/config";
import eslintConfigPrettier from "eslint-config-prettier/flat";
import playwright from "eslint-plugin-playwright";
import globals from "globals";
import tseslint from "typescript-eslint";

export default defineConfig(
  {
    ignores: [
      "**/node_modules/**",
      "**/playwright-report/**",
      "**/test-results/**",
      "**/blob-report/**",
      "**/coverage/**",
      "**/dist/**",
    ],
  },

  {
    files: ["**/*.{js,mjs,cjs}"],

    extends: [js.configs.recommended],

    languageOptions: {
      globals: {
        ...globals.node,
      },
    },
  },

  {
    files: ["**/*.ts"],

    extends: [
      js.configs.recommended,
      tseslint.configs.recommendedTypeChecked,
    ],

    languageOptions: {
      parserOptions: {
        projectService: true,
        tsconfigRootDir: import.meta.dirname,
      },

      globals: {
        ...globals.node,
      },
    },
  },

  {
    files: ["tests/**/*.ts", "**/*.spec.ts", "**/*.test.ts"],

    extends: [playwright.configs["flat/recommended"]],

    rules: {
      "playwright/no-commented-out-tests": "error",
      "playwright/no-duplicate-hooks": "error",
    },
  },

  eslintConfigPrettier,
);
```

This configuration provides:

- JavaScript linting
- Type-aware TypeScript linting
- Node.js globals
- Playwright-specific linting for tests
- Prettier conflict prevention
- Generated-folder exclusions

---

# 3. 🧠 Create `tsconfig.json`

Create:

```text
tsconfig.json
```

Use:

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "lib": ["ES2022"],
    "types": ["node", "@playwright/test"],

    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "exactOptionalPropertyTypes": true,
    "forceConsistentCasingInFileNames": true,
    "noImplicitOverride": true,
    "noEmit": true,

    "esModuleInterop": true,
    "resolveJsonModule": true,
    "allowSyntheticDefaultImports": true,

    "skipLibCheck": true
  },
  "include": ["**/*.ts"],
  "exclude": [
    "node_modules",
    "playwright-report",
    "test-results",
    "blob-report"
  ]
}
```

Important options:

- `strict` — enables strict TypeScript checks
- `noUncheckedIndexedAccess` — indexed access may return `undefined`
- `noImplicitReturns` — all code paths must return consistently
- `noFallthroughCasesInSwitch` — prevents accidental `switch` fallthrough
- `exactOptionalPropertyTypes` — distinguishes missing properties from explicit `undefined`
- `forceConsistentCasingInFileNames` — prevents Windows/Linux casing issues
- `noImplicitOverride` — requires the `override` keyword
- `noEmit` — type-check only

Verify:

```bash
npx tsc --noEmit
```

---

# 4. 🎭 Install the Playwright ESLint Plugin

Install:

```bash
npm install -D eslint-plugin-playwright
```

Useful Playwright rules include:

- preventing accidental `test.only`
- avoiding commented-out tests
- detecting duplicate hooks
- discouraging unreliable patterns
- validating Playwright assertions

---

# 5. 🎨 Install Prettier

Install Prettier and the ESLint compatibility configuration:

```bash
npm install -D prettier eslint-config-prettier
```

The responsibilities remain separate:

```text
ESLint  → code quality
Prettier → code formatting
```

`eslint-config-prettier` disables ESLint formatting rules that would conflict with Prettier.

---

# 6. 🖌️ Create `.prettierrc.json`

Create:

```text
.prettierrc.json
```

Use:

```json
{
  "printWidth": 100,
  "tabWidth": 2,
  "useTabs": false,
  "semi": true,
  "singleQuote": false,
  "trailingComma": "all",
  "arrowParens": "always",
  "bracketSpacing": true,
  "endOfLine": "lf"
}
```

---

# 7. 🚫 Create `.prettierignore`

Create:

```text
.prettierignore
```

Use:

```text
node_modules
playwright-report
test-results
blob-report
coverage
dist

package-lock.json
```

Lock files are generated artifacts, so they are intentionally ignored by Prettier.

---

# 8. ✅ Verify and Format the Repository

Check formatting:

```bash
npx prettier --check .
```

Format the entire repository once:

```bash
npx prettier --write .
```

Verify again:

```bash
npx prettier --check .
```

Expected result:

```text
Checking formatting...
All matched files use Prettier code style!
```

---

# 9. 📦 Add npm Scripts

Add these scripts to `package.json`:

```json
{
  "scripts": {
    "prepare": "husky",
    "test": "playwright test",
    "test:headed": "playwright test --headed",
    "test:report": "playwright show-report",
    "lint": "eslint .",
    "lint:fix": "eslint . --fix",
    "format": "prettier --write .",
    "format:check": "prettier --check .",
    "typecheck": "tsc --noEmit"
  }
}
```

These scripts provide a reusable project interface for local development, hooks, and CI.

---

# 10. 🪝 Install Husky and lint-staged

Install:

```bash
npm install -D husky lint-staged
```

Initialize Husky:

```bash
npm run prepare
```

Husky creates:

```text
.husky/
└── _/
```

Do not edit files inside `.husky/_`. They are managed by Husky.

---

# 11. 🚦 Configure lint-staged

Add this top-level property to `package.json`:

```json
{
  "lint-staged": {
    "*.{ts,js,mjs,cjs}": [
      "eslint --fix",
      "prettier --write"
    ],
    "*.{json,yml,yaml,md}": "prettier --write"
  }
}
```

Why this structure?

- ESLint and Prettier run only on staged files
- Commands in each array run sequentially
- Code files run ESLint first, then Prettier
- JSON, YAML, and Markdown only run Prettier

---

# 12. 🪝 Create the Husky Pre-Commit Hook

Create:

```text
.husky/pre-commit
```

Content:

```sh
npx lint-staged
```

The resulting flow is:

```text
git commit
    ↓
Husky
    ↓
lint-staged
    ↓
ESLint --fix
    ↓
Prettier --write
    ↓
Commit succeeds
```

Test manually:

```bash
npx lint-staged
```

If nothing is staged:

```text
lint-staged could not find any staged files.
```

---

# 13. 🧪 Test the Hook End to End

Make a formatting change in a TypeScript test file.

Stage it:

```bash
git add tests/example.spec.ts
```

Commit:

```bash
git commit -m "test husky"
```

A successful result confirms:

- Git invokes Husky
- Husky invokes lint-staged
- lint-staged runs ESLint
- lint-staged runs Prettier
- fixed files are re-staged
- the commit succeeds

---

# 14. 🌍 Add `.gitattributes`

Create:

```text
.gitattributes
```

Use:

```gitattributes
* text=auto

*.ts   text eol=lf
*.js   text eol=lf
*.mjs  text eol=lf
*.json text eol=lf
*.yml  text eol=lf
*.yaml text eol=lf
*.md   text eol=lf

*.bat text eol=crlf
*.cmd text eol=crlf

*.png binary
*.jpg binary
*.jpeg binary
*.gif binary
*.ico binary
*.pdf binary
```

Why repository-level configuration?

A global Git configuration affects only one developer’s machine. `.gitattributes` travels with the repository and gives every contributor the same line-ending rules.

Normalize existing files:

```bash
git add --renormalize .
```

Verify:

```bash
git status
```

---

# 15. 📝 Add `.editorconfig`

Create:

```text
.editorconfig
```

Use:

```ini
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
indent_style = space
indent_size = 2

[*.md]
trim_trailing_whitespace = false
```

Responsibilities:

| Tool | Responsibility |
|---|---|
| `.gitattributes` | Git repository line-ending policy |
| `.editorconfig` | Editor behavior |
| Prettier | Code formatting |

---

# 16. 🧑‍💻 Add VS Code Workspace Settings

Create:

```text
.vscode/settings.json
```

Use:

```json
{
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit",
    "source.organizeImports": "explicit"
  },
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact"
  ],
  "files.eol": "\n",
  "files.insertFinalNewline": true,
  "files.trimTrailingWhitespace": true
}
```

Install the official Prettier extension if needed:

```bash
code --install-extension esbenp.prettier-vscode
```

Reload VS Code:

```text
Developer: Reload Window
```

---

# 17. 🧩 Add VS Code Extension Recommendations

Create:

```text
.vscode/extensions.json
```

Use:

```json
{
  "recommendations": [
    "dbaeumer.vscode-eslint",
    "esbenp.prettier-vscode",
    "ms-playwright.playwright",
    "EditorConfig.EditorConfig"
  ]
}
```

Recommended extensions:

- ESLint
- Prettier
- Playwright Test for VS Code
- EditorConfig

---

# 18. 🧠 Add TypeScript as a Direct Dependency

Check whether TypeScript is only transitive:

```bash
npm ls typescript --depth=0
```

Install it explicitly:

```bash
npm install -D typescript@6.0.3
```

Verify:

```bash
npm ls typescript --depth=0
```

A project should not depend on another tool to provide its compiler transitively.

---

# 19. 🧹 Remove Redundant TypeScript ESLint Dependencies

If the project uses:

```text
typescript-eslint
```

then these direct dependencies are redundant:

```text
@typescript-eslint/parser
@typescript-eslint/eslint-plugin
```

Remove them:

```bash
npm uninstall -D @typescript-eslint/parser @typescript-eslint/eslint-plugin
```

Verify ESLint:

```bash
npm run lint
```

---

# 20. ⚙️ Fix `exactOptionalPropertyTypes` in Playwright Config

With:

```json
"exactOptionalPropertyTypes": true
```

this pattern may fail:

```typescript
workers: process.env.CI ? 1 : undefined,
```

The property exists and is explicitly assigned `undefined`.

Replace it with:

```typescript
...(process.env.CI ? { workers: 1 } : {}),
```

This produces:

```typescript
// CI
{
  workers: 1,
}
```

and locally:

```typescript
{
  // workers is omitted
}
```

---

# 21. ✅ Run the Complete Local Quality Gate

Run:

```bash
npm run lint && npm run typecheck && npm run format:check && npm test
```

This validates:

- ESLint
- TypeScript
- Prettier
- Playwright

Expected result: all commands pass.

---

# 22. 📁 Create the Framework Folder Structure

Create:

```text
src/
├── pages/
├── config/
├── components/
├── utils/
├── fixtures/
└── helpers/
```

Git does not track empty directories, so add placeholder `.gitkeep` files:

```bash
mkdir -p src/{pages,config,components,utils,fixtures,helpers} &&
touch src/{pages,config,components,utils,fixtures,helpers}/.gitkeep
```

Result:

```text
src/
├── pages/.gitkeep
├── config/.gitkeep
├── components/.gitkeep
├── utils/.gitkeep
├── fixtures/.gitkeep
└── helpers/.gitkeep
```

`.gitkeep` has no special Git behavior. It is simply a conventional placeholder.

Once a real file is added to a directory, its `.gitkeep` can be removed.

---

# 23. 🌿 Git Workflow for a Solo Project

For a personal repository, a pull request is optional.

Direct push is reasonable when:

- you are the only contributor
- `main` is not protected
- all local checks pass
- the changes are small and verified

Push:

```bash
git push origin main
```

Feature branches and pull requests are still useful for:

- practicing team workflows
- reviewing larger changes
- preserving discussion history
- requiring CI before merge

---

# 24. 🔍 Useful Verification Commands

Check ESLint:

```bash
npm run lint
```

Fix ESLint issues:

```bash
npm run lint:fix
```

Check formatting:

```bash
npm run format:check
```

Format files:

```bash
npm run format
```

Type-check:

```bash
npm run typecheck
```

Run tests:

```bash
npm test
```

Run the full quality gate:

```bash
npm run lint && npm run typecheck && npm run format:check && npm test
```

Inspect staged files:

```bash
git diff --cached --name-only
```

Inspect repository state:

```bash
git status --short
```

---

# 25. 🧯 Troubleshooting Notes

## `Unexpected key "eslintConfigPrettier"`

Incorrect:

```javascript
{
  rules: {},
  eslintConfigPrettier,
}
```

Correct:

```javascript
{
  rules: {},
},

eslintConfigPrettier,
```

`eslintConfigPrettier` is a complete configuration object, not a property inside another config.

---

## Type-aware ESLint rules fail on `.mjs`

Type-aware presets must not run on JavaScript config files unless type information is configured for those files.

The final configuration scopes:

- JavaScript rules to `*.js`, `*.mjs`, and `*.cjs`
- type-aware TypeScript rules to `*.ts`
- Playwright rules to test files

---

## Type-aware ESLint fails on `playwright.config.ts`

The cause is usually that `projectService: true` is scoped only to test files.

It must apply to every TypeScript file that receives type-aware rules:

```javascript
{
  files: ["**/*.ts"],
  languageOptions: {
    parserOptions: {
      projectService: true,
      tsconfigRootDir: import.meta.dirname,
    },
  },
}
```

---

## Prettier extension ID is rejected by VS Code

Install the extension:

```bash
code --install-extension esbenp.prettier-vscode
```

Then reload the VS Code window.

---

## Git warns that LF will be replaced by CRLF

Add `.gitattributes`, then normalize:

```bash
git add --renormalize .
```

Repository-level rules are preferable to relying only on each developer’s global Git settings.

---

# 🏁 Final Result

The project now has:

- ✅ Modern ESLint flat configuration
- ✅ Type-aware TypeScript linting
- ✅ Playwright-specific rules
- ✅ Prettier integration without running Prettier inside ESLint
- ✅ Husky pre-commit hooks
- ✅ lint-staged optimization
- ✅ Strong TypeScript compiler checks
- ✅ Cross-platform line endings
- ✅ EditorConfig
- ✅ VS Code workspace settings
- ✅ Recommended VS Code extensions
- ✅ Reusable npm quality scripts
- ✅ A complete local quality gate
- ✅ Git-tracked framework folder structure

This setup provides a strong foundation for a maintainable, professional Playwright automation framework.

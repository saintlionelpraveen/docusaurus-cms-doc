# File Structure and Modifications

This document outlines the detailed file and directory structure of our custom Docusaurus CMS, breaking down exactly what modifications were made to each file to achieve the required features.

## 1. Backend Server (`/server/`)
The `server/` directory contains the custom Node.js Express backend that handles authentication, database operations, and Git-backed file synchronization for the CMS.

### `/server/config/`
* **`database.js`**: Initializes the `better-sqlite3` database (`auth.db`) and creates the schema for `users`, `publish_requests`, `delete_requests`, `audit_log`, and `refresh_tokens`. 

### `/server/routes/`
* **`auth.js`**: Contains all authentication endpoints. Handles Local, Google, and GitHub login workflows using Passport.js, processes JWT access/refresh tokens, and implements secure token rotation.
* **`workspace.js`**: The core of the Git integration. Handles Git push/pull/clone operations via Personal Access Tokens (PAT). Manages document publishing from internal folders to the public `docs` folder. Processes delete requests for files and folders.
* **`admin.js`**: Manages the admin settings panel. Provides endpoints for fetching/updating user credentials (PATs), changing user roles, and managing branding details dynamically via the `.env` file (like title and logo).

### `/server/middleware/`
* **`auth.js`**: Contains middleware functions like `verifyAccessToken` and `requireRole` to protect API endpoints and ensure only authorized users can access CMS features.
* **`rateLimit.js`**: Implements rate-limiting for login and API routes to prevent brute-force attacks.

---

## 2. Frontend Interface (`/src/`)
Modifications in the `/src/` folder replaced standard Docusaurus pages with our custom React-based Dashboard and CMS interface.

### `/src/pages/internal/`
* **`admin.tsx`**: The Admin Dashboard. Re-styled with a professional monochrome "glassmorphism" design. Allows Admins to view the user table, manage system branding, update environment variables, and manage GitHub PATs.
* **`workspace.tsx`**: The main Workspace CMS. Features a customized sidebar for repository syncing and file browsing. Contains the logic for rendering the markdown editor, file preview, and the logic to submit Publish or Delete requests.

### `/src/contexts/`
* **`AuthContext.tsx`**: A React Context provider that manages the user session across the frontend. Intercepts API calls to automatically attach JWT tokens and handles silent token refreshes so users aren't logged out unexpectedly.

### `/src/components/`
* **`PremiumCard` / `GlassButton`**: Custom styling components introduced to give the dashboard its clean, high-contrast, premium aesthetic, overriding the default Docusaurus generic styles.

---

## 3. Configuration and Root Files

* **`docusaurus.config.ts`**: Modified to support multiple documentation instances (via `@docusaurus/plugin-content-docs`). We configured it so the default `docs/` acts as the public documentation, while `internal/` serves as the private workspace docs.
* **`.env`**: Contains secure environment variables including `JWT_SECRET`, database paths, OAuth Client IDs, and dynamic branding details (`SITE_TITLE`, `SITE_LOGO`).
* **`package.json`**: Updated to include backend dependencies (`express`, `better-sqlite3`, `passport`, `jsonwebtoken`, `simple-git`) and scripts to concurrently run the Express server and Docusaurus frontend (`npm run dev`).

---

## 4. Documentation Storage
* **`/docs/`**: The standard Docusaurus folder where **Public** documentation is stored.
* **`/internal/`**: A hidden folder structure where **Internal** drafts and workspaces are maintained before they are approved and copied over to `/docs/` via the publish requests.
* **`/project_docs/`**: Contains all architecture, technical stack, and design documentation outlining the history of this CMS project (e.g., `01_TECH_STACK_AND_ARCHITECTURE.md`, `06_ROLES_AND_ACCESS_CONTROL.md`, etc.).

---

### Summary of the Workflow Loop
Whenever an author writes documentation in the frontend (`src/pages/internal/workspace.tsx`), the request hits the Express backend (`server/routes/workspace.js`), which temporarily saves it in the SQLite Database (`server/data/auth.db` via `server/config/database.js`). Once an admin approves it, the backend script copies the `.md` file into the `/docs/` folder, and Docusaurus automatically hot-reloads it to the live site.

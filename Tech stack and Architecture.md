# Technology Stack & Architecture

This document outlines the core technologies used to transform the standard static site into a dynamic, Git-backed Documentation CMS, along with a brief description of why each was chosen.

## 1. Frontend Technologies

*   **Docusaurus (React.js)**
    *   *Description:* A modern static site generator (SSG) built on React.
    *   *Role:* Serves as the core framework. It handles routing, documentation layout, theming (dark/light mode), and converting Markdown files into web pages. We extended it to support dynamic client-side rendering for the CMS dashboard.
*   **Monaco Editor (`@monaco-editor/react`)**
    *   *Description:* The code editor that powers VS Code, packaged for the browser.
    *   *Role:* Provides developers with a familiar, syntax-highlighted, web-based editor inside the Internal Workspace for modifying Markdown files without leaving the browser.
*   **Vanilla CSS / CSS Modules**
    *   *Description:* Standard cascading style sheets.
    *   *Role:* Used to build the custom glassmorphism and modern monochrome UI components for the dashboards, ensuring a high-end professional aesthetic.

## 2. Backend Technologies

*   **Node.js & Express.js**
    *   *Description:* A fast, minimalist web framework for Node.js.
    *   *Role:* Acts as our custom backend API (`server/`). Standard Docusaurus has no backend; we added this to handle authentication, file system reading, and Git operations securely away from the frontend.
*   **SQLite (`better-sqlite3`)**
    *   *Description:* A lightweight, file-based relational database.
    *   *Role:* Stores user credentials, roles (Admin/Developer), publish requests, and system logs. It requires no heavy database server setup, keeping the system portable.
*   **Simple-Git (`simple-git`)**
    *   *Description:* A lightweight interface for running Git commands in NodeJS.
    *   *Role:* Executes `git clone`, `git pull`, `git commit`, and `git push` directly on the server when a user interacts with the UI in their workspace.

## 3. Authentication & Security

*   **JSON Web Tokens (JWT)**
    *   *Description:* An open standard for securely transmitting information as a JSON object.
    *   *Role:* Used for session management. When a user logs in, the backend issues a JWT. The frontend stores this and sends it with API requests to verify the user has access to protected Internal routes.
*   **Bcrypt**
    *   *Description:* A password-hashing function.
    *   *Role:* Securely hashes user passwords in the SQLite database so plain-text passwords are never stored.

## 4. System Architecture Summary

Our system operates in a **Hybrid Rendering Mode**:
1.  **Public Docs** are Statically Generated (SSG) by Docusaurus at build time for maximum speed and SEO.
2.  **Internal Dashboards** are Client-Side Rendered (CSR) Single Page Applications (SPAs) that communicate with the Express backend API to fetch real-time data, manage files, and sync with GitHub.

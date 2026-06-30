# Internal Developer Workspace & Admin Portal

This document outlines the internal, secured aspects of the Docusaurus CMS, detailing how developers interact with the system and how administrators manage it.

## 1. The Internal Developer Workspace (`/internal/workspace`)

The Workspace is an isolated web-based IDE built into the CMS, allowing developers to write and manage documentation without needing external local tools.

*   **Repository Selection & Syncing:** Developers can connect to multiple remote GitHub repositories. By entering a Personal Access Token (PAT) via a secure modal, they can pull the latest documentation into a local server-side sandbox.
*   **Web-Based Code Editor:** Powered by Monaco Editor, this provides a VS Code-like experience in the browser. It features a file tree sidebar on the left and a syntax-highlighted editing pane on the right.
*   **Saving and Pushing:** 
    *   **Save File:** Writes the current editor state to the server's local file system in the isolated workspace directory.
    *   **Git Push:** Automatically stages changes, creates a commit message, and pushes directly to the connected GitHub repository using backend Git execution.
*   **Publish Requests:** Once documentation is written in the workspace sandbox, developers can request to publish it. This creates a pending request in the database, waiting for Admin approval before it goes live on the public site.

## 2. The Admin Dashboard (`/internal/admin`)

The Admin dashboard is a centralized control panel for governance, accessible only to users with `admin` privileges.

*   **Approval Workflows:** Admins review "Publish Requests" submitted by developers. Approving a request triggers a backend process that copies the Markdown files from the isolated `workspaces/` directory into the live `docs/` directory.
*   **User & Role Management:** Admins can view all registered users in a clean data table and toggle their roles between `Viewer`, `Developer`, and `Admin`. They can also revoke access entirely.
*   **Branding & Profile Management:** Admins can update the CMS identity. They can upload new logo SVG/PNG files and change the overall Site Title. These changes modify the underlying `.env` configuration.
*   **Server Controls:** Because branding changes require rebuilding static assets, the Admin panel includes controls to trigger a soft or hard server restart.

## 3. UI/UX Design Approach

Both the Workspace and Admin portals utilize a strictly professional, minimalist UI. 
*   **Visual Identity:** The system utilizes a sophisticated monochrome palette (whites, off-whites, grays, and blacks) rather than distracting gradients or bright colors.
*   **Component Architecture:** Clean, flat-styled cards, discrete buttons, and modern SVG icons ensure the interface feels like an enterprise software product rather than a standard Docusaurus template.

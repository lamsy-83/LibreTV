# LibreTV - Gemini Instructional Context

This document provides an overview of the LibreTV project, its technical stack, and instructions for building and running it, intended for use as instructional context for Gemini.

## Project Overview

LibreTV is a lightweight, free, online video search and watching platform. It provides content search and playback services from multiple video sources, requiring no registration and supporting various devices. The project combines frontend technologies with backend proxy functionalities, designed for deployment on server-side-enabled website hosting services. It is a refactored and enhanced version of the `bestK/tv` project.

**Key Technologies:**

*   **Frontend:** HTML5, CSS3, JavaScript (ES6+), Tailwind CSS
*   **Video Playback:** HLS.js (for HLS stream processing), DPlayer (video player core)
*   **Backend/Proxy:** Serverless Functions (Cloudflare, Vercel, Netlify), Node.js for local development server, custom backend proxy for video content.
*   **Data Storage:** localStorage for local settings.

The architecture involves a client-side application that interacts with various video sources through a backend proxy, which can be deployed as serverless functions or a Node.js server.

## Building and Running

### Local Development Environment

To set up and run LibreTV locally for development:

1.  **Set up Environment Variables:**
    Copy the example environment file:
    ```bash
    cp .env.example .env
    ```
    **Note:** The `PASSWORD` environment variable is crucial for security. It must be set for the application to function correctly and prevent unauthorized access.

2.  **Install Dependencies:**
    ```bash
    npm install
    ```

3.  **Start Development Server:**
    ```bash
    npm run dev
    ```
    The application will typically be accessible at `http://localhost:8080`. The port can be modified via the `PORT` variable in the `.env` file.

    **Important:** Using simple static servers (e.g., `python -m http.server` or `npx http-server`) will not support the video proxy functionality, preventing video playback. Full functionality testing requires the Node.js development server.

### Docker Deployment

LibreTV can be deployed using Docker or Docker Compose.

#### Docker

```bash
docker run -d \
  --name libretv \
  --restart unless-stopped \
  -p 8899:8080 \
  -e PASSWORD=your_password \
  bestzwei/libretv:latest
```

#### Docker Compose

Create a `docker-compose.yml` file:

```yaml
services:
  libretv:
    image: bestzwei/libretv:latest
    container_name: libretv
    ports:
      - "8899:8080" # Maps internal 8080 to host's 8899
    environment:
      - PASSWORD=${PASSWORD:-111111} # Change 111111 to your desired password
    restart: unless-stopped
```

To start LibreTV with Docker Compose:

```bash
docker compose up -d
```

Access the application at `http://localhost:8899`.

## Development Conventions

*   **Password Protection:** A `PASSWORD` environment variable **must** be set for all deployments to ensure security. If not set, users will be prompted to set a password.
*   **API Compatibility:** LibreTV supports the standard Apple CMS V10 API format. When adding custom API sources, ensure they adhere to the following structure:
    *   **Search Interface:** `https://example.com/api.php/provide/vod/?ac=videolist&wd=keyword`
    *   **Detail Interface:** `https://example.com/api.php/provide/vod/?ac=detail&ids=videoID`
    *   Custom CMS sources can be added via the settings panel by providing the interface address (e.g., `https://example.com/api.php/provide/vod`).
*   **Updates and Synchronization:** The project discourages automatic synchronization requests from "Pull Bots" due to potential issues. It recommends enabling GitHub Actions for automatic synchronization (refer to `.github/workflows/sync.yml`) or using GitHub's official "Sync fork" feature for manual updates.
*   **Troubleshooting Updates:** If errors occur after an update, clear browser cookies and perform a hard refresh (Ctrl + F5).
*   **Keyboard Shortcuts:** The video player supports various keyboard shortcuts for playback control (Space, Arrow keys, M, F, Esc).

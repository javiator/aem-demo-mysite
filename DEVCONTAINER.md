# AEM Development Container Setup

This project is configured to run entirely within **Docker Containers** using the VS Code [Dev Containers](https://code.visualstudio.com/docs/devcontainers/containers) extension. This ensures a consistent development environment across all team members without needing to install complex dependencies (like specific Java versions, Maven, or Node.js) directly on your host machine.

## 🏗️ Architecture

The setup uses Docker Compose to orchestrate two primary environments:

-   **AEM Author & Build Environment (`aem-author`)**: 
    -   This is where your IDE (VS Code) "lives". 
    -   It runs the AEM Author instance and contains all tools: **JDK 17, Maven 3.x, Node.js 18.x**.
    -   *Design Choice*: We combine Build and Author in one container to simplify the Maven deployment flow (`localhost:4502` resolution) and reduce file-syncing overhead.
-   **AEM Publisher (`aem-publisher`)**:
    -   A secondary instance running in the background for testing content activation and delivery.

## 🚀 Getting Started

### Prerequisites
1.  **Docker** installed and running.
2.  **VS Code** with the **Dev Containers** extension.
3.  **AEM JAR & License**: Ensure `aem-quickstart.jar` and `aem-license.properties` are in the project root.

### Launching the Environment
1.  Open the project folder in VS Code.
2.  Press `Ctrl+Shift+P` and select **"Dev Containers: Reopen in Container"**.
3.  VS Code will build the image and start the containers.
    -   **First Run**: The container will automatically run `mvn clean install` to initialize local dependencies.
    -   **AEM Startup**: AEM usually takes 2-5 minutes to become fully healthy.

### Accessing AEM
-   **Author**: [http://localhost:4502](http://localhost:4502) (Default Login: `admin`/`admin`)
-   **Publisher**: [http://localhost:4503](http://localhost:4503)

## 🛠️ Development Workflow

-   **Building Code**: Open the integrated terminal in VS Code (which is now inside the container) and run:
    ```bash
    mvn clean install -PautoInstallSinglePackage
    ```
-   **Frontend Changes**: Navigate to `ui.frontend` and run standard npm commands:
    ```bash
    npm install
    npm run dev
    ```

## ⚖️ Pros and Cons

### Pros
-   ✅ **Consistency**: Every developer uses the exact same version of Java, Maven, and Node.js.
-   ✅ **Isolation**: No "pollution" of your host machine's environment.
-   ✅ **Persistence**: AEM data is stored in Docker **Named Volumes**, meaning your content survives container restarts but doesn't clutter your local folder.
-   ✅ **Remote Ready**: Works perfectly over SSH or in GitHub Codespaces.

### Cons
-   ❌ **Resource Intensive**: Running two AEM instances in Docker requires significant RAM (minimum 16GB recommended).
-   ❌ **Initial Build Time**: The first time you build the container, it needs to download about 1GB of image data and tools.

## 📦 Volume Management
AEM data is stored in these Docker volumes:
- `aem-author-data`: Author instance repository.
- `aem-publisher-data`: Publisher instance repository.

To reset AEM completely: `docker compose down -v`

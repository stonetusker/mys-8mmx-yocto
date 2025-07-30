### Summary: Yocto Build Workflow for mys-8mmx

This GitHub Actions workflow automates the process of building a Yocto-based Linux image for the MYIR mys-8mmx board. It uses Docker to ensure a consistent build environment and leverages caching to improve build efficiency.

### Step-by-Step Workflow Breakdown

**Trigger:**
- Manually triggered using `workflow_dispatch`.

**Execution Environment:**
- Runs on a self-hosted runner labeled `yocto-builder` or `embedded-builder`.

**Timeout:**
- Set to 1440 minutes (24 hours) to accommodate the lengthy Yocto build process.

### Setup & Initialization

- **Clean Workspace:**
  - Removes any previous data in the working directory and temporary SSH files.

- **Checkout Repository:**
  - Pulls the source code from the GitHub repository to use for the build.

- **Create Cache Directories:**
  - Initializes two host-side directories:
    - One for source downloads (DL_DIR)
    - One for the shared state cache (SSTATE_DIR)

### Docker Configuration

- **Build Docker Image:**
  - Builds a Docker image based on Ubuntu 18.04 with Yocto 3.0 tools using a custom Dockerfile.

- **Clean Existing Container:**
  - Ensures any previous container with the same name is removed to avoid conflicts.

- **Setup SSH Keys:**
  - Copies SSH credentials into a temporary folder and sets permissions.
  - Prepares the container to securely access private Git repositories.

- **Launch Docker Container:**
  - Starts a new container in detached mode.
  - Mounts the source code, SSH key directory, and cache directories into the container.

- **Fix Container Permissions:**
  - Sets ownership inside the container so the build user has the right access to mounted directories.

### Yocto Build Process

- **Set Up Build Environment:**
  - Runs the setup script to configure the Yocto build environment for the mys-8mmx board.

- **Fetch Sources:**
  - Downloads all required layers and source files to reduce the chance of download-related build failures.

- **Build Image:**
  - Executes the Yocto `bitbake` process to generate a full Linux image for the board.

### Post-Build Tasks

- **Cleanup:**
  - Resets workspace permissions and removes temporary SSH artifacts.

- **Collect Artifacts:**
  - Copies important outputs into a staging folder, including:
    - Compressed image files
    - Kernel binaries
    - Device tree blobs
    - Bootloader images
    - Root filesystem archives

- **Archive Artifacts:**
  - Packages all relevant files into a ZIP archive.

- **Upload Artifacts:**
  - Uploads the archive to GitHub as a downloadable workflow artifact.

### Final Outcome:

- A fully built Yocto image tailored for the mys-8mmx hardware.
- All relevant output files are archived and available as downloadable GitHub Actions artifacts.


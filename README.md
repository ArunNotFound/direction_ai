# Weave Tool Documentation

This document serves as a quick-start guide and reference for the `weave` command-line tool, based on observed usage and features.

## What is Weave?
Weave is a package and cluster management tool that allows you to manage repositories, install packages, and spin up local development sandbox environments. It interacts with cluster daemons and handles dependencies, node memberships, runtime secrets, and more.

## Installation
You can install `weave` using its official installation script.
```bash
curl -fsSL https://get.weave.build | sh
```
This will download and place the `weave` binary in your local bin directory (usually `~/.local/bin/weave`).

### Common Installation Dependencies
The `weave` binary requires certain shared libraries to run correctly.
- **libpq5**: Required for database interactions. If you see an error like `libpq.so.5: cannot open shared object file`, install it via your package manager:
  ```bash
  sudo apt-get update && sudo apt-get install -y libpq5
  ```

## Local Development Sandbox
Weave allows you to spin up a local sandbox cluster via Docker Compose to test packages and configurations securely.

### Prerequisites for Sandbox
- **Docker & Docker Compose**: The sandbox heavily relies on Docker Compose V2. Ensure you have it installed:
  ```bash
  sudo apt-get install -y docker.io docker-compose-v2
  ```

### Starting the Sandbox
To spin up a sandbox environment, use the `sandbox up` command:
```bash
LC_ALL=C.UTF-8 LANG=en_US.UTF-8 weave --accept-eula sandbox up --name <sandbox_name>
```

> [!TIP]
> **Troubleshooting Sandbox Bootstrapping**
> - **EULA Prompt**: The sandbox command might hang or crash while printing the End User License Agreement. Pass `--accept-eula` to bypass it.
> - **Encoding Errors**: If you encounter an error like `hGetContents: invalid argument (cannot decode byte sequence)`, it means the terminal's locale is struggling to decode output logs. Prefix the command with explicit UTF-8 locales (`LC_ALL=C.UTF-8 LANG=en_US.UTF-8`) to fix it.

Once the sandbox is running, it will provide you with a cluster address (e.g., `localhost:18080`), namespace, and worker count.

### Stopping the Sandbox
To tear down the sandbox:
```bash
weave sandbox down
```

## Basic Workflows

### Package Management
Once connected to a cluster (or your local sandbox), you can manage packages.
1. **Add a Repository:** `weave repository add <repo_url>`
2. **Update Index:** `weave update`
3. **Install Package:** `weave install <package_name>`
4. **View Installed:** `weave package`
5. **Remove Package:** `weave uninstall <package_name>`

### Common Commands Reference
Below are some of the most useful commands categorized by their function. Run `weave <command> --help` for specific details.

**Development & Building:**
- `weave build`: Build packages locally.
- `weave check`: Type-check workspace packages.
- `weave dev`: Live overlay dev inner-loop (push a local change and re-install only the diff).

**Publishing:**
- `weave dist`: Build and create distribution packages.
- `weave publish`: Build, dist, and publish packages to a repository.
- `weave release`: Capture a validated composition as a versioned release.

**Administration & Runtime:**
- `weave cluster`: Administer the cluster daemon.
- `weave namespace`: Manage namespaces.
- `weave secret`: Manage runtime secrets.
- `weave auth`: Authz introspection (can-i, who-can, whoami).
- `weave resource`: Inspect managed resources and claim allocations.

## Configuration & Data Paths
- **Binary Location**: `~/.local/bin/weave`
- **Configuration**: `~/.config/weave/` (Contains EULA acceptance, contexts, etc.)
- **Data Directory**: `~/.local/share/weave/`

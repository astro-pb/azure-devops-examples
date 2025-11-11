# Azure DevOps Examples

This repository contains three CI/CD pipeline samples that demonstrate different ways to deploy to an Astronomer environment.

- `azure-deploy-action.yaml` shows how to trigger an Astronomer deployment from GitHub Actions.
- `azure-deploy-basic.yml` is a minimal Azure Pipelines definition that installs the Astro CLI via the install script and deploys with service-account credentials.
- `azure-deploy-download-executable.yaml` is an Azure Pipelines definition that downloads the Astro CLI on the fly and runs `astro deploy`.

## GitHub Actions workflow (`azure-deploy-action.yaml`)

1. Copy the file into `.github/workflows/` in your repository.
2. Set a repository variable named `ASTRO_EXAMPLE_DEPLOYMENT_ID` to the Astronomer Deployment ID you want to target.
3. Create a repository secret `ASTRO_API_TOKEN` that holds an Astronomer API token with deploy permissions.
4. Push to `main` to trigger the workflow; it will invoke `astronomer/deploy-action@v0.10.1` with the commit message and link included in the deployment description.

Adjust the `on` block if you need different triggers (e.g., branches, tags, or manual dispatch).

## Azure Pipelines quick start (`azure-deploy-basic.yml`)

1. Import the YAML file into Azure DevOps and connect a pipeline to it.
2. Create two secure pipeline variables (or a variable group) named `ASTRONOMER_KEY_ID` and `ASTRONOMER_KEY_SECRET` populated with an Astronomer service account key that has deploy access to your workspace.
3. Set the `ASTRO_PROJECT_DIR` variable to the folder (relative to the repo root) that contains your Astronomer project. It defaults to `example_project_dir`.
4. Run the pipeline. It installs the latest Astro CLI via `install.astronomer.io`, changes into `$(Build.SourcesDirectory)/$(ASTRO_PROJECT_DIR)`, and executes `astro deploy` using the provided key pair.

This template is ideal when you just added a new DAG or updated an existing one and want a straightforward automated deploy. Add pre-deploy validation (tests, linting) as needed by inserting additional `script` steps.

## Azure Pipelines definition (`azure-deploy-download-executable.yaml`)

1. Import the YAML file into Azure DevOps and create a pipeline that points to it.
2. Set the pipeline variables to match your deployment:
   - `ASTRO_VERSION`: Astro CLI release to download.
   - `ASTRO_PROJECT_DIR`: folder within the repo that contains the Astronomer project files (defaults to `example_project_dir`).
   - `OS` / `ARCH`: platform used for the CLI download.
3. Add a secure pipeline variable or variable group for any required Astronomer credentials (for example `ASTRO_DEPLOYMENT_ID` or API token) and reference them from the script before running `./astro deploy`.

The script downloads the specified Astro CLI tarball, extracts it into `$(Build.SourcesDirectory)/$(ASTRO_PROJECT_DIR)`, marks the CLI executable, and executes `astro deploy`. Extend the script with additional steps (linting, testing, environment prep) as needed for your deployment flow.

## Getting Started Locally

Both pipelines expect that your repository already contains a valid Astronomer project. You can initialize one locally with:

```bash
astro dev init
```

After configuring and testing the project locally, commit the project files, add the desired pipeline YAML, and wire up the required variables/secrets in your CI system.

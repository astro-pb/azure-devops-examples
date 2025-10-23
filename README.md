# Azure DevOps Examples

This repository contains a pair of CI/CD pipeline samples that demonstrate a few different ways to deploy to an Astronomer environment.

- `azure-deploy-action.yaml` shows how to trigger an Astronomer deployment from GitHub Actions.
- `azure-deploy-download-executable.yaml` is an Azure Pipelines definition that downloads the Astro CLI on the fly and runs `astro deploy`.

## GitHub Actions workflow (`azure-deploy-action.yaml`)

1. Copy the file into `.github/workflows/` in your repository.
2. Set a repository variable named `ASTRO_EXAMPLE_DEPLOYMENT_ID` to the Astronomer Deployment ID you want to target.
3. Create a repository secret `ASTRO_API_TOKEN` that holds an Astronomer API token with deploy permissions.
4. Push to `main` to trigger the workflow; it will invoke `astronomer/deploy-action@v0.10.1` with the commit message and link included in the deployment description.

Adjust the `on` block if you need different triggers (e.g., branches, tags, or manual dispatch).

## Azure Pipelines definition (`azure-deploy-download-executable.yaml`)

1. Import the YAML file into Azure DevOps and create a pipeline that points to it.
2. Set the pipeline variables to match your deployment:
   - `ASTRO_VERSION`: Astro CLI release to download.
   - `WORKING_DIR`: folder within the repo that contains the Astronomer project files (defaults to `astro_project_dir`).
   - `OS` / `ARCH`: platform used for the CLI download.
3. Add a secure pipeline variable or variable group for any required Astronomer credentials (for example `ASTRO_DEPLOYMENT_ID` or API token) and reference them from the script before running `./astro deploy`.

The script downloads the specified Astro CLI tarball, extracts it into the working directory, marks the CLI executable, and executes `astro deploy`. Extend the script with additional steps (linting, testing, environment prep) as needed for your deployment flow.

## Getting Started Locally

Both pipelines expect that your repository already contains a valid Astronomer project. You can initialize one locally with:

```bash
astro dev init
```

After configuring and testing the project locally, commit the project files, add the desired pipeline YAML, and wire up the required variables/secrets in your CI system.

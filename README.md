# dab-templates

A collection of Databricks Asset Bundle (DAB) templates for streamlining development of Databricks projects with automated deployment via Azure Pipelines.

## Overview

This repository contains the following templates:

- **dab-project**: Base template for creating a new Databricks Asset Bundle project with Azure Pipelines integration
- **dab-target**: Template for adding new deployment targets (environments) to your project
- **dab-job**: Template for adding new Databricks jobs to your project

## Prerequisites

- [Databricks CLI](https://docs.databricks.com/aws/en/dev-tools/cli/install) installed and configured

## How to Use

### Creating a Project

Initialize a new Databricks Asset Bundle project with our template.
Replace `PROFILE_NAME` with the Databricks profile name you will use for developing and testing your project:

```sh
databricks bundle init https://github.com/husqvarnagroup/dab-template --template-dir dab-project --profile PROFILE_NAME
```

This will create a new project with the following structure:

- Azure Pipelines configuration for CI/CD
- Databricks bundle configuration
- Directory structure for jobs and targets

After creating your project, you should:

1. Install and configure pre-commit hooks as follows:

   ```sh
   pip install --user pre-commit  # or pipx install pre-commit
   pre-commit install
   ```

2. Update the `targets/dev.yml` file:
   - Verify the catalog
   - Update the storage_root (see your Databricks workspace or cloud storage configuration)

### Creating a New Job/Workflow

Add a new Databricks job to your project:

```sh
databricks bundle init https://github.com/husqvarnagroup/dab-template --template-dir dab-job --profile PROFILE_NAME
```

After creating a job, configure it by:

- Edit the Python file `jobs/[job_name]/<file_name>.py` to implement your job logic.
- Update the YAML configuration file `jobs/[job_name]/job.yml` with the appropriate settings.

### Creating a New Target

Add a new deployment target (environment) to your project as follows.
Replace `TARGET_PROFILE_NAME` with the Databricks profile name for the target environment (e.g., production, staging):

```sh
databricks bundle init https://github.com/husqvarnagroup/dab-template --template-dir dab-target --profile TARGET_PROFILE_NAME
```

For each target added:

1. Update the `targets/prod_<environment>.yml` file:
   - Verify the catalog
   - Update the storage_root (see your Databricks workspace or cloud storage configuration)
   - Update the slack_channel_email
   - Set the service_principal_name

2. Add the target to the `azure-pipelines.yml` file in the strategy matrix:

   ```yaml
   strategy:
     matrix:  # Add your targets here
       prod_<environment>:
         target: 'prod_<environment>'  # Your newly added target
   ```

### Deployment Workflow

The templates include Azure Pipelines configurations that will:

1. Validate your Databricks Asset Bundle
2. Deploy your bundle to the specified targets when merging to the main branch

### Useful Commands

```sh

# Validate bundle configuration for errors, warnings, and recommendations.
# Validation checks the configuration syntax and schema, permissions, etc.
databricks bundle validate -t <target>

# Summarize resources deployed by this bundle with their workspace URLs.
# Useful after deployment to see what was created and where to find it.
databricks bundle summary -t <target>

# Deploy your bundle
databricks bundle deploy -t <target>
```

## How to Contribute

We welcome contributions! Please open an issue or submit a pull request if you have suggestions or improvements.

For more information about Databricks Asset Bundle templates, see the official documentation:
<https://docs.databricks.com/aws/en/dev-tools/bundles/templates>

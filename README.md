# Generate Structurizr Diagrams Images from DSL Action

## Introduction

This GitHub Action automatically generates and updates Structurizr diagrams on pull requests. It's designed to simplify the process of keeping architecture diagrams up-to-date with the latest changes in your codebase.

-> [See this PR for a demo](https://github.com/sebastienfi/structurizr-github-actions-demo/pull/2).

## Prerequisites

Before you use this action, make sure you have:

- A GitHub repository.
- A `workspace.dsl` file in your repository for Structurizr.

## Usage

To use this action in your workflow, follow these steps:

1. **Set up the workflow**: Create a `.github/workflows` directory in your repository if it doesn't exist.
2. **Create a workflow file**: Inside the `.github/workflows` directory, create a new file (e.g., `structurizr-diagrams-update.yml`) and add the following content:

```yaml
name: Update Structurizr Diagrams

on:
    pull_request:
        paths:
        - 'docs/workspace.dsl' # This setting will have the action trigger only if certain file changes. Set the location of your DSL.

concurrency:
  group: ${{ github.workflow }}-${{ github.event.pull_request.number || github.ref }}
  cancel-in-progress: true

jobs:
    update-diagrams:
        runs-on: ubuntu-latest
        container: ghcr.io/sebastienfi/structurizr-cli-with-bonus:latest
        permissions: 
          contents: write # Allow to work with the contents of the repository, including git push.
        steps:
        - uses: sebastienfi/structurizr-gen-images@v1
          with:
            repo-token: ${{ secrets.GITHUB_TOKEN }} # This token will be used by the action to perform git pull, git push, pr read, pr comment, pr comment edit, pr comment reaction.
            workspace-path: 'docs/workspace.dsl' # This setting will have the action generate images from this DSL. Set the location of your DSL.
            output-path: 'docs/diagrams/' # The output folder for generated images.
```

3. Commit the workflow to `main` and create a PR which include a change in your DSL file.
4. The action will commit the generated images to your Pr's branch.

## Inputs

This action accepts the following inputs:

- `workspace-path`: Path to the `workspace.dsl` file.
  - **Required**: Yes
  - **Default**: `docs/workspace.dsl`

## Outputs

This action does not produces any output.

## Additional Notes

- Ensure that the `workspace.dsl` file is correctly set up and located in the specified path.
- The action will check for changes in the diagram images and update them if necessary.
- Generated diagrams are committed and pushed to the same branch that initiated the pull request. Of course, if your diagram does not change, there will be no commit.

## Contributing

Contributions to this action are welcome. Please ensure that your pull requests are well-described and adhere to the project's coding standards.

# Speckle Automate GitHub Composite Action

[![Twitter Follow](https://img.shields.io/twitter/follow/SpeckleSystems?style=social)](https://twitter.com/SpeckleSystems) [![Community forum users](https://img.shields.io/discourse/users?server=https%3A%2F%2Fdiscourse.speckle.works&style=flat-square&logo=discourse&logoColor=white)](https://discourse.speckle.works) [![website](https://img.shields.io/badge/https://-speckle.systems-royalblue?style=flat-square)](https://speckle.systems) [![docs](https://img.shields.io/badge/docs-speckle.guide-orange?style=flat-square&logo=read-the-docs&logoColor=white)](https://speckle.guide/dev/)

## Introduction

This repository contains the source code for the Speckle Automate GitHub Composite Action. It is a GitHub Action that registers, builds, and publishes a [Speckle Automate](https://speckle.systems) [Function](https://speckle.guide).

## Documentation

### Inputs

#### `speckle_automate_url`

Speckle Automate URL which we will publish our Function to. Should include the `https://` protocol and not end with a trailing slash. **The token must be stored in GitHub as an [encrypted secret](https://docs.github.com/en/actions/security-guides/encrypted-secrets)**

Defaults to [`https://automate.speckle.dev`](https://automate.speckle.dev). <!-- TODO update when we release production server -->

#### `speckle_token`

The Speckle Automate API token to use to publish the function. This token must have `functions:write` permissions.

**This must be stored in GitHub as an [encrypted secret](https://docs.github.com/en/actions/security-guides/encrypted-secrets)**. This token must be protected, as it allows anyone with access to it to publish functions as you to Speckle Automate.

If you believe your token has been compromised, please revoke it immediately on your [Speckle Automate Server](https://automate.speckle.dev/tokens). Please also audit your Speckle Automate Function changes to ensure that they were not made by an unauthorized party.

Please note that this is not a Speckle Account token, but a **Speckle Automate API** token. You can create one by logging into the [Speckle Automate Server](https://automate.speckle.dev) and going to the [API Tokens](https://automate.speckle.dev/tokens) page.

#### `speckle_function_id`

*Required.* The unique identifier of the function. Go to Speckle Automate to register your Function and get its Identifier.

Your Speckle Token must have write permissions for the Speckle Function with this ID, otherwise the publish will fail.

#### `speckle_function_input_schema_file_path`

The path to a file containing a JSON Schema. This JSON Schema defines the parameters required by the Function. The JSON Schema will be used to automatically create the User Interface displayed to users of your Function in Speckle Automate. Users will be able to provide their data to customise the Function.

Defaults to `./input-schema.json`

#### `speckle_function_command`

*Required.* The command to run to execute the function in a runtime environment.

For example, if you are using Python, you might use `python3 main.py`. If you are using Node.js, you might use `node main.js`. If using bash shell, `bash main.sh` etc. etc..  Even if the command is defined within your Dockerfile (for examples, using the `CMD` or `ENTRYPOINT` statements), you must provide the command here in order for Speckle Automate to run the function.

#### `speckle_function_recommended_cpu_m`

The recommended maximum CPU in millicores for the function. 1000 millicores = 1 CPU core. Defaults to 1000 millicores (1 CPU core). If the Function exceeds this limit, it will be throttled to run within the limit.

#### `speckle_function_recommended_memory_mi`

The recommended maximum memory in mebibytes for the function. 1024 mebibytes = 1 gibibyte. Defaults to 100 mebibytes. If the Function exceeds this limit, it will be terminated.

#### `dockerfile_path`

Path to the Dockerfile to be used to build the Speckle Function.

Defaults to `./Dockerfile`

#### `docker_context`

Path to the directory containing the build context for the Docker image. This is typically the root of the repository, or the directory containing the Dockerfile.

Defaults to `./`, the root of the repository.

### Outputs

This GitHub Action does not have any outputs. Please instead inspect the logs to see the result of the action, and view the results on Speckle Automate.

### Example usage

#### Publish a Speckle Function, and build the Docker Image using Docker

1. You will first need to create the required secret and [store it in GitHub Actions](https://docs.github.com/en/actions/security-guides/encrypted-secrets#creating-encrypted-secrets-for-a-repository).
    - `SPECKLE_FUNCTION_PUBLISH_TOKEN`: Please note that this is not a Speckle Account token, but a **Speckle Automate API** token. You can create one by logging into the [Speckle Automate Server](https://automate.speckle.dev) and going to the [API Tokens](https://automate.speckle.dev/tokens) page.

1. You will need to create a GitHub Actions workflow file. You can create a new workflow file, `.github/workflows/speckle-automate.yml`, in your repository.

1. You will need to add the following to your workflow file, replacing `XXXXXXXXXX` with your Speckle Function ID, and `echo "Hello World!"` with your Speckle Function command:

    ```yaml
    name: Speckle Function CI

    on:
      push:
        branches:
          - 'main'

    jobs:
      docker:
        runs-on: ubuntu-latest
        steps:
          -
            name: Register, Build, and Publish a Speckle Function
            uses: specklesystems/speckle-automate-github-composite-action
            with:
              speckle_token: ${{ secrets.SPECKLE_FUNCTION_PUBLISH_TOKEN }}
              speckle_function_id: XXXXXXXXXX # <-- REPLACE WITH YOUR SPECKLE FUNCTION ID
              speckle_function_command: 'echo "Hello World!"' # <-- REPLACE WITH YOUR SPECKLE FUNCTION COMMAND
    ```

1. And that's it! Now commit to your repository `main` branch, and you should see a new version of your Speckle Function registered in Speckle Automate!

## Developing & Debugging

### Running locally

This is a GitHub Action, intended to be run within GitHub Actions. It is not intended to be run locally.

### Developing

#### Prerequisites to developing

- [Pre-Commit](https://pre-commit.com/#install)

1. Clone this repository
1. Run `pre-commit install` to install the pre-commit hooks

#### Testing

There are no unit tests as this is a GitHub Action. You can test the action by running it in a GitHub Actions workflow.

#### Linting

1. Run `yarn precommit` to run all pre-commit hooks.
1. You must address all linting errors prior to committing changes. The CI will fail if there are any linting errors, and you will be unable to merge your PR.

## Contributing

Please make sure you read the [contribution guidelines](.github/CONTRIBUTING.md) and [code of conduct](.github/CODE_OF_CONDUCT.md) for an overview of the practices we try to follow.

## Community

The Speckle Community hangs out on [the forum](https://discourse.speckle.works), do join and introduce yourself & feel free to ask us questions!

## Security

For any security vulnerabilities or concerns, please contact us directly at security[at]speckle.systems.

## License

Unless otherwise described, the code in this repository is licensed under the Apache-2.0 License. Please note that some modules, extensions or code herein might be otherwise licensed. This is indicated either in the root of the containing folder under a different license file, or in the respective file's header. If you have any questions, don't hesitate to get in touch with us via [email](mailto:hello@speckle.systems).

## Acknowledgements

# Master to Main

A CLI tool to rename master branch to main for GitHub repos.

- [Quick Start](#quick-start)
- [Running](#running)
- [Details](#details)
- [Developing](#developing)

# Quick Start
master-to-main can be installed using either npm or yarn

```
npm install -g @guardian/master-to-main
```

```
yarn global add @guardian/master-to-main
```

It can then be run  as follows

```sh-session
$ m2m [OWNER/REPO] [TOKEN]
running command...
```

View all options and help information

```
$ m2m --help
...
```

View installed version

```sh-session
$ m2m (-v|--version)
master-to-main/0.0.0 darwin-x64 node-v14.9.0
```

It can also be used directly via npx

```
npx @guardian/master-to-main --help
```

# Running

This tool requires two arguments.

| Argument    | Description                                                                      |
| ----------- | -------------------------------------------------------------------------------- |
| accessToken | A GitHub personal access token. See the [auth](###auth) section for more details |
| repoName    | The name of the repository to migrate in the form `owner/repository`             |

This tool can also be run with a number of options. The following table lists them all.

| Option   | Short | Description                                                                               | Default |
| -------- | ----- | ----------------------------------------------------------------------------------------- | ------- |
| from     | -f    | The current name of the branch                                                            | master  |
| to       | -t    | The new name of the branch                                                                | main    |
| force    | -     | Disable any user prompts                                                                  | false   |
| dry-run  | -     | Log all of the steps but do not execute                                                   | false   |
| verbose  | -     | Output debug logs                                                                         | false   |
| guardian | -     | Run the guardian specific steps around build configuration. Disable using `--no-guardian` | true    |
| issues   | -     | Open issues for any further changes required. Disable using `--no-guardian`               | true    |

As well as this, the `--version` option can be used to display the current version install and the `--help` option can be used to display the help information.

# Details

This tool is built on top of the [oclif](https://oclif.io/) library to provide CLI functionality.

It interfaces with the GitHub API to carry out the necessary [steps](###steps) for the migration (more information below). It makes use of the [octokit](https://github.com/octokit/rest.js/) library.

### Auth

Authentication and authorisation is handled using GitHub Personal Access Tokens (PATs). You will need to provide this using the `-t` option when running the tool.

You can create a token from the `Developer settings` tab within GitHub settings. For public repos, the `public_repo` scope will suffice. For private repos, the `repo` scope is needed.

### Steps

The process carries out the following steps in order:

1. Check if the repository exists (by getting the repo object)
1. Check that the old branch name exists
1. Check if the new branch name already exists
1. Check if the user is an admin (by getting the branch protection for the master branch)
1. Get the number of open PRs and check with that user that they're happy to proceed
1. Get the most recent commit sha from the master branch
1. Create the new branch
1. Update the default branch (if it used to be the old branch)
1. Copy the branch protections from the old branch to the new branch
1. Remove the branch protection from the old branch
1. Update all outstanding PRs
1. Delete the master branch
1. Check if a `riff-raff.yaml` file is present and open an issue if it is (unless the `--no-guardian` option is passed)
1. Check for any files that reference the old branch name and open an issue if any exist
1. Open an issue to cover any (other) build configuration that may need updating

# Developing

## Requirements

This project is written in Typescript and uses the yarn package manager.

## Setup

1. Clone the repository

```
git clone https://github.com/guardian/master-to-main.git
cd master-to-main
```

2. Install the dependencies

```
yarn
```

3. Run the command locally

```
./bin/run ...
```

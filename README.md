<!-- Badges -->
[![Build & Deploy Docs](https://github.com/vrui-vr/docs/actions/workflows/build-and-deploy.yml/badge.svg)](https://github.com/vrui-vr/docs/actions/workflows/build-and-deploy.yml)
[![Documentation](https://img.shields.io/badge/docs-mkdocs-blue)](https://vrui-vr.github.io/docs/)
[![License: GPL v2](https://img.shields.io/badge/License-GPL_v2-blue.svg)](https://www.gnu.org/licenses/old-licenses/gpl-2.0.en.html)

# docs

A central repository that hosts the configuration and build settings for documentation across all repos in the VRUI organization.

The docs are built using [MkDocs](https://www.mkdocs.org/) with the [Material for MkDocs theme](https://squidfunk.github.io/mkdocs-material/).

Check out the [Table of Contents](#table-of-contents) for details regarding how 

## Table of Contents

- [docs](#contributing-to-vrui)
   - [Table of Contents](#table-of-contents)
   - [How it works](#how-it-works)
      - [Updating the docs site](#updating-the-docs-site)
      - [Site config](#site-config)
      - [Merging nav.yml files](#merging-navyml-files)
      - [Behind the scenes](#behind-the-scenes)
   - [Set up (VSCode)](#recommended-contributor-setup-and-workflow-vscode)
      - [Forking and Cloning](#forking-and-cloning)
         - [Forking](#forking)
         - [Cloning](#cloning)
      - [Setting Up and Fetching Upstream](#setting-up-and-fetching-upstream)
      - [Optional: Setting up a VSCode workspace](#setting-up-a-vscode-workspace)
   - [Workflow](#testing-changes-locally)
      <!-- - go to main branch
      - fetch and merge -->
      - [Adding documentation for a new repo](#adding-documentation-for-a-new-repo)
      - [Testing changes locally]()

## How it works

Documentation for each repo is hosted in the `docs/` directory of that repo. This repository aggregates and builds the documentation from all repos in the VRUI organization.

### Updating the docs site

When the `docs/` directory is updated in any repo (e.g. `vrui-vr/arsandbox`), the `docs-update.yml` GitHub Action is triggered, which tells the `docs` repo (this one) to pull in the latest changes and rebuild the documentation site.

The most up-to-date version of `docs-update.yml` is located in this repository under `templates/workflows/docs-update.yml`.

### Site config

The main configuration for the documentation site is in `mkdocs.base.yml`. This file specifies the site name, navigation menu, theme, plugins, and other settings.

This is important because it ensures a consistent look/feel across all documentation and access to plugins, regardless of which repo the docs come from.

### Merging `nav.yml` files 

While the base navigation structure is defined in `mkdocs.base.yml`, each repo can define its own navigation structure by adding a `nav.yml` file in its `docs/` directory.

The `nav.yml` files from each repo are merged into (`mkdocs.generated.yml`) which is used to build the documentation site. The merging is done in the order that the repos are listed in `repos.txt`.

The base navigation structure is defined in `mkdocs.base.yml`, which includes the home page and other common pages. The `mkdocs.yml` file then includes this base navigation and the generated navigation.

1. First, the `build-and-deploy-docs.yml` GitHub Action in this repository pulls in the latest changes under `<repo>/docs/`from all repos listed in `repos.txt`.
2. Then, it runs the `scripts/generate-mkdocs.py` script to merge the `nav.yml` files from each repo into a single `mkdocs.generated.yml` file.

### Behind the scenes

In order to make all the repos play nice with each other, the `docs-update.yml` workflow in each repo references a common `DOCS_DEPLOY_TOKEN` secret, which is a GitHub personal access token with `repo` and `workflow` scopes.

This token is an organization-level secret, so it can be used by any repo in the VRUI organization. That way, no individual repo needs to have its own token, which would be a pain to manage.

Most of the existing repositories in the VRUI organization already have access to this secret. However, if you are creating a new repo and want to add documentation for it, you will need to give that repo access to the `DOCS_DEPLOY_TOKEN` secret by going to VRUI's Settings > Secrets and variables > Actions > Organization secrets, and then clicking "Edit DOCS_DEPLOY_TOKEN" (the pencil icon). Once there, click the gear icon and add the new repository to the list of selected repositories that can access the secret.

Important!!! The `DOCS_DEPLOY_TOKEN` secret value is based on a personal access token (PAT) that is owned by a specific user (currently me: @nredick). It is set to never expire, but if that user leaves the organization, the token will need to be regenerated and updated in the organization secrets to someone else with permissions to make changes to the repos. In short, the PAT behind the `DOCS_DEPLOY_TOKEN` secret allows the repos to act on the user's behalf to tell `vrui-vr/docs` that it's time to rebuild (which requires a certain level of permissions).

What this looks like:

![Screenshot of GH Actions run](assets/media/gh-run.png)

A new build and deploy job is triggered, and appears to be "manually" run by the user who owns the PAT (currently me: @nredick).

## Recommended Contributor Setup and Workflow (VSCode)

Below, we'll include some tips of how to setup a VSCode workspace, clone and fork multiple repositories, and default/upstream repositories, if you choose to contribute to Vrui documentation and/or code.

### Setting up a VSCode workspace

To contribute to Vrui, either in documentation and/or in code, one easy way to work in multiple cloned Vrui repositories in parallel is in a VSCode workspace. A VSCode workspace is a collection of one or more folders that are opened in a VSCode window (instance). Vrui is organized in multiple repositories (found [here](https://github.com/vrui-vr)), including a repository for each supported application, a repository to setup documentation (`vrui-vr/docs`), and the toolkit itself (`vrui-vr/vrui`). As an example, we will show the steps to clone the `vrui-vr/docs` and `vrui-vr/vrui` repositories.

<!-- make dir for parent directory -->

??? note
   Cloning and Forking repositories will be expanded upon in the [next section](#cloning-and-forking), and is also covered in the [Contributing doc](https://vrui-vr.github.io/docs/CONTRIBUTING/).

To create a workspace, first you will need to create a folder in your filesystem, in which you will keep cloned (and forked) Vrui repositories. This can be done in a terminal in VSCode via the command: 

```sh
mkdir <parent-directory>
```

To move into are in the parent 

```sh
cd <vrui-title>
```


???+ tip
   If you would like to download VSCode, check out this link: [Download Visual Studio Code](https://code.visualstudio.com/download). For a more comprehensive explanation of workspaces, check out this link: [What is a VS Code workspace?](https://code.visualstudio.com/docs/editing/workspaces/workspaces)

### Forking and Cloning

?? info "Heads up!"
   These instructions are adapted from GitHub's site, which contains more comprehensive instructions: [Fork a repository](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/fork-a-repo)

#### Forking
To make a copy of an application repository (and the `vrui-vr/docs` repository), you will create a fork, a new repository that is a copy of the original “upstream” repository (created by Vrui maintainers). Forks can be used to create changes outside of the upstream repository, which are then proposed back to the upstream repository in the form of a pull request. 

To fork a repo, navigate to your preferred repo, click the fork button, and create a new fork with your GitHub account as the owner, checking the "Copy the `main` branch only" box: 

![](assets/media/Fork_Button_Screenshot.png)

This fork can now be found in your GitHub account's list of repositories.

#### Cloning

After forking, you will have a fork of a Vrui repo in you GitHub account. Next, you will need to clone that fork to save the files in that repository locally on your computer. The following steps cover one way of cloning your fork and opening it in your previously created VSCode workspace: 

1. First, navigate your fork in GitHub and copy the HTTPS URL:

![](assets/media/GitHub_clone_url.png)

2. In a new VSCode window, open your earlier created workspace (a folder in your computer's file system). 

3. Navigate to the Source Control tab: 

![](assets/media/VSCode_Source_Control_Tab.png)

And click the "Clone Repository" button, where you will be able to paste your fork's URL in VSCode's search bar:

![](assets/media/VSCode_Clone_URL.png)

?? info "Heads up!"
   Check out the "Cloning your forked repository" section in the GitHub page referenced earlier: [Fork a repository](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/fork-a-repo)


### Adding and Fetching the Upstream

After forking and cloning one of Vrui's repositories, you may need to add an "upstream" repository explicitly. An upstream repository is the original project repository that you forked or cloned from. In this case, you want to set the upstream as the original Vrui repo that you forked, as found on [Vrui's GitHub](https://github.com/vrui-vr). The upstream repository is where Vrui maintainers will add updates to code and documentation. By adding the upstream, you will then be able to safely incorporate Vrui maintainer's latest changes to the repositories into your local fork. In a VSCode terminal, copy the following commands in your VSCode terminal to add the original repository as the upstream:

??? info "Heads up!"
    Angle brackets `<>` in commands below are placeholders, meaning that you have to replace everything between, and including, the angle brackets with some text that depends on your specific circumstances.

To check whether the upstream was added automatically after cloning:

```sh
```


1. To find the link in this command, navigate to the upstream (`vrui-vr/<repo_name>`) repository in GitHub and copy the HTTPS URL (which is slightly different from the repo's URL in your browser):

![](assets/media/GitHub_clone_url.png)

```sh
git remote add upstream https://github.com/<ORIGINAL-OWNER/ORIGINAL-REPOSITORY.git>
```

2. This command downloads the latest changes made in the upstream repository into your local Git repository without automatically merging them into the current branch (which may be main, if you haven't created another branch) of your fork.

```sh
git fetch upstream
```

3. This command integrates the latest changes from the "upstream" repository, downloaded in the last command, into your own fork.

```sh
git merge upstream/main
```

?? info "Heads up!"
   Check out the "Configuring Git to sync your fork with the upstream repository" section in the GitHub page referenced earlier: [Fork a repository](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/fork-a-repo)

???+ tip
   Check out GitHub's page on repositories to understand the different kinds and terminology: [About repositories](https://docs.github.com/en/repositories/creating-and-managing-repositories/about-repositories).


## Getting started: adding documentation for a new repo

1. Ensure the new repo has a `docs/` directory with an `index.md` file.
2. Copy `templates/workflows/docs-update.yml` to `.github/workflows/docs-update.yml` in the repo that you are creating docs in. See [`vrui-vr/arsandbox/docs/nav.yml`](https://github.com/vrui-vr/arsandbox/blob/main/docs/nav.yml) for an example of how to structure the `nav.yml` file. Recommended: check out `mkdocs.generated.yml` in *this* repository to see how the `nav.yml` files are merged.
3. Create the `nav.yml` file in the `docs/` directory of the new repo, following the format of existing `nav.yml` files. (Do not rename it to something else, as the script expects it to be named `nav.yml`.)
4. Add the new repo to the `repos` list in `repos.text` in *this* repository.
5. Create a change in the main branch of the repo that you are adding docs for, which will trigger the `docs-update.yml` workflow and update the docs site.
6. Check out the changes at https://vrui-vr.github.io/docs/ after a the build-and-deploy job has completed (a few minutes).

### Workflow

####

####

#### Testing changes locally

You can (AND SHOULD) test any changes to the documentation site locally by following these steps:

1. Clone this repository (and the repository you are creating docs for) to your local machine.
2. Ensure that they are located in the same parent directory, e.g.:

   ```sh
   ~/src/docs
   ~/src/arsandbox
   ```

3. Make sure you have the correct dependencies installed. If you use conda, you can create and activate the environment with:

   ```sh
   conda env create -f environment.yml
   conda activate vrui
   ```

4. Run `./scripts/local-build-and-serve.sh` from the root of *this* repository. This will create symbolic links to the `docs/` directories of all repos listed in `repos.txt` if you have a local version of that repo, then it will generate the merged `mkdocs.generated.yml` file, and then start a local MkDocs server. When finished, it will clean up the symbolic links.

Example output:
```
❯ ./scripts/local_build_and_serve.sh
Preparing symlink for vrui...
✅ Linked /Users/<USER>/repos/datalab/vrui/docs → /Users/<USER>/repos/datalab/docs/docs/vrui
Preparing symlink for kinect...
✅ Linked /Users/<USER>/repos/datalab/kinect/docs → /Users/<USER>/repos/datalab/docs/docs/kinect
Preparing symlink for arsandbox...
✅ Linked /Users/<USER>/repos/datalab/arsandbox/docs → /Users/<USER>/repos/datalab/docs/docs/arsandbox
Generating mkdocs.yml...
Grabbing nav from docs/vrui/nav.yml
Grabbing nav from docs/kinect/nav.yml
Grabbing nav from docs/arsandbox/nav.yml
Starting local MkDocs server...
INFO    -  Building documentation...
INFO    -  Cleaning site directory
WARNING -  The following pages exist in the docs directory, but are not included in the "nav" configuration:
             - kinect/index.md
             - kinect/installation/intrinsically_calibrating_a_single_kinect.md
             - kinect/installation/per_pixel_depth_correction.md
             - kinect/installation/projection_matrix_calc.md
             - vrui/index.md
WARNING -  Doc file 'kinect/installation/guide.md' contains a link 'download_zip.png', but the target 'kinect/installation/download_zip.png' is not found among
           documentation files.
INFO    -  Documentation built in 0.32 seconds
INFO    -  [14:42:06] Watching paths for changes: 'docs', 'mkdocs.generated.yml', 'overrides', '/Users/<USER>/repos/datalab/vrui/docs',
           '/Users/<USER>/repos/datalab/kinect/docs', '/Users/<USER>/repos/datalab/arsandbox/docs'
INFO    -  [14:42:06] Serving on http://127.0.0.1:8000/docs/
^CINFO    -  Shutting down...
Cleaning up symlinks...
✅ Done.
```
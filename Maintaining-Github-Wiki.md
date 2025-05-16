# Maintaining the GitHub Wiki

# Introduction

GitHub Wikis are a convenient way to document projects, offering a simple interface for creating and managing content. However, their default editing experience poses significant limitations for collaborative and high-quality documentation efforts:

- **Live Edits Without Review**: All changes go live immediately, increasing the risk of publishing incomplete or incorrect content.
- **No Pull Requests or Reviews**: Wikis lack support for pull requests, making it difficult to review and manage contributions from multiple collaborators.
- **Limited Version Control Interface**: Although GitHub tracks changes, the wiki interface does not make it easy to compare revisions or revert changes effectively.

To overcome these limitations, this guide proposes using a separate GitHub repository to manage wiki content. This approach mirrors the official wiki while enabling a structured, collaborative documentation workflow. Key benefits include:

- **Pull Requests for Edits**: Contributors can submit changes via pull requests, enabling review, discussion, and approval before publishing.
- **Branch-Based Collaboration**: Multiple contributors can work in parallel without risking conflicts or overwrites.
- **CI/CD Integration with GitHub Actions**: Automated workflows push approved changes to the official GitHub Wiki, ensuring only vetted content is published.

This setup brings the rigor of modern software development—version control, code review, and automation—to your documentation process. It improves quality, reduces errors, and supports transparent collaboration.

The approach draws inspiration from best practices in the developer community, including:
- [The Secret GitHub Wiki Repo and CI/CD for Docs](https://medium.com/@thecybermonk/the-secret-github-wiki-repo-and-ci-cd-for-docs-fafe2583b72e) by [Sami Jawhar](https://github.com/sjawhar)
- [GitHub Wiki Tutorial for Technical Wiki Documentation](https://sparkbox.com/foundry/github_wiki_tutorial_for_technical_wiki_documentation) by [Ricardo Feiring](https://github.com/rfearing)


Both provide detailed guidance on setting up a standalone repository and integrating GitHub Actions for seamless documentation delivery.

For further information and context:

* GitHub Docs: [Creating and viewing wiki pages](https://docs.github.com/en/communities/documenting-your-project-with-wikis/creating-and-viewing-wiki-pages)
* GitHub Actions: [Creating a GitHub Action](https://docs.github.com/en/actions/creating-actions)

Whether you're fixing a typo, adding visuals, or documenting a new feature, this guide will walk you through contributing to the wiki in a maintainable, reviewable, and traceable way.

## Setup of the separate repository

The wiki is maintained in a separate repository named *<repository>-wiki*. This repository contains all the markdown files and assets used in the wiki. The actual GitHub Wiki is a separate repository called *<repository>.wiki*.

### Clone the *`<repository>.wiki`* repository

The first step is to clone the *`<repository>.wiki`* repository to your local machine. This repository contains the actual wiki content that is displayed on GitHub.

```bash
git clone <repository-url>.wiki.git
```

### Rename the cloned directory
After cloning the repository, rename the cloned directory to *`<repository-url>-wiki`*. This will be the main repository where all wiki content is stored and versioned.

```bash
mv <repository_url>.wiki <repository_url>-wiki
```

### Create a new repository on GitHub
Next, create a new repository on GitHub named *`<repository-url>-wiki`*. This will be the main repository where all wiki content is stored and versioned. Make sure to initialize the repository with a README file.
- Go to your GitHub account.
- Click on the **+** icon in the top right corner and select **New repository**.
- Name the repository *`<repository-url>-wiki`*.
- Add a description (optional).
- Choose whether to make the repository public or private.
- Initialize the repository with a README file.
- Click on **Create repository**.

### Remove the origin from the cloned repository
After creating the new repository, you need to remove the origin from the cloned repository. This is necessary to prevent any conflicts when pushing changes to the new repository.

```bash
cd repository-wiki
git remote remove origin
```

### Add the new repository as the origin
Now, add the new repository as the origin for the cloned repository. This will allow you to push changes to the new repository.

```bash
git remote add origin <repository-url>-wiki.git
```

### Push the cloned repository to the new GitHub repository
Finally, push the cloned repository to the new GitHub repository. This will upload all the wiki content to the new repository.

```bash
git push -u origin master 
```

## Create workflow
The next step is to create a GitHub Action workflow that will automatically push changes from the ''oai-pmh-wiki'' repository to the ''oai-pmh.wiki'' repository whenever a pull request is merged into the ''master'' branch of the ''oai-pmh-wiki'' repository.

You can create a new workflow by following these steps:
- Go to the **`<repository>-wiki`** repository on GitHub.
- Click on the **Actions** tab.
- Click on the **New workflow** button.
- Choose **Set up a workflow yourself**.
- This will create a new file called **`main.yml`** in the **`.github/workflows`** directory.

You can also create the workflow file manually by following these steps:
- Create a new directory called **`.github/workflows`** in the **`<repository>-wiki`** repository.
- Inside this directory, create a new file called **`<workflow-name>.yml`**.

Add the following content to the <code>push-wiki.yml</code> file:

```yaml
name: Push Wiki to GitHub Wiki

on:
  push:
    branches:
      - master

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Set up Git
        run: |
          git config --global user.email "${{ secrets.EMAIL }}"
          git config --global user.name "${{ secrets.USERNAME }}"

      - name: Clone the target wiki repository
        run: |
          git clone https://${{ secrets.API_KEY }}@github.com/pslits/oai-pmh-wiki.git wiki
          
      - name: Push to Wiki
        run: |
          cd wiki
          git remote remove origin || true
          git remote add origin https://${{ secrets.API_KEY }}@github.com/pslits/oai-pmh.wiki.git
          git push origin master
```

## Repository Structure

The repository structure is as follows:
* **`<repository>-wiki`**: The main repository where all wiki content is stored and versioned.
* **`<repository>.wiki`**: The actual GitHub Wiki repository, which is a mirror of the *`<repository>-wiki`* repository.

## How It Works

- The *`<repository>.wiki`* repository is a GitHub Wiki that is automatically updated from the ''oai-pmh-wiki'' repository.
- The *`<repository>-wiki`* repository is where all contributors work on the wiki content.
- Contributors can clone the *`<repository>-wiki`* repository, make changes, and submit pull requests.
- The pull requests are reviewed and merged into the ''master'' branch of the *`<repository>-wiki`* repository.
- Once a pull request is merged, a GitHub Action is triggered to push the changes to the *`<repository>.wiki`* repository.

## How to Make Changes

### Clone the *`<repository>-wiki`* repository to your local machine.
    
```bash
git clone <repository-url>.git
cd <repository>
```

### Create a new branch for your changes.

```bash
git checkout -b update-wiki-section
```

### Make your edits to the markdown files in the repository.
- For example, if you want to update the section on harvesting, edit the corresponding markdown file.
- You can also create new markdown files or folders as needed.

### Commit your changes and push them to the new branch.

```bash
git add .
git commit -m "Update wiki section on harvesting"
git push origin update-wiki-section
```

### Open a pull request from your branch into the ''master'' branch of the ''oai-pmh-wiki'' repository.'''
- Go to the **oai-pmh-wiki** repository on GitHub.
- Click on the **Pull requests** tab.
- Click on the **New pull request** button.
- Select your branch from the dropdown menu and click **Create pull request**.
- Add a title and description for your pull request, explaining the changes you made.

Once the pull request is reviewed and approved, it will be merged into the **master** branch.
- This triggers a GitHub Action that automatically pushes the changes to the actual GitHub Wiki (**oai-pmh.wiki**).

### Create Github access token

To enable the GitHub Action to push changes to the *`<repository>.wiki`* repository, you need to create a GitHub Personal Access Token (PAT) with the necessary permissions. This token will be used for authentication when pushing changes to the wiki repository.
- Go to your GitHub account settings.
- Click on **Developer settings** in the left sidebar.
- Click on **Personal access tokens**.
- Click on **Tokens (classic)**.
- Click on the **Generate new token** button.
- Give your token a descriptive name (e.g., "Wiki Push Token").
- Select the expiration date for the token (e.g., 30 days).
- Click on the **Generate token** button.
- Copy the generated token and store it in a secure place. You will need this token to set up the GitHub Action.

### Setting Up GitHub Secrets

To enable the GitHub Action to push changes to the *`<repository>.wiki`* repository, you need to set up some repository secrets in the *`<repository>-wiki`* repository. These secrets are used for authentication and commit attribution.
- Go to the **oai-pmh-wiki** repository on GitHub.
- Click on the **Settings** tab.
- In the left sidebar, click on **Secrets and variables**.
- Click on **Actions**.
- Click on the **New repository secret** button.
- Add the following secrets:

| Name     | Description                                                                 |
|----------|-----------------------------------------------------------------------------|
| API_KEY  | GitHub Personal Access Token with `repo` & `wiki` access                    |
| USERNAME | Your GitHub username (used for commit attribution)                          |
| EMAIL    | Your GitHub email (used for commit attribution)                             |

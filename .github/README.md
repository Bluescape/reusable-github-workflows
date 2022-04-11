# Reusable GitHub Workflows

A collection of workflows to be used across the Organization to limit maintenance. 

## create_jira_from_pr.yml

### Description
Comment `/jira` on a pull request to easily create a JIRA based on the context of the PR.

### Usage
```bash
# Create a task by specifying a project
/jira QAA
# It handles lower case as well
/jira qaa 
# It handles all projects that only require a Summary, Description, and Component.
/jira WebC
# It handles setting the component (only needed if required by the JIRA project)
/jira AD isam
```
The core functionality is handled by the GitHub Action: https://github.com/Bluescape/pr-jira-action

This will:
- Create a JIRA based on the project key you provide
- This JIRA will contain the PR title as a summary and the description as the description
- Assign it to the person who last committed this PR as long as the github email matches the JIRA email
- Mark it as "In Progress"
- Update the PR title to have the JIRA as a prefix

### Implementation
```yaml
name: Handle Comments

on:
  issue_comment:
    types: [created]

jobs:
  jira_comment:
    uses: bluescape/reusable-github-workflows/.github/workflows/create_jira_from_pr.yml@v1
    secrets:
      JIRA_HOST: ${{ secrets.JIRA_HOST }}
      JIRA_USERNAME: ${{ secrets.JIRA_USERNAME }}
      JIRA_PASSWORD: ${{ secrets.JIRA_PASSWORD }}
```


## deploy_comment_handler.yml

### Description
Comment `/deploy` on a pull request to execute actions for an on-demand deployment.

### Usage
```bash
# Cleanup an on-demand deployment related to the PR
/deploy destroy
```

### Implementation

```yaml
name: Handle Comments

on:
  issue_comment:
    types: [created]

jobs:
  deploy_comment:
    uses: bluescape/reusable-github-workflows/.github/workflows/deploy_comment_handler.yml@v1
    secrets:
      TOKEN: ${{ secrets.BDB_GITHUBPAT }}
```

## validate_pr.yml

### Description
Validates that a pull request title starts with a JIRA.

### Usage
When implemented in a repo, a check will run whenever a PR is created or the title/description is edited by a user.

### Implementation
```yaml
name: Validate PR

on:
  pull_request:
    types: [opened, synchronize, edited]

jobs:
  validate_pr:
    uses: bluescape/reusable-github-workflows/.github/workflows/validate_pr.yml@v1
```
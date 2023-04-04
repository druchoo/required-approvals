# Required Approvals GitHub Action
Checks if the required codeowners have approved a PR and requires a minimum number of approvals

### Inputs:
- `token`
  - The PR token, accessible at `secrets.GITHUB_TOKEN`
- `read_org_scoped_token`
  - A Personal Access Token (PAT) that has the `read:org` scope for the organization `org_name`
- `org_name`
  - The github organization to search for teams and team members
- `min_approvals`
  - The minimum number of approvals, regardless of codeowners team membership

### How to use this GitHub Action:
1. Ensure your repo has a codeowners file at `/.github/CODEOWNERS` or `/CODEOWNERS`  
    example `CODEOWNERS`:
    ```
    .github/** @YourOrg/some_team_name
    some_dir/** @YourOrg/some_other_team_name
    ```
2. Create a PAT that has the `read:org` scope enabled for your organization, and add it as a secret to your organization, repo, or environment.
3. Create a workflow that uses the action:  
    example workflow:
    ```yaml
    name: PR Approval Workflow

    on:
      pull_request:
        branches:
          - main
      pull_request_review:
        types: [submitted]

    jobs:
      check-approvals:
        runs-on: ubuntu-latest
        steps:
          - name: Check for required approvals
            id: check-approvals
            uses: skymoore/required-approvals@main
            with:
              token: ${{ secrets.GITHUB_TOKEN }}
              read_org_scoped_token: ${{ secrets.READ_ORG_SCOPED_TOKEN }}
              org_name: yourorg
              min_approvals: 1

          - name: Run action if all required approvals are met
            if: ${{ steps.check-approvals.outputs.approved == 'true' }}
            run: |
              echo "All required approvals are met. Running the action."
    ```
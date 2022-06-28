# Terraform Apply for kubernetes cluster in AWS using Vault secrets - works on changes in a PR

## About

This action will perform kubernetes cluster deployment in AWS account using Terraform using secrets in Hashicorp Vault. Supports cloning of the terraform modules in the remote repositories

## Limitation
This action works only on a Pull Request.

## Inputs

Below are the input variables for the action.

* 'tf-working-dir`

  Path to the terraform root module to apply

  - Type: string
  - Required


* `terraform-version`

  Terraform version to download

  - Type: string
  - Required

* `cluster-name`

  Kubernetes cluster name to set the kubernetes context

  - Type: string
  - Required

* `aws-role-to-assume`

  Role ARN in AWS with which the connection to AWS will be established

  - Type: string(Use Secret)
  - Required

* `aws-region`

  AWS Region

  - Type: string
  - Required

* vault-github-token

  GitHub Token used for accessing Vault

  - Type: string(Use Secret)
  - Required
    
* `github-token`

  Value of GITHUB_TOKEN

  - Type: string(Use Secret)
  - Required

*   github-user:
    
    GitHub user handle for whom the SSH key is passed for cloning the remote repo

  - Type: string
  - Optional

* 'private-ssh-key'
  
  SSH private key to add to the list of keys for downloading terraform modules from the remote GitHub repository
  
  - Type: string(Use Secret)
  - Optional

* `pr-dir`

  Specific directory in the PR contents if the PR contains changes in multiple directories 

  - Type: string
  - Required

## Outputs

* 'tf-fmt-outcome'
    Outcome of the 'terraform fmt' command

*  'tf-validate-outcome'
    Outcome of the 'terraform validate' command

*  'tf-validate-output'
    Output of the 'terraform validate' command

*  'tf-init-outcome'
    Outcome of the 'terraform init' command

*  'tf-plan-output'
    Output of the 'terraform plan' command

## Example usage

### WIP

This example workflow runs when PR is created or made ready for review to main.

```yaml
name: Apply

# Controls when the workflow will run
on:
  # Triggers the workflow on pull request events but only for targetted for the main branch and that too in specific folders
  pull_request_target:  
    types:
      - opened
      - edited
      - ready_for_review
    branches:    
      - main
    paths: 
      - 'relative-path-in-repository/**'
permissions:
      id-token: write
      contents: write
      pull-requests: write
      checks: write
      statuses: write
jobs:
  apply:
    runs-on: ubuntu-latest
    name: Apply approved plan
    env:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      
      - name: Terraform Apply for kubernetes cluster in AWS using Vault secrets - works on changes in a PR
        id: terraform-apply
        uses: paresh-deshmukh/terraform-apply-for-aws-using-secrets-in-vault@v1.3
        with:
          # Terraform working directory
          tf-working-dir: $DIR_PATH
          # Terraform version
          terraform-version: $TF_VERSION
          # Name of the kubernetes cluster
          cluster-name: $CLUSTER_NAME
          # Role ARN in AWS with which the connection to AWS will be established
          aws-role-to-assume: ${{ secrets.AWS_ROLE_TO_ASSUME }}
          # AWS region
          aws-region: $REGION
          # GitHub Token used for accessing Vault
          vault-github-token: ${{ secrets.VAULT_GITHUB_TOKEN }}
          # Pass secret GITHUB_TOKEN
          github-token: ${{ secrets.GITHUB_TOKEN }}
          # full path of a directory in a PR to filter for the terraform apply
          pr-dir: $DIR_PATH
          # SSH private key to add to the list of keys for downloading terraform modules from the remote GitHub repository
          ssh-private-key: ${{ secrets.SSH_PRIVATE_KEY }}
          # GitHub user handle for whom the SSH key is passed for cloning the remote repo
          github-user: "replace-this-with-github-user-handle"
```


## License
The project is available as open source under the terms of the [MIT License](LICENSE).

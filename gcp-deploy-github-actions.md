# Deploy to GCP with GitHub Actions

<br>

## Objectives

- Understand GitHub Actions
- Deploy an app to GCP via GitHub Actions

<br>

## Push to GitHub

1. Create a new repo and push
2. In your GitHub repo, select the "Actions" tab. Search for "google" and "Deploy to Cloud Run from Source"

    ![](images/gh-actions.png)

1. Click "Configure". GitHub will render a template.
1. Click "Commit Changes" and run `git pull origin main` to pull the files down locally. Open the `google-cloudrun-source.yml` file in VS Code.

    ![](images/pull-gh-workflows.png)

1. Get these from your [GCP Project Dashboard](https://console.cloud.google.com/) and [Cloud Run](https://console.cloud.google.com/run):

    ```js
    env:
      PROJECT_ID: ikea-practice-08222024 # TODO: update Google Cloud project id
      REGION: us-central1 # TODO: update Cloud Run service region
      SERVICE: ikea-users-app # TODO: update Cloud Run service name
      ```

    PROJECT_ID
    ![](images/project-id.png)

    SERVICE (name) and REGION
    ![](images/cloud-run-for-deploy.png)
  
4. In your GitHub actions file, add these under `steps` and `name: 'Checkout'`:

    ```js
    - name: Install Dependencies
      working-directory: ./react-app
      run: npm install
    
    - name: Build React App
      working-directory: ./react-app
      run: npm run build
    ```

    ![](images/gh-actions-yaml-edit1.png)

1. In the yaml file, delete the ` # Configure Workload Identity Federation and generate an access token.` section.

1. In it's place, add this option for authentication:

    ```yaml
    # NOTE: Alternative option - authentication via credentials json
    - name: Google Auth
      id: auth
      uses: 'google-github-actions/auth@v0'
      with:
      credentials_json: '${{ secrets.GCP_CREDENTIALS }}'
    ```

<br>

<details>
  <summary>Finished yml</summary>

```yaml
# This workflow will deploy source code on Cloud Run when a commit is pushed to
# the "main" branch.
#
# To configure this workflow:
#
# 1. Enable the following Google Cloud APIs:
#
#    - Artifact Registry (artifactregistry.googleapis.com)
#    - Cloud Build (cloudbuild.googleapis.com)
#    - Cloud Run (run.googleapis.com)
#    - IAM Credentials API (iamcredentials.googleapis.com)
#
#    You can learn more about enabling APIs at
#    https://support.google.com/googleapi/answer/6158841.
#
# 2. Create and configure a Workload Identity Provider for GitHub:
#    https://github.com/google-github-actions/auth#preferred-direct-workload-identity-federation.
#
#    Depending on how you authenticate, you will need to grant an IAM principal
#    permissions on Google Cloud:
#
#    - Artifact Registry Administrator (roles/artifactregistry.admin)
#    - Cloud Run Source Developer (roles/run.sourceDeveloper)
#
#    You can learn more about setting IAM permissions at
#    https://cloud.google.com/iam/docs/manage-access-other-resources.
#
# 3. Change the values in the "env" block to match your values.

name: 'Deploy to Cloud Run from Source'

on:
  push:
    branches: ['main']

env:
  PROJECT_ID: 'ikea-08202024' # TODO: update to your Google Cloud project ID
  REGION: 'europe-west1' # TODO: update to your region
  SERVICE: 'ikea-08202024' # TODO: update to your service name

jobs:
  deploy:
    runs-on: 'ubuntu-latest'

    permissions:
      contents: 'read'
      id-token: 'write'

    steps:
      - name: 'Checkout'
        uses: 'actions/checkout@692973e3d937129bcbf40652eb9f2f61becf3332' # actions/checkout@v4
      
      - name: 'Install Dependencies'
        working-directory: ./react-frontend
        run: npm install

      - name: 'Build React App'
        working-directory: ./react-frontend
        run: npm run build

      - name: 'Google Auth'
        id: auth
        uses: 'google-github-actions/auth@v0'
        with:
          credentials_json: '${{ secrets.GCP_CREDENTIALS }}'

      - name: 'Deploy to Cloud Run'
        uses: 'google-github-actions/deploy-cloudrun@33553064113a37d688aa6937bacbdc481580be17' # google-github-actions/deploy-cloudrun@v2
        with:
          service: '${{ env.SERVICE }}'
          region: '${{ env.REGION }}'
          # NOTE: If using a different source folder, update the image name below:
          source: './'

      # If required, use the Cloud Run URL output in later steps
      - name: 'Show output'
        run: |-
          echo ${{ steps.deploy.outputs.url }}
```
</details>

<br>

## Add Variables to GitHub Secrets

1. Go to the GCP Console. Then `IAm & Admin` -> `Service Accounts`. Select "Manage Keys".

    ![](images/gcp-manage-keys.png)

1. Select "ADD KEY" and "Create new key".

   ![](images/gcp-create-new-key.png)


1. Click "CREATE" and a JSON version of your key will be automatically downloaded.

    ![](images/gcp-download-key.png)


1. Open the file in VS Code. 

1. Add the entire downloaded JSON file to GitHub.

1. In your GitHub repo, go to `Settings` -> `Secrets and variables` -> `Actions`. Click "New repository secret". The name of the secret is `GCP_CREDENTIALS`. Copy and paste the entire JSON file. Then "Add secret".

    ![](images/gh-new-secret.png)

1. Repeat the previous step and add a secret named `GCP_PROJECT_ID`. It is the name of your `Project ID` from "Push to GitHub" step 5 above ^^. Then "Add secret".

    ![](images/gh-gcp-project-is.png)

<!-- 6. Change things to v2 -->

## Test that GitHub Actions work on a push to the `main` branch

1. Commit changes then git push
1. Make changes - add, commit and push and watch GH Actions.

    ![](images/gh-all-workflows.png)

    ![](images/gh-workflows.png)

1. Troubleshooting

    ```js
    name: 'Deploy to Cloud Run from Source'

    on:
      push:
        branches: ['main']
    ```
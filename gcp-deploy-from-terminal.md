
# DEPLOY TO GCP

<br>

## Objectives

- Deploy directly to GCP Cloud Run from the command line
- Understand the GCP CLI


<br>

## Login to GCP

1. `gcloud auth login`. This will open up a browser tab. Choose your account -> Sign in to Google Cloud SDK -> Hot Allow on "Google Cloud wnats to access your Google Account"
2. You should see a message "You are now authenticated with the gcloud CLI!"

## Build React App

1. `cd` into your `react-frontend` folder and run `npm run build`.

   ![](images/npm-run-build.png)

1. `gcloud config set project PROJECT_ID`

    ![](images/project-id.png)
  
   ![](images/set-project-id.png)

<br>

## Log into GCP CLI

1. In the Terminal, let's log into to the Google Cloud CLI: `gcloud auth login`. This will open your browser to confirm, then ask you to return back to the Terminal. 

1. Go to the [Google Cloud Console](https://console.cloud.google.com/)

2. Create a New Project

  ![](images/gcp-console.png)

3. Using Cloud Run which is serverless, only billed when someone sends a request

<br>

## Deploy to GCP from CLI

1. `cd` into the parent directory called `ikea-users-app`.

1. Go to the Terminal and run: `gcloud run deploy`. _Note - make sure you're in the parent directory._  Select "yes" to any prompts.

    ![](images/gcloud-run-deploy.png)


1. Choose the region closest to you and choose "yes" at any prompts.

    ![](images/gcp-select-region.png)

1. Deployment can take a few minutes. When deployed, the deployed URL be displayed.

    ![](images/gcp-deploy-complete.png)

1. Try out your Service URL in the Browser.

_Note: [follow these steps to grant public permissions if you don't have access.](https://cloud.google.com/run/docs/authenticating/public)_

<br>

## Additional Resources

- [Google Cloud Quick Start]( https://cloud.google.com/sdk/docs/quickstarts)
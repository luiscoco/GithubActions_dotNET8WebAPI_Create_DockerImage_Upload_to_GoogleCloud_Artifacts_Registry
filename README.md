# GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry

**Step 1**: Create a Service Account in Google Cloud Platform

Go to the GCP Console: Open the Google Cloud Console and log in to your account.

Select Your Project: Make sure you have the correct project selected in which you want to create the service account.

Navigate to IAM & Admin: In the left-hand menu, click on "IAM & Admin", then select "Service Accounts".

Create Service Account: Click on "Create Service Account" and fill in the necessary details:

Name: Give your service account a name.

ID: This is filled automatically based on the name but can be customized.

Description: (Optional) Add a description for your service account.

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/14e70f22-b55c-40f9-be13-88bae9b3f96f)

Grant Access: Assign the service account appropriate roles. For Docker images, roles like "Storage Admin" or "Artifact Registry Administrator" might be relevant. 

Be cautious with permissions to follow the principle of least privilege.

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/bace8465-8ce9-4f1e-9187-fa4beee45943)

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/26d5cf51-7ff7-4b6e-a023-a719a8b12cd2)

Create Key: After creating the service account, click on it to open its details. Under the "Keys" tab, click "Add Key", then select "Create new key". 

Choose "JSON" as the key type and click "Create". This will download the JSON key file to your computer.


**Step 2**: Add the Key as a Secret in Your GitHub Repository

Go to Your GitHub Repository: Open your GitHub repository in a web browser.

Navigate to Settings: Click on "Settings" in the top menu of your repository.

Access Secrets: In the left-hand sidebar, click on "Secrets", then select "Actions".

Add a New Secret: Click on "New repository secret".

Name Your Secret: Enter GOOGLE_CLOUD_CREDENTIALS as the name.

Paste the Key Content: Open the JSON key file you downloaded from GCP in a text editor, copy all its contents, and paste them into the secret's value field in GitHub.

Save the Secret: Click "Add secret" to save your new secret.

Now, your GitHub Actions workflow can use this **secret** to authenticate with Google Cloud services. In your workflow file, you can reference this secret as ${{ secrets.GOOGLE_CLOUD_CREDENTIALS }}.


Creating a **main.yml** file for a GitHub Actions workflow to build a .NET 8 WebAPI Docker image and push it to Google Cloud Artifact Registry involves several steps. 

Below is a sample main.yml file tailored for your requirements. This workflow assumes you have already set up Google Cloud credentials as secrets in your GitHub repository

```yaml
name: Build and Push Docker Image

on:
  push:
    branches:
      - main  # or the branch of your choice

env:
  PROJECT_ID: your-google-cloud-project-id
  IMAGE_NAME: your-image-name
  REPOSITORY: your-region-docker.pkg.dev/your-project-id/your-repo

jobs:
  build-and-push:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v1

    - name: Login to Google Cloud Artifact Registry
      uses: google-github-actions/auth@v0
      with:
        credentials_json: ${{ secrets.GOOGLE_CLOUD_CREDENTIALS }}

    - name: Configure Docker for Google Cloud Artifact Registry
      run: |
        gcloud auth configure-docker ${{ env.REPOSITORY }},gcr.io

    - name: Build and push Docker image
      uses: docker/build-push-action@v2
      with:
        context: .
        file: ./Dockerfile
        push: true
        tags: ${{ env.REPOSITORY }}/${{ env.IMAGE_NAME }}:latest

    - name: Verify the image was pushed
      run: |
        gcloud artifacts docker images list ${{ env.REPOSITORY }}/${{ env.IMAGE_NAME }}
```




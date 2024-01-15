# GithubActions: How to create .NET8 WebAPI Docker image and upload to Google Cloud Artifacts Registry

You can see this example source code in this github repo: 

https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry

## 0. Prerequisite

We have to create a new repo in Google Cloud Artifacts Registry

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/2d555fbe-1653-41c8-a254-b1f2d520c733)

We set the input data

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/8842fdf1-c579-4b98-933c-34820402a492)

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/6d7b12b6-1548-4207-bfd9-1b7bacd45fa4)

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/fcf8e8fa-15b3-441c-b111-f9a47e465524)

## 1. Create a Service Account in Google Cloud Platform

Go to the GCP Console: Open the Google Cloud Console and log in to your account.

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/cb9c3717-0cf6-4a47-b5fe-3ae8b8998188)

**Select Your Project**: Make sure you have the correct project selected in which you want to create the service account.

**Navigate to IAM & Admin**: In the left-hand menu, click on "IAM & Admin", then select "Service Accounts".

**Create Service Account**: Click on "Create Service Account" and fill in the necessary details:

**Name**: Give your service account a name.

**ID**: This is filled automatically based on the name but can be customized.

**Description**: (Optional) Add a description for your service account.

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/ac6d6f71-26db-4f53-97f4-ef4954c06fe7)

**Grant Access**: Assign the service account appropriate roles. For Docker images push we can assing the role: "**Artifact Registry Writer**"

Other similar roles could be: "**Storage Admin**" or "**Artifact Registry Administrator**"  

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/ebf54060-cb30-43f1-919b-5e779f8e1090)

Do not forget to set the project ID (for this example: extreme-axon-381209) in the **Service account admin role**: extreme-axon-381209@appspot.gserviceaccount.com	App Engine default service account

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/91e74724-9bc8-4561-ad52-2ac19aa3cffa)

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/eb3931cf-6b5c-4f6e-a6bf-c5c91f6ed30b)

Be cautious with permissions to follow the principle of least privilege.

**Create Key**: After creating the service account, click on it to open its details. Under the "Keys" tab, click "Add Key", then select "Create new key". 

Choose "JSON" as the key type and click "Create". This will download the JSON key file to your computer.

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/74960ef0-d30d-4ebf-8c46-2c75b3062432)

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/8168d90e-0d5d-4713-ab40-42012754ea4d)

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/1b6134ee-1324-4248-ac62-da276d3d83a6)

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/07a751b2-1dcb-4f23-bd11-63e7a56bb3be)

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/fcb327f6-255c-4411-a87b-487f01dd2dd4)

## 2. Add the Key as a Secret in your GitHub Repository

**Go to Your GitHub Repository**: Open your GitHub repository in a web browser.

Navigate to Settings: Click on "**Settings**" in the top menu of your repository.

**Access Secrets**: In the left-hand sidebar, click on "**Secrets**", then select "**Actions**".

Add a New Secret: Click on "**New repository secret**".

**Name Your Secret**: Enter **GOOGLE_CLOUD_CREDENTIALS** as the name.

Paste the Key Content: Open the **JSON** key file you downloaded from GCP in a text editor, copy all its contents, and paste them into the secret's value field in GitHub.

**Save the Secret**: Click "Add secret" to save your new secret.

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/6444ba70-a0c4-45e4-9f52-8bce62ee105a)

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/2fe07140-7442-4ebf-9dc6-3e5250b490c5)

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/e466a636-3ec1-4ef9-b5e7-50bd30e67f83)

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/1e24e921-2952-46ab-8d67-0d05a02fd221)

Now, your GitHub Actions workflow can use this **secret** to authenticate with Google Cloud services. In your workflow file, you can reference this secret as ${{ secrets.GOOGLE_CLOUD_CREDENTIALS }}.

## 3. Create the main.yml file for Github actions workflow

Below is the **main.yml** file tailored for your requirements. 

This workflow assumes you have already set up Google Cloud credentials as secrets in your GitHub repository

```yaml
name: Build and Push Docker Image

on:
  push:
    branches:
      - main

env:
  PROJECT_ID: extreme-axon-381209
  IMAGE_NAME: my-dotnetwebapi
  REPOSITORY: europe-southwest1-docker.pkg.dev/extreme-axon-381209/myfirstrepo
  TAG: latest

jobs:
  build-and-push:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v4

    - name: Authenticate to Google Cloud
      uses: google-github-actions/auth@v2
      with:
        credentials_json: ${{ secrets.GOOGLE_CLOUD_CREDENTIALS }}

    - name: Configure Docker for Google Cloud Artifact Registry
      run: |
        echo '${{ secrets.GOOGLE_CLOUD_CREDENTIALS }}' | gcloud auth activate-service-account --key-file=-
        gcloud auth configure-docker europe-southwest1-docker.pkg.dev --quiet
    - name: Build Docker image
      run: |
        docker build -t ${{ env.REPOSITORY }}/${{ env.IMAGE_NAME }}:${{ env.TAG }} .
    - name: Push Docker image
      run: |
        docker push ${{ env.REPOSITORY }}/${{ env.IMAGE_NAME }}:${{ env.TAG }}
    - name: Verify the image was pushed
      run: |
        gcloud artifacts docker images list ${{ env.REPOSITORY }}/${{ env.IMAGE_NAME }}
```

## 4. Verify the Docker image uploaded to Google Cloud
  
Navigate to Google Cloud Artifacts Registry repo

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/9181218c-a84d-4a1f-b6fb-06e3150c7006)

We can see inside the repo the uploaded Docker image

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/84a35720-a2d9-45a3-9926-916961d07514)

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/9b06084c-088e-4c2a-8ef5-f972cd4ea2b0)

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/8c0979b7-4fbd-495c-9d35-facbf330bfd3)

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/d3a98822-3e04-4c05-bdaf-056a0e346f0f)

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/b1c728b3-6f08-4e01-9424-b7e8415419cd)

We run this command to pull the image and to 

```
docker pull europe-southwest1-docker.pkg.dev/extreme-axon-381209/myfirstrepo/my-dotnetwebapi:latest
```

We verified the downloaded image in Docker Desktop

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/2cc47e49-a5a2-44d1-836e-c4c4e64871ba)

Also we can see the image with the command

```
docker images
```

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/7d742d84-cffc-4cab-9cfb-fbcbff66882b)

We run the image in our Docker Desktop

```
docker run -p 8080:8080 europe-southwest1-docker.pkg.dev/extreme-axon-381209/myfirstrepo/my-dotnetwebapi:latest
```

We see the running image with the command

```
docker ps
```

And also we can see the image in Docker Desktop

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/3e7553e2-355d-403e-b4c4-95ff5f6b18bc)

We can verify the running Docker container 

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/15ebd833-959c-4c97-af6e-c168db0755d9)

## 5. Modify the main.yml for creating a new repo if it doesn't exist

As prerequisite for creating a new Artifact Registry repo it is mandatory to assign to the Service Account the permission **Artifact Registry Administrator**

We also set the permission **** to push Docker image to the Artificat Registry rerpo

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/19011a91-59df-4fa0-b0c6-ee093e1c690d)

![image](https://github.com/luiscoco/GithubActions_dotNET8WebAPI_Create_DockerImage_Upload_to_GoogleCloud_Artifacts_Registry/assets/32194879/83aacfc9-7078-458c-ac82-f4cff7cdaddb)



Below is the complete main.yml file based on your initial workflow, with the added functionality to check for the existence of a Google Cloud Artifact Registry repository and create it if it does not exist. 

This workflow includes steps for authentication, checking and creating the repository, building and pushing the Docker image, and verifying the push.

```yml
name: Build and Push Docker Image

on:
  push:
    branches:
      - main

env:
  PROJECT_ID: extreme-axon-381209
  IMAGE_NAME: my-dotnetwebapi
  REPOSITORY: myfirstrepo
  TAG: latest

jobs:
  build-and-push:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v4

    - name: Authenticate to Google Cloud
      uses: google-github-actions/auth@v2
      with:
        credentials_json: ${{ secrets.GOOGLE_CLOUD_CREDENTIALS }}

    - name: Configure Docker for Google Cloud Artifact Registry
      run: |
        echo '${{ secrets.GOOGLE_CLOUD_CREDENTIALS }}' | gcloud auth activate-service-account --key-file=-
        gcloud auth configure-docker europe-southwest1-docker.pkg.dev --quiet

    - name: Check if Google Cloud Artifact Registry repository exists
      id: check-repo
      run: |
        if gcloud artifacts repositories describe ${{ env.REPOSITORY }} --location=europe-southwest1 --project=${{ env.PROJECT_ID }}; then
          echo "Repository exists"
        else
          echo "::set-output name=repo_exists::false"
        fi

    - name: Create Google Cloud Artifact Registry repository if it does not exist
      if: steps.check-repo.outputs.repo_exists == 'false'
      run: |
        gcloud artifacts repositories create ${{ env.REPOSITORY }} --repository-format=docker --location=europe-southwest1 --project=${{ env.PROJECT_ID }}
        echo "Repository created"

    - name: Build Docker image
      run: |
        docker build -t europe-southwest1-docker.pkg.dev/${{ env.PROJECT_ID }}/${{ env.REPOSITORY }}/${{ env.IMAGE_NAME }}:${{ env.TAG }} .

    - name: Push Docker image
      run: |
        docker push europe-southwest1-docker.pkg.dev/${{ env.PROJECT_ID }}/${{ env.REPOSITORY }}/${{ env.IMAGE_NAME }}:${{ env.TAG }}

    - name: Verify the image was pushed
      run: |
        gcloud artifacts docker images list europe-southwest1-docker.pkg.dev/${{ env.PROJECT_ID }}/${{ env.REPOSITORY }}/${{ env.IMAGE_NAME }}
```

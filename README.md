In this blog post, I’ll guide you through the process of building and deploying a chatbot capable of querying a Confluence space and providing meaningful responses to user queries. We’ll leverage technologies such as Langchain for natural language processing, OpenAI as the language model, and Streamlit for the user interface. The chatbot will be containerized using Docker and deployed to Google Kubernetes Engine (GKE), with a fully automated deployment pipeline built using GitHub Actions.

-------------------------------------------

# 1. Create Google Cloud Account

If you haven't already, you need to create a Google Cloud account:

1. Go to the [Google Cloud Platform website](https://cloud.google.com/).
2. Click on the "Get started for free" button or "Go to Console" if you already have an account.
3. Follow the instructions to create your account.
4. Provide the necessary information, including billing details.

## 2. Setting Up Google Cloud CLI

Once you have a Google Cloud account, you need to set up the Google Cloud CLI:

1. Install the Google Cloud SDK by following the instructions provided [here](https://cloud.google.com/sdk/docs/install).
2. After installation, run the following command in your terminal to authenticate the gcloud CLI tool:

    ```bash
    gcloud auth login
    ```

3. Follow the prompts to authenticate using your Google Cloud account.

4. Set the default project for the gcloud CLI by running:

    ```bash
    gcloud config set project YOUR_PROJECT_ID
    ```

   Replace `YOUR_PROJECT_ID` with the ID of your Google Cloud project.

## 3. Create Kubernetes Cluster with GKE

Now that you have Google Cloud CLI set up, you can create a Kubernetes cluster using Google Kubernetes Engine (GKE):

1. Open your terminal and run the following command to create a new Kubernetes cluster:

    ```bash
    gcloud container clusters create YOUR_CLUSTER_NAME --num-nodes=3 --zone=YOUR_ZONE
    ```

    Replace `YOUR_CLUSTER_NAME` with the desired name for your cluster and `YOUR_ZONE` with the desired zone for your cluster.

    For example:

    ```bash
    gcloud container clusters create chatwithconfluence --num-nodes=1 --zone=us-central1-a
    ```

2. Wait for the cluster creation process to complete. It may take a few minutes.

3. Once the cluster is created, configure `kubectl`, the Kubernetes command-line tool, to use the new cluster:

    ```bash
    gcloud container clusters get-credentials YOUR_CLUSTER_NAME --zone=YOUR_ZONE
    ```

    Replace `YOUR_CLUSTER_NAME` and `YOUR_ZONE` with the appropriate values.

    For example:

    ```bash
    gcloud container clusters get-credentials chatwithconfluence --zone=us-central1-a
    ```

4. Verify that `kubectl` is configured correctly by running:

    ```bash
    kubectl get nodes
    ```

    You should see the nodes of your Kubernetes cluster listed.

Congratulations! You have successfully created a Google Cloud account, set up Google Cloud CLI, and created a Kubernetes cluster using Google Kubernetes Engine (GKE). You can now deploy and manage your applications on Kubernetes.

# Delete Steps


    gcloud container clusters delete chatwithconfluence --zone=us-central1-a


- GO to google console and create GAR repository with name chatwithconfluence. 


# Creating a Google Container Repository
Google Container Registry (GCR) is where your Docker images will be stored before deployment to Google Kubernetes Engine (GKE).

## Steps to Create a Google Container Repository:
1. Login to Google Cloud Console and navigate to the Container Registry.
2. Enable Container Registry API: Go to APIs & Services > Library.
3. Search for “Container Registry API” and enable it.
4. Create a project if you don’t have one, and make sure it’s selected.
5. In the Google Cloud Console, navigate to Container Registry.

--------------------------------------------------------------------------------

# Setting Up OpenAI API Key

## To use OpenAI’s language model, you'll need an API key.

Steps to Create an OpenAI API Key:
1. Sign up or log in to the OpenAI platform.
2. Go to your account settings by clicking on your profile in the top-right corner.
3. Navigate to the API Keys section.
4. Click on Create new secret key and name your key.
5. Once generated, copy the key and keep it secure. You won’t be able to view it again after you leave the page.

-------------------------------------------------------------------------------------

# Setting Up Confluence Private API Key

To pull data from Confluence, you need a private API key.

Steps to Create a Confluence Private API Key:

1. Login to your Confluence account.
2. Go to your account settings by clicking on your profile in the top-right corner.
3. Navigate to Security and click on API Token.
4. Click on Create API Token, name your token (e.g., “Chatbot Integration”), and click Create.
5. Copy the token and store it securely. This token will be required to authenticate API requests to Confluence.

---------------------------------------------------------------------------------------------

# Creating a Google Cloud Service Account
A Google Cloud Service Account is needed to grant the chatbot and CI/CD pipeline permission to interact with Google Cloud services, such as Google Container Registry (GCR) and Google Kubernetes Engine (GKE). The service account allows GitHub Actions to deploy your Docker container securely.

## Steps to Create a Google Cloud Service Account:
1. Login to Google Cloud Console and select the project you want to use.
2. Navigate to IAM & Admin > Service Accounts.
3. Click on + Create Service Account.
    - Service account name: Give your service account a meaningful name (e.g., gke-deployment-sa).
    - Service account description: Optionally, add a description for the account.
4. Click Create and proceed to the Permissions section.
5. Add the following roles to the service account:
    - Kubernetes Engine Developer: Grants permissions to interact with GKE clusters.
    - Storage Admin: Allows access to Google Cloud Storage, which is necessary for pushing and pulling Docker images from Google Container Registry.
6. Once the roles are assigned, click Done.

## Generating a Key for the Service Account:
1. From the Service Accounts page, click on the newly created service account.
2. Go to the Keys tab, and click on Add Key > Create New Key.
3. Select JSON and click Create.
4. A .json file containing the service account key will be downloaded to your machine. This file is necessary for authenticating the CI/CD pipeline.


# Adding Secrets in GitHub Actions
To securely use the OpenAI API key, Confluence token, and other sensitive data within your GitHub Actions CI/CD pipeline, you need to add them as secrets in your GitHub repository.

Steps to Add Secrets in GitHub Actions:

1. Go to your GitHub repository.
2. Click on the Settings tab.
3. On the left sidebar, select Secrets and Variables > Actions.
4. Click on New repository secret.

Add the following secrets one by one:

- OPENAI_API_KEY: The API key generated from OpenAI.
- CONFLUENCE_PRIVATE_API_KEY: The token generated from Confluence.
- GKE_SA_KEY: The service account key file for authenticating Google Cloud services (this is generated from Google Cloud IAM).
- CONFLUENCE_SPACE_KEY: confluence space key (myconfluencespace)
- CONFLUENCE_SPACE_NAME: confluence space url ("https://<username>.atlassian.net/wiki")
- EMAIL_ADRESS: email address used for confluence login
- GKE_CLUSTER: Google cluster name
- GKE_PROJECT: google cluster project

For each secret, click Add secret after entering the name and the corresponding value.

# Building the deploying

I have used github workflows to build the app as docker image , push it to google artifactory and then deploy to GKE (google kubernetes engine). So steps to follow the same 

1. Clone this repository locally
2. Add the secrets for github actions , as stated in steps above. 
3. Create a new repository in the github, then push the code into newly created respository.
4. Goto to Actions tab on the github portal and see if pipeline is executed∂. 
5. On successful deployment you should be able to see browser the application through load balancer IP provided on the kubernetes service. 


# Browse the application 

The streamlite application can be browsed with the service loadbalancer external IP on the port 8501

```bash
    kubectl get service 
```
### Module 4- AWS DevOps CI/CD Deploying a Static Web App on AWS

S3 → Buckets → Create Bucket → Bucket type (General Purpose)
→ Bucket name (you set it) → Object Ownership (default) 
→ Block Public Access settings for this bucket ( uncheck “Block all public access” and acknowledge)
→ Bucket Versioning ( Disable)
→ Default encryption (leave default)
→ Create Bucket

Now we have to restrict access
→ Click on your Bucket name → Permissions → Bucket policy (Edit and paste this in the Policy Editor and replace “Ressource” field with the corresponding bucket arn + “/*”)
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicRead",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::mlops-july/*"
        }
        ]
}

check if there are no errors and issues, then → Save changes
We just restricted the bucket to public.


Now we should enable Static web hosting
→ Click on your Bucket name → Properties → Static website hosting (Edit and Enable) → Hosting Type (Host a static website) → Index document (index.html) → If there are errors, then Error document - optional (error.html) otherwise index.html → Save changes

Copy the Bucket Website endpoint 
→ Click on your Bucket name → Properties → Static website hosting → Bucket website endpoint
http://aws-devops-april-2025.s3-website-us-east-1.amazonaws.com

Congratulations on Setting the S3 bucket.


Now go to your Github repo to grab the repo link and clone it to your local pc

AWS Code Pipeline →  Create Pipeline → Category (Build custom Pipeline) → Next → 
Choose pipeline settings 
Step 2 of 7
Pipeline name → Provide a name for the Pipeline
Execution mode (Queued) → Service role (New Service role) → Advanced Setting (Keep defaults) → Next

Add source stage 
Step 3 of 7
Source provider ( GitHub (via OAuth App) and connect to your GitHub account) → Repository (Search for the repo name) → Branch ( Choose the branch who has the code) → Next

Add build stage
Step 4 of 7 
Build provider (Other Build providers) → AWS CodeBuild → Project name (Create Project link) →
	Create build project
Project configuration
Project name → Provide a name
Keep everything to default
Buildspec (Use a buildspec file) → Buildspec name (buildspec.yml (optional)) → Continue to CodePipeline → Leave

Build is complete, then Next

Add test stage Info
Step 5 of 7

Test - optional ( Skip Test stage)

Add deploy stage Info
Step 6 of 7

Deploy - optional
Deploy provider → Amazon S3 → Bucket (the s3 bucket name you created) → check the checkbox “Extract file before deploy” → Deployment path - optional (type “prod”) → Next

Review everything → Create Pipeline

Now you edit your files in your repo and you push them, the Pipeline will run automatically.

The next step is to add stages to the Basic Pipeline we just created.

To edit a pipeline click on the Pipeline → Edit 

To add a Manual Approval stage before Deploy → Just before “Edit: Deploy” → Add stage → Stage name (Manual-Approval-Prod) → Add action Group → Action Name (Manual-Approval-Prod) → Action Provider (Manual approval) → Keep everything else as is → Done

Add stage (staging-deploy) → Action Name (staging-deploy) → Action Provider (Amazon S3) → Input artifacts ( Build Artifact) → Bucket (same S3 bucket as before) → Deployment path (staging) → check the checkbox “Extract file before deploy” → Done

Add stage → Stage name (staging-approval) → Add action Group → Action Name (staging-approval) → Action Provider (Manual approval) → Keep everything else as is → Done

If everything is ok then Save the Pipeline, and then make a change to your repo and commit something to see the pipeline running.


### Module 4- Azure DevOps CI/CD Deploying a Static Web App on Azure

dev.azure.com/temajozias/

Configure the Hosting Environment, Azure Static Web Apps
Search Bar → Static Web Apps → Select the Static Web Apps result.
Static Web Apps → +Create( or Create a Static Web app on the center if empty) → Ressource Group → Create new → Name (my-gen-ai-app) → Static Web App details → Name (my-gen-ai-app) → Hosting Plan → Plan type → Free: For hobby or personal projects → Deployment details → Other → Review + Create → Create
 
Since Azure Portal is not directly connected to Azure DevOps, we need to make a connection through a Token (Deployment Token) to be able to deploy our app on the Static Web Apps platform on → Save →

Back to Home Page Microsoft Azure → Select Static Web App (my-gen-ai-app)
Overview → Manage Deployment Token → Copy and save it on a bloc note → Close

Now head to Azure DevOps → New Project → Project name (my-gen-ai-app) → Description (Optional) → Visibility (Private) → Create

We need to create a GitHub connection to our Project in Azure DevOps
Enter the project console (my-gen-ai-app) → Project settings → Under Boards → GitHub Connections → Connect your GitHub account → Add GitHub repositories → Choose the corresponding repo you want to add → Go back to the project Home Summary (my-gen-ai-app) → Pipelines → Create Pipeline → Where is your code?(GitHub) → Select a repository (Azure-CI-CD-static-app) → Configure your pipeline (Existing Azure Pipelines YAML file) → Select an existing YAML file → Branch( Master) → Path/yml-pipelines/azure-static-app-build-deploySatage.yml) →
Continue → Review your pipeline YAML → Variables → New Variable → Name (deployment_token) → Value (the value of the deployment token copied in Azure Portal) → Check “Keep this value secret” → Ok → Save → Run

The Pipeline will run. Now, you edit your project locally and push the modifications on GitHub and the pipeline will be automatically triggered.

Renaming a Pipeline
Pipelines → Menu of the Pipeline you added → Rename/Move (give a new name) → “Build and Deploy” → Save

How to use Boards with Pipelines
Project Summary → Boards Panel → Boards → To Do → New Item (Update title of the App) → Assign the task to yourself or a team member → Move the task to “Doing” stage → Click on the task title to open task details, description and discussion panel with tema members → copy the number assigned to the task #[Board Number] like #2 → Edit your local repo and push the changes with the commit in a different form like “fixed AB#[Board Number]” like “fixed AB#2” and push to the remote → The task will move from Doing to Done.

To create another Pipeline we need to disable the current one, this is to prevent both pipelines to run when the dev push changes to the remote.
Go Back to Pipelines → enter the current Pipeline → 3 dots at top corner right → Settings → Toogle “Disabled” → Save

Now, we can add a new pipeline(azure-static-app.yml), and you already know how to do it.

To rollback to a specific pipeline execution, enter the pipeline and the stage and then click “Rerun Stage”

Now you delete everything, the project on Azure DevOps and the ressource group on Azure Portal to avoid costs.

Linting code is reviewing code


### Module 4- GitHub Actions DevOps CI/CD Deploying a Static Web App
Your repo is already hosted on GitHub
Your GitHub Profile → Settings → Developers Settings → Personnal Acces T	okens → Tokens(classic) → Generate a new token → Generate a new token(classic) → Note(Input meaningful name for the token) → Expiration(choose expiration time) → Select scopes(repo, workflow, delete_repo) → Generate token → copy and save the token.

Your Repo → Settings → Security → Secrets and Variables → Actions → New repository secret → Name(The configured Name in the Pipeline) → Secret( the GitHub token you just created) → Add Secret.

Now you edit your code and push the changes for the pipeline to run.

Your Repo → Actions → enter the workflow to gab the url of the deployed page and open in a new link.

To rollback changes, your rollback in your repo with “git revert” and push the changes

### Module 4- GCP DevOps CI/CD Deploying a Static Web App
To rollback changes, your rollback in your repo with “git revert” and push the changes
Create a project → Project name(chatappgcp-cicd) → Create → Select Project

Now we must enable API and services we will use
Search Bar → “Enable API and Services” →  → Search Bar → Cloud Build API → Enable → 
Back Arrow →  “+Enable API and Services” → Search Bar → Cloud Run Admin API → Enable → 
Back Arrow →  “+Enable API and Services” → Search Bar → Google Container Registry API --> Enable if disabled
Back Arrow →  “+Enable API and Services” → Search Bar → Artifact Registry API --> Enable if disabled

Home --> Search Bar --> Cloud Run --> Create Service --> A service exposes a unique endpoint and automatically scales the underlying infrastructure to handle incoming requests. Service name and region cannot be changed later. (Continuously deploy from a repository (source or function)) --> Set up with Cloud Build --> Authenticate (your GitHub) --> Continue --> Authorize Cloud Build --> Install Goolgle Cloud Build --> All repositories(/Only select repositories) --> Install --> Confirm --> Repository(gcp-cicd) --> Check the checkbox(I understand...) --> Next --> Build Configuration --> Branch(master) --> Build file(Go, Node.js, Python, Java, .NET, ...) --> Save --> Authentication --> check the checkbox --> check(Allow unauthenticated invocations) to allow people from the internet to access the web app --> CPU allocation and pricing(CPU is only allocated during request processing) --> Service scaling(0) --> Ingress control(All) --> review the rest and -->  Create 

Enter the created service --> Copy the endpoint URL --> open it in a new window to acces your static web app

Make changes and push them to se the pipeline running, to see the history of builds
Search Bar --> Cloud Build

You can create new triggres if you want or edit present ones.
Cloud Build --> Triggers  --> Chose the default created trigger --> Approval(check the checkbox to create a manual approval process) --> Save

Cloud Build --> Dashboard --> 

To see your Artifact
Search Bar --> Artifact registry

To rollback changes, Select a build from build history --> Rebuild

Now you delete ressources
Search Bar --> Manage ressources --> heck the project you created (chatappgcp-cicd) --> Delete --> opy the project id and paste --> Shut down anyway --> Ok

### Module 5- Docker & Kubernetes Overview (Docker )
#### Docker Overview
Open  a CMD
grab the docker version
docker --version

for detailled response about the version
docker version

list all docker images
docker images

build a image, you must be in the parent folder where there is a file named "Dockerfile"
docker build -t [app-name]:[tag-name] [docker-file-location]
docker build -t node-app:v1.0  .  # Because the Docker file is in the parent directory

to run your dockerised app
docker run -it -p 5000:8080 [app-name]:[tag-name]
docker run -it -p 5000:8080 node-app:v1.0 

run in detach mode, remember to copy the container ID, so you can easily terminate the container
docker run -it -d -p 5000:8080 [app-name]:[tag-name]
docker run -it -d -p 5000:8080 node-app:v1.0 

stop the container in detach mode
docker stop [container-id]
docker stop 34ead35ede7fc5e9d2008de2b84830215b81d864a35f0344b4cd5cde7939edac

when you have a container ID, you can directly run it 
docker start [container-id]
docker start 34ead35ede7fc5e9d2008de2b84830215b81d864a35f0344b4cd5cde7939edac

If the container is crashing, try restart it
docker restart [container-id]
docker restart 34ead35ede7fc5e9d2008de2b84830215b81d864a35f0344b4cd5cde7939edac

to list all the containers that are running
docker ps

to list all the containers
docker ps -a

remove a container, make sur it is stopped beforehand
docker rm [container-id]
docker rm 34ead35ede7fc5e9d2008de2b84830215b81d864a35f0344b4cd5cde7939edac



to get logs from a particular container 
docker logs [container-id]
docker logs 61e119287c1b911599ca6050b7902843d68d9d07d611b2e127b97b6ed7f9f708

to get the real time logs
docker logs -f [container-id]
docker logs -f 61e119287c1b911599ca6050b7902843d68d9d07d611b2e127b97b6ed7f9f708

to create multiple differents versions of your docker images, you must create multiple different tags for each and every version of the docker image, considering you have made some changes in your app and you want to create a new version of it. Or you can create multiple images with different tags for different teams to work on, in this case the image id will remain same, assuming you have not made any changes
docker image tag [old-image-name]:[tag-name] [new/old-image-name]:[tag-name]
docker image tag node-app:v1.0 node-app:v1.1

then you can show all images
docker images

docker registries are tools that we use to version your docker images DockerHub, Nginx, ...

whenever you are making changes to your app, you must build it again, this time with a different tag to track the version
docker build -t [app-name]:[new-tag-name] [docker-file-location]
docker build -t node-app:v2.0  .

you can use ci/cd tools on aws, azure, gcp, github actions to automatically build your docker images

to pull your image to dockerHub you must add your username before the image, to do so, you create a copy using tags
docker image tag [old-image-name]:[tag-name] [new/old-image-name]:[tag-name]
docker image tag node-app:v1.0 temajozias/node-app:v1.0

to push the image to the DockerHub, you first need to authenticate
docker login

to push an image
docker push [username]/[image-name]:[tag-name]
docker push temajozias/node-app:v1.0

Your image yould be showing in your DockerHub account under the Repositories section

choose a name of a docker container instead of a random name
docker run -it -d --name [your-container-name] -p 5000:8080 [app-name]:[tag-name]
docker run -it -d --name webapp -p 5000:8080 temajozias/node-app:v1.0 

every container has a linux environment inside, to run a linux terminal inside a container, you should make sure first that the container is up and running
docker exec -it [container-id] /bin/bash
docker exec -it cf94a1f045fb /bin/bash

to exit the container terminal
exit

#### Docker Network Types

List all the networks
docker network ls

Create your own network
docker network create [network-name]
docker network create node-network

to verify, you can list all networks
docker network ls

To check if any container is attached/connected to the created network
docker inspect [network-name]
docker inspect node-network

Now we need to connect/attach the network to the container
docker network connect [network-name] [container-id]
docker network connect node-network cf94a1f045fbb1fec94ff3cd79d812a6273404d5c7794a3ab22787efe8fc3908

Now you can inspect the network again to see the newly attached container
docker inspect [network-name]
docker inspect node-network

To remove a network
docker network rm [network-name]
docker network rm node-network

To disconnect a container from a network
docker network disconnect [network-name] [container-id]
docker network disconnect node-network cf94a1f045fbb1fec94ff3cd79d812a6273404d5c7794a3ab22787efe8fc3908

Now you can inspect the network again to acknowledge
docker inspect [network-name]
docker inspect node-network

#### Docker Volumes
To list volumes
docker volume ls

#### Docker Compose
Docker compose is a tool used for defining and running multi-container Docker applications

Hands-on on Placement-rank app

first build the app
docker build -t placement-rank-app:v1.0  .

now you run the app
docker run -it -d -p 9696:9696 placement-rank-app:v1.0

new tag to push to dockerhub
docker image tag placement-rank-app:v1.0 temajozias/placement-rank-app:v1.0

now you push to dockerhub
docker push temajozias/placement-rank-app:v1.0

to reduce the size of your container, you can chose a suitable base image, slim or lightweight versions prefered.

Docker Compose
To start running your Docker compose
docker-compose up

To run your project in detached mode
docker-compose up -d

list all the running containers on docker compose
docker-compose ps 

restart all the running containers on docker compose
docker-compose restart 

get logs for all the running containers on docker compose
docker-compose logs 

stop docker compose
docker-compose stop

#### Docker Swarm
Docker Swarm is a tool for managing a group of Docker containers accross multiple servers as a single system.

### Module 5- Docker & Kubernetes Overview (Kubernetes)

Kubernetes is an open source platform for automating deployment, scaling, and management of containerized applications

to start Minikube 
minikube start

to get the status of the minikube
minikube status

Kubernetes

get all information and configuration on all clusters running or not
kubectl get all

get all information on nodes
kubectl get nodes

get all information on pods
kubectl get pods

get all information on services
kubectl get services

a namespace is a logical partition you can able to create in a cluster, as a project in google cloud.


create a deployment based on a deployment.yaml file
kubectl create --filename [path-yaml-script]
kubectl create -f [path-yaml-script]
kubectl create -f deployment.yaml

now we check the status
kubectl get all

create a service based on a service.yaml file
kubectl create --filename [path-yaml-script]
kubectl create -f [path-yaml-script]
kubectl create -f service.yaml

now we check the status
kubectl get all


to describe and get detailled information about a service
kubectl describe services [service-name]
kubectl describe services recruitment-rank-app

to describe and get detailled information about a deployment
kubectl describe deployment [deployment-name]
kubectl describe deployment recruitment-rank-app

To interact with a minikube cluster we need to create a tunnel
to access our application through a web browser we need to enable a tunnel

minikube service [service-name]
minikube service recruitment-rank-app

Now you run the web app and open the web app in a new browser window

get a pod name, just copy it from the command below
kubectl get pods

extract logs from a specific pod
kubectl logs [pod-name]

to get the names of all deployments in order to copy names before delete them
kubectl get deployments
kubectl get services

delete deployments and services you created
kubectl delete deployment [deployment-name]

kubectl delete service [service-name]
kubectl delete svc [service-name]

#### Deploying an ML Model using Docker and Amazon EKS
Loggin into aws
Search Bar --> Elastic Kubernetes Service --> Create cluster --> Cluster configuration --> Name(you provide a name) --> Kubernetes version(pick the most suitable for you) --> Cluster IAM role(Create recommended role) --> Node IAM role(Create recommended role) --> VPC(Create VPC) --> Create

You can also create using AWS CLI, it is suitable to work on production than UI

first we should authenticate our cli to connect to our aws account
Search Bar --> IAM --> (Left side navigation panel) Access management --> Users --> Create User --> User name(You provide a name(eks-user)) --> Do not check the check box --> Next --> Set permissions --> Permissions options(Add user to group) --> User Groups --> Create group --> User group name(eks-user-group) --> Permissions policies(AdministratorAccess) --> Create user group --> User groups(eks-user-group) --> Next --> Review and create --> Create user --> Select the user you created --> Security credentials --> Access Keys --> Create access key --> Use case(Command Line Interface) --> Confirmation (check the checkbox) --> Next --> Description tag value(add a description) --> Create access key(you download or copy the key and the secret) --> Done

Now we configure this access key to our PC
Command Prompt --> aws configure
AWS Access Key ID [None]: ********FT72
AWS Secret Access Key [None]: **************SFPL
Default region name [None]: us-east-1
Default output format [None]: json

Now we should install another cli to interact with eks. "eksctl"

Windows PowerShell(Administrator Mode)--> choco install -y eksctl
if error you should install chocolatey in administrator powershell with this command

Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

check the eksctl version
eksctl version

To create a cluster on top of aws using eksctl
eksctl create cluster --name test-cluster --version 1.31 --region us-east-1 --zones=us-east-1a,us-east-1b,us-east-1c,us-east-1d --nodegroup-name linux-nodes --node-type t2.micro --nodes 2

we will be installing this in kubernetes
https://github.com/temajozias/aws-eks.git

launch docker and minikube

get all available informations
kubectl get all

to deploy the flask version 1 app, open a coman prompt in the version 1 directory
kubectl create -f placement-flask-deployment.yaml
kubectl create -f placement-flask-service.yaml

check 
kubectl get all

now we create a tunnel to interact with the app
minikube service flask-app-service

the application runs and open into a web browser
input the container port for the model 80 and hit predict

list all services
minikube service list

kubectl config set-context --current --namespace=default

list all
kubectl get all

kubectl will not work if you turn off your docker desktop, so, if you quit your docker desktop, and you run 'kubectl get all' and there is no errors, it means that your kubernetes are running on cloud.

move to this folder
"MLOPS Specialization Course\Materials\mlops-k8s-project-placement-rank\mlops-k8s-project-placement\AWS EKS"

kubectl create -f app-deployment.yaml
kubectl create -f loadbalancer.yaml

then 
kubectl get all

copy the domain name to your notepad

move to this folder
"MLOPS Specialization Course\Materials\mlops-k8s-project-placement-rank\mlops-k8s-project-placement\AWS EKS web-app"

edit the web-predict-app.py file and paste the domain url you just copied in the url variable and save it

open a terminal in the current folder
python web-predict-app.py

so your app is running locally but your model is running on aws
you set the port to 80 and test your app if the prediction works


to delete a cluster on top of aws
eksctl delete cluster --name [cluster-name]
eksctl delete cluster --name test-cluster

in web app v1
launch recruitment-rank-app clusterip service
and flask-app-service loadbalancer service
and both deployments

to know the history of deployments
kubectl rollout history deployment [deployment-name]
kubectl rollout history deployment flask-app
kubectl rollout history service flask-app-service

to deploy the 2nd version of the web app, get into the web app directory and open a command prompt
kubectl apply -f [.yaml]

only use "kubectl create [.yaml]" when you are trying to deploy your app for the first time

to update your existing deployment you use
kubectl apply -f [.yaml]
kubectl apply -f placement-flask-deployment.yaml

do not update the service, just and only the deployment

kubectl get all

now you open the tunnel to access the web app
minikube service flask-app-service


to rollback to the previous version
kubectl rollout undo deployment [deployment-name]
kubectl rollout undo deployment flask-app
kubectl rollout undo service flask-app-service

to rollback to a specific version
kubectl rollout undo deployment [deployment-name] --to-revision=[revision-number]
kubectl rollout undo deployment flask-app --to-revision=1

to check the status of your rollout
kubectl rollout status deployment [deployment-name] flask-app
kubectl rollout status deployment flask-app

assuming you edited the number of repicas, you update your yaml script and
kubectl apply -f [.yaml]
kubectl apply -f placement-flask-deployment.yaml

or you can directly update the number of replicas through kubectl
kubectl scale deployment [deployment-name] --replicas=[new-number]
kubectl scale deployment flask-app --replicas=4

you check the status
kubectl get all


to access minikube dashboard
minikube dashboard

### Hands on DVC
DVC works with git

pc------(aws configure)-----> aws S3

we will use S3 as our cloud storage

Github [Data(information)]          Storage(S3) [Data]
data.xml.dvc                        data.xml

example-get-started(local repo) ----> 2 remote repos [1. Info of your data(GitHub), 2. Storage of your data(AWS S3)]

initialisation of the local repo using git and dvc
example-get-started --> git init
example-get-started --> dvc init

git status

git commit -m "Initialize DVC repo"

git status

now you add a file to the folder
dvc get https://github.com/iterative/dataset-registry get-started/data.xml -o data/data.xml

the data is stored locally and on aws s3 but the information of a data(data.xml.dvc) is stored in github

we neeed to start versionning the data
dvc add [filepath]
dvc add data/data.xml
dvc add .

now you track the changes of the data using git
git add 'data\.gitignore' 'data\data.xml.dvc'
git add .

you commit
git commit -m "added raw data"

to add a remote storage to aws s3
data --> S3
dvc remote add -d storage [path/uri(unique ressource identifier)(s3,blob,gdrive)]

now we create a s3 bucket on aws s3
s3://dvc-data-store-tema/dvcstore

so to add the remote
dvc remote add -d storage s3://dvc-data-store-tema/dvcstore

get the status of the repo
git status

you add and commit the changes
git add .
git commit -m "configured remote storage"

you want to push the data to s3
pc---data.xml-->s3

dvc push  --> [s3://data-store-dvc/dvcstore]

dvc push will make a connection to aws through credentials already configured and push to s3
dvc push --> AWS (Cred) --> S3

Now we remove the file and the cache to test test dvc pull
rm -f data/data.xml
rm -rf .dvc/cache

now we pull the data from the remote repo
dvc pull

now lets double the file content
cp data/data.xml /tmp/data.xml
cat /tmp/data.xml >> data/data.xml

now you add the modified data to the staging area for dvc
dvc add data/data.xml

you can check the modifications
git status
git diff

now you commit the changes
git add .
git commit -m "data got updated"

now you push the new version of your data to s3
dvc push

list the modifications
git log --oneline

now to rollback to a previous version of the data
    first you need to checkout the previous version with git to get the corresponding information about the data
    git checkout HEAD^1 data/data.xml.dvc
    Now you pull the data cooresponding to the version of the information you have
    dvc pull
now you create a repo on github to push your changes
git remote add origin https://github.com/temajozias/dvc-store-data.git
git branch -M main
git push -u origin main

if a user wants to download a data from a repository
dvc get [git-repo-url] data
dvc get https://github.com/temajozias/dvc-store-data.git data

what if another user wants to work on th repository
first he needs to have an access to your repository
git clone https://github.com/temajozias/dvc-store-data.git

to download the data since you have information about it, get inside the folder, open a terminal
dvc pull

now you edit the data and save it
git status
dvc status
dvc status --json

you can try to check the differencres in the data, but it is the same as dvc status
dvc diff

now you add your data changes, commit them and push
dvc add data/data.xml
git add .
git commit -m "little edit to the data"
git push origin main
dvc push

we are done with dvc

s3 can also version data without dvc
S3 --> Buckets --> Create Bucket --> Bucket name(input name) --> Create Bucket
inside bucket --> Properties --> Bucket Versionning --> Edit --> Enable --> Save changes

S3 --> Buckets --> Create Bucket --> Bucket name(input name) --> Bucket versionning(nable) --> Create Bucket

now you upload anything inside the bucket

you edit your uploaded file and save it without changing the name and upload it again

in s3 you enter the file --> versions

### Module 7- High-level Overview of Model Management Tools Hands-on
login into aws --> CloudShell --> clone the repo
git clone https://github.com/03sarath/amazon-sagemaker-mlflow-fargate.git
cd amazon-sagemaker-mlflow-fargate/

follow the readme file to deploy
pip3 install -r requirements.txt

replace account_id and aws_region with yours to deploy the Cloud Deployment Kit on top of aws
cdk bootstrap aws://195275646384/us-east-1

cdk deploy --parameters ProjectName=mlflow --require-approval never

force the deletion of a secret 
Use the AWS CLI to permanently delete the secret
Replace your-secret-name with your Secrets Manager secret ID and your-region with your AWS Region:

aws secretsmanager delete-secret --secret-id your-secret-name --force-delete-without-recovery --region your-region
To verify that you permanently deleted the secret, run the DescribeSecret API call:

aws secretsmanager describe-secret --secret-id your-secret-name --region your-region
Note: The deletion has a short time delay.

When you delete the secret, you receive the following error:

An error occurred (ResourceNotFoundException) when calling the DescribeSecret operation: Secrets Manager can't find the specified secret.


aws secretsmanager delete-secret --secret-id dbPassword --force-delete-without-recovery --region us-east-1

to doublecheck

aws secretsmanager describe-secret --secret-id dbPassword --region us-east-1


Now you copy the endpoint and paste to a new tab to access the ui
MLflow-MLFLO-p348juwhHcHt-e923a91313220257.elb.us-east-1.amazonaws.com
MLflow-MLFLO-p348juwhHcHt-e923a91313220257.elb.us-east-1.amazonaws.com

SageMaker AI --> Applications and IDEs --> Notebooks --> Create a notebook instance --> Notebook instance name(your name) --> IAM role --> Dropdown --> Create a new role --> Keep default --> Create role --> Create notebook instance --> Open Jupyter/ JupyterLab --> 

Clone the repo https://github.com/03sarath/amazon-sagemaker-mlflow-fargate.git 

In Jupyter create a folder named "source_dir" then upload files from the repo to the folder source_dir in jupyter

copy all the remaining files on the lab folder in the parent directory

Open "1_track_experiments.ipynb" --> replace the tracking uri with your own url and run the notebook

You will see the experiment appearing in the mlflow server

How to register a model
Select an experiment --> Register model --> Create New Model --> Model Name (your name) --> Register

How to deploy a registered model
Open "3_deploy_model.ipynb" --> replace the tracking uri with your own url

Add permissions for Amazon SageMaker to create and push docker images to Amazon ECR
Amazon SageMaker --> Notebooks --> Name of the notebook (mlflow-notebook) --> Permissions and encryption --> click on the IAM role and open in a new window --> Permissions policies --> Add Permissions --> Create Inline Policy --> Select a service (Elastic Container Registry) --> Actions allowed --> Manual Actions --> All Elastic Container Registry actions --> Ressources --> All --> Next --> Review and Create --> Policy name (ecr-full-access-mlflow-may-25) --> reate Policy

Now you can run the "setup environment" cell in the notebook

Search Bar --> ECR --> Private Registry --> Repositories --> Create repository --> repository name (mlflow) --> create --> copy the URI of the repo (195275646384.dkr.ecr.us-east-1.amazonaws.com/mlflow)

Notebook cell N7 --> replace the URI with your own you just copied --> run the cell

Notebook cell N9 --> replace the URI with your own you just copied --> run the cell

Notebook cell N11 --> replace the URI with your own you just copied --> run the cell

Notebook cell N11 --> replace the URI with your own you just copied --> run the cell

model URI format
'models:/[model-group-name]/[version-number]'
models:/boston-housing-prod/1

replace the model URI in cell N12

Once your endpoint is set
Sagemaker AI --> Inference --> Endpoints

Register a 2nd version of a model
mlflow --> choose another experiment --> Register model --> choose the same model group --> Register 

to deploy the 2nd version of your model, you need to register first, a 2nd version of a model. Then you change the verion number in the model uri to the second version

model URI format
'models:/[model-group-name]/[version-number]'
models:/boston-housing-prod/2

You must update the deployment instead of creating a new one


You must delete the endpoint to avoid bills.

You must delete the stacks MLflowStack and CDKToolkit

in the AWS CloudShell
aws cloudformation delete-stack --stack-name MLflowStack
aws cloudformation delete-stack --stack-name CDKToolkit

where to see the logs
CloudWatch --> Logs --> Log groups --> search bar(sagemaker) --> Filter by event time 

### Module 8- Feature Store - Feast Part 1 Hands-on
Docker Desktop --> 

Feast Setup

Getting started with ubuntu environment

Pull the ubuntu image

docker pull ubuntu:latest

run ubuntu image

docker run -it -p 8888:8888 ubuntu:latest

inside the operating system

apt-get update
apt-get upgrade

1. Install Git
apt install git
git --version

2. Install Python & Python Env
apt install python3 -y
python3 --version

3. Install pip
apt install python3-pip -y
pip3 --version

4. Install Python venv
apt install python3-venv -y

5. Clone the repo






### Module 9- AWS MLOps - SageMaker Pipelines Hands-on
Using Amazon SageMaker Studio to build an end to end ML pipeline

First we create a domain
SageMaker AI --> Admin configurations(left side navigation panel) --> domains --> Create domain --> Set up for single user (Quick setup) --> Set Up

Enter your Domain --> Domain Settings --> Authentication and permissions(scroll down) --> Copy the Default execution role

Domain Default execution role
arn:aws:iam::195275646384:role/service-role/AmazonSageMaker-ExecutionRole-20250509T224837

Search Bar --> Systems manager --> Applications Tools (left side navigation panel) --> Parameter Store --> Create parameter --> Name(/sagemaker/execution/role) --> Keep defaults --> Value(Paste the excution role you copied) --> Create parameter

1. Create a Sagemaker Custom Project Template
fork the repo
https://github.com/03sarath/mlops-cdk-github-action.git

    1. Domain Ok
    2. Systems Manager ok
    3. OpenID Connect

Create a connexion from GitHub/GitHub Action(endpoint) <---(OpenID Connect(Token))---> (endpoint)AWS 

GitHub endpoint URL : https://token.actions.githubusercontent.com

AWS endpoint URL : sts.amazonaws.com 
(STS = Securrity Token Service)

This is to allow GitHub Actions to create ressources on aws services
    ---> Access
    ---> Permissions(Create, Read, Update, Delete) aws services (SageMaker, Service Catalog, etc)

    a. Configuring OpenID Connect(authentication technique from aws to a third party platform such as GitHub Action)
        
        We need to create a token first, then roles and assign permissions to it in aws
        
        Search bar --> IAM --> Access Management(Left side nav pan) --> Identity providers --> Add provider --> Provider Type(OpenId Connect) --> Provider url(GitHub endpoint URL) --> Audience(AWS endpoint URL) --> Add provider

        Now we create a role and assign policies to it

        IAM --> Access Management(Left side nav pan) --> Roles --> Create Role --> Trusted Entity Type(Web Identity) --> Web Identity --> Identity provider(the token we just created(token.actions.githubusercontent.com)) --> Audience(sts.amazonaws.com) --> GitHub Organisation(temajozias)(GitHub username) --> GitHub repository(mlops-cdk-github-action) --> GitHub Branch(main) --> Next --> Permissions Policies(
            AmazonEC2ContainerRegistryFullAccess
            AmazonS3FullAccess
            AWSServiceCatalogAdminFullAccess
            AWSCloudFormationFullAccess
            IAMFullAccess
            AmazonSageMakerFullAccess
            AmazonSSMFullAccess) --> Next --> Name, review, and create --> Role details --> Role name(mlops-cdk-github-action-may-25) --> Create role

        Enter the created role(mlops-cdk-github-action-may-25) --> Copy the role ARN
        OpenID Role ARN
        arn:aws:iam::195275646384:role/mlops-cdk-github-action-may-25

    1. Domain Ok
    2. Systems Manager ok
    3. OpenID Connect ok

    1st Pipeline to deploy the infrastructure on top of AWS
    --> AWS Account ID: 195275646384
    --> Region : us-east-1

    Now we configure aws variables as GitHub secrets into the github repo

    GitHub Repo(mlops-cdk-github-action-may-25) --> Settings --> Secrets and Variables --> Actions --> New repository secret --> Name(AWS_ACCOUNT_OPENID_IAM_ROLE) --> Secret(OpenID Role ARN) (arn:aws:iam::195275646384:role/mlops-cdk-github-action-may-25) --> Add secret

    New repository secret --> Name(AWS_REGION) --> Secret(us-east-1) --> Add secret

    New repository secret --> Name(AWS_ACCOUNT) --> Secret(195275646384) --> Add secret


Now we make sure we have deleted the cdk toolkit to be sure there are no errors in running the pipeline
Delete:
1. MLFlow Stack first and CDKToolkit later
Search Bar --> CloudFormation -->  and delete any stack
aws cloudformation delete-stack --deletion-mode FORCE_DELETE_STACK --stack-name dev-MLOpsServiceCatalog
2. ECR Repo
Search Bar --> ECR -->  and delete any repo starting with 'cdk'
3. S3
Search Bar --> S3 -->  empty and delete any bucket starting with 'cdk'

the most important folder for a mlops engineer in this repo is "seed_code" 
the subfolder "build_app" for the Data Scientist
the subfolder "deploy_app" for the MLOps Engineer

Now we run the first Pipeline to deploy the infrastructure on top of AWS
mlops-cdk-github-action/.github/workflows/sm_template_register_service_catalog.yml
workflow_dispatch is a way to maually run the pipeline

Manually run the pipeline
GitHub repo(mlops-cdk-github-action) --> Actions --> Sagemaker Project Template Register in Service Catalog --> Run Workflow --> Branch: main --> Log Level(warning) --> Run Workflow

Go to Service atalog for confirmation
Search Bar --> Service  Catalog --> Administration --> Portfolios --> Local Portfolios (SageMaker Organization Templates) (there is an organisation portfolio comming from your GitHub repo) --> Products(first product comming from the first run) (MLOps with GitHub Action template for real-time deployment)


Now head to Sagemaker
Search Bar --> SageMaker AI --> Admin Configuration --> Domains(select the domain you created) --> User Profiles(default user) --> Launch --> Studio --> Deployments --> Projects --> Create Project --> Organisation templates --> MLOps with GitHub Action template for real-time deployment (Your template must appear here) --> Next 

Now we need to configure few more credentials to allow SageMaker to create repositories on your GitHub repo, so  we need to configure GitHub Tokens

GitHub Username Menu --> Settings --> Developper Settings --> Personnal Access Token --> Token(classic) --> Generate new token(classic) --> Note(mlops-sagemaker-may-25) --> Expiration --> Select scopes("repo", "workflow", "delete_repo") --> Generate token --> copy and save the token 
GitHub Token
******BjXHqXPMV9zBSTn3Qng1WwDene6Ikt******


Now Back to Sagemaker studio
Search Bar --> SageMaker AI --> Admin Configuration --> Domains(select the domain you created) --> User Profiles(default user) --> Launch --> Studio --> Deployments --> Projects --> Create Project --> Organisation templates --> MLOps with GitHub Action template for real-time deployment (Your template must appear here) --> Next --> Project details --> Name(mlops-sagemaker-may-25) --> GithubUserName(temajozias) --> GithubAccessToken(******BjXHqXPMV9zBSTn3Qng1WwDene6Ikt******) --> Create


Now we go back to our GitHub repo to see if the repos "mlops-sagemaker-may-25-build" and "mlops-sagemaker-may-25-deploy" have been created
GitHub Username Menu --> Your Repositories --> you should see 2 repos("mlops-sagemaker-may-25-build" and "mlops-sagemaker-may-25-deploy") 

the content of "seed_code/build_app" is inside "mlops-sagemaker-may-25-build"
the content of "seed_code/deploy_app" is inside "mlops-sagemaker-may-25-deploy"

Now you enter the repo named "mlops-sagemaker-may-25-build"
"mlops-sagemaker-may-25-build" --> Actions --> Your Model build Pipeline is running

Now bact to sagemaker studio --> SageMaker Studio --> Projects --> mlops-sagemaker-may-25 --> Pipelines --> click on the pipeline --> check the execution status of the Pipeline --> lick on the execution --> there are graphs describing the steps.

Projects --> mlops-sagemaker-may-25 --> Model groups --> there is a model group created after training the model --> enter the model group --> There is the first version of your model --> enter it to see details and approve the model for deployment

Once you approve, the model starts to be deployed due to a trigered model deployment pipeline


Go back to GitHub, the repo "mlops-sagemaker-may-25-deploy" --> Actions --> The Pipeline for Model deployment is executiong.

to check the status of the model being depolyed
SageMaker domain Studio --> Deployments --> Endpoints --> There is an endpoint being created.

To test the endpoint, go to test inference

text/libsvm

 9.0 2:-0.1581639832819342 3:-0.18020374283281237 4:-0.22754502585380668 5:-0.31558758483463023 6:-0.19315215043463071 7:-0.3612518271019884 8:-0.4334549241447536 9:1.0

Trigering a SageMaker Pipeline
    Trigger a SageMaker Pipeline using EventBridge
        Creating Schedule Rules on EventBridge(CRON Expression)
        Creating a Data Rule on EventBridge

Pipelines Schedules in relation with EventBridge are in the Pipeline section of the SageMaker Studio

or 

Search Bar --> Amazon EventBridge --> (Left Side Panel) Scheduler --> Schedules --> Create Schedule --> Schedule name(mlops-may25) --> Schedule Pattern(Recurring schedule) --> Schedule type(Rate-based schedule) --> Rate expression(10 minutes) --> Flexible time window(10 minutes) --> TimeFrame(optional) --> Next --> Select Target --> Templated targets --> SageMaker --> SageMaker Pipelines --> Select the corresponding Pipeline ARN --> Next --> Settings -Optional (Use defaults) --> Next --> Review and create schedule --> create schedule

Now you can delete your scheduler
Amazon EventBridge --> (Left Side Panel) Scheduler --> Schedules --> Create Schedule --> Schedule name(mlops-may25) --> Delete --> Delete

Let's say tomorrow you want to work on your own use case, how to change and adapt your Pipeline (MLOps Specialisation course Day 16 at 01h35min)

Option1
mlops-cdk-github-action
seed code

Option2
mlops-sagemaker-may25-build
mlops-sagemaker-may25-deploy

edit pipeline.py in mlops-sagemaker-may25-build.ml_pipelines.training


Too enable auto scaling, go to Endpoints in your studio --> Select a model endpoint --> Auto-scaling --> AllTraffic --> Enable (Value(the maximum cpu utilization value above which, sagemaker triggers the launch of another instance to load the traffic))


Now we are going to delete the ressources we just created to avoid bills

Now bact to sagemaker studio --> SageMaker Studio --> Projects --> (select) mlops-sagemaker-may-25 --> 3 dots right side --> Delete Project --> Delete.

Now CloudFormation --> Stacks
    --> Delete "dev-MLOpsServiceCatalog"
    --> Delete "dev-mlops-sagemaker-may-25-p-dzmzyskkeozw"

Noe we delete Endpoints

SageMakerAI --> Inference --> Endpoints --> Delete the Endpoints -->

Now we delete ecr containers
ECR --> Private Repository --> Repositories --> cdk- --> Delete --> Delete

Now we delete the S3 buckets
S3 --> 

### Module 9- Azure MLOps - Azure Pipelines Hands-on

repo to work with, to fork
https://github.com/03sarath/mlops-v2-ado-demo.git

Azure MLOps v2 Python SDK (MLFlow tracking server with no UI (SDK))

we will have 3 pipelines


Step 1: Connection between Azure DevOps and Azure Cloud

You must edit this code snippet with your own details
```
projectName="<your project name>"
roleName="Contributor"
subscriptionId="<subscription Id>"
environment="<Dev|Prod>" #First letter should be capitalized
servicePrincipalName="Azure-ARM-${environment}-${projectName}"
# Verify the ID of the active subscription
echo "Using subscription ID $subscriptionID"
echo "Creating SP for RBAC with name $servicePrincipalName, with role $roleName and in scopes     /subscriptions/$subscriptionId"
az ad sp create-for-rbac --name $servicePrincipalName --role $roleName --scopes /subscriptions/$subscriptionId
echo "Please ensure that the information created here is properly save for future use."
```
Subscription ID : a4e45119-38b2-4358-a741-057df1780756
Subcription name : Azure subscription 1
we should have something like this.
```
projectName="mlops-azureml-may25-tema"
roleName="Contributor"
subscriptionId="a4e45119-38b2-4358-a741-057df1780756"
environment="Prod" #First letter should be capitalized
servicePrincipalName="Azure-ARM-${environment}-${projectName}"
# Verify the ID of the active subscription
echo "Using subscription ID $subscriptionID"
echo "Creating SP for RBAC with name $servicePrincipalName, with role $roleName and in scopes     /subscriptions/$subscriptionId"
az ad sp create-for-rbac --name $servicePrincipalName --role $roleName --scopes /subscriptions/$subscriptionId
echo "Please ensure that the information created here is properly save for future use."
```
Now we run the above script into Azure Cloud Shell

Right side pannel, next to Copilot --> Cloud Shell --> Bash --> No Storage account required --> Subscription(Azure Subscription 1) --> Apply --> Copy and Paste the above mentionned code --> copy and save the Shell Output
```
{
  "appId": "*************************",
  "displayName": "Azure-ARM-Prod-mlops-azureml-may25-tema",
  "password": "**************************************",
  "tenant": "*************************************"
}
Please ensure that the information created here is properly save for future use.
```
Now we go to Azure DevOps portal to create our first project.


Azure DevOps --> New Project --> Project name(azure-mlops-may25) --> Visibility(Private) --> Create

Now we need to make a connection between the project(azure-mlops-may25) in Azure DevOps and Azure Cloud 

Azure DevOps project(azure-mlops-may25) --> (Left Side nav panel at bottom) --> Project Settings --> Pipelines --> Service Connections --> Create Service Connection --> Azure ressource Manager --> Identity type("App registration or managed identity (manual)") --> Credential(Secret) --> Environment(Azure Cloud) --> Scope level(Subscription) --> Subscription ID(**************************************) --> Subscription name(Azure subscription 1) --> Application (client) ID ("AppID") (************************************) --> Directory (tenant) ID ("tenant") (******************************************) --> Credential (Service Principal Key) --> Client Secret ("password") (**********************************) --> Verify --> Service connection name (Azure-ARM-Prod) --> Security --> (Check) Grant access permission to all pipelines --> Verify and Save --> You should see "Azure-ARM-Prod" in the service connections page

We will use Azure Repos as our repository

Azure DevOps project(azure-mlops-may25) --> Repos --> Import a Repository --> Import --> lone URL (https://github.com/temajozias/mlops-v2-ado-demo.git) --> Import 

We need go and give some permissions to this repository

Azure DevOps project(azure-mlops-may25) --> (Left Side nav panel at bottom) --> Project Settings --> Repos --> Repositories --> azure-mlops-may25 --> Security --> Users --> azure-mlops-may25 Build Service(temajozias) --> Contribute(Allow) --> Create branch(Allow)

Now we move to the Pipeline section to allocate some permissions

Azure DevOps project(azure-mlops-may25) --> Pipelines --> 3 dots right to Create Pipeline --> Manage Security --> Users --> azure-mlops-may25 Build Service(temajozias) --> Edit build pipeline(Allow)

Step 1 is complete

Now we will be preparing our data to start running
Before we start running our Pipeline in Azure DevOps, we need to configure Variables in our Azure Repos

Azure DevOps project(azure-mlops-may25) --> Repos --> Open file "config-infra-prod.yml" --> Edit --> namespace(mlopsaztema) --> postfix(9448) --> location(eastus) (it is up to your organisation) --> Commit --> Comment(you can keep default) --> Commit

Now it is time to run our First Pipeline

--------------Step 2: Infrastructure Deployment---------------
Now it is time to run our First Pipeline

Azure DevOps project(azure-mlops-may25) --> Pipelines --> Create Pipeline --> Where is Your code ? (Azure Repos Git) --> Select a repository (azure-mlops-may25) --> Configure your Pipeline(Existing Azure Pipeline YAML file) --> Branch(main) --> Path(/mlops/devops-pipelines/cli-ado-deploy-infra.yaml) --> Continue --> Run

Now we rename our pipeline corresponding to the infrastructure deployment 

Azure DevOps project(azure-mlops-may25) --> Pipelines --> azure-mlops-may25(3 dots) --> Rename/move --> "Infra Deploy" --> Save

Now we go to ressource group to see all services attached to our ressources

Azure Portal --> Search Bar --> Ressource Groups --> (there is a ressource group created by our Infra deploy) "rg-mlopsaztema-9448-prod" --> mlw-mlopsaztema-9448-prod ("Azure Machine Learning workspace") --> Launch Studio --> (Left side nav panel) Manage

To see the compute cluster we requested
Launch Studio --> (Left side nav panel) Manage --> Compute --> Compute clusters

Explanations
Run: cli-ado-deploy-infra.yml

Child Task:
install-az-cli.yml --> Install Azure CLI Cloud
install-aml-cli.yml --> Install AML CLI
create-resource-group.yml --> Install or create a resource group
create-workspace.yml (AML) --> Creates AML workspace
connect-to-workspace.yml --> Config & connect for AML CLI
create compute.yml --> Create a server to Build, train, eval & register model

--------------Step 3: Build/Train/Eval/Register---------------
Let's move to Step 3
Azure DevOps project(azure-mlops-may25) --> Pipelines --> Create Pipeline --> Where is Your code ? (Azure Repos Git) --> Select a repository (azure-mlops-may25) --> Configure your Pipeline(Existing Azure Pipeline YAML file) --> Branch(main) --> Path(/mlops/devops-pipelines/deploy-model-training-pipeline.yml) --> Continue --> Run

Now we rename our pipeline corresponding to the model build-eval-register

Azure DevOps project(azure-mlops-may25) --> Pipelines --> azure-mlops-may25(3 dots) --> Rename/move --> "model build-prep-eval-register" --> Save

Now go to your Azure Machine Learning Workspace Studio
Machine Learning Studio (mlw-mlopsaztema-9448-prod) -->(left side nav panel) Assets --> Pipelines --> 

Explanations Day 17 at 3h20min

--------------Step 4: Deployment to Production----------------
Let's move to Step 4
Azure DevOps project(azure-mlops-may25) --> Pipelines --> Create Pipeline --> Where is Your code ? (Azure Repos Git) --> Select a repository (azure-mlops-may25) --> Configure your Pipeline(Existing Azure Pipeline YAML file) --> Branch(main) --> Path(/mlops/devops-pipelines//mlops/devops-pipelines/deploy-online-endpoint-pipeline.yml) --> Continue --> Run

Now we rename our pipeline corresponding to the model build-eval-register

Azure DevOps project(azure-mlops-may25) --> Pipelines --> azure-mlops-may25(3 dots) --> Rename/move --> "Deploy to Online Endpoint" --> Save

```
{"code":"SubscriptionNotRegistered","message":"Resource provider [N/A] isn't registered with Subscription [N/A]. Please see troubleshooting guide, available here: https://aka.ms/register-resource-provider","details":[],"additionalInfo":[]}
```
Solution on this link
https://learn.microsoft.com/en-sg/answers/questions/2068819/machine-learning-online-endpoint-conflicts-error

Microsoft.Cdn
Microsoft.PolicyInsights

Go to Azure Portal -> Select your Subscription -> Select Settings -> Select Resource Providers

Make sure you add Microsoft.PolicyInsights and Microsoft.Cdn policies. Refresh and retry deployment.


To Schedule a trining Job, enter a the job and there is an Option "Schedule"

To delete ressources, 
Azure Portal --> Search Bar --> Ressources Groups --> rg-mlopsaztema-9448-prod --> Delete ressource group --> Enter the ressource group name to confirm deletion --> rg-mlopsaztema-9448-prod --> Delete --> Delete


### Module 8- Feature Store - Feast Part 2 Hands-on

MLOps Day 18 at 2h40min to the end

Run the Ubuntu machine

docker start [container-id]
docker start 47b0fdbdd170b75fd415e69f76e0116e8eed245ba9b60156876fcc00686e0eaa

We log into the Ubuntu console
docker exec -it [container-id] /bin/bash
docker exec -it 47b0fdbdd170b75fd415e69f76e0116e8eed245ba9b60156876fcc00686e0eaa /bin/bash

Now you are into your Ubuntu console

We check the existance of installed libraries
git --version
python3 --version
pip3 --version

Now we create a virtual environment and we activate it
python3 -m venv myvenv
source myvenv/bin/activate

Now we install feat in our environment
pip3 install feast

Feast is a programmatic feature store, not a no code feature store

We start by creating a simple project on feast
feast init my_tema_project

Then you list the folders to confirm the project creation
ls

then we enter the project
cd my_tema_project
ls

cd feature_repo

all datasets are in the folder
cd data

Now back to the root of your feast project
cd /my_tema_project/feature_repo

We now create a feature store
feast apply

back to the root directory
cd ..
cd ..

Now you clone the repository
git clone https://github.com/03sarath/feast-store-local.git
ls

we will move all the .py files of feast-store-local repo inside feature_repo (/my_tema_project/feature_repo) of our project

cd feast-store-local
ls

mv [files to move] [path]
mv gen_train_data.py  get_data_inference.py  inspect_data.py /my_tema_project/feature_repo
ls
cd ..
cd /my_tema_project/feature_repo
ls

now let's understand the data using inspect_data.py
python inspect_data.py

Now that we have already created a feature store, let's try to fetch data for our model training (offline feature store) how to invoke an offline feature store ?
python gen_train_data.py 

```
----- Feature schema -----

<class 'pandas.core.frame.DataFrame'>
RangeIndex: 3 entries, 0 to 2
Data columns (total 10 columns):
 #   Column                              Non-Null Count  Dtype
---  ------                              --------------  -----
 0   driver_id                           3 non-null      int64
 1   event_timestamp                     3 non-null      datetime64[ns, UTC]
 2   label_driver_reported_satisfaction  3 non-null      int64
 3   val_to_add                          3 non-null      int64
 4   val_to_add_2                        3 non-null      int64
 5   conv_rate                           3 non-null      float32
 6   acc_rate                            3 non-null      float32
 7   avg_daily_trips                     3 non-null      int32
 8   conv_rate_plus_val1                 3 non-null      float64
 9   conv_rate_plus_val2                 3 non-null      float64
dtypes: datetime64[ns, UTC](1), float32(2), float64(2), int32(1), int64(4)
memory usage: 336.0 bytes
None

----- Example features -----

   driver_id           event_timestamp  ...  conv_rate_plus_val1  conv_rate_plus_val2
0       1001 2021-04-12 10:59:42+00:00  ...             1.219632            10.219632
1       1002 2021-04-12 08:12:10+00:00  ...             2.676280            20.676280
2       1003 2021-04-12 16:40:26+00:00  ...             3.315079            30.315079

[3 rows x 10 columns]
(myvenv) root@47b0fdbdd170:/my_tema_project/feature_repo#
```

we just learned how to extract data from our offline feature store.
Now we will get the data for invoking a model (online feature store)
python get_data_inference.py
```
{'acc_rate': [None, None],
 'avg_daily_trips': [None, None],
 'conv_rate': [None, None],
 'driver_id': [1004, 1005]}
```
we are getting no response because there is no data in the table, we are needed to load a data to the online table. We will load data from the current hour to the online feature store

CURRENT_TIME=$(date -u +"%Y-%m-%dT%H:%M:%S")

feast materialize-incremental $CURRENT_TIME
```
Materializing 2 feature views to 2025-06-04 02:56:33+00:00 into the sqlite online store.

driver_hourly_stats_fresh from 2025-06-03 02:56:49+00:00 to 2025-06-04 02:56:33+00:00:
0it [00:00, ?it/s]
driver_hourly_stats from 2025-06-03 02:56:49+00:00 to 2025-06-04 02:56:33+00:00:
0it [00:00, ?it/s]
(myvenv) root@47b0fdbdd170:/my_tema_project/feature_repo#
```

Now we have a data in out sql database, we can now get the data to invoke a model
python get_data_inference.py
```
{'acc_rate': [0.15498384833335876, 0.7225118279457092],
 'avg_daily_trips': [371, 605],
 'conv_rate': [0.015781890600919724, 0.5051556825637817],
 'driver_id': [1004, 1005]}
(myvenv) root@47b0fdbdd170:/my_tema_project/feature_repo#
```

### Module 9:  GCP MLOps - Module 10: Kubeflow Stack and ML Pipeline development Hands-on
MLOps Day 19 

Git repo https://github.com/temajozias/Kubeflow-pipelines-mlops/tree/main/kubeflow-steup-Local-Minikube

run one command at a time with Git Bash

export PIPELINE_VERSION=2.2.0
kubectl apply -k "github.com/kubeflow/pipelines/manifests/kustomize/cluster-scoped-resources?ref=$PIPELINE_VERSION"

kubectl wait --for condition=established --timeout=60s crd/applications.app.k8s.io
kubectl apply -k "github.com/kubeflow/pipelines/manifests/kustomize/env/platform-agnostic?ref=$PIPELINE_VERSION"



kubectl apply -k "github.com/kubeflow/pipelines/manifests/kustomize/cluster-scoped-resources?ref=2.2.0"
kubectl wait --for condition=established --timeout=60s crd/applications.app.k8s.io
kubectl apply -k "github.com/kubeflow/pipelines/manifests/kustomize/env/platform-agnostic?ref=2.2.0"


export PIPELINE_VERSION=2.4.0
kubectl apply -k "github.com/kubeflow/pipelines/manifests/kustomize/cluster-scoped-resources?ref=$PIPELINE_VERSION"
kubectl wait --for condition=established --timeout=60s crd/applications.app.k8s.io
kubectl apply -k "github.com/kubeflow/pipelines/manifests/kustomize/env/dev?ref=$PIPELINE_VERSION"


Now head to console.cloud.google.com

gcp --> New Project --> mlops-gcp-may-tema --> Create Project --> Select Project --> 

Search Bar --> Vertex AI --> Dashboad --> Get started with Vertex AI --> Enable all recommended API

KubeFlow Components
Logical components that make up kubeflow
- Central Dashboard
- Kubeflow Notebooks
- Kubeflow Pipelines
- Katib
- Training Operators
- Multi-Tenancy

KubeFlow features
- Notebooks
- Model Training: Fairing (Takes a training notebook, tranforms it a docker image and runs it as a job in kubernetes)
- Model Serving (KServe)
- Pipelines

repo with source code https://github.com/temajozias/vertex-ai-mlops-kfp2

colab file https://colab.research.google.com/drive/1hPv3KFI7dmmLnRHI9A2i5VXbsTwf20mt

how to run the piline file file manually in VertexAI (ml_winequality.yaml)
VertexAI --> Pipelines --> Create Run --> Upload file --> ml_winequality.yaml --> Run Schedule(One-off/Recurring) --> Continue --> Submit

If you run the Pipeline programmatically, got to
VertexAI --> Pipelines\

https://www.google.com/url?sa=D&q=http%3A%2F%2Faiplatform.googleapis.com%2F

custom_model_training_cpus

LOCATION
http://aiplatform.googleapis.com/custom_model_training_cpus

com.google.cloud.ai.platform.common.errors.AiPlatformException: code=RESOURCE_EXHAUSTED, message=The following quota metrics exceed quota limits: aiplatform.googleapis.com/custom_model_training_cpus, cause=null; Failed to handle the pipeline task. Task: Project number: 301136090151, Job id: 5421710665700081664, Task id: -5257138479673901056, Task name: get-wine-data, Task state: DRIVER_SUCCEEDED, Execution name: projects/301136090151/locations/us-central1/metadataStores/default/executions/14583053097397103741

I am from the business sales team. As the project is for a university and for the education sector, I have to point you to the education sales team. You can fill this form and team will contact you to help https://support.google.com/code/contact/project_quota_increase


git add . ; git commit -m 'adding code for kubeflow pipelines' ; git push origin main
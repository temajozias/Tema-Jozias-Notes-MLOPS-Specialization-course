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
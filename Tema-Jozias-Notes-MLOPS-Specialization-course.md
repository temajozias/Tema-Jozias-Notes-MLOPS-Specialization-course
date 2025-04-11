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
Project Summary → Boards Panel → Boards → To Do → New Item (Update title of the App) → Assign the task to yourself or a team member → Move the task to “Doing” stage → Click on the task title to open task details, description and discussion panel with tema members → copy the number assigned to the task #<Board Number> like #2 → Edit your local repo and push the changes with the commit in a different form like “fixed AB#<Board Number>” like “fixed AB#2” and push to the remote → The task will move from Doing to Done.

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

Home --> Search Bar --> Cloud Run --> Create Service --> 


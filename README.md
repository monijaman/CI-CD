# ![aws](https://github.com/julien-muke/Search-Engine-Website-using-AWS/assets/110755734/01cd6124-8014-4baa-a5fe-bd227844d263) Deploy React App with Full CI/CD Pipeline on AWS | GitHub + CodePipeline + S3 🚀

<div align="center">

  <br />
    <a href="https://youtu.be/1k6s4shjpRc?si=NOQo77cZQtTA6eGW" target="_blank">
      <img src="https://github.com/user-attachments/assets/9f9b55f3-c9c2-4b87-97c7-030be3464a83" alt="Project Banner">
    </a>
  <br />

<h3 align="center">Deploying a React app using EC2 and Terraform</h3>

   <div align="center">
     Build this hands-on demo step by step with my detailed tutorial on <a href="http://www.youtube.com/@julienmuke/videos" target="_blank"><b>Julien Muke</b></a> YouTube. Feel free to subscribe 🔔!
    </div>
</div>

## 🚨 Tutorial

This repository contains the steps corresponding to an in-depth tutorial available on my YouTube
channel, <a href="http://www.youtube.com/@julienmuke/videos" target="_blank"><b>Julien Muke</b></a>.

If you prefer visual learning, this is the perfect resource for you. Follow my tutorial to learn how to build projects
like these step-by-step in a beginner-friendly manner!

<a href="https://youtu.be/1k6s4shjpRc?si=NOQo77cZQtTA6eGW" target="_blank"><img src="https://github.com/sujatagunale/EasyRead/assets/151519281/1736fca5-a031-4854-8c09-bc110e3bc16d" /></a>

## <a name="introduction">🤖 Introduction</a>

In this tutorial, you'll learn how to build a fully automated CI/CD pipeline using AWS CodePipeline, CodeBuild, and Amazon S3 to deploy a React.js application hosted on GitHub. Say goodbye to manual deployments, every time you push to your repo, your app will automatically build and deploy to a static website on S3!

## <a name="steps">🔧 How it Works:</a>

1. Push code to the `main` branch on GitHub.
2. AWS CodePipeline detects the change.
3. AWS CodeBuild installs dependencies and builds the React app.
4. The build output is deployed to the S3 bucket.
5. S3 serves the app as a public static website.

## ➡️ Step 1 - Setup your React.js App on GitHub

First, we’ll set up a React app by cloning the React app from my GitHub repository. You can use your own or follow along with mine. Make sure the app is committed to GitHub.

```bash
git clone https://github.com/monijaman/CI-CD.git
```

## ➡️ Step 2 - Create S3 Bucket for Hosting

For the deploy provider we are going to use Amazon S3, we will create an S3 bucket.

1. Head over to the S3 service.
2. Click Create bucket.
3. Name it something unique like `ms3-ci-cd`

![Image](img/create-s3.jpg)

Once the s3 bucket is created, leave it for now, as we will come for it to finish the setup later.

## ➡️ Step 3 - Create CodePipeline

Now the fun part—building the pipeline.

1. Go to AWS CodePipeline, click Create pipeline.
2. Name your pipeline: `ms3-codepineline`
3. Choose a new service role or an existing one.
4. Service Role:

Create a new service role with these permissions:

CloudWatchLogsFullAccess
S3FullAccess (if deploying to S3)
CodeBuildBasePolicy

![Image](img/pipeline-1.jpg)

4. Add source stage:
   <br>- Source provider: GitHub (connect your GitHub account).
   <br>- Select your repository and branch.

⚠️Note: Make sure you select the repository that we cloned in Step 1

![Image](img/pipeline-2.jpg)

<br>- Once you are connected to your Github and select your repository, then choose "Next"

![Image](img/pipeline-3.jpg)

5. Add build stage:
   <br>- Provider: AWS CodeBuild.
   <br>- Choose "Create project"
   ![Image](img/pipeline-5.jpg)
   ![Image](img/cp1.jpg)
   ![Image](img/cp2.jpg)
   ![Image](img/cp3.jpg)
   ![Image](img/cp4.jpg)

Let's proceed to next step and create the CodeBuild Project.

OR you can create CodeBuild following this

## ➡️ Step 4 - Create CodeBuild Project

Now let’s set up CodeBuild to build and package your React app for deployment.

**Step-by-step:**

1. Go to AWS CodeBuild and click **Create Build Project**.
2. Name your project (e.g., `react-cicd-pipeline-demo`).

   ![Image](https://github.com/user-attachments/assets/4f26b687-a04f-409d-877f-092e8dc59f46)

3. For Environment, choose a managed image: `aws/codebuild/standard:7.0` (or latest available).
4. Under Buildspec, select **Use a buildspec file**.

   ![Image](https://github.com/user-attachments/assets/85452545-3411-4766-84ae-b9571389c11f)

5. In your GitHub repo, create a file named `buildspec.yml` in the root directory:

   ```yaml
   version: 0.2

   phases:
     install:
       runtime-versions:
         nodejs: 20
       commands:
         - echo Installing dependencies...
         - npm ci --legacy-peer-deps

     build:
       commands:
         - echo Building the React app...
         - npm run build

   artifacts:
     files:
       - "**/*"
     base-directory: dist
     discard-paths: no
   ```

   > This file tells CodeBuild to install dependencies, build your app, and package the production files from the `dist` folder.

6. In CodeBuild, leave other settings as default and link this project to your CodePipeline build stage.
7. Once added, continue to the next step in CodePipeline setup.

   ![Image](https://github.com/user-attachments/assets/d5d2ffa9-7c7b-4502-86af-689f7bbe0dec)

8. Add a deploy stage:
   - Provider: **Amazon S3**
   - Bucket: Select the one you created earlier (e.g., `my-react-cicd-demo`)
   - Extract file option: YES, then click "Next"

![Image](https://github.com/user-attachments/assets/ed4dfacf-40f5-4ddb-bf0b-20837c37ac8c)

<br>- Lastly, review all the configuration and click "Create pipeline"

Once the pipeline is successfully created, you’ll see it run through the `source` `build` and `deploy` stages.

## Let's finish our S3 Buckect configuration

1. Go to Amazon S3 console
2. Select our existing S3 bucket `my-react-cicd-demo`
3. You should see the S3 bucket with objects inside, extracted from our CodePipeline.
4. Now let's make this S3 Bucket public:
   <br>- On the top bar, choose "Properties"

![Image](https://github.com/user-attachments/assets/d0f13940-48fc-42ab-a42b-f57fca2eb618)

<br>- Scroll down to "Static Website Hosting" and click "Edit"

![Image](https://github.com/user-attachments/assets/24f26fed-ec71-4a0b-96df-72f51de20d02)

<br>- Under "Static Website Hosting", choose "Enable"
<br>- And specify `index.html` as the index document, then click "Save"

![Image](https://github.com/user-attachments/assets/c25619a1-822a-40bd-b43a-f941c6c2c3c8)

<br>- Next, edit some permissions, still on the tob bar choose "Permissions"
<br>- Uncheck "Block all public access" to allow public access, then click "Save changes"

![Image](https://github.com/user-attachments/assets/e4c76949-667c-4cba-a6ef-637e4d3dcc4a)

<br>- Next, we will add a bucket policy to allow public read access inside our s3 bucket. Here's the sample policy you can use:

✅ Steps to Add Bucket Policy in AWS Console
Go to the S3 console:
👉 https://s3.console.aws.amazon.com/s3

Click your bucket name (e.g., your-bucket-name).

From the left sidebar, click "Permissions" tab.

Scroll down to Bucket policy section.

Click “Edit” (or “Add policy” if none exists).

Paste your policy below (replacing your-bucket-name with your actual bucket name):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::your-bucket-name/*"
    }
  ]
}
```

## Add Theese Permission:

![Image](img/bucket_roles.jpg)
![Image](img/bucket_roles2.jpg)
![Image](img/bucket_roles2.jpg)

⚠️ Replace: `your-bucket-name` with your actual bucket name, then click "Save"

![Image](https://github.com/user-attachments/assets/625e9e8b-88ec-413d-931d-922cd21303d8)

<br>- Go back to the S3 Bucket console, on the top bar, choose Objects, then click on `index.html`
<br>- To visit your React.js App, click on the Object URL.

![Image](https://github.com/user-attachments/assets/ad404089-e051-40a6-b6c3-6de7a1acc4df)

<br>- You should see your React.js App running on Amazon S3

![Image](https://github.com/user-attachments/assets/4d6c02fd-44ba-49d5-967f-23f3d189087e)

## ➡️ Step 5 - Test the Pipeline

![Image](img/build-project-4.jpg)

## Go to IAM Console

Open the AWS Console and go to:
IAM > Roles > bangla-service-role.

Edit Trust Relationship

Click the Trust relationships tab, then click Edit trust policy.

Set the trust policy like this:

```bash
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": [
          "codebuild.amazonaws.com",
          "codepipeline.amazonaws.com"
        ]
      },
      "Action": "sts:AssumeRole"
    }
  ]
}

```

```bash
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowCodeBuildStartAndGet",
      "Effect": "Allow",
      "Action": [
        "codebuild:StartBuild",
        "codebuild:BatchGetBuilds"
      ],
      "Resource": "arn:aws:codebuild:ap-southeast-1:590183819081:project/bangla-build"
    },
    {
      "Sid": "AllowCodeStarConnectionUse",
      "Effect": "Allow",
      "Action": "codestar-connections:UseConnection",
      "Resource": "arn:aws:codeconnections:us-east-1:590183819081:connection/87bd85df-c814-44b5-856c-385ab958f971"
    }
  ]
}




```

When you use CodePipeline with a CodeConnection (like GitHub), the pipeline's service role (the IAM role you assign to the pipeline) needs permission to use the connection.

✅ Solution: Add codestar-connections:UseConnection permission
Go to IAM > Roles

Open the role used by CodePipeline (not CodeBuild).

Example: bangla-service-role

Click Add permissions > Add inline policy

Use this custom JSON policy:

````bash
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowUseCodeConnection",
      "Effect": "Allow",
      "Action": "codestar-connections:UseConnection",
      "Resource": "arn:aws:codeconnections:us-east-1:590183819081:connection/87bd85df-c814-44b5-856c-385ab958f971"
    }
  ]
}
```bash
````

```bash
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ListBucket",
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::lolita-go"
    },
    {
      "Sid": "PutDeleteObjects",
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:DeleteObject"
      ],
      "Resource": "arn:aws:s3:::lolita-go/*"
    }
  ]
}

```

# notes-microservice-lab-10-10

A serverless notes-taking microservice built with AWS Lambda, DynamoDB, and API Gateway. This project provides a complete REST API for creating, reading, updating, and deleting notes with user authentication via custom headers.

## Project Structure

- **notes-api/functions** - Lambda functions for notes operations:
  - `add-note.mjs` - Create a new note (POST /note)
  - `get-notes.mjs` - Retrieve all notes for a user (GET /notes)
  - `get-note.mjs` - Retrieve a specific note by ID (GET /note/n/{note_id})
  - `update-note.mjs` - Update an existing note (PATCH /note)
  - `delete-note.mjs` - Delete a note (DELETE /note/t/{ts})
- **notes-api/lib** - Shared utility functions
- **notes-api/tests** - Unit tests for Lambda functions
- **events** - Sample invocation events for testing
- **template.yaml** - SAM template defining AWS resources (Lambda, DynamoDB, API Gateway)

The application uses several AWS resources, including Lambda functions and an API Gateway API. These resources are defined in the `template.yaml` file in this project. You can update the template to add AWS resources through the same deployment process that updates your application code.

If you prefer to use an integrated development environment (IDE) to build and test your application, you can use the AWS Toolkit.  
The AWS Toolkit is an open source plug-in for popular IDEs that uses the SAM CLI to build and deploy serverless applications on AWS. The AWS Toolkit also adds a simplified step-through debugging experience for Lambda function code. See the following links to get started.

* [CLion](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
* [GoLand](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
* [IntelliJ](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
* [WebStorm](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
* [Rider](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
* [PhpStorm](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
* [PyCharm](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
* [RubyMine](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
* [DataGrip](https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/welcome.html)
* [VS Code](https://docs.aws.amazon.com/toolkit-for-vscode/latest/userguide/welcome.html)
* [Visual Studio](https://docs.aws.amazon.com/toolkit-for-visual-studio/latest/user-guide/welcome.html)

## Deploy the sample application

The Serverless Application Model Command Line Interface (SAM CLI) is an extension of the AWS CLI that adds functionality for building and testing Lambda applications. It uses Docker to run your functions in an Amazon Linux environment that matches Lambda. It can also emulate your application's build environment and API.

To use the SAM CLI, you need the following tools.

* SAM CLI - [Install the SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html)
* Node.js - [Install Node.js 22](https://nodejs.org/en/), including the NPM package management tool.
* Docker - [Install Docker community edition](https://hub.docker.com/search/?type=edition&offering=community)

To build and deploy your application for the first time, run the following in your shell:

```bash
sam build
sam deploy --guided
```

The first command will build the source of your application. The second command will package and deploy your application to AWS, with a series of prompts:

* **Stack Name**: The name of the stack to deploy to CloudFormation. This should be unique to your account and region, and a good starting point would be something matching your project name.
* **AWS Region**: The AWS region you want to deploy your app to.
* **Confirm changes before deploy**: If set to yes, any change sets will be shown to you before execution for manual review. If set to no, the AWS SAM CLI will automatically deploy application changes.
* **Allow SAM CLI IAM role creation**: Many AWS SAM templates, including this example, create AWS IAM roles required for the AWS Lambda function(s) included to access AWS services. By default, these are scoped down to minimum required permissions. To deploy an AWS CloudFormation stack which creates or modifies IAM roles, the `CAPABILITY_IAM` value for `capabilities` must be provided. If permission isn't provided through this prompt, to deploy this example you must explicitly pass `--capabilities CAPABILITY_IAM` to the `sam deploy` command.
* **Save arguments to samconfig.toml**: If set to yes, your choices will be saved to a configuration file inside the project, so that in the future you can just re-run `sam deploy` without parameters to deploy changes to your application.

You can find your API Gateway Endpoint URL in the output values displayed after deployment.

## Build and Test Locally

Build your application with the `sam build` command.

```bash
notes-microservice-lab-10-10$ sam build
```

The SAM CLI installs dependencies defined in `notes-api/package.json`, creates a deployment package, and saves it in the `.aws-sam/build` folder.

Test a single function by invoking it directly with a test event. Test events are included in the `events` folder in this project.

Run functions locally and invoke them with the `sam local invoke` command.

```bash
notes-microservice-lab-10-10$ sam local invoke AddNoteFunction --event events/event.json
```

The SAM CLI can also emulate your application's API. Use the `sam local start-api` to run the API locally on port 3000.

```bash
notes-microservice-lab-10-10$ sam local start-api
```

## API Endpoints

The Notes API provides the following endpoints (all require `App-User-Id` and `App-User-Name` headers):

- `POST /note` - Create a new note
- `GET /notes` - Retrieve all notes for the authenticated user
- `GET /note/n/{note_id}` - Retrieve a specific note
- `PATCH /note` - Update an existing note
- `DELETE /note/t/{ts}` - Delete a note by timestamp

Example requests with curl:
```bash
# Create a note
curl -X POST http://localhost:3000/note \
  -H "App-User-Id: user123" \
  -H "App-User-Name: John Doe" \
  -H "Content-Type: application/json" \
  -d '{"title":"My Note","content":"Note content"}'

# Get all notes
curl http://localhost:3000/notes \
  -H "App-User-Id: user123" \
  -H "App-User-Name: John Doe"
```

## Add a resource to your application
The application template uses AWS Serverless Application Model (AWS SAM) to define application resources. AWS SAM is an extension of AWS CloudFormation with a simpler syntax for configuring common serverless application resources such as functions, triggers, and APIs. For resources not included in [the SAM specification](https://github.com/awslabs/serverless-application-model/blob/master/versions/2016-10-31.md), you can use standard [AWS CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html) resource types.

## Fetch, tail, and filter Lambda function logs

To simplify troubleshooting, SAM CLI has a command called `sam logs`. `sam logs` lets you fetch logs generated by your deployed Lambda function from the command line. In addition to printing the logs on the terminal, this command has several nifty features to help you quickly find the bug.

`NOTE`: This command works for all AWS Lambda functions; not just the ones you deploy using SAM.

```bash
notes-microservice-lab-10-10$ sam logs -n AddNoteFunction --stack-name notes-microservice-lab-10-10 --tail
```

You can find more information and examples about filtering Lambda function logs in the [SAM CLI Documentation](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-logging.html).

## Unit tests

Tests are defined in the `notes-api/tests` folder in this project. Use NPM to install the [Mocha test framework](https://mochajs.org/) and run unit tests.

```bash
notes-microservice-lab-10-10$ cd notes-api
notes-api$ npm install
notes-api$ npm run test
```

## CI/CD Pipeline Setup

This project includes an automated CI/CD pipeline using AWS CodePipeline, CodeBuild, and GitHub integration for continuous deployment.

### Pipeline Components

- **pipeline.yaml** - CloudFormation template that defines the CI/CD infrastructure:
  - CodePipeline that orchestrates the build and deployment workflow
  - CodeBuild project for building and deploying the SAM application
  - GitHub App Connection for secure repository access
  - IAM roles with appropriate permissions for CodePipeline and CodeBuild

- **buildspec.yml** - AWS CodeBuild specification file that defines the build process:
  - Installs Node.js 22 and project dependencies (`npm ci`)
  - Builds the SAM application (`sam build`)
  - Deploys to AWS CloudFormation with proper IAM capabilities
  - Uses dynamic region configuration from the CodePipeline environment

### Deploy the CI/CD Pipeline

1. **Install AWS CLI** - [Install the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) if not already installed

2. **Deploy the pipeline stack**:
   ```bash
   aws cloudformation deploy \
     --template-file pipeline.yaml \
     --stack-name notes-microservice-pipeline \
     --parameter-overrides \
       GitHubRepo=<your-github-username>/notes-microservice-lab-10-10 \
       GitHubBranch=main \
     --capabilities CAPABILITY_NAMED_IAM \
     --region us-east-1
   ```
   
   Replace `<your-github-username>` with your actual GitHub username.

3. **Authorize GitHub Connection**:
   - Once the CloudFormation stack is created, navigate to the [AWS CodePipeline Console](https://console.aws.amazon.com/codesuite/codepipeline/pipelines)
   - Look for pending connections in the "Connections" section (left sidebar)
   - Find the connection named `notes-micro-github`
   - Click on it and select **"Update pending connection"**
   - Click the **"Connect"** button to authorize AWS to access your GitHub repository
   - A GitHub authorization window will open - authorize the connection
   - Once authorized, the connection status will change to "Available"

4. **Verify Pipeline Execution**:
   - Return to the CodePipeline console
   - Your pipeline should now automatically trigger on changes to the main branch
   - Watch the pipeline progress through Source, Build, and Deploy stages
   - Check the CodeBuild logs for detailed build output

### Pipeline Workflow

1. **Source Stage**: Triggered when you push changes to the GitHub main branch
2. **Build Stage**: CodeBuild executes buildspec.yml:
   - Installs dependencies
   - Runs `sam build` to package the application
   - Runs `sam deploy` to deploy to CloudFormation
3. **Deployment**: SAM automatically:
   - Creates/updates Lambda functions
   - Provisions DynamoDB table
   - Sets up API Gateway endpoints

## Cleanup

To delete the sample application that you created, use the AWS CLI. Assuming you used your project name for the stack name, you can run the following:

```bash
sam delete --stack-name notes-microservice-lab-10-10
```

To delete the CI/CD pipeline:

```bash
aws cloudformation delete-stack \
  --stack-name notes-microservice-pipeline \
  --region us-east-1
```

## Resources

See the [AWS SAM developer guide](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html) for an introduction to SAM specification, the SAM CLI, and serverless application concepts.

Next, you can use AWS Serverless Application Repository to deploy ready to use Apps that go beyond hello world samples and learn how authors developed their applications: [AWS Serverless Application Repository main page](https://aws.amazon.com/serverless/serverlessrepo/)

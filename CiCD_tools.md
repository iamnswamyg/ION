**Basic comparison of CI/CD YAML steps for Azure DevOps YAML, GitHub Actions YAML, and GitLab CI/CD YAML**

<table><thead><tr><th>Step</th><th>Azure DevOps YAML</th><th>GitHub Actions YAML</th><th>GitLab CI/CD YAML</th></tr></thead><tbody><tr><td>**Checkout Source Code**</td><td>`checkout`</td><td>`checkout`</td><td>`git clone`</td></tr><tr><td>**Build**</td><td>`task: Build`</td><td>`run: npm build` or `run: make`</td><td>`script: - make build` or `script: - npm run build`</td></tr><tr><td>**Test**</td><td>`task: Test`</td><td>`run: npm test` or `run: make test`</td><td>`script: - make test` or `script: - npm run test`</td></tr><tr><td>**Deploy**</td><td>`task: Deploy`</td><td>`run: npm deploy` or custom deployment task</td><td>`script: - make deploy` or custom deployment task</td></tr><tr><td>**Artifact Publish**</td><td>`publish: Artifact`</td><td>`upload-artifact`</td><td>`artifacts: paths:`</td></tr><tr><td>**Environment Setup**</td><td>`task: Setup Environment`</td><td>Custom environment setup steps</td><td>`before_script:` or `script:` with setup commands</td></tr><tr><td>**Notification**</td><td>`task: Send Notification`</td><td>Notifications in workflow or custom notification steps</td><td>`notify:` or `when: manual` for manual approval steps</td></tr><tr><td>**Conditional Steps**</td><td>`condition: expression`</td><td>`if: condition`</td><td>`rules:`</td></tr><tr><td>**Docker Build/Push**</td><td>`task: Docker`</td><td>`build-push` with Docker actions</td><td>`script: - docker build -t myimage .` and `script: - docker push myimage`</td></tr></tbody></table>This table provides a high-level comparison of common CI/CD steps. Note that each platform has its own specific syntax and features. It's crucial to refer to the official documentation for the most accurate and up-to-date information.

------------------

 ** Building and deploying a Node.js application as an example for each CI/CD tool**

#### Azure DevOps YAML:
```yaml<button class="flex ml-auto gizmo:ml-0 gap-1 items-center"><svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg" class="icon-sm"><path fill-rule="evenodd" clip-rule="evenodd" d="M12 4C10.8954 4 10 4.89543 10 6H14C14 4.89543 13.1046 4 12 4ZM8.53513 4C9.22675 2.8044 10.5194 2 12 2C13.4806 2 14.7733 2.8044 15.4649 4H17C18.6569 4 20 5.34315 20 7V19C20 20.6569 18.6569 22 17 22H7C5.34315 22 4 20.6569 4 19V7C4 5.34315 5.34315 4 7 4H8.53513ZM8 6H7C6.44772 6 6 6.44772 6 7V19C6 19.5523 6.44772 20 7 20H17C17.5523 20 18 19.5523 18 19V7C18 6.44772 17.5523 6 17 6H16C16 7.10457 15.1046 8 14 8H10C8.89543 8 8 7.10457 8 6Z" fill="currentColor"></path></svg>Copy code</button>
# File: azure-pipeline.yml

trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

jobs:
- job: BuildAndDeploy
  steps:
  - checkout: self

  - task: NodeTool@0
    inputs:
      versionSpec: '14.x'
    displayName: 'Install Node.js'

  - script: |
      npm install
      npm run build
    displayName: 'Build Application'

  - task: AzureRmWebAppDeployment@4
    inputs:
      ConnectionType: 'AzureRM'
      azureSubscription: 'YourAzureServiceConnection'
      appType: 'webAppLinux'
      WebAppName: 'YourWebAppName'
      packageForLinux: '$(Build.ArtifactStagingDirectory)/**/*.zip'

```
Replace 'YourAzureServiceConnection' and 'YourWebAppName' with your actual Azure service connection name and web application name.

#### GitHub Actions YAML:
```yaml<button class="flex ml-auto gizmo:ml-0 gap-1 items-center"><svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg" class="icon-sm"><path fill-rule="evenodd" clip-rule="evenodd" d="M12 4C10.8954 4 10 4.89543 10 6H14C14 4.89543 13.1046 4 12 4ZM8.53513 4C9.22675 2.8044 10.5194 2 12 2C13.4806 2 14.7733 2.8044 15.4649 4H17C18.6569 4 20 5.34315 20 7V19C20 20.6569 18.6569 22 17 22H7C5.34315 22 4 20.6569 4 19V7C4 5.34315 5.34315 4 7 4H8.53513ZM8 6H7C6.44772 6 6 6.44772 6 7V19C6 19.5523 6.44772 20 7 20H17C17.5523 20 18 19.5523 18 19V7C18 6.44772 17.5523 6 17 6H16C16 7.10457 15.1046 8 14 8H10C8.89543 8 8 7.10457 8 6Z" fill="currentColor"></path></svg>Copy code</button>
# File: github-actions.yml

name: Build and Deploy

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Repository
      uses: actions/checkout@v2

    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '14'

    - name: Install Dependencies
      run: npm install

    - name: Build Application
      run: npm run build

    - name: Deploy to Azure Web App
      uses: azure/webapps-deploy@v2
      with:
        app-name: 'YourWebAppName'
        slot-name: 'production'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: .

```
Replace 'YourWebAppName' with your actual web application name. Make sure to set up the `AZURE_WEBAPP_PUBLISH_PROFILE` secret in your GitHub repository with the Azure publish profile.

#### GitLab CI/CD YAML:
```yaml<button class="flex ml-auto gizmo:ml-0 gap-1 items-center"><svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg" class="icon-sm"><path fill-rule="evenodd" clip-rule="evenodd" d="M12 4C10.8954 4 10 4.89543 10 6H14C14 4.89543 13.1046 4 12 4ZM8.53513 4C9.22675 2.8044 10.5194 2 12 2C13.4806 2 14.7733 2.8044 15.4649 4H17C18.6569 4 20 5.34315 20 7V19C20 20.6569 18.6569 22 17 22H7C5.34315 22 4 20.6569 4 19V7C4 5.34315 5.34315 4 7 4H8.53513ZM8 6H7C6.44772 6 6 6.44772 6 7V19C6 19.5523 6.44772 20 7 20H17C17.5523 20 18 19.5523 18 19V7C18 6.44772 17.5523 6 17 6H16C16 7.10457 15.1046 8 14 8H10C8.89543 8 8 7.10457 8 6Z" fill="currentColor"></path></svg>Copy code</button>
# File: .gitlab-ci.yml

stages:
  - build
  - deploy

variables:
  NODE_VERSION: "14"

before_script:
  - apt-get update -qy
  - apt-get install -y nodejs
  - npm install

build:
  stage: build
  script:
    - npm run build
  tags:
    - custom-runner

deploy:
  stage: deploy
  script:
    - echo "Deploy to Azure Web App"
    # Add your deployment script or commands here
  tags:
    - custom-runner

```
In this GitLab CI/CD example, you might need to customize the deployment script based on your requirements.

Remember to adapt these examples to your specific project structure and requirements. Also, ensure that you have the necessary permissions and configurations for each CI/CD platform.

------------------

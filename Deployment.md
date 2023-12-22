# CY2 FAQ System Deployment

This is a guide on how to deploy the CY2 LLM application for both the frontend & backend.

Sections:

## Deployment of the backend

In our backend application deployment using Github Actions, we are leveraging certain secrets that are crucial for its smooth operation. These secrets include:

1. **AZUREAPPSERVICE_PUBLISHPROFILE**: This is a necessary secret for publishing our application on Azure App Service. You can obtain this secret by following the instructions provided here.

2. **DATABASE_URL**: This secret is the connection string for our PostgreSQL database hosted on Azure. It follows the format: `postgresql://{username}:{password}@{server}.postgres.database.azure.com:5432`

Please ensure that these secrets are correctly configured in your environment to avoid any disruptions in the service.

The following file is found in the .github directory in the backend repository:

```yaml
name: Build and deploy Node.js app to Azure Web App - cy2-project

on:
  push:
    branches:
      - main
  workflow_dispatch:

jobs:
  build_and_deploy:
    runs-on: ubuntu-latest
    environment:
      name: "Production"
      url: ${{ steps.deploy-to-webapp.outputs.webapp-url }}
    steps:
      - uses: actions/checkout@v4

      - name: Set up Node.js version
        uses: actions/setup-node@v3
        with:
          node-version: "18.x"

      - name: npm install, build, and test
        run: |
          npm install
          npm run build --if-present
          npm run test --if-present

      - name: "Deploy to Azure Web App"
        uses: azure/webapps-deploy@v2
        with:
          app-name: "cy2-project"
          slot-name: "Production"
          publish-profile: ${{ secrets.AZUREAPPSERVICE_PUBLISHPROFILE }}
          package: .

  migrations:
    runs-on: ubuntu-latest
    needs: build_and_deploy
    steps:
      - name: Checkout repo
        uses: actions/checkout@v4
      - name: Setup Node
        uses: actions/setup-node@v3
      - name: Install dependencies
        run: npm install
      - name: Build the app
        run: npm run build
      - name: Apply all pending migrations to the database
        run: npx prisma migrate deploy
        env:
          DATABASE_URL: ${{ secrets.DATABASE_URL }}
```

## Deployment of the frontend

### Using Vercel

### Checkout Code:

Uses the actions/checkout action to fetch the latest version of the code.

### Login to DockerHub:

Uses the docker login command to authenticate with DockerHub using the provided DockerHub username and password secrets.

### Build and Push Docker Image:

Builds a Docker image using the docker build command, tagging it as $DOCKER_USERNAME/frontend:latest.

Uses build arguments to pass environment variables (REACT_APP_REDIRECT_URI, REACT_APP_CLIENT_ID, REACT_APP_AUTHORITY_ID, REACT_APP_BACK_END_BASE_URL) to the Docker build process.

Pushes the built Docker image to DockerHub using the docker push command.

Note: The actual deployment to a server or a hosting platform is not included in this workflow.

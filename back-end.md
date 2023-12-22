# Project Title: LLM-based FAQ System for CY2

## Introduction

CY2, an Oracle development partner, aims to enhance its FAQ system by incorporating Large Language Models (LLMs) to provide accurate responses to user queries. This project focuses on creating a proof of concept for a FAQ system that utilizes LLMs, and is adaptable to various educational institutions.

## Prerequisites

Before setting up and running the project, ensure the following prerequisites are met:

- Node.js & npm
- PostgreSQL
- SharePoint access credentials
- Pinecone access credentials

## Getting Started

### Installation

1. Clone the repository:

```bash
git clone https://github.com/CY2Project/backend.git
cd backend
```

2. Install dependencies:

```bash
npm install
```

3. Create a .env file based on the .env.example file and fill in the needed credentials, for more details refer to the **Configuration** section of this document

4. Generate prisma client:

```bash
npx prisma generate
```

5A. Run the database migrations:

```bash
npx prisma migrate deploy
```

5B. or use the push command during development:

```bash
npx prisma migrate db push
```

### Development

Everything should be conifgured, now you can run the development server.

Run the development server:

```bash
npm run start:dev
```

Visit [http://localhost:4000/api](http://localhost:4000/api) to access the swagger documentation.

Request to the API have to be made to [http://localhost:4000](http://localhost:4000).

## Basic Usage

To get started you have to first create a user, this can be done using the frontend. After that you can login using the frontend, this stores the userID from entraID in the database.

After that you can get the IdToken from browser storage and use it to authorize requests to the API.

Before you can create a pipeline, llm or document source, you have to create an organisation. (POST /organisation) This organisation is used to group llms, document sources, pipelines and users.

The FAQ system is designed to be able to use multiple LLMs, currently only OpenAI is supported. (POST /llm/openai)
Multiple document sources are also supported, currently only SharePoint is supported. (POST /documentsource/sharepoint)

These can be combined to create a pipeline (POST /pipeline), which is used to generate responses. (POST /chat-bot/:pipelineId/message)

After the pipeline is created, it fetches all the documents from the document source and creates embeddings for them and adds the to pinecone. This can take a while depending on the amount of documents.

For each pipeline, gets a namespace created in pinecone, this namespace is used to store the embeddings for that pipeline. It's alos used to search for similar embeddings within a namespace.

## Project Structure

The project structure is based on the NestJS framework, for more details refer to the [NestJS documentation](https://docs.nestjs.com/).

Each directory contains a similar structure, with a controller, service, dto, and entity file. The controller handles the incoming requests, the service handles the business logic, the dto handles the data transfer objects, and the entity handles the database model.

```
/[module-name]
    /[module-name].controller.ts
    /[module-name].service.ts
    /[module-name].dto.ts
    /[module-name].entity.ts
```

These are the most important directories:

```
/.github
/prisma
/src
    /accessToken
    /authentication
    /chat-bot
    /documentsource
        /sharepoint
    /llm
        /openai
    /ms-graph
    /organisation
    /pinecone
    /pipeline
    /prisma
    /user
    main.ts
    app.module.ts
```

### Structure details

1. /.github

This directory is used to store GitHub-related files, configurations and workflows(Github Actions).

2. /prisma

This directory contains the prisma schema and migrations.

The prisma schema is used to define the database model, for more details refer to the [Prisma documentation](https://www.prisma.io/docs/concepts/components/prisma-schema).

To deploy the migrations run:

```bash
npx prisma migrate deploy
```

To generate the prisma client run:
**Required** after every change to the prisma schema.

```bash
npx prisma generate
```

To create a new migration run:
migration-name should be a short description of the migration. And also written using \_ instead of spaces.

```bash
npx prisma migrate dev --name [migration-name]
```

3. /src

   3.1. /accessToken

   This directory contains the access token module, which is used to generate and store access tokens for the pipelines.

   3.2. /authentication

   This directory contains the authentication module, which is used to authenticate users.

   3.3. /chat-bot

   This directory contains the chat bot module, which is used to handle the chat bot functionality.

   This module contains the functionality to generate responses using the LLM module.

   3.4. /documentsource

   This directory contains the document source module, which is used to handle the document source functionality.

   The FAQ system is designed to be able to use multiple document sources, currently only SharePoint is supported.

   From the controller you can create a new document source.

   3.4.1. /sharepoint

   This directory contains the SharePoint module, which is used to handle the SharePoint functionality.

   This module contains SharePoint specific functionality, such validating the connection to the API.

   3.5. /llm

   This directory contains the LLM module, which is used to handle the LLM functionality.

   The FAQ system is designed to be able to use multiple LLMs, currently only OpenAI is supported.

   This module contains the functionality to create LLM contexts. Generating responses is done in the chat bot module.

   3.5.1. /openai

   This directory contains the OpenAI module, which is used to handle the OpenAI functionality.

   This module contains openAI specific functionality, such validating the connection to the API.

   3.6. /ms-graph

   This directory contains the Microsoft Graph module, which is used to handle the Microsoft Graph functionality.

   This service contains the functionality to access the Microsoft Graph API. It also contains the functionality to fetch files from a sharepoint site.

   3.7. /organisation

   This directory contains the organisation module, which is used to handle the organisation functionality.

   3.8. /pinecone

   This directory contains the Pinecone module, which is used to handle the Pinecone functionality.

   This service contains the functionality to create embeddings, delete, and to search for similar embeddings.

   3.9. /pipeline

   This directory contains the pipeline module, which is used to handle the pipeline functionality.

   This service also contains a cron job, wich is used to update the embeddings in pinecone.

   3.10. /prisma

   This directory contains the prisma module, which is used to handle the prisma functionality.

   this provides a service to access the prisma client.

   To make use of it, you should inject the PrismaService into your service.

   ```typescript
    constructor(private prismaService: PrismaService) {}
   ```

   And add the PrismaModule to the imports array of your module

   ```typescript
   imports: [PrismaModule],
   ```

   3.11. /user

   This directory contains the user module, which is used to handle the user functionality.

4. main.ts

This file is the entry point of the application.

It also contains the configuration for the swagger documentation.

Cors configuration is also done in this file.

This file also contains a configuration setting called whitelist, if set to true validator will strip validated object of any properties that do not have any decorators. Meaning that if you have a dto with a property that is not decorated with a decorator, it will be **removed** from the validated object.

5. app.module.ts

This file is the root module of the application. When you add a new module, you have to add it to the imports array of this file.

## Technologies used

- NestJS
- Prisma
- OpenAI
- Pinecone
- Microsoft Graph (SharePoint)

### Database ERD

[ERD](Architecture.md) contains the ERD of the database.

### Configuration

The following environment variables are required:

- **DATABASE_URL**: The connection string to the database.
- **PORT**: The port the server should run on. default: 4000

Authentication variables (used to authenticate users):

- **AZURE_AD_CLIENT_ID**: The client id of the Azure AD application.
- **AZURE_AD_TENANT_ID**: The tenant id of the Azure AD application.

both can be found on the Azure AD application overview page.

openai variables (used to create the embeddings):

- **OPENAI_API_KEY**: The api key of the OpenAI application.

pinecone variables:

- **PINECONE_API_KEY**: The api key of the Pinecone application.
- **PINECONE_ENVIRONMENT**: The environment of the Pinecone application.
- **PINECONE_INDEX_NAME**: The name of the index to use.

these can be found on the Pinecone application overview page.

### Missing features

- Currently the system only supports OpenAI, this should be extended to support multiple LLMs.

- Similarly, the system only supports SharePoint, this should be extended to support multiple document sources.

- Another missing feature is the ability to delete specific embeddings from Pinecone. Currently the only way to delete embeddings is to delete the entire namespace and regenrate the embeddings. The issue with this is that it is difficult to keep track of wich files in sharepoint are deleted, edited or added.

  Currently when a file is embedded, we also store the filename and a file content hash within it's metadata. This is used to check if the file has been changed. If the file has been changed, the embedding is deleted and regenerated.

  This is not ideal, because it is possible that the file has been changed, but the content hash is still the same. This would result in the embedding not being regenerated.

  Also when a file is deleted, we don't know from sharepoint wich file has been deleted. So we can't delete the embedding from pinecone by filename. We can check the recently deleted files, but this is not ideal because it is possible that the file has been deleted before the cron job runs. This would result in the embedding not being deleted. **This is a major issue**, because the reason for deleting a file is most likely because it is outdated or contains sensitive data.

- Another missing feature is the ability to control wich files are fetched from SharePoint. Currently all files are fetched.

# Extra notes

- This file also contains a configuration setting called whitelist, if set to true validator will strip validated object of any properties that do not have any decorators. Meaning that if you have a dto with a property that is not decorated with a decorator, it will be **removed** from the validated object.

- Embeddings contain the filename and a file content hash within it's metadata. Wich can be used to find specific embeddings.
  These functions can be found in the pinecone service:

  - findEmbeddingByFilename
  - deleteEmbeddingByFilename

- The cron job is configured to run every week, this can be changed in the pipeline service.

- When embeddings a created they are grouped by namespace.

- Each pipeline has it's own namespace in pinecone.

- Each pipeline is a seperate chatbot, with it's seperate knowledge base.

- Each pipline can have multiple document sources, but a single llm.

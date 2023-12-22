# Project Title: LLM-based FAQ System for CY2

## Introduction

CY2, an Oracle development partner, aims to enhance its FAQ system by incorporating Large Language Models (LLMs) to provide accurate responses to user queries. This project focuses on creating a proof of concept for a FAQ system that distinguishes between System Administrators and Developers, utilizes LLMs, and is adaptable to various educational institutions.

## Prerequisites

Before setting up and running the project, ensure the following prerequisites are met:

- Node.js
- npm (Node Package Manager)
- MongoDB
- SharePoint access credentials

## Getting Started

### Installation

1. Clone the repository:

   ```bash
   git clone https://github.com/CY2Project/Frontend_v2.git
   cd Frontend_v2
   ```

2. Install dependencies:

   ```bash
   npm install
   ```

### Development

Create a .env file based on the .env.example file and fill in the needed credentials, for more details refer to the **Configuration** section of this document.

Run the development server:

```bash
npm run start
```

Visit [http://localhost:3000](http://localhost:3000) to access the front-end.

## Project Structure

```
/.github
/public
/src
    /assets
    /components
    /hooks
    /layouts
    /pages
    /validation
```

### Structure details

1. /.github

This directory is used to store GitHub-related files and configurations.

2. /public

The /public directory contains static assets that need to be served directly, such as images, fonts, or HTML files. These files are not processed by build tools and are directly accessible to users.

3. /src

   3.1. /assets
   The /assets directory stores static assets used in the application, such as images, icons, or stylesheets. These assets are likely to be processed by build tools during the build process.

   3.2. /components
   The /components directory contains React components. These components are modular, reusable pieces of the user interface that can be composed together to build pages or layouts.

   3.3. /hooks
   The /hooks directory contain custom React hooks. React hooks are functions that enable developers to use state and lifecycle features in functional components.

   3.4. /layouts
   The /layouts directory contains higher-order components (HOCs) or layout components. These components define the overall structure or layout of pages in the application.

   3.5. /pages
   The /pages directory contains React components that represent individual pages of the application. Each page is associated with a route.

   3.6. /validation
   The /validation directory contains code related to form validation or data validation in the application.

### Summary:

- GitHub Configuration: /github
- Static Assets: /public
- Source Code:
  - /assets: Static assets processed by build tools.
  - /components: Reusable React components.
  - /hooks: Custom React hooks.
  - /layouts: Layout components or higher-order components.
  - /pages: React components representing individual pages.
  - /validation: Code related to form or data validation.

## Technologies Used

- React
- JavaScript
- bootstrap
- reactstrap
- sass

## Features

- Role-based system (Azure AD)
- CRUD (Create, read, update, and delete) for:
  - Organizations
  - Data Sources
  - LLMs
  - Pipelines
  - Tokens

### Missing features

There is a hard coded dashboard, this should display statistics based on the pipeline usage.
Auth tokens from Azure do not refresh, currently the user is required to log out and in every hour.
Pipeline demo currently requires user to fill in a token themselves, this should be automated.
When logging in, automatically select the organization that was last selected.

## Configuration

The .env file contains a series of values that the user will need to specify:

- **REACT_APP_REDIRECT_URI**: Location the front-end is running on, for local development this is [http://localhost:3000](http://localhost:3000).
- **REACT_APP_BACK_END_BASE_URL**: Location the back-end is running on, for local development this is [http://localhost:4000](http://localhost:4000).
- **REACT_APP_CLIENT_ID**: Client id from Azure.
- **REACT_APP_AUTHORITY_ID**: Tenant id from Azure.
- **REACT_APP_CLIENT_SECRET**: Client secret from Azure.

## Usage

### Microsoft Authentication Library (MSAL) Configuration

The authentication process in this project is facilitated by the Microsoft Authentication Library (MSAL). MSAL is responsible for managing authentication tokens and interacting with the Azure Active Directory (Azure AD) for secure user authentication.

#### msalConfig

The msalConfig object holds essential configurations required by MSAL:

- clientId: The client ID of the application registered in Azure AD.
- redirectUri: The URI to which the authentication response is redirected.
- authority: The authority or identity provider endpoint, constructed using the Azure AD authority URL and the environment-specific ID.

```javascript
export const msalConfig = {
  clientId: process.env.REACT_APP_CLIENT_ID,
  redirectUri: process.env.REACT_APP_REDIRECT_URI,
  authority:
    "https://login.microsoftonline.com/" + process.env.REACT_APP_AUTHORITY_ID,
};
```

This code can be found in /src/authConfig.js

#### MSAL Instance Initialization

The msalInstance function initializes and returns an instance of MSAL. It configures the instance to store authentication state in the session storage for a seamless user experience.

```javascript
export const msalInstance = async () => {
  const msalApp = new PublicClientApplication({
    auth: msalConfig,
    cache: {
      cacheLocation: "sessionStorage",
      storeAuthStateInCookie: true,
    },
  });
  await msalApp.initialize();
  return msalApp;
};
```

This code can be found in /src/authConfig.js

#### getTokenClaims

This function uses the active MSAL instance to get the active user account and returns the ID token claims.

```javascript
export const getTokenClaims = async () => {
  try {
    const msal = msalInstance();
    const account = (await msal).getActiveAccount();
    if (!account) throw new Error("No accounts signed in.");
    return account.idTokenClaims;
  } catch (error) {
    console.error("Error acquiring token:", error);
  }
  return null;
};
```

This code can be found in /src/components/auth/GetToken.js

#### getAccessToken

Similar to getTokenClaims, this function retrieves the active user account and returns the access token.

```javascript
export const getAccessToken = async () => {
  try {
    const msal = msalInstance();

    const account = (await msal).getActiveAccount();
    if (!account) throw new Error("No accounts signed in.");
    return account.idToken;
  } catch (error) {
    console.error("Error acquiring token:", error);
  }
  return null;
};
```

This code can be found in /src/components/auth/GetToken.js

#### Default Headers for Authenticated Requests

The defaultHeaders function sets up default headers for HTTP requests, including the Authorization header with the access token.

```javascript
const accessToken = await getAccessToken();
const headers = {
  headers: {
    Authorization: `Bearer ${accessToken}`,
  },
};
return headers;
```

This code can be found in /src/components/auth/GetToken.js

### Hooks

The RoleInstance and OrganizationInstance hooks play pivotal roles in managing user roles and organizational data within the application.

#### RoleInstance

The RoleInstance hook, encapsulated within the RoleContext, facilitates the handling of user roles.

Upon component mount, the hook leverages Azure AD tokens to extract the user's role and an array of roles.

The GetRole function ensures the dynamic retrieval of user roles, allowing for real-time updates.

Furthermore, the AddUser function facilitates the addition of users to the system, contingent upon the availability of a valid token.

#### OrganizationInstance

The OrganizationInstance hook, nested within the OrganizationContext, orchestrates the management of diverse organizational data.

Upon component mount, it initiates data retrieval, fetching organizational details such as:

- organizations
- pipelines
- data sources
- LLMs
- tokens

The hook dynamically adjusts its behavior based on changes in the active organization, ensuring up-to-date data.

This hook provides a suite of functions for fundamental operations within an organization, including adding and deleting organizations, LLMs, data sources, pipelines, and tokens.

#### Hook usage

##### Provider setup

Ensure that the component where you intend to use the useRole hook is nested within the RoleInstance provider.
This typically happens in your application's main file or a higher-level component.

```javascript
import React from "react";
import { RoleInstance } from "hooks/roleInstance";

const App = () => {
  return <RoleInstance>{/* Your components go here */}</RoleInstance>;
};
```

##### Use the useRole Hook in Your Component:

Now, within any component that is a child of RoleInstance, you can use the useRole hook.

```javascript
import React from "react";
import { useRole } from "hooks/roleInstance";

const YourComponent = () => {
  const { role, roles, GetRole, AddUser } = useRole();

  return (
    <div>
      <p>User Role: {role}</p>
      {/* Your component UI */}
    </div>
  );
};
```

##### Access Role-Related Functions and State:

The useRole hook provides access to the user role (role), an array of roles (roles), and functions like GetRole and AddUser that allow you to fetch and update role-related information.

Customize your component's logic using these values and functions to create a dynamic user experience based on the user's role within the application.

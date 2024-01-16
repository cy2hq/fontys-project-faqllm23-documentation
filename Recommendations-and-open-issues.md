# Recommendations & open issues

### Frontend

#### Design

Before front-end development, we started with the design. We used Figma to create a prototype of the eventual end product. In Figma, you can find components of the desired library of the stakeholder (Argon Dashboard 2), with these components you can redesign the page or add new components if necessary.

#### UI components

Before the software development part of the frontend, we created designs using Figma with pre-built components of argon dashboard 2 made by creative Tim. We recommend using a more sophisticated front-end components solution like tailwind or MUI as it will load much better code.

The current design is incomplete, we miss pages that support features such as adding users to an organisation and viewing the pipelines as a developer as there is also no explicit guide on setting up a chatbot.

We highly recommend using Figma for the frontend designs. We have designs available for the following groups. We suggest to copy these files as soon as possible, as the figma workspace might be removed in the near future.

https://www.figma.com/file/AkhRQThsQoDdTb9QgMzQ4C/CY2-Prototype?type=design&node-id=7%3A6&mode=design&t=sbxA62x8v9oawihU-1

#### Proposal

We propose that if the following group is to work on the frontend, first understand the current backend interactions (endpoints), create a new design which supports previously mentioned features, approve it with the stakeholder, and then work on the implementation of the design and work on absent backend features.

Since we are of a software development background, we most likely have not made the “best” design. Redesigning the website is quite likely to be a followed option and in that case, first redesign, approve with stakeholders, and then implement.

As of now the backend also does not support the feature to update data sources LLM’s, this might be one of the more essential features of the overall product, as chatbots are made to be configured.

### User management

#### Authentication

The stakeholders wanted us to create this solution for multiple schools. Since most schools make use of Azure, we choose to use Azure AD for the authentication system, however, there are institutions which may not use this authentication method, and opt for other services such as Google. In this case, new authentication services need to be added to the overall architecture and new code needs to be implemented to support this functionality.

#### Authentication to user management

Users have to be manually added to Azure since the user management was not recommended to be implemented by us. The user is then added to the database, but there is no connection to an organisation. The linking of the user to the organisation is still an open issue, which we do now manually as of writing this document.

For the sake of GDPR compliance, the stakeholders recommended the of cloud services (in our case Azure) to manage the users. This meant that the users would need to be manually added to our Azure working environment to gain access to the app. This is not a problem on its own, however it is the logic which comes with our current setup. As of now only when the user authenticates them through our app, we create the user instance in our backend. This user however is not linked to any organisation. This link between the user and the organisation is done manually.

#### Proposal

We propose to extract the organisation from the Azure environment and propagate this data to the backend. However, when executing this solution, you should define which software takes care of the user management (as of now Azure) and how you will propagate the organisation data. Think of cases like: “What should happen if an institute has multiple authentication providers?”, “What should happen if institutes have the same name?”.

How we foresaw it is when we authenticate the user we receive a token, with this token we could retrieve the user's organisation and propagate this data to the backend. This idea is not implemented.

As of now the user management and authentication are working, but are not ready on a production level. It is possible to continue with the current setup but it is greatly ill-advised as you will run into problems in the long run.

# Data source management

After setting up your chatbot and pipeline it is possible to select multiple data sources. Our project faces the issue that the data source's integrity may alter over time due to deletion, modification, or additions of data and our application does not keep track of these changes as it only recieves them once the connection is made. The idea was to create a mechanism that fetches the data over a course of time. Some datasources might have a available webhook to call to check if there is a change or perhaps a function inside the app needs to be made to check for any changes in the data source, but this would be a quite expensive and what we forsee a nonscalable solution.









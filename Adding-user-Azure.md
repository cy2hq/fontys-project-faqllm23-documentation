# How to add Users in Azure

### Adding new users to Azure Entra ID

Navigate to Azure Entra ID.
![Alt text](assets/0.png)

Add user role.
![Alt text](assets/1.png)

### Adding roles

To add roles go to Enterprise applications.
![Alt text](assets/2.png)

Then go to Authentication.
![Alt text](assets/3.png)

Then Users and groups and add a user.
![Alt text](assets/4.png)

Then assign a role to the user and click Assign.
![Alt text](assets/5.png)

### Adding user to an organization

Note: As we currently do not have user organization management, we need to add the user role in the database within the `UsersInOrganisation` table.

![Alt text](assets/6.png)

The `userID` is set by the `sub` value from the Azure idToken.
![Alt text](assets/7.png)

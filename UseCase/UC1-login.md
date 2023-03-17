# Specification


# The use case - “The user logs into the system”
## Trigger Event
A user asks the system to log them in

## Main success scenario
The system renders the login screen and asks the user to provide their credentials \
The user provides its credentials (email and password) and asks the system to log them in \
The system asks the backend API to verify users’ credentials \
The backend API confirms that the provided credentials are valid \
The backend API confirms that the user has an active subscription \
The backend API provides the system with an authentication token for the user \
The system logs the user in \
The main success scenario ends.


## Extension 2a: The user authenticates via 3rd party system (Google, Facebook)
2a1. The user asks the system to authenticate it via 3rd party authentication system (Google or Facebook) \
2a2. The system redirects the user to the appropriate 3rd party authentication system \
2a3. The user successfully authenticates using 3rd party authentication system and the 3rd party authentication system provides the system with the user’s identification information \
2a4. The system asks the backend API to confirm that the user has a valid account and a valid subscription \
2a5. The backend API confirms that the user indeed has a valid account and a subscription \
2a6. The backend system provides the system with the appropriate authentication token \
2a7. The main success scenario resumes from step 7

## Extension 2a4a: The user does not have a valid account
2a4a1. The backend system determines that the user does not have a valid account and informs the System of that fact \
2a4a2. The system informs the user that their credentials are not valid \
2a4a3. The main success scenario restarts from step 1

## Extension 2a4b: The user does not have a valid subscription
2a4b1. The backend system determines that the user does have an account but does not have a valid subscription and informs the System of that fact \
2a4b2. The system informs the user that it does not have a valid subscription, and instructs them to go to the web to buy a subscription \
2a4b3. The main success scenario terminates

```mermaid
flowchart TD;
   %% Trigger event
   TRIGGER([The user asks the system to log them in])-->S1;
   style TRIGGER fill:#ccf
 
   %% Main success scenario
   S1[The system renders the login screen<br /> and asks the user to provide their credentials]-->S2;
   S2[/"The user provides its credentials (email and password)<br /> and asks the system to log them in"/]-->S3;
   S3[The system asks the backend API to verify users credentials]-->S4;
   S4{The backend API confirms that<br /> the provided credentials are valid?};
   S4--Yes-->S5;
   S5{The backend API confirms that<br /> the user has an active subscription?};
   S5--Yes-->SUCCESS
  
   %% Extension 2a: The user authenticates via 3rd party system (Google, Facebook)
   S1-->EXT2a1;
   EXT2a1["The user asks the system to authenticate it<br /> via 3rd party authentication system <br />(Google or Facebook)"]-->EXT2a2;
   EXT2a2[The system redirects the user to the<br /> appropriate 3rd party authentication system]-->EXT2a3;
   EXT2a3{The user successfully authenticates <br />using 3rd party authentication system?};
   EXT2a3--Yes-->EXT2a4;
   EXT2a3--No-->EXT4a2;
   EXT2a4[the 3rd party authentication system<br /> provides the system with the user's<br /> identification information]-->EXT2a5;
   EXT2a5[The system asks the backend system to confirm<br /> that the user has a valid subscription]-->EXT2a6;
   EXT2a6{The backend API confirms that<br /> the user has an active subscription?};
   EXT2a6--Yes-->EXT2a7;
   EXT2a6--No-->EXT5a2;
   EXT2a7[The backend system provides the system<br /> with the appropriate authentication token]-->SUCCESS;
 
   %% Extension 4a: The user does not have a valid account
   S4--No-->EXT4a1;
   EXT4a1[The backend system determines<br /> that the user does not have a valid account<br /> and informs the System of that fact]-->EXT4a2;
   EXT4a2[The system informs the user that<br /> their credentials are not valid];
   EXT4a2-->S1;
 
   %% Extension 5a: The user does not have a valid account
   S5--No-->EXT5a1;
   EXT5a1[The backend system determines<br /> that the user does have an account but <br />does not have a valid subscription<br /> and informs the System of that fact]-->EXT5a2;
   EXT5a2[The system informs the user that <br />it does not have a valid subscription]-->EXT5a3;
   EXT5a3[The system instructs the user to </br />go to the web to buy a subscription];
   EXT5a3-->FAILURE;   
 
   SUCCESS([System logs the user in]);
   style SUCCESS fill:#cfc
 
   FAILURE(Login flow terminates);
   style FAILURE fill:#fcc
```
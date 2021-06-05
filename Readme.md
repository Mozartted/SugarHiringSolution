# Sugar living test notes.

Created: Jun 4, 2021 1:29 AM
Created by: Mozart Osita
Last edited: Jun 5, 2021 10:34 PM
Projects: https://www.notion.so/Get-a-new-remote-job-5f7cf8868de643a4b21c15c07ad76508

This documentation provides all insights into my thought process while working on the problem. As well as defining my solution paths and informing my descision making process.

# Problem statement.

Sugar is preparing to launch at City View Apartments. City View uses the Acme Locks keyless entry system to allow residents to unlock doors within the building. They have provided us with the following information:

- A list of resident information (including which doors each user has access to): `residents.csv`
- A list of doors and their corresponding Acme Locks device IDs: `doors.csv`
- Credentials to access the Acme Locks API: `api.txt`

**Your task is to build an application that allows all City View residents to securely lock and unlock the doors that they have access to.**

The description of this task is purposefully ambiguous - we want to see how well you can scope, design, and build an end-to-end application that solves a real problem for users.

Feel free to use whatever languages / frameworks / tools you are most comfortable with to build this application. You can build any kind of application (web, mobile, API, something else entirely), as long as it solves the core problem of allowing residents to securely lock and unlock the doors that they have access to.

Please limit the time you spend working on this project to a **maximum of 3-5 hours**.

The scope of this problem may be larger than the timeframe allotted to solving it - that's ok. If that is the case, please describe in detail any other components of your application that you would build to solve the core problem given more development time.

Please submit your completed application via email as a Github repo containing all source code, assets, and documentation that you wish to include in your submission.

To make this challenge fair to everyone, plase don't fork this repo to develop your application (unless you use a private repository) or share your application with others. Please don't copy any one else's solutions if you find them online.

Happy hacking

# Data Analysis.

Insight into my analysis of the data and how that informs the structure of the database models for the different apartments and their organization in the database.

There are 2 datasets, 

- Doors
- Redisdents.

The doors database provides all information on the doors and the corresponding ACME ID for them and the resident dataset provides details for the residents and which doors they hava access to, so residents can have access to multiple doors. So they should be open to access which ever door they need to open.

Each door and corresponding resident belongs to City Hall Apartment, therefore it's important to be able to connect doors and apartments to the individual users that belong to each. So we should be able to open a door if the user is allowed access to that door.

This lead to 3 data models

### Apartments.

Data structure 

- Name
- ID

Relationships

- Doors
- Residents

### Doors

Data structure

- Name
- ID
- ACME ID

Relationships.

- Apartment
- Resident

### Residents / Users

Data structure

- Name
- ID

Relationships

- Apartment
- Door.

---

# Usability.

Users in their apartments looking to access their doors would usually have only on device possily handy, Their phones, which means it's better to have them be able to unlock their doors from an App, 

The Platform needs to be able to add multiple different apartments, and their residents via a csv or excel upload of residents and doors, Therefore this has to reside on a service which would allow the app be able to make a request for an apartment and therefore allow users be able to access their doors, after accessing the app.

The App can't be tied down to one apartment, therefore it needs to be able to allow users authenticate for their apartment and provide them with their door information to unlock their doors.

So in terms of architecture there are 3 apps.

- A mobile app for the users.
- A service api for the App to make it's request.
- A web frontend app for the business to upload their Apartments and residents and be able to see all information of apartments and residents being added.

The service Api would be the core of the application as it's supposed to be able to provide the mobile app and the admin web app all data resources they need to manage their requests.

So here's the product requirement.

# User experience.

How do users use the mobile app.

The aim is for users to simply be able to unlock and lock doors they have access to. They should only have access to their doors.

## Interfaces.

There would be 2 interfaces on the mobile app.

- Welcome interface - Allowing users to login, with only their emails for now.
- Dashboard interface - The interface in which users would be able to lock or unlock the doors they have access to.

At the moment we are going for something really simply and usuable.

# Product requirement.

The platform would require 3 apps.

- An api service for processing the requests from the mobile app and managing the data for apartments, because the intention is to scale the product to multiple apartments.
    - Be able to request door management operations (LOCK, UNLOCK) from authorized users/residents
    - Be able to process uploaded Apartments and their CSV files.
    - Be able to Authenticate Users / Residents
    - Be able to retrieve information on the different Residents and Rooms (Door units) in an apartment.
- A web app for connecting to the Api service for uploading apartments and their corresponding door units and residents.
    - Be able to upload apartments
    - Be able to retrieve apartment data
- A mobile app for being able to manage the doors that the residents are given access to.
    - Be able to authenticate into the app as a resident.
    - Be able to lock and unlock my doors.

# Tools and Technical Choices.

For development of this service, to faciliate speed, I opted for tools I currently use actively at the time of development. 

That said, I made to also consider thoughts around scaling the service and how I'd improve the architecture in the future.

### The Api Server.

I wanted to the apis to run on GraphQL, so I developed a nodejs graphql backend running on a containerized postgreSQL database.

Another thought was to use firebase in developing a cloud service for this api backend, while storing the data on FireStore. This is a part I would have take for a larger scale process on the development of the service. But I opted for the VPS hosted backend because of the basic assumption that A business like this would like to run have these data on private servers which it has active control over.

I hosted this service on a vps server which I manage. And linked it to a subdomain on legobox.io, on the server I used pm2 to manage and monitor the service operations, as well as providing benchmark data and running rebooting and other scripting operations when the need arises.

I intentionally set the graphql playground on production to provide the documentations and insight into the data models.

Here's a hosted version.

[GraphQL Playground](https://sugar-living-api.legobox.io/graphql)

Here's the git repository.

[Mozartted/sugar-test-graphql-api](https://github.com/Mozartted/sugar-test-graphql-api)

### The Web App.

I needed a means of being able to upload apartments and considered  the idea of being able to add more than one apartment as the need arises, Therefore the app needed an admin web app service with which to manage these apartments.

At the moment the only support available through this service is to upload apartments and view them with the residents in them as well as their access to the  doors.

This was developed with React, it's a basic create-react-app, running an apollo client to connect to the graphql API.

![Sugar%20living%20test%20notes%202b0872f38e644e1ba074e79a18b288fe/Screenshot_2021-06-05_at_22.06.52.png](Sugar%20living%20test%20notes%202b0872f38e644e1ba074e79a18b288fe/Screenshot_2021-06-05_at_22.06.52.png)

![Sugar%20living%20test%20notes%202b0872f38e644e1ba074e79a18b288fe/Screenshot_2021-06-05_at_22.07.04.png](Sugar%20living%20test%20notes%202b0872f38e644e1ba074e79a18b288fe/Screenshot_2021-06-05_at_22.07.04.png)

Here's the git repository.

[Mozartted/Sugar-admin](https://github.com/Mozartted/Sugar-admin)

And here's a link to a hosted version of the webapp (its running on firebase hosting)

[Sugar living admin](https://sugarservice-a3b90.web.app/)

### The Mobile App.

The mobile app is the app which the users would use in their buildings to access the doors they need to access. This was developed with react native, and for now I focused more on the iOS portion of it. 

![Sugar%20living%20test%20notes%202b0872f38e644e1ba074e79a18b288fe/Simulator_Screen_Shot_-_iPhone_11_-_2021-06-05_at_22.10.15.png](Sugar%20living%20test%20notes%202b0872f38e644e1ba074e79a18b288fe/Simulator_Screen_Shot_-_iPhone_11_-_2021-06-05_at_22.10.15.png)

![Sugar%20living%20test%20notes%202b0872f38e644e1ba074e79a18b288fe/Simulator_Screen_Shot_-_iPhone_11_-_2021-06-05_at_22.11.47.png](Sugar%20living%20test%20notes%202b0872f38e644e1ba074e79a18b288fe/Simulator_Screen_Shot_-_iPhone_11_-_2021-06-05_at_22.11.47.png)

Here's the repository.

[Mozartted/sugar-react-native](https://github.com/Mozartted/sugar-react-native)

# Possibilties.

With respect to the solution, It's far from being a functional product, There are a lot of things I would consider implementing given more time, but let's discuss on a few.

With respect to each service and app.

### The GraphQL Api.

1. To scale horizontally, I'd run multiple instances of the server with nginx load balancers  while scaling the docker base architecture of the database to be able to run for much larger number of buildings. (this assumes that we don't intend to move on to using AWS or GCP services) in scaling up.
2. Add extra mutations and functialities to be able to manage users, create and change passwords, Add password to authentication and also allow 2-factor authentications for users that turn on the feature.
3. Provide admin class users (I already setup the datastructure to support that) and admin class queries and mutations for the project. I'd also setup admin authentication for the admin web app, as well as proving mutations to manage residents and re-assign access to the doors and the apartments.

### The Web App.

1. As an admin webapp, I wanted to add an authentication page, or at the very least provide basic http authentication to provide some authentication for admin class users.
2. The admin app would be extended to be able to properly and fully manage apartments. Allowing admins to be able to perform the following.
    - Reassign rooms.
    - Remove residents.
    - Add new residents directly.
    - Lock down or unlock doors directly from the admin panel. (This is important for buildings which are closed down).

### The Mobile App.

1. I'd add a password to the authentication process and also provide the ability to use the face unlock or thumbprint depending on the OS(android or iOS).

    Note: For the future it would have been good to consider 2 factor authentication as part of the processes that would be undertaken.

2. Provide more interface for mangement of the users account
    - Changing password.
    - Adding phone details
    - Providing extra information (Photo ID, username, etc)
3. Improve the quality of the UI of the product.
# backend-learning
A pathway for learning backend web coding

1. Select a programming language. Common ones include:
    - Javascript/Typescript (node)
    - Python
    - Go
    - Java
2. Build a web-server that serves an html document that has the text `Hello World!` in it.
    - Prefer common web-server libraries, like the standard library, over obscure frameworks.
    - In Node.js, this would be Express.
    - In Python, this would be Django or Flask.
    - In Go, this would be the standard library, with a third party router, like Gorilla or Chi.
    - In Java, this would be the standard library.
3. Add a POST endpoint that takes in a JSON message in the body, and replies with the message reversed in a JSON response.
    - The goal here is to parse and format JSON data.
    - To help test this endpoint, Postman and curl are both valid options.
    - Make sure to set the `Content-Type` header on the response.
    - **Extra-credit**: Accept input in JSON, XML, and form data formats, using the `Content-Type` header on the request to determine which to use.
    - **Extra-credit**: Support returning data in either JSON or XML format based on the `Accept` header on the request. Don't forget to set the `Content-Type` on the response based on the format you are returning.
4. Add endpoints that allow for saving and editing a more complicated data type. Just save the information in-memory for now, using a dictionary, map, object, etc. Do not forget thread-safety.
    1. Add a create endpoint that allows for creating a new item, and saving it.
        - The item you are saving should have at least three fields.
        - Respond with an ID of the item that can be used later to retrieve it.
    2. Add a retrieve endpoint that allows for getting a saved item using the ID.
    3. Add a delete endpoint that can be used to delete a saved item using the ID.
    4. Add a list endpoint that gives a list of IDs that have been saved.
    5. Add an edit endpoint that takes in the ID and other fields, and changes the saved information
5. Create a Dockerfile, you should be able to run your server with just the Docker image afterwards without needing any other dependencies.
    - **Extra-credit**: add a second Dockerfile that supports live-reload, so you do not need to rebuild the docker image during development.
6. Add a database, to save the information from your system, even if the server goes down.
    - Hard-code anything needed to connect the database, like a URL, project ID, password, etc.
    - Use Docker Compose to allow running both your server and the database at the same time.
        - Make sure to use docker volumes to keep the database information saved between runs.
    - Common databases include:
        - PostgreSQL
        - MySQL
        - MongoDB
7. Add support for config values. You want to be able to pass in the database connection information in config.
    - For development work, having support for a config file in yaml or json format is convienent.
    - For a live system, being able to set config using environment variables is better.
        - Docker Compose allows you to set environment variables, use them to pass the credentials to the database when you run in compose.
    - For one-off config adjustments, command-line flags can be nice as well.
    - Try to support all systems, they are each useful in different circumstances.
        - In a situation where the same config value is present in multiple locations, command-line should be highest priority, followed by environment variables, and finally the config file.
    - **Extra-credit**: If the credentials for accessing the database are missing, use the in-memory system instead.
        - Do not use the in-memory system if the credentials are present, but incorrect. The user in that case obviously wants the database, and may be surprised by suddenly running in memory.
8. Add some tests to confirm all code so far works as intended. Our system is starting to get complicated enough that standardized testing is useful.
    - For all future changes, make sure to add tests for them.
    - Prefer smaller tests when possible, with fewer asserts per test, and descriptive test names.
    - Prefer unit tests over end-to-end tests. Adjust your code to allow for mocking dependencies like the database if needed.
9. Add security on your endpoints. Require the user to be authenticated to access anything but the list and retrieve endpoints.
    - For now, just add a new endpoint that authenticates you, and another to de-authenticate. Do not bother with checking passwords or individual user accounts yet.
    - Cookies and bearer tokens are both good choices to do the authentication.
    - Make sure that whatever you use to authenticate is validated using cryptographic algorithms.
        - HMAC and JWT are both good standard technologies for this.
        - Your cryptographic key should be stored in your config.
10. Add user accounts.
    - Adjust your authenticate endpoint to just take in the ID or username of a user account. No passwords yet.
    - Having a separate endpoint for creating a new user account and authenticating from the endpoint for just authenticating may be useful.
        - Prefer a username over an email address unless you need the email address.
    - You should keep the user ID separate from the username/email. IDs cannot change, usernames and emails can.
11. Adjust your endpoints so that you can only edit and delete your own items.
    - Add a new endpoint that lists the IDs of items the currently authenticated user created.
    - **Extra-credit**: Add a permission system that lets some users edit or delete other user's items, useful for an admin or moderator without needing direct database access.
12. Add passwords to your authentication system.
    - Passwords should never be saved in a way where it is possible to retrieve the password from the saved information.
    - bcrypt and scrypt are two well known password hashing systems for securly saving passwords.
    - Adjust your register and login endpoints accordingly.
    - **Extra-credit**: Add support for an app-generated second factor. This is called TOTP, and is supported by many apps, Google Authenticator being the most well-known.
        - Add one-time-use recovery codes. If the user looses access to their phone, they can use a recovery code to get into their account. Make sure the recovery codes cannot be reused.
        - Add a way to generate a QR code from the secret. It is standard for users to scan a QR code over entering a secret value.
    - **Extra-extra-credit**: Add support for the [Web Authentication API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Authentication_API), used by browsers to support fingerprint logins and dongles.
13. Document your endpoints, so another person can use the API without knowledge of the code.
    - It is recommended to use a standard documentation schema, like OpenAPI (Previously named Swagger).
    - **Extra-credit**: Use a schema-first library to generate your endpoints from the schema. This way your documentation will always be up-to-date.
        - Potential other systems for your schema and transport are:
            - OpenAPI
                - most generally compatible
            - gRPC
                - smallest and fastest, but does not support web browsers by default
            - GraphQL
                - most flexible for users of your API, but when working in large teams where you do not know how the API will be consumed
14. Allow for anonymous users of your API.
    - Unauthenticated users should be able to create items and edit their own items.
    - Two separate anonymous users should not be able to edit each others' items.
    - If an anonymous user creates an account, any items they created should be associated with that account.
    - **Extra-credit**: Items created by unauthenticated users should be cleaned up after a period of inactivity.
        - The cleanup job should run once every minute, and delete any items from an annonymous user that has no activity for the last 5 minutes.
        - **Extra-extra-credit**: Make sure that the cleanup task will not be run twice if you are running two copies of your server at once.
15. Add a file storage system, so a user can upload files
    - Start with an in-memory version, similar to the database.
    - Then use a folder on the local disk.
        - Prevent users from being able to write to a location outside the folder, even if they upload a file with a filename like `../name.txt`
    - Then add support for a object storage system.
        - Minio is fully AWS S3 compatible, but supports working in a local docker container.
        - You can also use a cloud object storage system directly, like AWS S3, or Google Cloud Storage.
    - A good file to let the user upload is an Avatar picture for their account.
    - Make sure to add some security features, like preventing a user from uploading a 10 gb file.
16. Add an eventing system, where an event can be raised, like user created, and then additional code can be run in the background based on the events.
    - Start with an in-memory version, similar to the database.
    - Popular messaging systems that can be used for this include:
        - Kafka
        - RabbitMQ
        - Google Cloud PubSub
    - Create an item authomatically for the user when they create an account.
        - Sending an email to a user when they create an account is a common use for this type of system.
    - Create a dead-letter queue in case the code responding to an event fails several times in a row.
    - Add an endpoint to review items in the dead-letter queue. Make sure to only allow administrators to view this. Recommend going back and doing the extra credit in section 11 above if you have not done so yet.
17. Add a live chat system for your app.
    - gRPC supports streaming responses.
    - GraphQL supports subscriptions.
    - Standard web technologies includes [Server-sent events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events)
        - For API consumers that do not support server-sent events, an endpoint that they can poll periodically will work.
    - The eventing system may be useful if you have multiple copies of your server running at once. Send an event when a chat message comes in, and each copy of your server that has a user listening for events can have a subscription to the event stream.

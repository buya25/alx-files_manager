# Tasks

## Redis utils

### `utils/redis.js`

This module provides utilities for interacting with Redis.

#### `RedisClient`

A class representing a Redis client.

##### Constructor

Creates a client to Redis.

- Any errors from the Redis client are displayed in the console.

##### Methods

- `isAlive()`: Returns `true` when the connection to Redis is successful; otherwise, `false`.
- `async get(key)`: Takes a string `key` as an argument and returns the Redis value stored for this key.
- `async set(key, value, duration)`: Takes a string `key`, a `value`, and a `duration` in seconds as arguments to store it in Redis with an expiration set by the `duration` argument.
- `async del(key)`: Takes a string `key` as an argument and removes the value in Redis for this key.

#### Usage Example

```javascript
import redisClient from './utils/redis';

(async () => {
    console.log(redisClient.isAlive());
    console.log(await redisClient.get('myKey'));
    await redisClient.set('myKey', 12, 5);
    console.log(await redisClient.get('myKey'));

    setTimeout(async () => {
        console.log(await redisClient.get('myKey'));
    }, 1000*10)
})();
```

## MongoDB utils

### `utils/db.js`

This module provides utilities for interacting with MongoDB.

#### `DBClient`

A class representing a MongoDB client.

##### Constructor

Creates a client to MongoDB.

- `host`: From the environment variable `DB_HOST` or default: `localhost`.
- `port`: From the environment variable `DB_PORT` or default: `27017`.
- `database`: From the environment variable `DB_DATABASE` or default: `files_manager`.

##### Methods

- `isAlive()`: Returns `true` when the connection to MongoDB is successful; otherwise, `false`.
- `async nbUsers()`: Returns the number of documents in the collection `users`.
- `async nbFiles()`: Returns the number of documents in the collection `files`.

#### Usage Example

```javascript
import dbClient from './utils/db';

const waitConnection = () => {
    return new Promise((resolve, reject) => {
        let i = 0;
        const repeatFct = async () => {
            await setTimeout(() => {
                i += 1;
                if (i >= 10) {
                    reject();
                } else if (!dbClient.isAlive()) {
                    repeatFct();
                } else {
                    resolve();
                }
            }, 1000);
        };
        repeatFct();
    });
};

(async () => {
    console.log(dbClient.isAlive());
    await waitConnection();
    console.log(dbClient.isAlive());
    console.log(await dbClient.nbUsers());
    console.log(await dbClient.nbFiles());
})();
```

## First API

### `server.js`

This module creates an Express server.

- Listens on the port set by the environment variable `PORT` or by default 5000.
- Loads all routes from the file `routes/index.js`.

### `routes/index.js`

This module contains all endpoints of the API.

- `GET /status` => `AppController.getStatus`
- `GET /stats` => `AppController.getStats`

### `controllers/AppController.js`

This module contains the definition of the 2 endpoints.

- `GET /status`: Returns if Redis is alive and if the DB is alive too.
- `GET /stats`: Returns the number of users and files in DB.

## Create a new user

### `routes/index.js`

This module adds a new endpoint:

- `POST /users` => `UsersController.postNew`

### `controllers/UsersController.js`

This module contains the new endpoint:

- `POST /users`: Creates a new user in DB.

## Authenticate a user

### `routes/index.js`

This module adds 3 new endpoints:

- `GET /connect` => `AuthController.getConnect`
- `GET /disconnect` => `AuthController.getDisconnect`
- `GET /users/me` => `UserController.getMe`

### `controllers/AuthController.js`

This module contains new endpoints:

- `GET /connect`: Signs-in the user by generating a new authentication token.
- `GET /disconnect`: Signs-out the user based on the token.

### `controllers/UsersController.js`

This module adds a new endpoint:

- `GET /users/me`: Retrieves the user based on the token used.

## First file

### `routes/index.js`

This module adds a new endpoint:

- `POST /files` => `FilesController.postUpload`

### `controllers/FilesController.js`

This module contains the new endpoint:

- `POST /files`: Creates a new file in DB and in disk.

## Get and list file

### `routes/index.js`

This module adds 2 new endpoints:

- `GET /files/:id` => `FilesController.getShow`
- `GET /files` => `FilesController.getIndex`

### `controllers/FilesController.js`

This module contains the 2 new endpoints:

- `GET /

files/:id`: Retrieves a file from DB based on the provided `id`.
- `GET /files`: Lists all files in DB.

## Modify a file

### `routes/index.js`

This module adds a new endpoint:

- `PUT /files/:id` => `FilesController.putPublish`

### `controllers/FilesController.js`

This module contains the new endpoint:

- `PUT /files/:id`: Modifies a file in DB based on the provided `id`.

## User usage

### `routes/index.js`

This module adds a new endpoint:

- `GET /users/:id` => `UsersController.getShow`

### `controllers/UsersController.js`

This module contains the new endpoint:

- `GET /users/:id`: Retrieves a user from DB based on the provided `id`.

## File usage

### `routes/index.js`

This module adds a new endpoint:

- `GET /files/:id/download` => `FilesController.getDownload`

### `controllers/FilesController.js`

This module contains the new endpoint:

- `GET /files/:id/download`: Retrieves a file from disk based on the provided `id` and serves it for download.

## Nginx setup

### `nginx.conf`

Configure the reverse proxy to forward requests to the Express server.

## Deploys

### Dockerfile

Builds the Docker container.

### Build and run

Builds and runs the Docker container.

## API documentation

### `openapi.yaml`

A YAML file describing the API endpoints and their parameters.

## Testing

### `tests/test_files.js`

Unit tests for the files endpoints.

### `tests/test_users.js`

Unit tests for the users endpoints.

### `tests/test_app.js`

Unit tests for the app endpoints.

### `tests/test_auth.js`

Unit tests for the authentication endpoints.

## Load balancing

### `docker-compose.yml`

A Docker Compose file to run multiple instances of the API behind a load balancer.

### `nginx.conf`

Nginx configuration for load balancing.

### `docker-compose.prod.yml`

A Docker Compose file to run multiple instances of the API in a production environment.

## Monitoring

### `monitoring.txt`

Instructions for setting up monitoring.

## Task: Redis utils

In `utils/redis.js`, I've created a class `RedisClient` that provides utilities for interacting with Redis. It includes methods for checking the connection status, getting, setting, and deleting keys in Redis.

# Authentication
### this repo is not completely made by us, this is based on this tutorial:
https://www.smarthomebeginner.com/authelia-docker-compose-guide-2024-v2/


### technologies used:
> * Authelia
> * Docker compose
> * Sqlite
> * Redis
> * Traefik

first of all the authelia folder need special permisions 
```
 sudo chown -R user:user /docker/appdata/authelia
```
change the `uùser` to yours
```
sudo find /docker/appdata/authelia/ -type d -exec chmod 750 {} \;
sudo find /docker/appdata/authelia/ -type f -exec chmod 640 {} \;
```
throgout this tutorial you'll have to change the places where a user is set and use yours

## authelia config
here are the configs you have to seek for

**Log**

Default is set to info. But when creating bypass rules or troubleshooting, you may change the level to debug or trace.
```yaml	
# https://www.authelia.com/configuration/miscellaneous/logging/
log:
  level: info
  format: text
  file_path: /config/authelia.log
  keep_stdout: true
```
**authentication_backend**

We are going to use file-based authentication (users.yml) with one of the strongest hashing algorithms for passwords (argon2id). We will create the users file in the later steps.
```yaml
authentication_backend:
  password_reset:
    disable: false
  refresh_interval: 5m
  file:
    path: /config/users.yml
    password:
      algorithm: argon2id
      iterations: 1
      salt_length: 16
      parallelism: 8
      memory: 256 # blocks this much of the RAM
```
**storage**

Authelia has built-in session storage using SQLite. This is sufficient for a single-user environment.
```yaml
storage:
  # For local storage, uncomment lines below and comment out mysql. https://docs.authelia.com/configuration/storage/sqlite.html
  # This is good for the beginning. If you have a busy site then switch to other databases.
  local:
   path: /config/db.sqlite3
```
## secrets
we are using [docker secrets](https://docs.docker.com/engine/swarm/secrets/) to manage sensitive data
Next, use the following commands to automatically create a random string for each secret and save them in the secrets folde

```bash
tr -cd '[:alnum:]' </dev/urandom | fold -w "64" | head -n 1 | tr -d '\n' | sudo tee docker/secrets/authelia_jwt_secret
```
```bash
tr -cd '[:alnum:]' </dev/urandom | fold -w "64" | head -n 1 | tr -d '\n' | sudo tee docker/secrets/authelia_storage_encryption_key
```
```bash
tr -cd '[:alnum:]' </dev/urandom | fold -w "64" | head -n 1 | tr -d '\n' | sudo tee docker/secrets/authelia_session_secret
```
ensure permisions
```bash
sudo chown root:root docker/secrets/authelia*
sudo chmod 600 docker/secrets/authelia*
```
now that you have your key they have to be added to the compose:
```yml
secrets:
...
  authelia_jwt_secret:
    file: $DOCKERDIR/secrets/authelia_jwt_secret
  authelia_session_secret:
    file: $DOCKERDIR/secrets/authelia_session_secret
  authelia_storage_encryption_key:
    file: $DOCKERDIR/secrets/authelia_storage_encryption_key
```
## authelia users
A `users.yml` file must be created to the build in local storage from Authelia
In that file you have to replace the env variables `AUTHELIA_USERNAME`, `AUTHELIA_USER_DISPLAY_NAME`, also the pasword must be hashed thus a hash algorithm must do it usign the argon2id permites by Authelia:
```bash
sudo docker run -v docker/appdata/authelia/configuration.yml:/configuration.yml -it authelia/authelia:4.38.8 authelia crypto hash generate --config /configuration.yml --password MYSTRONGPASSWORD
```
## Traefik
[Traefik](https://doc.traefik.io/traefik/) is an open-source Application Proxy that makes publishing your services a fun and easy experience. It receives requests on behalf of your system and identifies which components are responsible for handling them, and routes them to get started securely. 

You will have to set a **middleware** in order to do so folow the oficcial docs https://doc.traefik.io/traefik/getting-started/install-traefik/, after done that you can continue, at this point you must create a file called middlewares-authelia.yml in your Traefik rules folder and change the `DOMAINENAME_HS` in the content which is:
```yaml

http:
  middlewares:
    middlewares-authelia:
      forwardAuth:
        address: "http://authelia:9091/api/verify?rd=https://authelia.{{env "DOMAINNAME_HS"}}"
        trustForwardHeader: true
        authResponseHeaders:
          - "Remote-User"
          - "Remote-Groups"
```
Also already made that create the middleware for authelia, which in our root shows as: `chain-authelia.yml` 

## Docker-compose

- We are going to fix the Authelia docker image as 4.38.8 because, sometimes, latest tag brings in breaking changes, which can crash your setup.
- Docker profiles is commented out as explained previously (see my Docker guide for how I use profiles).
- networks: We added Authelia to t3_proxy and default networks. You could probably leave out the default network. I included default because my MariaDB container was on default network.
- ports: Exposing ports is typically not needed for Authelia.
- The environmental variable $DOCKERDIR is already defined in our .env file. All - - Authelia data is being stored in an authelia-specific folder within appdata.
- In the environment and secret sections, we are specifying the 3 Authelia secrets we created previously (steps 3 and 4 in adding secrets).
- With the labels, we are specifying that Authelia will use the websecure entrypoint and chain-no-auth file provider we created previously.
- Authelia listens on port 9091. So, we point authelia-rtr.service to a service name authelia-svc and in the next line, we define where that service is listening at (authelia-svc.loadbalancer.server.port=9091.
 
At this point you should be ableto run your compose 
```bash
docker-compose up -d
```
the three services must be up 
```bash
docker-compose ps
```
This page contains all the information you need to bootstrap a production environment using Docker.

*Note*: the setup assumes that you are using the local file system to store data such as the card images and the database files. You can as well decide to use Docker volumes instead. See the official [Docker volume documentation](https://docs.docker.com/storage/volumes/).

## Discovering the infrastructure

The infrastructure consists of :
- a netrunner `server` that runs the game
- a public `endpoint` that serves the card images and reverse proxy the `server`
- a MongoDB `database` that stores the data of the `server`

## Generating a production docker compose file

The project provides a Docker compose file that can be used to run the `server`, the `database` and the public `endpoint`.
The `lein generate-docker` command helps with creating and maintaining the docker compose file based on the template available at `docker/prod/docker-compose.yml.tpl` or any template specified with the `-t` option.

```
Usage: lein generate-docker [options]

Options:
  -t, --template PATH                      docker/prod/docker-compose.yml.tpl  Path to docker-compose template
  -o, --output PATH                        docker-compose.prod.yml             Path to generated docker-compose file
  -i, --image IMAGE-NAME                                                       Image name is required
  -p, --port PORT                          1042                                Port exposing Netrunner
  -r, --folder-resources FOLDER-RESOURCES  ./resources/public                  Path to the public resources
  -f, --config CONFIG-FILE                 ./docker/prod/prod.edn              Path to the configuration file
  -m, --image-mongodb IMAGE-NAME-MONGODB   mongo                               Image name of MongoDB
  -d, --folder-mongodb FOLDER-MONGODB      ./data                              Folder of the MongoDB database
  -c, --close-mongodb                                                          Disable MongoDB connectivity outside of Docker internal network
```

To generate the production docker compose file with the default settings and the image name `your-name/your-image`, proceed as follows

```
lein generate-docker --name your-name/image-name
```

## Building a production Docker imageb

To build the production Docker image, proceed as follows

```
docker compose -f docker-compose.prod.yml build
 ```

Building the image takes quite some time as it compiles everything from scratch and generate the uber jar.

## Setting authentication secret

To update the authentication secret in the default configuration file, proceed as follows:
- with your favorite editor, edit `docker/prod/prod.edn`
- set the value of the `:web/auth` `:secret` to a long random string

## Running a production infrastructure

To run the production infrastructure proceed as follows

```
docker compose -f docker-compose.prod.yml up
```

## Populating the database

To populate the `database`, run the following commands :

```
$ lein fetch 
1648 cards imported

$ lein create-indexes
Indexes successfully created.
```

## Restarting server

Before you can access the `server`, you need to restart it to avoid an exception. To restart the `server`, proceed as follows :

```
docker compose -f docker-compose.prod.yml restart server
```

## Access the production instance

You can access Netrunner at [http://localhost:8042](http://localhost:8042).

## Reducing service exposure

Once the `database` has been populated, it is possible to disable the ports so that the `database` is not accessible outside of Docker internal network. To disable the ports, regenerate the production docker-compose file with the image name `your-name/your-iamge` as follows

```
lein generate-docker --image your-name/your-image -c
```

## Backing up and restoring

It is a good practice to back up the `database` and image files.

Based on the official MongoDB documentation, you can simply turn off the database and copy the data folder specified in the Docker compose volume section. Other back up strategies can be implemented, simply [read the official MongoDB backup documentation](https ://www.mongodb.com/docs/manual/core/backups/).

Similarly, you can save the image folder specified in the Docker compose file by copying it.

Restoring both `database` and images consists of copying back the backup to the locations specified in the Docker compose file.

## Known issues

### First access raises exception

Upon first access to the website you may face an exception `Tried to select server-card for The Shadow: Pulling the Strings`. Restarting the server as follows is enough to make this go away.

```
docker compose -f docker-compose.prod.yml restart server
```

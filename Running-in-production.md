This page contains all the information you need to bootstrap a production environment using docker.

*Note*: the setup assumed that you are using the local filesystem to store data such as the images and the database. You can as well decide to use docker volumes instead.

## Building a production docker image

To build the production docker image, proceed as follows

```
docker build -t <your-org>/netrunner . -f docker/prod/Dockerfile
 ```

Building the image takes quite some time as it compiles everything from scratch and generate the uber jar.

## Setting authentication secret

To update the authentication secret:
- edit docker/prod/prod.edn
- set the value of the :web/auth :secret to a random long string

## Running a production infrastructure

The project provides a docker compose file that can be used to run the server, the database and the public endpoint.

It is required to update this file to reference your own image. To update proceed as follows:
- with your favorite editor, open docker-compose.prod.yml
- locate the definition of the service called `server`
- update the image name according the name chosen in the build step above

You can then run the server by issuing the following command:
```
docker compose -f docker-compose.prod.yml up
```

## Populating the database

To populate the database, run the following commands:

```
$ lein fetch 
1648 cards imported

$ lein create-indexes
Indexes successfully created.
```

## Restarting server

Before you can access the server, you need to restart it to avoid an exception

```
docker compose -f docker-compose.prod.yml restart server
```

## Access the production instance

Netrunner is available at http://localhost:8042

## Reducing service exposure

Once the database has been populated, it is possible to remove the ports exposed by the corresponding `database` as follows:
- with your favorite editor, open docker-compose.prod.yml
- locate the definition of the service called `database`
- comment out the port definition

## Backuping up and restoring

The installation may require to back up the database and image files.

Based on the official Mongodb documentation, you can simply turn off the database and copy the data folder specified in the docker compose volume section. Other backup strategies can be implemented, simply [read the documentation](https://www.mongodb.com/docs/manual/core/backups/#back-up-by-copying-underlying-data-files).

Similarly, you can save the image folder specified in the docker compose file by copying it.

Restoring both database and images consists of copying back the backup to the locations specified in the docker compose file.

## Known issues

### First access raises exception

Upon first access to the website you may face an exception "Tried to select server-card for The Shadow: Pulling the Strings". Restarting the server as follows is enough to make this go away.

```
docker compose -f docker-compose.prod.yml restart server
```

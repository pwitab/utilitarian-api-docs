# Upgrade instructions

How to upgrade Utilitarian to a newer version.

!!! warning
       These instructions assume that you are able to take Utilitarian down for 
       maintenance and allow time for the migrations to run.
       If you need you Utilitarian instance to be up during the migration please contact
       us and we will create an upgrade strategy together.
       
!!! warning
       When upgrading Utilitarian API you might need to upgrade other Utilitarian 
       services. If you are unsure of which services that need upgrading, please 
       contact us.
       
## 1. Backup

Take a backup of your database and verify the backup. This can be done using `pg_dump`.
[pg_dump documentation](https://www.postgresql.org/docs/10/app-pgdump.html)

If you have the ability to take a snapshot of your database server it is also recommended.

## 2. Close down services

Close down all services that depend on the API. Most edge services will keep handling 
messages and they will be queued up in RabbitMQ for when the services come online again.

Example:

* Utilitarian DLMS Parser
* Utilitarian Poster

## 3. Close down Utilitarian API

Close down Utilitarian API, beat process and all worker processes.

## 4. Pull new versions 

Pull down new image version of Utilitarian from the docker repository

```
docker pull quay.io/pwit/utilitarian:{version}
```

## 5. Upgrade docker config

Upgrade run config, for example `docker-compose` files with the new image.

!!! note
    You also have to remember to update the beat-process and all worker processes since 
    they are based on the utilitarian image.
    
## 6. Start Utilitarian API

Start only the Utilitarian API processes.

## 7. Run migrations

!!! note
    This command can take a long time

Depending on how you are running Utilitarian how you initiate the command to run 
migrations can be a bit different.

The command is:

```bash
python manage.py migrate
```

if you are using docker-compose you should run it like:

```bash
docker-compose exec utilitarian-api sh -c "python manage.py migrate"
```

## 8. Compress static assets

Some upgrades might have changes to the static assets. It is good to run the compression 
again.

The command is:

```bash
python manage.py compress
```

if you are using docker-compose you should run it like:

```bash
docker-compose exec utilitarian-api sh -c "python manage.py compress"
```

## 9. Start up all services

Now start up all remaining services to get back all functionality to Utilitarian.


## 10. Verify upgrade

Verify that everything seems to be correct in the application. If you find an issue 
please contact us.
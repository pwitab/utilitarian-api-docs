# On Premise

*How to install and run Utilitarian*



## Requirements

Utilitarian depends on the following systems:

* Postgres 10
* RabbitMQ


## Docker

All Utilitarian components are distributed via docker images.  


When you have purchased a Utilitarian License you will get a login account to 
be able to fetch the images via `docker-cli`.


As of now all PWIT docker images are stored in Quay and you will need to use the 
credentials supplied by us to log in and pull the images.


```
$ docker login quay.io
Login against server at https://quay.io/v1/
Username: pwitab+yourpullaccount
Password: ThePasswordGivenToYouFromPWIT
Email: any@example.com

```

Once you have logged in you can pull the images.

```
docker pull quay.io/pwit/repo-name:version

```

## Installation

We will provide information on how to install and run the applications via 
`docker-compose`. In the future we will also provide instructions for Kubernetes.
Docker compose might not be the right tool to for you depending on your 
installation and system requirements but it is a good starting point and you 
will get a lot of insight on how to run the systems via this instruction. If 
you need assistance running Utilitarian in another environment please [contact us](index.md#get-in-contact). 


### Postgres

We use Postgres as our database of choice. We recommend that you set up 
Postgres on a dedicated server or use a managed service for it. You can find out 
a lot about running Postgres [online](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-18-04).

Also make sure you have proper [security settings](https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps)
and follow best practices. If you are running Postgres yourself you also want 
to set up a stable [backup function](https://www.digitalocean.com/community/tutorials/how-to-backup-postgresql-databases-on-an-ubuntu-vps).

After you have set up postgres you will supply the connection string in the format:

```
postgres://[user[:password]@][host][:port][,...][/dbname][?param1=value1&...]
``` 

```
# Example
postgres://myuser:mysecretpassword@localhost:5432/db_name
```

Postgres is available as a docker container and if you also want to run Postgres 
via docker make sure you set up a volume for it so that you don't loose any of 
your data. 

```
docker pull postgres:10.7-alpine
```

### RabbitMQ

RabbitMQ is a message broker for the AMQP protocol. Utilitarian uses it for 
inter-application communication and data processing.

Checkout the [official RabbitMQ](https://www.rabbitmq.com/) site to learn how 
to run RabbitMQ or purchase a managed solution from, for 
example [CloudAMQP](https://www.cloudamqp.com/).

You will need to provide a similar connection string for RabbitMQ as for Postgres:

```
amqp://[user[:password]@][host][:port][/vhost]]

```

```
# Example
amqp://guest:guest@localhost:5672//
```

RabbitMQ can also be run as a container. Just be sure to add a volume for it so
you don't loose any data and we recommend that you include the management plugin 
so that you have an interface for management and debugging if something is not 
working correctly.

```
docker pull rabbitmq:3.7.13-management-alpine
```


## Hardware

We don’t have any real numbers to tell you what kind of hardware you’re going 
to need, but we’ll help you make your decision based on existing usage from 
real customers. It all depends on how many meters you are managing in Utilitarian 
and how much data you collect from them. If you have a wide range of different 
meters you must also take into consideration that you will have multiple 
services of different kinds running in your environment.

We provide means to scale horizontally but the database is still the single 
point of failure. Since you are free to set up the database the way your IT 
department or Ops team requires we leave this implementation detail to the 
customer.   


## Monitoring

We do not supply a standard solution to monitor the applications. All logs 
from the different applications are outputed on stdout via docker. So they are
accessible via docker logs. You can then hook up the docker logs to different 
logging drivers, see the [official docker documentation](https://docs.docker.com/config/containers/logging/configure/). 

There are several solutions to application and log monitoring. For example 
Elastic Stack or Splunk. [Contact](index.md#get-in-contact) us if you want help setting this up for your 
installation.

We are exposing a healthcheck endpoint at `/_health/` that will return 200 OK if 
the the API is working correctly. The healthcheck can be disabled using the env 
variable [`HEALTHCHECK_DISABLED`](component_settings.md#healthcheck_disabled) 


## Initial setup Utilitarian API

### Generate application SECRET_KEY

Utilitarian needs a SECRET_KEY for cryptographic algorithms and signing. 
We recommend at least 50 chars. 

```
# Example generating SECRET_KEY with openssl

openssl rand -base64 50

>> CZKcDahINwxy+eYzMItr+NZRqrONvMmJ12g+hF+W/QOMh/A4tRxhQ3onvRjJv47l
```

Set the SECRET_KEY environment variable to this key.

### Migrate the database

The first time you set up Utilitarian API you will need to migrate the database 
so all tables are present. 

Assuming you have started all services via `docker-compose` run the following 
command:

```bash

docker-compose exec utilitarian-api sh -c "python manage.py migrate"

```


### Create Superuser

Assuming you have started all services via `docker-compose` you should run the
following command


```bash

docker-compose exec utilitarian-api sh -c "python manage.py createsuperuser"

```


### Collect static assets

Assuming you have started all services via `docker-compose` you should run the
following command


```bash

docker-compose exec utilitarian-api sh -c "python manage.py collectstatic --no-input"

```

### Compress static assets

Assuming you have started all services via `docker-compose` you should run the
following command


```bash

docker-compose exec utilitarian-api sh -c "python manage.py compress"

```






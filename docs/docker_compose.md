# Setting up with docker-compose


## Install docker-compose

Easiest is to use pip

```
pip install docker-compose
```

## Define docker-compose file

Create a new docker-compose.yaml file

```
touch docker-compose.yaml
```

Define all services with environment variables and volumes if needed.

!!! note
    We are using docker containers for Postgres and RabbitMQ just to show the
    system as a whole. You don't need to run them in containers, as we except 
    just the connection string as input to the other services. 
    
    
```yaml
# Example docker compose containers for Utiltarian
version: '3'
services:
  rabbitmq:
    image: rabbitmq:3-management
    restart: unless-stopped
    ports:
      - "5672:5672"
      - "15672:15672"

  postgres:
    image: postgres:10.7-apline
    restart: unless-stopped
    ports:
      - "15432:5432"
    
    volumes:
      - postgres_data:/var/lib/postgresql/data/

  utilitarian-api:
    image: quay.io/pwit/utilitarian:vx.x.x
    restart: unless-stopped
    ports:
      - "8000:8000"
    depends_on:
      - rabbitmq
      - postres
    environment:
      # Debug
      # SECURITY WARNING: Don't use in production
      - UTILITARIAN_DEBUG=false
      - UTILITARIAN_LOGLEVEL=debug
      - 
      # General settings
      - DATABASE_URL=postgres://postgres/dbname
      - SECRET_KEY=verysecretkey  
      - ALLOWED_HOSTS=utilitarian-api
        
      - AMQP_CONNECTION_STRING=amqp://guest:guest@rabbitmq:5672/

  dlms-processor:
    image: quay.io/pwit/utilitarian-dlms-processor:vX.X.X
    restart: unless-stopped
    depends_on: 
      - rabbitmq
      - utilitarian-api
    environment:
      - DLMS_CONSUMER_DEBUG=false
      - AMQP_CONNECTION_STRING=amqp://guest:guest@rabbitmq:5672/
      - DLMS_CONSUMER_CONSUME_FROM=utilitarian.dlms_push_messages
      - DLMS_CONSUMER_PREFETCH_COUNT=100
      - DLMS_CONSUMER_PUBLISH_TO=utilitarian
      - UTILITARIAN_BASE_URL=http://utilitarian-api:8000
      - UTILITARIAN_AUTH_TOKEN=024281a8c6a12b5fb5a8445439bb9236555975fe
      - UTILITARIAN_REQUEST_TIMEOUT=15

  dlms-udp-server:
    image: quay.io/pwit/utilitarian-dlms-udp-server:vX.X.X
    restart: unless-stopped
    ports:
      # You need to specify that it is an UDP port and not TCP!
      - '4059:4059/udp'
    depends_on: 
      - rabbitmq
    environment:
      - DLMS_UDP_SERVER_DEBUG=false
      - AMQP_CONNECTION_STRING=amqp://guest:guest@rabbitmq:5672/
      - AMQP_EXCHANGE_NAME=utilitarian
      - AMQP_DEFAULT_QUEUE_NAME=utilitarian.dlms_push_messages
      - AMQP_DEFAULT_QUEUE_ROUTING_KEY="new_dlms_push_message.#"
      - UTILITARIAN_APPLICATION_CONTEXT=units11291

  utilitarian-poster:
    image: quay.io/pwit/utilitarian-poster:vX.X.X
    restart: unless-stopped
    depends_on: 
      - rabbitmq
      - utilitarian-api
    environment:
      - POSTER_DEBUG=false
      - AMQP_CONNECTION_STRING=amqp://guest:guest@rabbitmq:5672/
      - POSTER_CONSUME_FROM=utilitarian.new_meter_readings
      - POSTER_PREFETCH_COUNT=100
      - UTILITARIAN_BASE_URL=http://utilitarian-api:8000
      - UTILITARIAN_AUTH_TOKEN=024281a8c6a12b5fb5a8445439bb9236555975fe
      - UTILITARIAN_REQUEST_TIMEOUT=15


volumes:
  - postgres_data:

```

!!! note
    Do not name your service names using underscores. For example the host for 
    making HTTP requests will become something_something and having underscore 
    in HTTP_HOST header is not valid according to RFC 1034/1035. It will result 
    in an error. 
# Components in Utilitarian


## Utilitarian API

The main application for Utilitarian. It stores all data and handles all 
scheduling of reading jobs. 
We provide a [REST API](api_docs/v_1_0_0.html) to manage the data and jobs.

```
docker pull quay.io/pwit/utilitarian:version
```

See [component settings](component_settings.md#utilitarian-api) on how to 
configure Utilitarian API
 

## DLMS UDP Server

A high throughput UDP server to receive DLMS DataNotifications from meters.


```
docker pull quay.io/pwit/utilitarian-dlms-udp-server:version
```


## DLMS Processor

Many DLMS push messages are encrypted and to decrypt UDP messages in the 
receiving server would reduce throughput and we could loose messages if the server 
is not available to process them. Instead the UDP server sends the DLMS messages
to the message broker and they are consumed by the DLMS Processor where they get 
decrypted and parsed into Meter Readings that is published back to the message broker.


```
docker pull quay.io/pwit/utilitarian-dlms-processor:version
```

## Utilitarian Poster

The Utilitarian poster is a queue consumer that will receive all data that are to be 
saved in the Utilitarian API and send it over HTTP to the correct endpoint.


```
docker pull quay.io/pwit/utilitarian-poster:version
```

## Database

Utilitarian uses PostgreSQL as database.


## Message Broker

Utilitarian uses RabbitMQ as message broker.



# Component Settings

*Our docker images are handed settings via Environment Variables*


## Utilitarian API

#### `UTILITARIAN_DEBUG`

:   Enables debugging features.

    Defaults to `False`.
    
    !!! warning
        Do not use in production! 

#### `UTILITARIAN_LOGLEVEL`

:   Sets the loglevel of the application. Valid inputs 
    are: `debug`, `info`, `warning`, `error`, `critical`. 
    
    Defaults to `info` 


#### `DATABASE_URL`

:   Connection string to Postgres database.

#### `SECRET_KEY`

:   Used for internal cryptographic signing and cryptographic methods. Should be 
    generated once at first deploy time. Recommended length is 50 characters.

    !!! warning
        The SECRET_KEY plays an important role in securing the application. Make 
        sure it remains secret.
     
#### `ALLOWED_HOSTS`

:   A list of allowed host for the application. The application will only accept 
    requests with a host in this list. Example: `example.com, 127.0.0.1`.


#### `ACCOUNT_ALLOW_REGISTRATION`
:   If you want to allow user registration in the application. 

    Defaults to `False`.

#### `AMQP_CONNECTION_STRING`

:   AMQP Connection string to RabbitMQ broker. Example: 
    `amqp://guest:guest@rabbitmq:5672//`.

#### `AMQP_PUBLISH_TO`

:   RabbitMQ exchange where messages from Utilitarian API is published.
    
#### `SECURE_SSL_REDIRECT`

:   It is better to use DNS or a reverse proxy for this functionality but if 
    that is not possible setting to `True` will redirect all non HTTPS requests 
    to HTTPS. 
    
    Defaults to `False`.

#### `SECURE_HSTS_SECONDS`
  
:   If set to a non-zero integer value the application will set the 
    HTTP Strict Transport Security header on all responses that do not 
    already have it.
    This will tell browsers and clients that the application is only to be served
    under HTTPS. 
    When enabling you should always set to a low value (60) and increase it 
    after it works.
    
    Defaults to `0`.
    
    !!! warning
        Using this setting wrongly can make your application unaccessable for 
        quite some time. [Please see the full documentation of this feature 
        before doing any changes.](https://docs.djangoproject.com/en/2.1/ref/middleware/#http-strict-transport-security) 
    
    
#### `SECURE_CONTENT_TYPE_NOSNIFF`

:   If True, Utilitarian API sets the X-Content-Type-Options: nosniff header on all 
    responses that do not already have it. 
    
    Defaults to `True`.

#### `SECURE_BROWSER_XSS_FILTER`

:   If True, Utilitarian API sets the X-XSS-Protection: 1; mode=block header 
    on all responses that do not already have it. 
    
    Defaults to `True`.
    
    
#### `SESSION_COOKIE_SECURE`

:   Whether to use a secure cookie for the session cookie. If this is set to 
    True, the cookie will be marked as “secure,” which means browsers may 
    ensure that the cookie is only sent under an HTTPS connection. 
    
    Defaults to `False`.
    
#### `CSRF_COOKIE_SECURE`

:   Whether to use a secure cookie for the CSRF cookie. If this is set to 
    True, the cookie will be marked as “secure,” which means browsers may 
    ensure that the cookie is only sent under an HTTPS connection. 
    
    Defaults to `False`.
    
#### `USE_X_FORWARDED_HOST`

:   A boolean that specifies whether to use the X-Forwarded-Host header in 
    preference to the Host header. This should only be enabled if a proxy which 
    sets this header is in use.
    This setting takes priority over USE_X_FORWARDED_PORT. Per 
    [RFC 7239#page-7](https://tools.ietf.org/html/rfc7239.html#page-7), the 
    X-Forwarded-Host header can include the port number, in which case you 
    shouldn’t use USE_X_FORWARDED_PORT.
    
    Defaults to `False`.
    
    
#### `USE_X_FORWARDED_PORT`

:   A boolean that specifies whether to use the X-Forwarded-Port header in 
    preference to the SERVER_PORT META variable. This should only be enabled 
    if a proxy which sets this header is in use.
    USE_X_FORWARDED_HOST takes priority over this setting.


#### `USE_X_FORWARDED_PROTO`

:   X-Forwarded-Proto header that comes from our proxy, and any time its value 
    is 'https', then the request is guaranteed to be secure (i.e., it 
    originally came in via HTTPS).
    You should only set this setting if you control your proxy or have some 
    other guarantee that it sets/strips this header appropriately
    
    Defaults to `False`
    
    !!! warning
        Modifying this setting can compromise your Utilitarian API’s security. 
        Ensure you fully understand your setup before changing it.

        Make sure ALL of the following are true before setting this (assuming 
        the values from the example above):
        
        * Your Utilitarian API is behind a proxy.
        * Your proxy strips the X-Forwarded-Proto header from all incoming 
          requests. In other words, if end users include that header in their 
          requests, the proxy will discard it.
        * Your proxy sets the X-Forwarded-Proto header and sends it to 
          Utilitarian API, but only for requests that originally come in via 
          HTTPS.
        
        If any of those are not true, you should keep this setting set to False. 
        
        
#### `HEALTHCHECK_DISABLED`

:   Disables the health check endpoint `/_health/`
    
    Defaults to `False`
    
#### `DATA_RETENTION_DAYS`

:   Sets the amount of days you want to keep data in Utilitarian. Only affects 
    time series data as meter readings and meter system events.

    For example:
    365 days in  a year. If you want to keep all data that is younger than 2 
    years and discard everything that is older than 2 years you would set 
    `DATA_RETENTION_DAYS=730`.
    If the environment variable is not set data is kept forever.
    
    Defaults to `None`
    
#### `STAGED_DATA_RETENTION_DAYS`

:   Sets the amount of days you want to keep staged data in Utilitarian. 
    For example staged meter readings. It is assumed that staged data is 
    short lived so you shouldn't have to long data retention for staged data. 
    
    Defaults to `30` days        
    
#### `CORS_WHITELIST`

:   A list of origins that are authorized to make cross-site HTTP requests. 

    An Origin is defined by the CORS RFC Section 3.2 as a URI scheme + hostname + port, 
    or the special value 'null'. Default ports (HTTPS = 443, HTTP = 80) are optional here.
    
    Defaults to [].
    
    Example: `https://example.com,https://sub.example.com,http://localhost:8080,http://127.0.0.1:9000`
    
#### `CORS_ALLOW_ALL`

:   If True, the whitelist will not be used and all origins will be accepted. 

    Defaults to False. Useful for testing but should not be used in production.
    
#### `JWT_SIGNING_KEY`

:  Holds the value of the JWT_SINGING_KEY that is used to sign JSON Web Tokens. 

    Defaults to value of `SECRET_KEY`. It is useful to have a separate key if you want 
    the possibility to invalidate all current tokens. It is recomended to set up a 
    key rotation schedule. 
    
#### `JWT_EXPIRATION_TIME_SECONDS`

:  How long a JWT should be valid in seconds. 

    Defaults to 1200 seconds (20 min)
    
    
#### `ALLOW_TOKEN_AUTH`

:   If True, token authentication will be allowed on the API.  

    Defaults to True. It is recomended if you expose the API to "normal" users via JWT 
    Auth to disallow Token Auth so that no endpoints are not exposed. Instead you run 
    septarate instances for your internal applications that allows TokenAuth.  
    
#### `DEFAULT_METER_TIMEZONE`

:   Sets the default meter timezone that is used for MeterDeviceConfig. 

    Deaults to "UTC". For possible values see https://en.wikipedia.org/wiki/List_of_tz_database_time_zones
    All values might not be available on your system.
   
    
## DLMS UDP Server

#### `DLMS_UDP_SERVER_DEBUG`

:   Will set the loglevel to debug.

    Defaults to `False`


#### `AMQP_CONNECTION_STRING`

:   AMQP Connection string to RabbitMQ broker. Example: 
    `amqp://guest:guest@rabbitmq:5672//`.

#### `AMQP_EXCHANGE_NAME`

:   The exchange where messages will be published.
    
    Defaults to `utilitarian`

#### `AMQP_DEFAULT_QUEUE_NAME`

:   To ensure that the broker always have a queue that will receive our 
    messages we declare in both publishers and subscribers. This is the name of
    the queue.
    
    Defaults to `utilitarian.dlms_push_messages`

#### `AMQP_DEFAULT_QUEUE_ROUTING_KEY`

:   The routing key we want to bind to the default key.
    
    Defaults to new_dlms_push_message.#"`

#### `UTILITARIAN_APPLICATION_CONTEXT`

:   Since there are a few different variations (companion standards) to 
    DLMS/COSEM and some implements some things differntly we add the application 
    context of receiving server to add it to any outgoing messages.
     
    You should run a separate instance of the UDP server for each different 
    application context. It is not possible to see in the messages what 
    companion standard the meter is using so we separate them by setting them up 
    against different server.
    
    Defaults to `units11291`
    
    
## DLMS PROCESSOR

#### `DLMS_CONSUMER_DEBUG`

:   Will set the loglevel to debug.

    Defaults to `False`

#### `AMQP_CONNECTION_STRING`

:   AMQP Connection string to RabbitMQ broker. Example: 
    `amqp://guest:guest@rabbitmq:5672//`.

#### `DLMS_CONSUMER_CONSUME_FROM`

:   The queue from where to consume messages. 

    Defaults to `utilitarian.dlms_push_messages`

#### `DLMS_CONSUMER_PREFETCH_COUNT`

:   The consumer prefetch count, how many unacknowledged messages the consumer 
    is allowed to have in memory.  
    
    Default to `100`

#### `DLMS_CONSUMER_PUBLISH_TO` 

:   Exchange to publish meter readings to.

    Defaults to `utilitarian`

#### `UTILITARIAN_BASE_URL`

:   The base url for Utilitarian API. Example: 
    `https://utilitarian.example.com:8000`


#### `UTILITARIAN_AUTH_TOKEN` 

:   Token for authentication to Utilitarian API. Example: 
    `9c72cba03f4920dcb4b62c4d2723fe5718990024`

#### `UTILITARIAN_REQUEST_TIMEOUT`

:   Request timeout for HTTP calls to Utilitarian API. IF you start seeing a 
    high rate of `TimeoutErrors` your database might not have enough resources or 
    you need to load balance the API. But increasing the timeout could solve 
    you problems short-term. Defined in seconds
    
    Defaults to `15`

## Utilitarian Poster

#### `POSTER_DEBUG`

:   Enables debug. Sets loglevel to `debug`

    Defaults to `False`
      

#### `AMQP_CONNECTION_STRING`

:   AMQP Connection string to RabbitMQ broker. Example: 
    `amqp://guest:guest@rabbitmq:5672//`.

#### `POSTER_CONSUME_FROM`

:   Name of the queues to consume from.
    
    Defaults to `utilitarian.new_meter_readings`

#### `POSTER_PREFETCH_COUNT` 

:   The consumer prefetch count, how many unacknowledged messages the consumer 
    is allowed to have in memory.  
    
    Default to `100`

#### `UTILITARIAN_BASE_URL`

:   The base url for Utilitarian API. Example: 
    `https://utilitarian.example.com:8000`

#### `UTILITARIAN_AUTH_TOKEN`

:   Token for authentication to Utilitarian API. Example: 
    `9c72cba03f4920dcb4b62c4d2723fe5718990024`

#### `UTILITARIAN_REQUEST_TIMEOUT`

:   Request timeout for HTTP calls to Utilitarian API. IF you start seeing a 
    high rate of `TimeoutErrors` your database might not have enough resources or 
    you need to load balance the API. But increasing the timeout could solve 
    you problems short-term. Defined in seconds
    
    Defaults to `15`

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


#### `SECURE_HSTS_INCLUDE_SUBDOMAINS`

:   Enable if SECURE_HSTS_SECONDS should include all subdomains too. 
    It will not take affect unless the SECURE_HST_SECONDS 
    are set to a non zero value.
    
    Defaults to`False`. 
    
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
    
    Defaults to `True`.
    
#### `CSRF_COOKIE_SECURE`

:   Whether to use a secure cookie for the CSRF cookie. If this is set to 
    True, the cookie will be marked as “secure,” which means browsers may 
    ensure that the cookie is only sent under an HTTPS connection. 
    
    Defaults to `True`.
    
#### `USE_X_FORWARDED_HOST`

:   A boolean that specifies whether to use the X-Forwarded-Host header in 
    preference to the Host header. This should only be enabled if a proxy which 
    sets this header is in use.
    This setting takes priority over USE_X_FORWARDED_PORT. Per 
    [RFC 7239#page-7](https://tools.ietf.org/html/rfc7239.html#page-7), the 
    X-Forwarded-Host header can include the port number, in which case you 
    shouldn’t use USE_X_FORWARDED_PORT.
    
    Defaults to `False`.
    
    
#### `USE_X_FORWARDED_HOST`

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
        
#### `UTILITARIAN_TIME_ZONE`

:   Local time zone for this installation. Choices can be found here:
    [http://en.wikipedia.org/wiki/List_of_tz_zones_by_name](http://en.wikipedia.org/wiki/List_of_tz_zones_by_name)
    although not all choices may be available on all operating systems.
    
    Utilitarian still handles all datetimes as time zone aware internally.
    
    Defaults to `'UTC'`
    
## DLMS UDP Server

* LOGLEVEL


## DLMS PROCESSOR

* LOGLEVEL

## Utilitarian Poster


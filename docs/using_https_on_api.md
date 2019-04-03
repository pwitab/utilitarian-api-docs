# Run Utilitarian API under HTTPS


The main application does not provide any means to run under HTTPS.

This is done via a reverse proxy like NginX or HAProxy. Having a reverse 
proxy also simplifies load balancing if you need to scale up the API. You can
run it as a stand alone service or run it as docker container.


We leave it up to the customer to define the way they want to terminate SSL/TLS 
and handle load balancing but will of course provide help if needed. 


## Settings in Utilitarian API if running under HTTPS:

[`SESSION_COOKIE_SECURE`](component_settings.md#session_cookie_secure) should be set to `true`

[`CSRF_COOKIE_SECURE`](component_settings.md#csrf_cookie_secure) should be set to `true`

Depending on your proxy settings you should set the [`USE_X_FORWARDED_HOST`](component_settings.md#use_x_forwarded_host),
[`USE_X_FORWARDED_PORT`](component_settings.md#use_x_forwarded_port) and 
[`USE_X_FORWARDED_PROTO`](component_settings.md#use_x_forwarded_proto) to `true` 

[`SECURE_HSTS_SECONDS`](component_settings.md#secure_hsts_seconds) should be 
set to 60 and when you have made sure it works properly it can be increased to a 
higher value
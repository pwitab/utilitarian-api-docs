# Run Utilitarian API under HTTPS


The main application does not provide any means to run under HTTPS.

This is done via a reverse proxy like NginX or HAProxy. Having a reverse 
proxy also simplifies load balancing if you need to scale up the API. You can
run it as a stand alone service or run it as docker container.


We leave it up to the customer to define the way they want to terminate SSL/TLS 
and handle load balancing but will of course provide help if needed. 


## Settings in Utilitarian API if running under HTTPS:

TODO... 
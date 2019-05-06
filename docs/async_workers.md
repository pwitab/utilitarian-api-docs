# Async Workers

Most of the heavy lifting in Utilitarian is made in asynchronous worker processes 
outside of the request-response cycle of the Utilitarian API. Tasks are transported to 
workers using RabbitMQ.

!!! note
    We are using Celery to manage tasks and workers. You can learn a lot in their 
    documentation on how to run and monitor tasks and workers.
    
## Queues

We separate different types of tasks on different queues. This makes you able to spin 
up more workers dedicated to certain queues depending on your load.

If you start a worker without dedicated queues it will consume from all queues.

## Workers

A worker is a process that consumes tasks and execute them. Since the tasks are 
delivered via RabbitMQ you can split the processes up over several servers if you need 
to.
It is possible to run a worker with increased concurrency. That means that the process 
will spawn several subprocess' to process tasks concurrently. The concurrency settings 
are something that might need tuning since you want to maximize your server utilization 
of CPU and memory and depending on the type of tasks (the queues) the workers consumes 
the load on the server might differ. It all depends on what kind of meters Utilitarian 
is reading and how many meters and at what frequency the readings are made. Since this 
can vary very much from customer to customer we cannot give you exact numbers on how to 
set up you workers but we will help you optimize for you unique circumstances.

### Run a worker

We use the same docker image as the Utilitarian API, with all the same settings. 
If you are using docker-compose you define 

``` yaml
utilitarian-worker:
    image: quay.io/pwit/utilitarian:version
    hostname: utilitarian-worker
    command: celery -A utilitarian worker --loglevel=INFO
    environment: *common_environment  # unpacks settings so you only have to define it once in the docker-compose
    
```

## Beat

The beat process is the scheduler for scheduled or periodic tasks. Utilitarian has a 
number of maintenance tasks that needs to be run at certain intervals. The beat process 
is keeping control of when a task was run last and initiates new tasks when they are 
scheduled. It is also the beat process that initiates scheduled polling of meter data 
from polled meters.

Only run one single beat process. If not you will end up with duplicate tasks. 
Duplicate tasks are usually ok, but will increase load on the system and might lead to 
higher operations costs such as data transfer cost.

### Run the beat process


``` yaml
utilitarian-beat:
    image: quay.io/pwit/utilitarian:version
    hostname: utilitarian-beat
    command: celery -A utilitarian beat --loglevel=INFO
    environment: *common_environment  # unpacks settings so you only have to define it once in the docker-compose
    
```
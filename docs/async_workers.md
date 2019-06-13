# Async Workers

Most of the heavy lifting in Utilitarian is made in asynchronous worker processes 
outside of the request-response cycle of the Utilitarian API. Tasks are transported to 
workers using RabbitMQ.

!!! note
    We are using Celery to manage tasks and workers. You can learn a lot in their 
    documentation on how to run and monitor tasks and workers.
    
## Queues

We separate different types of tasks on different queues. This makes you able to spin 
up more workers dedicated to certain queues depending on your load. It also enables you 
to lower your infrastructure bill since some queues used for AMR Tasks are only used 
daily or monthly depending on your AMR requirements. For example: you could just spin 
up the monthly amr task workers for the first day of the month to handle all the work. 
When all values have been collected you can shut it donw until next month.

If you start a worker without dedicated queues it will consume from all queues.

### List of queues.

* utilitarian.tasks.default
* utilitarian.tasks.amr
* utilitarian.tasks.amr.scheduled.quarterly
* utilitarian.tasks.amr.scheduled.hourly
* utilitarian.tasks.amr.scheduled.daily
* utilitarian.tasks.amr.scheduled.monthly
* utilitarian.tasks.amr.scheduled.yearly
* utilitarian.tasks.amr.on_demand
* utilitarian.tasks.commits
* utilitarian.tasks.commits.meter_readings
* utilitarian.tasks.commits.meter_system_events
* utilitarian.tasks.external
* utilitarian.tasks.external.publish
* utilitarian.tasks.cleanup


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

### Optimizing workers

Depending on how you run Utilitarian, ie. what type of meter you use, how many etc, the 
load can look very different. That is why we split the work on separate queues so that 
it is possible to start more workers that just handle the high load queues 

#### Start worker for specific queues

Your can specify which queues the worker should consumer from at start-up by using the 
`-Q` option and give a comma separated list with queue names.

```
celery -A utilitarian worker -l info -Q utilitarian.tasks.amr.scheduled.quarterly,utilitarian.tasks.amr.on_demand
```

#### Worker concurrency

Workers start up child processes to handle tasks concurrently. This defaults to the 
number of CPUs available on the system. But since most AMR task are I/O bound we could 
start up even more to process more tasks concurrently. 

Exactly how many workers to run and how high the concurrency should be is something 
that is highly dependant on exacly how you use Utilitarian and you will have to 
experiment to find the best utilization of your resources. It is generally seen to be 
better to start more workers than keep on increasing concurrency.


You start a worker with specific concurrency using the `--concurrency` option

```
celery -A utilitarian worker -l info --concurrency=10
```

How high concurrency and the amount of workers is something you will have to weigh 
against yor need and budget (more workers might need more servers). It might be 
fine for you that daily polling tasks take a couple of hours to poll all meter. But if 
not start up more workers and increase concurrency higher concurrency. Just keep track 
of your memory and CPU utilization. 

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
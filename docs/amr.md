# Automatic Meter Readings

## Pushing architecture

In a pushing architecture the meter pushes data to Utilitarian at fixed intervals. This 
is a good general approach that simplifies AMR operations. 

## Polling architecture

In a polling architecture Utilitarian is responsible for contacting the meter and 
reading out the needed values. Many older meters operate using this method. The 
challenging part is to handle the process of reading out meters when the meter 
population is very large.  


## Mixed architecture

Most advanced metering infrastructures (AMI) have need for both pushing and polling 
operations. It is very lightweight on the head end system (Utilitarian) to have the 
meters to have normal operations run in a pushing architecture since we don't have to 
spend resources on polling the meters at fixed interval. 

But there is always need for the ability to poll. For example we might be missing meter 
readings from an interval due to communication outage and need to get the values for 
invoicing. Then we most certainly need to poll the meter for the values. 
We might also want to read values that are not part of the normal push operations at 
certain intervals. For example: statistics about radio reception level. Other times we 
might need to write values to the meter, for example correcting time.


## How Utilitarian handles AMR operations.

Every meter reading that is collected is turned into a `NewMeterReading` message and 
put on the message queue. The Utilitarian API then receives the messages and saves it 
as meter readings in the database.

Pushing meters sends their meters readings to meter specific or protocol specific 
receivers that convert the data from the meter into `NewMeterReading` messages.

Polled meters are controlled via `AmrTask`. `AmrTasks` can be grouped in an `AmrTaskGroup`.

### AmrTasks

An `AmrTask` controls how a polling of a meter is done.

`name`

:   A unique name for the task

`description`

:   Longer description of the task

`schedule`

:   Decides on what schedule the task should be run. Time is in UTC!

    *  `quarterly`, Task is run every 15 min, at 00, 15, 30 and 45.
    * `hourly`, Task is run every hour at 00
    * `daily`, Task is run every day at 00:00
    * `weekly`, Task is run every monday at 00:00
    * `monthly`, Task is run the 1st of every month at 00:00
    * `yearly`, Task is run the 1st of January at 00:00 every year
    
`run_offet_seconds`

:   If you are in another timezone than UTC you might want to run the task at different 
    times than what is specified in the schedule.
    Many meters also have a slight clock synchronisation error so a value for a 00:00 
    might not have been registered in the meter when Utilitarian want to poll it.
    
    Setting the `run_offset_seconds` will enable you to delay the execution of the 
    task for that amount of seconds. You can only set as many seconds as there is until 
    the next run of the task. For example you can only delay a quarterly task maximum 
    of 15 minutes.
    
    !!! note
        It is not guarenteed that your task will be executed at the specific time set. 
        But it will not be executed before that time.
        It all depends on the workload of the workers.
    

`meter_execution_type`

:   Decides in what execution context the task should run. Utilitarian has a number of 
    implemented meters exeution contexts that can be used. Example: `generic_lis200`
    
    
`task_call`

:   In the specified meter execution context there will be a number of available calls 
    to make to the meter. Example: `set_time`, `read_archive_by_offset_seconds`
    
    
`task_kwargs`

:   For every `task_call` there might be some additional info needed to run the task. 
    These are supplied as key value arguments in the `task_kwargs` field. 
    Example for task_call=`read_archive_by_offset_seconds` -> `{"offset_seconds": 1000,
     "archive_number": 3, "control_position": 3}`
    
### AmrTaskGroups

Several AmrTasks can be grouped together in an AmrTaskGroup. This is useful for 
managing all the different tasks for a specific meter type.
    
### Connecting to Meter Devices

Meter Devices can have several AmrTaskGroup connected to it via the `task_groups` 
field. This should be the normal operation of managing tasks for a meter. 

But sometimes you might want to do some extra tasks on specific meters and then it is 
possible to add tasks via the `meter_tasks` field. Tasks defined there will only run 
for that meter.
    
### Direct invocation

It is possible to invoke a task directly via the API. No AmrTask is saved in the 
database but the same arguments are used. There tasks will be sent to the amr.on_demand 
queue and it is recomended to run a separate worker for this queue if you want the 
results fast.

Example:
```
{
    "meter_device": "890b61bb-6594-48db-ad90-584d56754455",
    "task_call": "read_archive_by_offset_seconds",
    "task_kwargs": {
        "offset_seconds": 1000,
        "archive_number": 3,
        "control_position": 3
    },
    "run_offset_seconds": 0,
    "meter_execution_type": "generic_lis200"
}
```


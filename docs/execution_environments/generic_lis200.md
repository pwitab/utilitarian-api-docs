# Generic LIS200 
Execution environment name: *generic_lis200*

## Function Calls

* Read Single Value: read_single_value
* Read Archive By Date: read_archive_by_date
* Read Archive By Offset Seconds: read_archive_by_offset_seconds
* Set Time: set_time

### Read Single Value

*read_single_value*

Argument | Type | Required | Description
--- | --- | --- | ---
address | string | yes | The LIS-200 address to read

```json
# example issuing an on demand amr task via the API
{
    "meter_device": "890b61bb-6594-48db-ad90-584d56754455",
    "task_call": "read_single_value",
    "task_kwargs": {
        "address": '2.301',
    },
    "run_offset_seconds": 0,
    "meter_execution_type": "generic_lis200"
}
```


### Read archive by date

*read_archive_by_date*

In LIS200 saved data are stored archives and you need to specify which one you need 
to read. The archives can have different structures and the important thing to know is 
the control position of the archive, ie. which data entry in an archive row represents 
the timestamp that you want to use as reference for the read.
Archives are read from the oldest data to new newest data, so start_data represents 
the oldest data in the database

Reading by date is useful to re-read old data. Maybe a task failed to collect them or 
there was a network error or device error. 

Argument | Type | Required | Description
--- | --- | --- | ---
start_dat | datetime | yes | The date for the oldest data to start reading from.
end_date | datetime | no | The date to stop reading. If not proved all data from the start date will be read up until the latest entry
archive_number | int | yes | The number of the archive you wish to read
control_position | int | yes | The position of the timestamp in the archive

```json
# example issuing an on demand amr task via the API
{
    "meter_device": "890b61bb-6594-48db-ad90-584d56754455",
    "task_call": "read_archive_by_date",
    "task_kwargs": {
        "start_date": "2020-01-15T06:00:00+0100",
        "end_date": "2020-01-16T06:00:00+0100",
        "archive_number": 3,
        "control_position": 3
    },
    "run_offset_seconds": 0,
    "meter_execution_type": "generic_lis200"
}
```

### Read archive by offset

*read_archive_by_offset_seconds*

Similar to reading by date but instead you use the current time and read backwards 
by the offset seconds. For example `offset_seconds=900` would read the last 15 min in 
an archive. Best used with a scheduled AmrTask.

Argument | Type | Required | Description
--- | --- | --- | ---
offset_seconds | int | yes | Number of seconds to read by.
archive_number | int | yes | The number of the archive you wish to read
control_position | int | yes | The position of the timestamp in the archive

!!! note
    When using with a scheduled AmrTask that also is put in an offset schedule you 
    might need to increase the offset to get all requested values. 


```json
# example issuing an on demand amr task via the API
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

### Set time

*set_time*

Will sync the time in the meter with the server. No parameters needed.

Argument | Type | Required | Description
--- | --- | --- | ---
 |  | | 

```json
# example issuing an on demand amr task via the API
{
    "meter_device": "890b61bb-6594-48db-ad90-584d56754455",
    "task_call": "set_time",
    "task_kwargs": {},
    "run_offset_seconds": 0,
    "meter_execution_type": "generic_lis200"
}
```
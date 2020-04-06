# Generic Corus 
*generic_corus*

Execution environment name: *generic_corus*

## Function Calls

* Read Parameters: read_parameters
* Read Database: read_database_by_date
* Read Database By Offset Seconds: read_database_by_offset_seconds
* Set Time: set_time

### Read Parameters

*read_parameters*

It is possible to read several parameters in the same request.

Argument | Type | Required | Description
--- | --- | --- | ---
parameters | list[string] | yes | List of parameters to read. See parameters list.

```json
# example issuing an on demand amr task via the API
{
    "meter_device": "890b61bb-6594-48db-ad90-584d56754455",
    "task_call": "read_parameters",
    "task_kwargs": {
        "parameters": ["index_converted", "index_unconverted"],
    },
    "run_offset_seconds": 0,
    "meter_execution_type": "generic_corus"
}
```


### Read database

*read_database*

Corus databases are read from the latest value to the oldest. So start_date is the 
newest value and stop_date is the oldest value. If no dates are provided all data in 
database is read. 

Reading by date is useful to re-read old data. Maybe a task failed to collect them or 
there was a network error or device error. 

Argument | Type | Required | Description
--- | --- | --- | ---
start_dat | datetime | no | The date for the oldest data to start reading from.
end_date | datetime | no | The date to stop reading. If not proved all data from the start date will be read up until the latest entry
database | str | yes | Name of database. Possible values: `interval`, `hourly`, `daily`, `monthly`.
control_position | int | yes | The position of the timestamp in the archive

```json
# example issuing an on demand amr task via the API
{
    "meter_device": "890b61bb-6594-48db-ad90-584d56754455",
    "task_call": "read_database",
    "task_kwargs": {
        "end_date": "2020-01-15T06:00:00+0100",
        "start_date": "2020-01-16T06:00:00+0100",
        "database": 'interval',
    },
    "run_offset_seconds": 0,
    "meter_execution_type": "generic_corus"
}
```

### Read database by offser

*read_database_by_offset_seconds*

Similar to reading by date but instead you use the current time and read backwards 
by the offset seconds. For example `offset_seconds=900` would read the last 15 min in 
an archive. Best used with a scheduled AmrTask.

Argument | Type | Required | Description
--- | --- | --- | ---
offset_seconds | int | yes | Number of seconds to read by.
database | string | yes | Name of database. Possible values: `interval`, `hourly`, `daily`, `monthly`.

!!! note
    When using with a scheduled AmrTask that also is put in an offset schedule you 
    might need to increase the offset to get all requested values. 


```json
# example issuing an on demand amr task via the API
{
    "meter_device": "890b61bb-6594-48db-ad90-584d56754455",
    "task_call": "read_database_by_offset_seconds",
    "task_kwargs": {
        "offset_seconds": 1000,
        "database": 'hourly',
    },
    "run_offset_seconds": 0,
    "meter_execution_type": "generic_corus"
}
```

### Set time

*set_time*

Will sync the time in the meter with the server. No parameters needed.

Argument | Type | Required | Description
--- | --- | --- | ---
 allowed_drift_seconds| int | yes | The time in seconds the meters time is allowed to drift from the server until the time is corrected. 

```json
# example issuing an on demand amr task via the API
{
    "meter_device": "890b61bb-6594-48db-ad90-584d56754455",
    "task_call": "set_time",
    "task_kwargs": {
      "allowed_drift_seconds": 60
    },
    "run_offset_seconds": 0,
    "meter_execution_type": "generic_lis200"
}
``` 

## Parameters

Parameter | Description
--- | ---
index_unconverted | Current unconverted index of device
index_converted | Current converted index of device
datetime | Current time in device
battery_days | Battery life in days
temperature_base | Base Temperature
temperature_low | Low threshold for temperature
temperature_high | High threshold for temperature
pressure_base | Base pressure
pressure_low | Low threshold for pressure
pressure_high | High threshold for pressure
compressibility_formula | Code for the formula used in the device.
firmware_version | Firmware version of the device
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

### Read database by offset

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

## Parameters for instantaneous readout

Parameter | Description
--- | ---
index_unconverted | Current unconverted index of device
index_converted | Current converted index of device
datetime | Current time in device
battery_days | Battery life in days
input_pulse_weight | The weight of input pulses
parameter_mapping_version | The parameter mapping of the device
temperature_base | Base Temperature
temperature_limit_low | Low threshold for temperature
temperature_limit_high | High threshold for temperature
temperature | Current gas temperature
pressure_base | Base pressure
pressure_limit_low | Low threshold for pressure
pressure_limit_high | High threshold for pressure
pressure | Current gas pressure
pressure_2 | Current gas pressure, secondary pressure sensor. *Only on devices with secondary sensor*
flowrate_unconverted | Current unconverted flowrate
flowrate_converted | Current converted flowrate
compressibility_formula | Code for the formula used in the device.
firmware_version | Firmware version of the device
kernel_version | Kernel version of the device. *Only available on MID devices*


## Parameters for database readout

The main difference in AMR perspective between reading databases from a non-MID devices 
and a MID-device is that the metering index is available in all databases on a MID 
devices. It is always better to collect the index and calculate the consumption instead 
of just collecting consumptions as loosing data will make it hard to interpolate. 

For reading non-MID devices on a schedule shorter than monthly we recommend that 
reading the metering is done via the `read_parameters` call on the schedule you like 
and complement with a monthly read of the monthly database to get a values that is 
guaranteed to be on the gas day change. Depending on users queue setup and number of 
meters there will be a delay in reading instantaneous values of the metering index.

On MID-devices just read the databases as they are. If a shorter period than 
hourly is needed use the interval database and set it to the period you like on the 
device and schedule readout either quarterly or hourly. 

### Interval

#### Non MID devices
Parameter | Description
--- | ---
record_duration | Duration of the database record
status | Record status
end_date | End datetime of record
consumption_unconverted_interval | Unconverted consumption during period
consumption_converted_interval | Converted consumption during period
consumption_unconverted_interval_under_alarm | Unconverted consumption under alarm during period
consumption_converted_interval_under_alarm |  Converted consumption under alarm during period
temperature_interval_minimum |  Minimum temperature during period
temperature_interval_maximum | Maximum temperature during period
temperature_interval_average | Average temperature during period
pressure_interval_minimum |  Minimum pressure during period
pressure_interval_maximum | Maximum pressure during period
pressure_interval_average | Average pressure during period
flowrate_unconverted_interval_minimum | Minimum unconverted flowrate during period
flowrate_unconverted_interval_maximum | Maximum unconverted flowrate during period
flowrate_converted_interval_minimum | Minimum converted flowrate during period
flowrate_converted_interval_maximum | Maximum converted flowrate during period
flowrate_unconverted_interval_average | Average unconverted flowrate during period
flowrate_converted_interval_average | Average converted flowrate during period
start_date | Start datetime of record

#### MID Devices

All parameters from non-MID including the following:

Parameter | Description
--- | --- 
index_unconverted | Metering index unconverted gas volume
index_converted | Metering index converted gas volume
counter_unconverted_under_alarm | Counter unconverted under alarm
counter_converted_under_alarm | Counted converted under alarm
pressure_2_interval_minimum | Minimum pressure during period, secondary sensor. *Only available on devices with a secondary pressure sensor*
pressure_2_interval_maximum | Maximum pressure during period, secondary sensor. *Only available on devices with a secondary pressure sensor*
pressure_2_interval_average | Average pressure during period, secondary sensor. *Only available on devices with a secondary pressure sensor*

### Hourly

#### Non MID devices

Parameter | Description
--- | --- 
record_duration | Duration of the database record
status | Record status
end_date | End datetime of record
consumption_unconverted_hourly | Unconverted consumption during period
consumption_converted_hourly | Converted consumption during period
consumption_unconverted_hourly_under_alarm | Unconverted consumption under alarm during period
consumption_converted_hourly_under_alarm |  Converted consumption under alarm during period
temperature_hourly_minimum |  Minimum temperature during period
temperature_hourly_maximum | Maximum temperature during period
temperature_hourly_average | Average temperature during period
pressure_hourly_minimum |  Minimum pressure during period
pressure_hourly_maximum | Maximum pressure during period
pressure_hourly_average | Average pressure during period
flowrate_unconverted_hourly_minimum | Minimum unconverted flowrate during period
flowrate_unconverted_hourly_maximum | Maximum unconverted flowrate during period
flowrate_converted_hourly_minimum | Minimum converted flowrate during period
flowrate_converted_hourly_maximum | Maximum converted flowrate during period
flowrate_unconverted_hourly_average | Average unconverted flowrate during period
flowrate_converted_hourly_average | Average converted flowrate during period
start_date | Start datetime of record

#### MID Devices

All parameters from non-MID including the following:

Parameter | Description
--- | --- 
index_unconverted | Metering index unconverted gas volume
index_converted | Metering index converted gas volume
counter_unconverted_under_alarm | Counter unconverted under alarm
counter_converted_under_alarm | Counted converted under alarm
pressure_2_hourly_minimum | Minimum pressure during period, secondary sensor. *Only available on devices with a secondary pressure sensor*
pressure_2_hourly_maximum | Maximum pressure during period, secondary sensor. *Only available on devices with a secondary pressure sensor*
pressure_2_hourly_average | Average pressure during period, secondary sensor. *Only available on devices with a secondary pressure sensor*


### Daily

#### Non MID devices

Parameter | Description
--- | --- 
record_duration | Duration of the database record
status | Record status
end_date | End datetime of record
consumption_unconverted_daily | Unconverted consumption during period
consumption_converted_daily | Converted consumption during period
consumption_unconverted_daily_under_alarm | Unconverted consumption under alarm during period
consumption_converted_daily_under_alarm | Converted consumption under alarm during period
temperature_daily_minimum | Minimum temperature during period
temperature_daily_maximum | Maximum temperature during period
temperature_daily_average | Average temperature during period
pressure_daily_minimum | Minimum pressure during period
pressure_daily_maximum | Maximum pressure during period
pressure_daily_average | Average pressure during period
flowrate_unconverted_daily_minimum | Minimum unconverted flowrate during period
flowrate_unconverted_daily_maximum | Maximum unconverted flowrate during period
flowrate_converted_daily_minimum | Minimum converted flowrate during period
flowrate_converted_daily_maximum | Maximum converted flowrate during period
flowrate_unconverted_daily_average | Average unconverted flowrate during period
flowrate_converted_daily_average | Average converted flowrate during period
start_date |  Start datetime of record

#### MID Devices

All parameters from non-MID including the following:

Parameter | Description
--- | --- 
index_unconverted | Metering index unconverted gas volume
index_converted | Metering index converted gas volume
counter_unconverted_under_alarm | Counter unconverted under alarm
counter_converted_under_alarm | Counted converted under alarm
pressure_2_daily_minimum | Minimum pressure during period, secondary sensor. *Only available on devices with a secondary pressure sensor*
pressure_2_daily_maximum | Maximum pressure during period, secondary sensor. *Only available on devices with a secondary pressure sensor*
pressure_2_daily_average | Average pressure during period, secondary sensor. *Only available on devices with a secondary pressure sensor*

### Monthly

#### Non MID devices

Parameter | Description
--- | --- 
record_duration | Duration of the database record
status | Record status
end_date | End datetime of record
consumption_unconverted_monthly | Unconverted consumption during period
consumption_converted_monthly | Converted consumption during period
consumption_unconverted_monthly_under_alarm | Unconverted consumption under alarm during period
consumption_converted_monthly_under_alarm | Converted consumption under alarm during period
temperature_monthly_minimum | Minimum temperature during period
temperature_monthly_maximum | Maximum temperature during period
temperature_monthly_average | Average temperature during period
pressure_monthly_minimum | Minimum pressure during period
pressure_monthly_maximum | Maximum pressure during period
pressure_monthly_average | Average pressure during period
flowrate_unconverted_monthly_minimum | Minimum unconverted flowrate during period
flowrate_unconverted_monthly_maximum | Maximum unconverted flowrate during period
flowrate_converted_monthly_minimum | Minimum converted flowrate during period
flowrate_converted_monthly_maximum | Maximum converted flowrate during period
index_unconverted | Metering index unconverted gas volume at end_date of record
index_converted | Metering index converted gas volume at end_date of record
counter_unconverted_under_alarm | Counter unconverted under alarm at end_date of record
counter_converted_under_alarm | Counter converted under alarm at end_date of record
consumption_unconverted_monthly_maximum | Maximum unconverted consumption of interval period during month
consumption_unconverted_monthly_maximum_date |  Date when maximum unconverted consumption was registered.
consumption_converted_monthly_maximum | Maximum converted consumption of interval period during month
consumption_converted_monthly_maximum_date | Date when maximum converted consumption was registered.
flowrate_unconverted_monthly_average | Average unconverted flowrate during period
flowrate_converted_monthly_average | Average converted flowrate during period
start_date | Start datetime of record

#### MID Devices

All parameters from non-MID including the following:

Parameter | Description
--- | --- 
pressure_2_monthly_minimum | Minimum pressure during period, secondary sensor. *Only available on devices with a secondary pressure sensor*
pressure_2_monthly_maximum | Maximum pressure during period, secondary sensor. *Only available on devices with a secondary pressure sensor*
pressure_2_monthly_average | Average pressure during period, secondary sensor. *Only available on devices with a secondary pressure sensor*

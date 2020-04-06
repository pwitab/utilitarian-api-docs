# Time in Utilitarian

One of the harder things to manage in AMR Operations is time. Especially when you are 
developing a solution that should be able to use many different kinds of meters in 
different regions under different regulatory rules. 

An example of this is Sweden. For electricity meters the time of meters should be 
Swedish normal time, or Swedish time without day light savings times change. But for 
gas meter the gas day is counted from 06:00-06:00 in Swedish local time, with day 
light savings.

## Timestamps 

To make it possible to have meters that are in different timezones all timestamps in 
Utilitarian has a timezone attached to it. If the timezone isn't UTC it will be 
transformed to UTC upon saving.

## Timezones.

In MeterDeviceConfig it is possible to set a timezone for a meter. If you want the 
meter to follow normal daylight savings rules for a timezone just use the normal one. 
Ex `Europe/Stockholm`. But if you need a meter in a zone that doesn't change at 
Daylight Savings Time (DST) you should use a constructed zone like `Etc/GMT-2`. 
To learn more about available timezones visit [https://en.wikipedia.org/wiki/List_of_tz_database_time_zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)

!!! warning
       When using constructed zones, ex `Etc/GMT-2` the sign of the hour is inverted from 
       that of the ISO 8601 standard. So `Etc/GMT-2` is 2 hours ahead of GMT.
       [read more here...](https://en.wikipedia.org/wiki/Tz_database#Area) 
   
## Meter time

How the time is handled inside a meter device can be very different depending on 
manufacturer and protocol. Some meters have no knowledge of timezone, some have but 
have turned it off or some has detailed information about timezone and follow DST 
changes. We implement the time management in our execution environments and meter 
integrations according to the way the meter handles it internally.

## Syncing time

When we want to sync time we do it against the servers internal clock and timezone UTC.
When time needs to be corrected in a meter we convert the server time to the local time 
for the meters timezone and write it to the meter. This is run as an AmrTask and can be 
run on a schedule that suits the application.   
 
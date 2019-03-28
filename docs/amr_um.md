# Unified Messaging for Automatic Meter Readings

AMR UM is a project trying to define clearer messages that are focused on
AMR operations. In metering it is common to use CIM (IEC 61968) to model your 
business processes and define messages for different type of operations. 

But it is all on quite high level. It is also a bit ambiguous how to use the 
models. There are several possiblities for the DSO (Distribution System Operators) to
model the same data so focusing to comply with CIM in messages within 
Utilitarian would not be beneficial.

Instead we are focusing on making clearly defined messages and document the usage 
well. We will if needed, provide translation services for CIM or other formats. 
But as of now it is up to the user to hook in to the message stream and get 
the data they need.

We plan on sending all messages serialized using Avro and have a schema server 
for deserialization so that we can handle versioning of schemas. But this is a 
work in progress and for now we send messages in json format.

We have developed a helper library in Python to build objects and make payloads 
for AMR UM messages: [Check it out on GitHub](https://www.github.com/pwitab/amr-um) 

## Message Types


### DLMS Push Message

Contains a push message (DataNotification) from a DLMS Meter

```json

{
    "payload": "base64encodedbytes",
    "transport": "udp",
    "source_address": 'ip_address_of_origin',
    "source_port": 5678,
    "application_context": "dsmr",
    "dlms_wrapper": {
      "source_wport": 1,
      "destiniation_wport": 10,
      "version":1
      }
}

```

### New Meter Reading

Message containing a single meter reading

```json

{
  "meter": "FIOR78464920374",
  "series": "7-0-13-26-0-101",
  "timestamp": "2019-02-24T06:00:00T+01:00",
  "value": "14567.000"

}
```



 
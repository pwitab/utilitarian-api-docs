# Open Source

We want Utilitarian to be a simple companion to our customers in their AMR 
operations. We provide documented interfaces and APIs to our services and 
messaging system. So you have the freedom of making use of the data as 
you see fit.

For example if you need to make a special analytics service for some customers
you can either get the data from the Utilitarian API or subscribe directly to it 
via the message broker.

We provide some helpers to write your own integrations written in Python 
but since we are using AMQP and HTTP you can find implementations of those 
protocols in many programing languages and use the documentation in our helpers 
to speed up development of your own integrations in your language of choice.

We also provide many libraries of the protocols we support as open 
source.

## Utilitarian Helpers

### Utilitarian Queue Consumer

[Check it out on GitHub:  https://github.com/pwitab/utilitarian-queue-consumer](https://github.com/pwitab/utilitarian-queue-consumer)

The Utilitarian Queue Consumer is a micro framework to write consumers for the 
messages in Utilitarian. It gives you good default for consuming messages and 
producing new messages.


### AMR UM

[Check it out on GitHub: https://github.com/pwitab/amr-um](https://github.com/pwitab/amr-um)

AMR UM (Unified Messaging for Automatic Meter Readings) is our attempt to have 
standard and well documented messaging framwork. In the repo you can find 
Python helpers to format the data and documentation on the different schemas used.


## Protocols

### DLMS/COSEM

[Check it out on GitHub: https://github.com/pwitab/dlms-cosem](https://github.com/pwitab/dlms-cosem)


DLMS/COSEM (IEC 62056, EN13757-1) is the global standard for smart energy 
metering, control and management. It specifies an object-oriented data model,
an application layer protocol and media-specific communication profiles.


### IEC 62056-21

[Check it out on GitHub: https://github.com/pwitab/iec62056-21]( https://github.com/pwitab/iec62056-21)


IEC 62056-21 (earlier IEC 61107 or sometimes just IEC 1107, is an international 
standard for a computer protocol to read utility meters. It is designed to operate over 
any media, including the Internet. A meter sends ASCII (in modes A..D) or HDLC (mode E)
data to a nearby hand-held unit (HHU) using a serial port. The physical media are 
usually either modulated light, sent with an LED and received with a photodiode, or a 
pair of wires, usually modulated by a 20mA current loop. The protocol is usually 
half-duplex.

 

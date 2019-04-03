# Protocols

*The different protocols we support*

## DLMS/COSEM

DLMS/COSEM (IEC 62056, EN13757-1) is the global standard for smart energy 
metering, control and management. It specifies an object-oriented data model,
an application layer protocol and media-specific communication profiles.

[PWIT is developing an open source library for DLMS/COSEM](https://github.com/pwitab/dlms-cosem)


## IEC 62056-21

IEC 62056-21 superseded IEC 61107 (sometimes just called IEC 1107).

It is used for direct local data exchange. It has been designed to act as the
protocol used when reading the meter via its optical port. But it is used with 
several medias, including sending the data over the internet.

## M-Bus

M-Bus (Meter-Bus) is a European standard (EN 13757-2 physical and link layer, 
EN 13757-3 application layer) for the remote reading of gas or electricity 
meters. M-Bus is also usable for other types of consumption meters. The M-Bus 
interface is made for communication on two wires, making it cost-effective. 
A radio variant of M-Bus (Wireless M-Bus) is also specified in EN 13757-4.

## Modbus

Modbus is a serial communications protocol originally used with programmable 
logic controllers (PLCs) but has become a de facto standard communication 
protocol and is now a commonly available means of connecting industrial 
electronic devices.

Many meters that are not focused only on residential measurement are equipped with 
a Modbus port so that they can easily be used in industrial applications.
 
## ANSI C12.18

ANSI C12.18 is an ANSI standard that describes a protocol used for two-way 
communications with a meter, mostly used in North American markets. 

## LIS-200

LIS-200 is a subset of IEC62056-21. It as implemented when the IEC62056-21 didn't 
have all the functionality needed. It is basically the same as IEC62056-21 in the 
flow but object identification is done differently.

## I-Flag

I-Flag is a proprietary protocol developed by Itron. It is used in many of Itrons 
products.


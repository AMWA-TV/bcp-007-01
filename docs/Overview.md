# AMWA BCP-NMOS-NDI: NMOS With NDI: Overview
{:.no_toc}

* A markdown unordered list which will be replaced with the ToC, excluding the "Contents header" from above
{:toc}

_(c) AMWA 2023, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_


## Introduction

> Provide an overview of the Specification.

Familiarity with the [JT-NM Reference Architecture](https://jt-nm.org/reference-architecture/) is assumed.

See also the [NMOS Technical Overview](https://specs.amwa.tv/nmos/main/docs/Technical_Overview.html).


## Use of Normative Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY",
and "OPTIONAL" in this document are to be interpreted as described in [RFC-2119](https://datatracker.ietf.org/doc/html/rfc2119).

## Dependencies
This document depends upon the following reference documents:
- [NDI® Advanced SDK Version 5.5](https://ndi.video/sdk/), 2023-02-01, Newtek Inc, referred henceforth as **“NDI SDK Documentation”**.
- [NMOS IS-04](https://specs.amwa.tv/is-04/) V1.3.2
- [NMOS IS-05](https://specs.amwa.tv/is-05/) V1.1.2
- [NMOS BCP-004-01](https://specs.amwa.tv/bcp-004-01/) V1.0.0

## Definitions

The NMOS terms ‘Controller’, ‘Node’, ‘Source’, ‘Flow’, ‘Sender’, ‘Receiver’ are used as defined in the [NMOS Glossary](https://specs.amwa.tv/nmos/main/docs/Glossary.html).

This specification also defines the following terms.

### Native NDI Device
A “device” as defined in the NDI SDK Documentation. This shall not be inferred to be a “device” as defined in the NMOS Glossary. Note that the same physical or logical apparatus may simultaneously act as an NMOS device and a Native NDI Device.

### Native NDI Sender
A sender of NDI an NDI stream as defined in the NDI SDK Documentation. This shall not be inferred to be a “sender” as defined in the NMOS Glossary. Note that the same physical or logical apparatus may simultaneously instantiate an NMOS sender and Native NDI Sender.

### Native NDI Receiver
A receiver of NDI an NDI stream as defined in the NDI SDK Documentation. This shall not be inferred to be a “receiver” as defined in the NMOS Glossary. Note that the same physical or logical apparatus may simultaneously instantiate an NMOS receiver and Native NDI Receiver.

## Model
> Describe the model here, including use cases of NMOS NDI->NMOS NDI, NATIVE NDI->NMOS NDI. Include discovery model for NMOS and Native Senders and Receivers.


## NDI IS-04 Sources, Flows and Senders
### Sender Capabilities
Mention Codecs and protocols here.
```
 [{
        "protocols": "auto”,
        “audio_codec": “native",  
        “video_codec": “native”
}]
```

**protocols**  Indicate the NDI sub-protocol(s) to use/allow
- auto (default)
- unicast
- multicast
- tcp
- rudp

**audio_codec**  Indicate the advanced audio codec to use. The values shall be compatible with the constraints. If not specified it defaults to “native”. (schema MUST be flexible to allow new values)
Possible values are:
- “native” when and advanced codec is not used
- “aac” to use the AAC codec. (advanced SDK required)

**video_codec**  Indicate the advanced video codec to use. The values shall be compatible with the constraints. If not specified it defaults to “native”. (schema MUST be flexible to allow new values) .Possible values are:
- “native” when an advanced codec is not used
- “h264” (advanced SDK required)
- “h265” (advanced SDK required)



### Metadata
Metadata flow may be implicitly connected when video connection is made.
Metadata flow may be bidirectional, i.e. one flow in each direction (e.g. PTZ camera control). These flows are not explicitly connected via controller.

### Mux Flows


## NDI IS-04 Receivers

#### Receiver Capabilities
```
 [{
        "protocols": "auto”,
        “audio_codec": “native",  
        “video_codec": “native”
}]
```

**protocols**  Indicate the NDI sub-protocol(s) to use/allow
- auto (default)
- unicast
- multicast
- tcp
- rudp

**audio_codec** Indicate the advanced audio codec to use. The values shall be compatible with the constraints. If not specified it defaults to “native”. (schema MUST be flexible to allow new values)
Possible values are:
- “native” when and advanced codec is not used
- “aac” to use the AAC codec. (advanced SDK required)

**video_codec**  Indicate the advanced video codec to use. The values shall be compatible with the constraints. If not specified it defaults to “native”. (schema MUST be flexible to allow new values) .Possible values are:
- “native” when an advanced codec is not used
- “h264” (advanced SDK required)
- “h265” (advanced SDK required)






## NDI IS-05 Senders and Receivers
### Transport Type
NDI Flows shall utilize a new transport type in IS-05:

```
urn:x-nmos:transport:ndi
```
This will encapsulate all flavors of NDI (SHQ / HX / HX2 / HX3 …). Details to be specified in transport_params

### Sender Transport Parameters
```
"transport_params": [{
        "server_ip": "10.10.10.10",
        "server_port": 5960,
        “source_name": "ndi-sender-unique-name",
        "group_name": "camera1",

}]
```
**server_ip**
IP address hosting the NDI server (IP address of interface bound to the server). If the parameter is set to auto the Sender should establish for itself which interface it should use, based on its own internal configuration. A null value indicates that the Sender has not yet been configured.

**server_port**
Port for the NDI server. If the parameter is set to auto the Sender should establish for itself which port it should use, based on its own internal configuration. 

**source_name**
The name of the stream as declared by the NDI Sender. The stream may contains multiple elements like video, audio, data, etc. => at most one of each

**group_name**
Indicate the NDI group of the source. Null indicates the default group.



### Receiver Parameters

```
 "transport_params": [{
        “interface_ip": "10.10.10.10",
        “server_host": "10.10.10.20",
        "server_port": 5960,
        “source_name": "ndi-sender-unique-name",
        "group_name": "camera1",
        “discovery_servers” : "10.20.20.20"
    }]
```

**interface_ip**
IP address of the network interface the receiver should use. 

**server_host**
Hostname or IP hosting the NDI server. If the parameter is set to “auto” the Receiver should establish for itself which server it should use, based on a discovery mechanism or its own internal configuration. A null value indicates that the Receiver has not yet been configured.

**server_port**
Port for NDI server. If the parameter is set to “auto” the Receiver should establish for itself which port it should use, based on a discovery mechanism or its own internal configuration.

**source_name**
The name of the stream as declared by the NDI sender. The stream may contain multiple elements like video, audio, data, etc. => at most one of each

**group_name**
Indicate the NDI group of the source, null indicates the default group


**discovery_servers**
Provides a list of servers for discovering the NDI streams when server_host is set to auto. If this parameter is set to auto the Receiver should establish for itself which discovery server it should use, based on its own internal configuration or default to mDNS discovery.



## Controllers
### Query of Registered Nodes, Senders and Receivers
### Discovery of Native NDI Senders







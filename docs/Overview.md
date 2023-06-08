# AMWA BCP-NMOS-NDI: NMOS With NDI: Overview
{:.no_toc}

- A markdown unordered list which will be replaced with the ToC, excluding the "Contents header" from above
{:toc}

_(c) AMWA 2023, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_

## About BCP-NMOS-NDI

### What does it do?
 - Enables Registration, Discovery, and Connection Management of NDI Endpoints using the AMWA IS-04 and IS-05 NMOS Specifications.
 - Specifies the interaction of NMOS-enabled NDI nodes and non-NMOS nodes.

### Why does it matter?
- It allows NDI endpoints and gateways to integrate in an NMOS-managed control environment.

### How does it work?
- It specifies how NDI senders and receivers are defined within the scope of IS-04 and IS-05.
- It specifies how connections can be made to non-NMOS NDI senders.
- It defines the representation of muxed NDI flows of video, audio and metadata.


# AMWA BCP-NMOS-NDI: NMOS with NDI

## Introduction
NDI (Network Display Interface) is an IP transport and control technology created by Newtek, now part of VizRT. It includes definitions of encoding, transport and provides a full SDK to implement IP media transport. This document outlines how NDI devices can be managed through NMOS IS-04 and IS-05.

Familiarity with the [JT-NM Reference Architecture](https://jt-nm.org/reference-architecture/) and the [NDI® SDK](https://ndi.video/sdk/) are assumed.

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

This specification also defines the following terms:

### Native NDI Device

A “device” as defined in the NDI SDK Documentation. This shall not be inferred to be a “device” as defined in the NMOS Glossary. Note that the same physical or logical apparatus may simultaneously act as an NMOS device and a Native NDI Device.

### Native NDI Sender

A sender of NDI an NDI stream as defined in the NDI SDK Documentation. This shall not be inferred to be a “sender” as defined in the NMOS Glossary. Note that the same physical or logical apparatus may simultaneously instantiate an NMOS sender and Native NDI Sender.

### Native NDI Receiver

A receiver of NDI an NDI stream as defined in the NDI SDK Documentation. This shall not be inferred to be a “receiver” as defined in the NMOS Glossary. Note that the same physical or logical apparatus may simultaneously instantiate an NMOS receiver and Native NDI Receiver.

### NDI full bandwidth, NDI HX, NDI HX2, NDI HX3

**NDI full bandwidth** utilizes proprietary codecs for audio and video with typical bandwidth of 125-140Mb/s per HD video flow.

**NDI HX** utilizes h.264 video encoding and aac audio encoding with typical bandwidth of 8-20Mb/s per HD video flow. NDI HX is sometimes stylized **NDI|HX** in some documentation.

**NDI HX2** allows the use of h.264 or h.265 long-gop video coding and aac audio encoding utilizing approximately half the bandwidth of NDI HX when h.265 encoding is utilized. NDI HX2 is sometimes stylized **NDI|HX2** in some documentation.

**NDI HX3** allows the use of h.264 or h.265 short-gop coding to minimize latency with aac audio coding, utilizing approximately 80Mb/s per HD video flow. NDI HX3 is sometimes stylized **NDI|HX3** in some documentation.

> This document shall use the term "NDI" when referring to all NDI variants, and specify "NDI full bandwidth", "NDI HX", "NDI HX2", or "NDI HX3" where the text applies to specific NDI variants.



## Model

NDI media flows utilize a variety of codecs to compress media flows. In many cases, the NDI SDK negotiates between nodes to select the codec, transport parameters, and encoding parameters used for a media flow.

### NDI Full Bandwidth
The NDI SDK, by default, automatically selects and negotiates encoding parameters between nodes. Media content enters the Native NDI Sender as raw, uncompressed media and raw, uncompressed media emerges from Native NDI Receivers. 

Since the NDI SDK controls the encoding and interfaces the host application using raw media, NMOS models NDI flows as:
- `video/raw` for video flows
- `audio/L8`, `audio/L16`, `audio/L24` for audio flows
- *`application/<some format>`* for metadata flows

### NDI HX, NDI HX2, NDI HX3
The NDI Advanced SDK supports compressed flows utilizing h.264, h.265, and aac codecs. In these implementations, the host application presents compressed frames of media to the Native NDI Sender and a Native NDI receiver presents compressed media frames to the host application. 

For NDI HX, HX2 and HX3 implementation, NMOS models NDI flows as:
- `video/H264`, `video/H265` for video flows
- `audio/mpeg4-generic` for audio flows
- *`application/<some format>`* for metadata flows



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

```json
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

```json
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







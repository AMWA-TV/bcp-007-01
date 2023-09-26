# AMWA BCP-007-01: NMOS With NDI
{:.no_toc}

- A markdown unordered list which will be replaced with the ToC, excluding the "Contents header" from above
{:toc}

_(c) AMWA 2023, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_

## Introduction

NDI (Network Display Interface) is an IP transport and control technology created by Newtek, a division of Vizrt Group. It includes definitions of encoding, transport and provides a full SDK to implement IP media transport. This document outlines how NDI devices can be managed through NMOS IS-04 and IS-05.

Familiarity with the [JT-NM Reference Architecture](https://jt-nm.org/reference-architecture/) and the [NDI® SDK](https://ndi.video/sdk/) are assumed.

See also the [NMOS Technical Overview](https://specs.amwa.tv/nmos/main/docs/Technical_Overview.html).

## Use of Normative Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY",
and "OPTIONAL" in this document are to be interpreted as described in [RFC-2119](https://datatracker.ietf.org/doc/html/rfc2119).

> Make sure normative terms are UPPERCASE.   Check with Peter on MUST vs SHALL

## Dependencies


This document was written based upon [NDI® Advanced SDK Version 5.5](https://ndi.video/sdk/), however devices which implement other versions of the SDK MAY also be supported.

This document depends upon the following reference documents:
- [NMOS IS-04 - Discovery and Registration](https://specs.amwa.tv/is-04/) - V1.3.2
- [NMOS IS-05 - Device Connection Management](https://specs.amwa.tv/is-05/) - V1.1.2
- [NMOS BCP-004-01 - Receiver Capabilites](https://specs.amwa.tv/bcp-004-01/) - V1.0.0
- [NMOS BCP-006-02 - NMOS with H.264](https://specs.amwa.tv/bcp-006-01/) - V1.0.0
- [NMOS BCP-006-03 - NMOS with H.265](https://specs.amwa.tv/bcp-006-01/) - V1.0.0


## Definitions

The NMOS terms ‘Controller’, ‘Node’, ‘Source’, ‘Flow’, ‘Sender’, ‘Receiver’ are used as defined in the [NMOS Glossary](https://specs.amwa.tv/nmos/main/docs/Glossary.html).

This specification also defines the following terms:

### NDI SDK

The NDI Software Development Kit (SDK) is offered in two variants. The base version SHALL be referred to as **NDI Standard SDK**, The Advanced SDK SHALL be referred to as **NDI Advanced SDK**. References to **NDI SDK** SHALL be interpreted to apply to both the **NDI Standard SDK** and **NDI Advanced SDK**.

> Describe what we mean by "Native" in the context of IS-05 Non-NMOS devices
> Define Non-NMOS Device / Sender / Receiver -> Native Device/Sender/Receiver which doesnt implement NMOS.

### Native NDI Device

A “device” as defined in the NDI SDK. This SHALL NOT be inferred to be a “Device” as defined in the NMOS Glossary. Note that the same physical or logical apparatus MAY simultaneously act as an NMOS Device and a Native NDI Device.

### Non-NMOS NDI Device
A Native NDI Device which does not support the NMOS specifications.

### NDI Stream

A "stream" as defined in the NDI SDK. An NDI Stream is a multiplexed structure that may include one or more video, audio and metadata flows. 

### Native NDI Sender

A "sender" of an NDI stream as defined in the NDI SDK. This SHALL NOT be inferred to be a “Sender” as defined in the NMOS Glossary. Note that the same physical or logical apparatus MAY simultaneously act as an NMOS Sender and a Native NDI Sender.

### Native NDI Receiver

A "receiver" of an NDI stream as defined in the NDI SDK. This SHALL NOT be inferred to be a “Receiver” as defined in the NMOS Glossary. Note that the same physical or logical apparatus MAY simultaneously act as an NMOS Receiver and a Native NDI Receiver.

### NDI Device
A Native NDI Device which implements IS-04 and allows registration in an NMOS Registry

### NDI Sender

An NMOS Sender which implements the NDI transport.

### NDI Receiver

An NMOS Receiver which implements the NDI transport.

### NDI Full Bandwidth

An NDI stream which utilizes proprietary codecs for audio and video. NDI Full Bandwidth is supported by both the NDI Standard SDK and NDI Advanced SDK.

### NDI HX, NDI HX2, NDI HX3

NDI High Efficiency profiles, named **NDI HX**, **NDI HX2**, and **NDI HX3** utilize H.264/AVC, H.265/HEVC, AAC and Opus codecs. These are supported by the NDI Advanced SDK only.

**NDI HX** utilizes Long-GOP H.264 video encoding at maximum Full HD resolution and AAC audio encoding. NDI HX is sometimes stylized **NDI|HX** in some documentation.

**NDI HX2** utilizes H.264 or H.265 Long-GOP video coding up to UHD resolution and AAC or Opus audio encoding. NDI HX2 is sometimes stylized **NDI|HX2** in some documentation.

**NDI HX3** utilizes H.264 or H.265 Short-GOP video coding up to UHD resolution and AAC or Opus audio encoding. NDI HX3 is sometimes stylized **NDI|HX3** in some documentation.

### NDI

This document SHALL use the term "NDI" when referring to all NDI variants, and specify "NDI Full Bandwidth", "NDI HX", "NDI HX2", or "NDI HX3" where the text applies to specific NDI variants.

## Native NDI Model

NDI Streams utilize a variety of codecs to compress media. In many cases, the NDI SDK negotiates between Native NDI Devices to select the transport and encoding parameters used for an NDI Stream. A Native NDI Receiver does not learn about the encoding and transport parameters of an NDI Stream until a connection is established.


### NDI Full Bandwidth

The NDI SDK, by default, automatically selects and negotiates encoding parameters between Native NDI Devices. Media content enters the Native NDI Sender as raw, uncompressed media and raw, uncompressed media emerges from Native NDI Receivers.

### NDI HX, NDI HX2, NDI HX3

The NDI Advanced SDK supports compressed NDI Streams utilizing H.264, H.265, AAC and Opus codecs. Media content enters the Native NDI Sender as compressed media and compressed media emerges from Native NDI Receivers.

### NDI Metadata

Metadata connections can be implicitly established by the NDI SDK when video connections are established. In some cases, bi-directional metadata connections can be established by the NDI SDK between the Native NDI Sender and Native NDI Receiver. In such cases, NDI Metadata MAY NOT be represeneted explicitly in NMOS.

## NMOS-NDI Model

Native NDI Devices, Native NDI Receivers and Native NDI Senders MAY be represented by NMOS Devices/Nodes, Receivers and Senders repectively.

A controller which supports NDI connection management via IS-05 SHOULD support connection of NDI Receivers to NDI Senders. This controller MAY also support connection of NDI Receivers to Native NDI senders, however it SHALL determine its list of available Native NDI Senders through its own means. 

NDI Devices SHOULD NOT preclude connections established with Native NDI devices via the NDI SDK.

If an NDI Stream is connected to an NDI Receiver outside of IS-05, the transport parameters of the Connection API of the Receiver MUST be updated to reflect the running parameters which have been set via the alternate protocol. The Receiver MUST update its Node API `subscription.active` property accordingly and set `subscription.sender_id` to `null`.

Native NDI Senders do not appear in an NMOS Registry.  They MAY be discovered through the NDI discovery mechanism through the NDI SDK. 

### Use Case Scenarios (Informative)

> Perhaps move this to the end as an appendix???

Use cases are informative only and provided as examples.

![NMOS-NDI Model](images/NMOS-NDI-Ecosystem-drawio.svg)


#### Use Case 1: Native NDI Sender to NDI Receiver - IS-05 Connection

![NMOS-NDI Model](images/NMOS-NDI-UseCase1-drawio.svg)

- Controller is informed of sender (device W) by its own means; this could be via NDI discovery.
- Controller is informed of receiver (device A) through IS-04 
- Controller initiates connection to receiver (device A) from sender (device W) via IS-05
- NDI Receiver (device A) reports its status as outlined in IS-04.

#### Use Case 2: Native NDI Sender to NDI Receiver - Non-NMOS Connection

![NMOS-NDI Model](images/NMOS-NDI-UseCase2-drawio.svg)

- NDI Receiver (device B) is informed of Native NDI Sender (device X) by its own means; this could be via NDI discovery.
- NDI Receiver (device B) initiates connection to Native NDI Sender (device X) directly via NDI SDK.
- NDI Receiver updates its IS-04 `active` state, setting the `sender_id` to `null`
- The controller does not initiate the connection, but can monitor the connection status via IS-04 and IS-05

#### Use Case 3: NDI Sender to Native NDI Receiver - Non-NMOS Connection

![NMOS-NDI Model](images/NMOS-NDI-UseCase3-drawio.svg)

- In this scenario, it is assumed the NDI Sender is active.
- Native NDI Receiver (device X) is informed of NDI Sender (device B) by its own means; this could be via NDI discovery.
- Native NDI Receiver (device X) initiates connection to NDI Sender (device B) directly via NDI SDK.
- The controller does not initiate the connection and cannot monitor the state of the connection.

#### Use Case 4: NDI Sender to NDI Receiver - IS-05 Connection

![NMOS-NDI Model](images/NMOS-NDI-UseCase4-drawio.svg)

- Controller is informed of sender (device C) through IS-04.
- Controller is informed of receiver (device D) through IS-04. 
- Controller initiates connection to receiver (device D) from sender (device C) via IS-05
- NDI Sender (device C) and NDI Receiver (device D)  report their status as outlined in IS-05.

#### Use Case 5: Native NDI Sender to Native NDI Receiver - Non-NMOS Connection

![NMOS-NDI Model](images/NMOS-NDI-UseCase5-drawio.svg)

- This scenario exists outside the realm of NMOS. The controller may not be aware of these devices, and does not initiate any connection between them.
- Native NDI Receiver (device Z) and Native NDI Sender (device Y) discover  through their own means. this could be via NDI discovery.
- Connection from Native NDI Sender (device Y) to Native NDI Receiver (device Z) is performed by the NDI SDK.

#### Use Case 6: NDI Sender to NDI Receiver - Non-NMOS Connection

![NMOS-NDI Model](images/NMOS-NDI-UseCase6-drawio.svg)

- In this scenario, it is assumed the NDI Sender is active.
- In this scenario, both sender (device E) and receiver (device F) are NMOS devices, but connection is established outside of IS-05. 
- NDI Receiver (device E) and NDI Sender (device F) discover  through their own means. this could be via NDI discovery.
- Connection from NDI Sender (device E) to NDI Receiver (device F) is performed by the NDI SDK.
- NDI Receiver SHALL update its IS-04 `active` state, setting the `sender_id` to `null`
- The controller does not initiate the connection, but can monitor the connection status via IS-04 and IS-05.


### Multiplexed Flow Model

Receivers of NDI Streams SHALL have the `format` attribute set to `urn:x-nmos:format:mux` and Senders of NDI Streams SHALL be associated with a Flow having the `foramt` attribute set to `urn:x-nmos:format:mux`, as NDI connections MAY contain multiple essences including video, audio and metadata.

A Flow of `urn:x-nmos:format:mux` format SHALL have parents video or audio sub-Flows. The current NDI SDK limits to a maximum of one of each video, audio and metadata sub-Flow, but this specification supports future SDK versions which may support multiple audio, video or metadata sub-Flows.

NDI Metadata is not explicitly modeled in NMOS and SHOULD be considered implicit with an audio or video Sub-Flow.

![MUX Sender Model](images/NMOS-NDI-Mux-sender-drawio.svg)

## NDI IS-04 Sources, Flows and Senders

### Flows

**MUX Flow**

An NDI Sender must be associated with a mux Flow having the `format` attribute set to `urn:x-nmos:format:mux` and the `media_type` attribute set to `application/ndi`. 

The mux Flow must have parent video and/or audio sub-Flows identifing the sub-Flows multiplexed in the NDI Stream. The mux Flow must be associated with a Source of format `urn:x-nmos:format:mux`.

The mux Flow must have the attributes `audio_layers`, `video_layers` and `data_layers` set to the number of audio, video and data sub-Flows multiplexed in the NDI Stream.

**Video sub-Flows**

For NDI Full Bandwidth the video sub-Flows `media_type` attribute SHALL be `video/raw`. The NDI SDK may transcode the raw video sub-Flow to some native transport format.

For NDI HX, HX2 and HX3 the video sub-Flows `media_type` attribute SHALL be `video/H264` or `video/H265`

NDI Senders SHOULD map the employed `NDIlib_FourCC_video_type` to the NMOS `bit-depth`, `component` and `sub-sampling` properties. 

NDI supports video+alpha video flows. These SHALL be modeled as a single video Flow, including a channel labelled `A` in the `components` parameters for the alpha channel.

The video sub-Flows must have their `layer` attribute set to the video sub-Flow index.

**Audio sub-Flows**

For NDI Full Bandwidth the audio sub-Flows `media_type` SHOULD be a supported PCM type such as `audio\L16`, `audio\L20`, or `audio\L24`. The NDI SDK may transcode the audio sub-Flow to some native transport format.

For NDI HX, HX2 and HX3 the audio sub-Flows `media_type` attribute SHALL be `audio/mpeg4-generic` for audio sub-Flows utilizing the AAC codec, and SHALL be `audio/opus` for audio flows utilizing the Opus codec.

The audio sub-Flows must have their `layer` attribute set to the video sub-Flow index.

### Sources

The Source associated with a mux Flow must have its `format` attribute set to `urn:x-nmos:format:mux` and its `parents` attribute must enumerate the Sources associated with the sub-Flows.

### Senders

NDI Senders do not utilize SDP to describe the NDI Stream; therefore NDI Senders SHOULD have theit `manifest_href` attribute set to `null`.

NDI Senders must have their `transport` attribute set to `urn:x-nmos:transport:ndi`.

NDI Senders must be associated with a mux Flow.

## NDI IS-04 Receivers

### Receivers
The NDI Receiver SHALL have its `format` attribute set to `urn:x-nmos:format:mux` and the `media_types` array of the `caps attribute must contain `application/ndi`. 

When an NDI Receiver is connected to a Native NDI sender through a connection API other than IS-05, the transport parameters of the Connection API of such Receiver MUST be updated to reflect the running parameters which have been set via the alternate protocol. The Receiver MUST update its Node API `subscription.active` property accordingly and set `subscription.sender_id` to `null`.

#### Receiver Capabilities

An NDI Receiver must specify as a minimum the following capabilities:

```json
"caps" : {
    "media_types" : [
       "application/ndi"
    ]
}
```

Additional capabilities may be expressed in the `constraint_sets` array attribute of the Receiver for the stream of media type `application/ndi` and for the associated sub-streams. A sub-stream constraint set must have an associated `urn:x-nmos:cap:meta:format` attribute set to `urn:x-nmos:format:video` or `urn:x-nmos:format:audio`, an `urn:x-nmos:cap:meta:layer` attribute set to the associated per-format index and a `urn:x-nmos:cap:format:media_type` set to the associated sub-stream media type. Addional attributes of a sub-stream constraint set may complete the definition of the capabilities for such sub-srteam.

> Additional capabilities for sub-flows MAY need to be expressed. This relies on additional work that has yet to be done.
> Codecs for audio and video will need to be expressed

## NDI IS-05 Senders and Receivers

### Transport Type

The IS-05 transport for NDI is urn:x-nmos:transport:ndi.

### Sender transport_file

Not used.

### Sender Transport Parameters

The IS-05 schemas sender_transport_params_ndi.json and constraints-schema-rtp.json describe the transport parameters assocaited with the NDI transport.

```json
"transport_params": [{
        "source_ip": "10.10.10.10",
        "source_port": 5960,
        "source_name": "ndi-sender-unique-name",
        "group_name": "camera1"

}]
```

**source_ip**
IP address hosting the NDI server (IP address of interface bound to the server). If the parameter is set to auto the Sender SHOULD establish for itself which interface it SHOULD use, based on its own internal configuration. A null value indicates that the Sender has not yet been configured.

**source_port**
Port for the NDI server. If the parameter is set to auto the Sender SHOULD establish for itself which port it SHOULD use, based on its own internal configuration.

**source_name**
The name of the stream as declared by the NDI Sender. The stream MAY contains multiple elements like video, audio, data, etc. => at most one of each

**group_name**
Indicate the NDI group of the source. Null indicates the default group.

Although the NDI Advanced SDK does provide provisions for NDI Native Devices to specify additional transport parameters, these parameters and properties SHALL be considered device-specific.

### Receiver Parameters

```json
 "transport_params": [{
        "interface_ip": "10.10.10.20",
        "server_host": "10.10.10.10",
        "server_port": 5960,
        "source_name": "ndi-sender-unique-name",
        "group_name": "camera1"
    }]

```

**interface_ip**
IP address of the network interface the receiver should use. If set to auto the receiver should determine which interface to use for itself, for example by using the routing tables.

**server_host**
Hostname or IP hosting the NDI server. A null value indicates that the Receiver has not yet been configured.

**server_port**
Port for NDI server. If the parameter is set to “auto” the Receiver SHOULD establish for itself which port it SHOULD use, based on a discovery mechanism or its own internal configuration.

**source_name**
The name of the stream as declared by the NDI sender. The stream MAY contain multiple elements like video, audio, data, etc. => at most one of each

**group_name**
Indicate the NDI group of the source, null indicates the default group

Although the NDI Advanced SDK does provide provisions for NDI Native Devices to specify additional transport parameters, these parameters and properties SHALL be considered device-specific.

## Controllers

### Query of Registered Nodes, Senders and Receivers

A controller MAY query registered Nodes, NDI Senders and NDI Receivers from the registry. An NDI Receiver MUST be registered in the registry in order to allow a controller to manage connections to it through IS-05.

A Native NDI Device MAY be registered in the registry if it implements the required IS-04 services of an NDI Device. However, a controller MAY direct a NDI Receiver to connect to a Native NDI Sender if the controller has knowledge of that sender (either through NDI discovery or other means).

### Discovery of Native NDI Senders

A controller MAY discover Native NDI Senders via the NDI SDK. This could allow a controller to establish connections between an NDI Receiver and a NDI Native Sender.

Native NDI Devices which do not implement NMOS IS-04 SHALL NOT be registered in an NMOS Registry.

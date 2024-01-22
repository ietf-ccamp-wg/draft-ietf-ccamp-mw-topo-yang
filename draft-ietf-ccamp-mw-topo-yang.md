---
coding: utf-8

title: "A YANG Data Model for Microwave Topology"
abbrev: "Microwave Topology YANG Model"
category: std
docname: draft-ietf-ccamp-mw-topo-yang-latest
ipr: trust200902
submissiontype: IETF
v: 3
area: Routing
workgroup: CCAMP Working Group
keyword: Internet-Draft
venue:
   group: CCAMP
   type: Working Group
   mail: ccamp@ietf.org
   arch: https://datatracker.ietf.org/wg/ccamp/about/
   github: https://github.com/ietf-ccamp-wg/draft-ietf-ccamp-mw-topo-yang
   latest: https://github.com/ietf-ccamp-wg/draft-ietf-ccamp-mw-topo-yang
pi: [toc, sortrefs, symrefs]

author:
 -
   role: editor
   fullname: Scott Mansfield
   organization: Ericsson Inc
   email: scott.mansfield@ericsson.com
 -
   fullname: Jonas Ahlberg
   organization: Ericsson AB
   street: Lindholmspiren 11
   city: Goteborg
   code: 417 56
   country: Sweden
   email: jonas.ahlberg@ericsson.com
 -
   fullname: Min Ye
   organization: Huawei Technologies
   street: No.1899, Xiyuan Avenue
   city: Chengdu
   code: 611731
   country: China
   email: amy.yemin@huawei.com
 -
   fullname: Xi Li
   organization: NEC Laboratories Europe
   street: Kurfursten-Anlage 36
   city: Heidelberg
   code: 69115
   country: Germany
   email: Xi.Li@neclab.eu
 -
   fullname: Daniela Spreafico
   organization: Nokia - IT
   street: Via Energy Park, 14
   city: Vimercate (MI)
   code: 20871
   country: Italy
   email: daniela.spreafico@nokia.com

contributor:
 -
   fullname: Italo Busi
   organization: Huawei Technologies
   email: italo.busi@huawei.com

normative:

informative:
   EN301129:
    title: >
      Transmission and Multiplexing (TM); Digital Radio
      Relay Systems (DRRS); Synchronous Digital Hierarchy (SDH);
      System performance monitoring parameters of SDH DRRS
    author:
      organization: ETSI
    date:  May 1999
    seriesinfo: EN 301 129 V1.1.2

   EN302217-1:
    title: >
      Fixed Radio Systems; Characteristics and
      requirements for point-to-point equipment and antennas;
      Part 1: Overview, common characteristics and system-
      dependent requirements
    author:
      organization: ETSI
    date:  May 2017
    seriesinfo: EN 302 217-1 V3.1.0

--- abstract
This document defines a YANG data model to describe microwave/millimeter radio links in a network topology.

--- middle

# Introduction

This document defines a YANG data model to describe topologies of microwave/millimeter wave (hereafter microwave is used to simplify the text).  The YANG data model describes radio links, supporting carrier(s) and the associated termination points {{!RFC8561}}. A carrier is a description of a link providing transport capacity over the air by a single carrier.  It is typically defined by its transmitting and receiving frequencies.  A radio link is a link providing the aggregated transport capacity of the supporting carriers in aggregated and/or protected configurations, which can be used to carry traffic on higher topology layers such as Ethernet and TDM.  The model augments "YANG Data Model for Traffic Engineering (TE) Topologies" defined in {{!RFC8795}}, which is based on "A YANG Data Model for Network Topologies" defined in {{!RFC8345}}.

The microwave point-to-point radio technology provides connectivity on Layer 0 / Layer 1 (L0/L1) over a radio link between two termination points, using one or several supporting carriers in aggregated or protected configurations.  That application of microwave technology cannot be used to perform cross-connection or switching of the traffic to create network connectivity across multiple microwave radio links. Instead, a payload of traffic on higher topology layers, normally Layer 2 (L2) Ethernet, is carried over the microwave radio link and when the microwave radio link is terminated at the endpoints, cross-connection and switching can be performed on that higher layer creating connectivity across multiple supporting microwave radio links.

The microwave topology model is expected to be used between a Provisioning Network Controller (PNC) and a Multi Domain Service Coordinator (MDSC) {{?RFC8453}}. Examples of use cases that can be supported are:

1. Correlation between microwave radio links and the supported links on higher topology layers (e.g., an L2 Ethernet topology).  This information can be used to understand how changes in the performance/status of a microwave radio link affect traffic on higher layers.
2. Propagation of relevant characteristics of a microwave radio link, such as bandwidth, to higher topology layers, where it could be used as a criterion when configuring and optimizing a path for a connection/service through the network end to end.
3. Optimization of the microwave radio link configurations on a network level, with the purpose to minimize overall interference and/or maximize the overall capacity provided by the links.

## Abbreviations
The following abbreviations are used in this document:

CTP Carrier Termination Point

RLT Radio Link Terminal

RLTP Radio Link Termination Point

SNIR Signal Noise Interference Ratio

MDSC Multi Domain Service Coordinator

PNC Provisioning Network Controller

## Tree Structure
A simplified graphical representation of the data model is used in chapter 3.1 of this document.  The meaning of the symbols in these diagrams is defined in {{?RFC8340}}.

## Prefixes in Data Node Names
In this document, names of data nodes and other data model objects are prefixed using the standard prefix associated with the corresponding YANG imported modules, as shown in {{tab-prefix}}.

| Prefix   | YANG Module             | Reference
| mwt      | ietf-microwave-topology | This document
| nw       | ietf-network            | {{!RFC8345}}
| nt       | ietf-network-topology   | {{!RFC8345}}
| mw-types | ietf-microwave-types    | {{!RFC8561}}
| tet      | ietf-te-topology        | {{!RFC8795}}
{: #tab-prefix title="Prefixes for imported YANG modules"}

# Microwave Topology YANG Data Model

## YANG Tree
~~~~ yangtree
{::include ./trees/mw.tree}
~~~~
{: artwork-name="mw.tree"}

## Relationship between radio links and carriers
A microwave radio link is always an aggregate of one or multiple carriers, in various configurations/modes.  The supporting carriers are identified by their termination points and are listed in the container bundled-links as part of the te-link-config in the YANG Data Model for Traffic Engineering (TE) Topologies {{!RFC8795}} for a radio-link.  The exact configuration of the included carriers is further specified in the rlt-mode container (1+0, 2+0, 1+1, etc.) for the radio-link.  Appendix A includes JSON examples of how such a relationship can be modelled.

## Relationship with client topology model
A microwave radio link carries a payload of traffic on higher topology layers, normally L2 Ethernet.  The leafs supporting-network, supporting-node, supporting-link, and supporting-termination-point in the generic YANG module for Network Topologies {{!RFC8345}} are expected to be used to model a relationship/dependency from higher topology layers to a supporting microwave radio link topology layer.  Appendix A includes JSON examples of an L2 Ethernet link transported over one supporting microwave link.

## Applicability of the Data Model for Traffic Engineering (TE) Topologies
Since microwave is a point-to-point radio technology, a majority of the leafs in the Data Model for Traffic Engineering (TE) Topologies augmented by the microwave topology model are not applicable.  An example of which leafs are considered applicable can be found in appendices {{examples-mw-only}} and {{examples-mw-imports}} in this document.

More specifically in the context of the microwave-specific augmentations of te-topology, admin-status and oper-status leafs (from te-topology) are only applicable to microwave carriers (in the mw-link tree) and not microwave radio links. Enable and disable of a radio link is instead done in the constituent carriers. Furthermore the status leafs related to mw-tp can be used when links are inter-domain and when the status of only one side of the link is known, but since microwave is a point-to-point technology where both ends normally belong to the same domain it is not expected to be applicable in normal cases.

## Microwave Topology YANG Module
This module imports typedefs and modules from {{!RFC8345}}, {{!RFC8561}}, and {{!RFC8795}}, and it references {{EN301129}} and {{EN302217-1}}.

~~~~ yang
{::include ./ietf-microwave-topology.yang}
~~~~
{: sourcecode-markers="true" sourcecode-name="ietf-microwave-topology@2024-01-19.yang"}

# Security Considerations

   The YANG module specified in this document defines schemas for data
   that is designed to be accessed via network management protocols such
   as NETCONF {{!RFC6241}} or RESTCONF {{!RFC8040}}.  The lowest NETCONF layer
   is the secure transport layer, and the mandatory-to-implement secure
   transport is Secure Shell (SSH) {{!RFC6242}}.  The lowest RESTCONF layer
   is HTTPS, and the mandatory-to-implement secure transport is TLS
   {{!RFC8446}}.

   The NETCONF access control model {{!RFC8341}} provides the means to
   restrict access for particular NETCONF or RESTCONF users to a
   preconfigured subset of all available NETCONF or RESTCONF protocol
   operations and content.

   The YANG module specified in this document imports and augments the
   ietf-network and ietf-network-topology models defined in {{!RFC8345}}.
   The security considerations from {{!RFC8345}} are applicable to the
   module in this document.

   There are a several data nodes defined in this YANG module that are
   writable/creatable/deletable (i.e., config true, which is the
   default).  These data nodes can be considered sensitive or vulnerable
   in some network environments.  Write operations (e.g., edit-config)
   to these data nodes without proper protection can have a negative
   effect on network operations.  These are the subtrees and data nodes
   and their sensitivity/vulnerability:

  -  rlt-mode: A malicious client could attempt to modify the mode in
      which the radio link is configured and thereby change the
      intended behavior of the link.

   - tx-frequency, rx-frequency and channel-separation: A malicious
      client could attempt to modify the frequency configuration of
      a carrier which could modify the intended behavior or make
      the configuration invalid and thereby stop the operation of it.

# IANA Considerations

   IANA is asked to assign a new URI from the "IETF XML Registry" {{!RFC3688}} as follows:

~~~~
URI: urn:ietf:params:xml:ns:yang:ietf-microwave-topology
Registrant Contact: The IESG
XML: N/A; the requested URI is an XML namespace.
~~~~

   It is proposed that IANA record the YANG module names in the "YANG
   Module Names" registry {{!RFC6020}} as follows:

~~~~
    Name: ietf-microwave-topology
    Maintained by IANA?: N
    Namespace: urn:ietf:params:xml:ns:yang:ietf-microwave-topology
    Prefix: mwt
    Reference: RFC XXXX
~~~~

--- back

# Microwave Topology Model with base topology models {#examples-mw-only}

   This appendix provides some examples and illustrations of how the Microwave Topology Model can be used.  The tree illustrates an example of a complete Microwave Topology Model including the relevant data nodes from network-topology and te-topology (base topology models).  There are also JSON based instantiations of the Microwave Topology Model for a couple of small network examples.

   The tree below shows an example of the relevant leafs for a complete Microwave Topology Model including the augmented Network Topology Model defined in {{!RFC8345}} and the Traffic Engineering (TE) Topologies model defined in {{!RFC8795}}.

~~~~ yangtree
{::include ./trees/mw-only.tree}
~~~~
{: artwork-name="mw-only.tree"}

The Microwave Topology Model augments the TE Topology Model.

~~~~ ascii-art
{::include ./art/mw-only-art.txt}
~~~~
{: artwork-name="mw-only-art.txt"}
{: #fig-mw-model title="Example for L2 over microwave"}

## Instance data for 2+0 mode for a bonded configuration

~~~~ json
{::include ./json/example2plus0-mw-only.json}
~~~~
{: artwork-name="example2plus0-mw-only.json"}
{: sourcecode-markers="false" sourcecode-name="example2plus0-mw-only.json"}

## Instance data for 1+1 mode for a protected configuration

~~~~ json
{::include ./json/example1plus1-mw-only.json}
~~~~
{: artwork-name="example1plus1-mw-only.json"}
{: sourcecode-markers="false" sourcecode-name="example1plus1-mw-only.json"}

# Microwave Topology Model with example extensions {#examples-mw-imports}

   This appendix provides examples of how the Microwave Topology Model can be used with the interface reference topology (ifref) {{?I-D.draft-ietf-ccamp-if-ref-topo-yang}} and the bandwidth-availability-topology (bwa) {{?I-D.draft-ietf-ccamp-bwa-topo-yang}} models. There is also a snippet of JSON to show geolocation information instance data.  When the JSON files have long lines, {{?RFC8792}} is used to wrap the long lines.

   The tree below shows an example of the relevant leafs for a complete Microwave Topology Model including interface reference topology (ifref) {{?I-D.draft-ietf-ccamp-if-ref-topo-yang}} and bandwidth-availability-topology (bwa) {{?I-D.draft-ietf-ccamp-bwa-topo-yang}} models.

~~~~ yangtree
{::include ./trees/full.tree}
~~~~
{: artwork-name="full.tree"}

   Microwave is a transport technology which can be used to transport client services, such as L2 Ethernet links.  When an L2 link is transported over a single supporting microwave radio link, the topologies could be as shown below.  Note that the figure just shows an example, there might be other possibilities to demonstrate such a topology.  The example of the instantiation encoded in JSON is using only a selected subset of the leafs from the L2 topology model {{?RFC8944}}. The example below uses {{fig-mw-model}} and adds the Interface related information.

~~~~ ascii-art
{::include ./art/mw-extensions-art.txt}
~~~~
{: artwork-name="mw-extensions-art.txt"}
{: #fig-mw-extensions title="Interface extension example for L2 over microwave"}

## Instance data for 2+0 mode

A L2 network with a supporting microwave network, including microwave-topology (MW) and bandwidth-availability-topology (BWA) models as well as the reference to the associated interface management information, is encoded in JSON as follows:

~~~~ json
{::include ./json/example2plus0.json}
~~~~
{: artwork-name="example2plus0.json"}
{: sourcecode-markers="false" sourcecode-name="example2plus0.json"}

## Instance data for geolocation information
This example provides a json snippet that shows geolocation information.

~~~~ ascii-art
{::include ./json/geo-example.json}
~~~~
{: artwork-name="geo-example.json"}

{: numbered="false"}
# Acknowledgments
   This document was prepared using kramdown (thanks Martin Thomson).

   The authors would like to thank Tom Petch and Éric Vyncke for their reviews.

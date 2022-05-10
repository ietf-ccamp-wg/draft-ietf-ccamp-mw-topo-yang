---
title: "A YANG Data Model for Microwave Topology"
abbrev: "Microwave Topology YANG Model"
docname: draft-ietf-ccamp-mw-topo-yang-latest
category: std
ipr: trust200902
area: Routing
workgroup: ccamp
keyword: Internet-Draft
stand_alone: yes
pi: [toc, sortrefs, symrefs]
consensus: true

author:
 -
   ins: J. Ahlberg
   name: Jonas Ahlberg
   organization: Ericsson
   email: jonas.ahlberg@ericsson.com
 -
   ins: S. Mansfield
   name: Scott Mansfield
   organization: Ericsson
   email: scott.mansfield@ericsson.com
 -
   ins: M. Ye
   name: Min Ye
   organization: Huawei Technologies
   email: amy.yemin@huawei.com
 -
   ins: I. Busi
   name: Italo Busi
   organization: Huawei Technologies
   email: italo.busi@huawei.com
 -
   ins: X. Li
   name: Xi Li
   organization: NEC Laboratories Europe
   email: xi.li@neclab.eu
 -
   ins: D. Spreafico
   name: Daniela Spreafico
   organization: Nokia - IT
   email: daniela.spreafico@nokia.com

normative:

informative:

--- abstract
This document defines three YANG data models to describe topologies of microwave/millimeter radio links and bandwidth availability for a link in general, as well as to reference interface management information from a termination point.

TODO

--- middle

# Introduction

This document defines three YANG data models to describe topologies of microwave/millimeter wave (hereafter microwave is used to simplify the text).  The first YANG data model describes radio links, supporting carrier(s) and the associated termination points. A carrier is a description of a link providing transport capacity over the air by a single carrier. It is typically defined by its transmitting and receiving frequencies. A radio link is a link providing the aggregated transport capacity of the supporting carriers in aggregated and/or protected configurations, which can be used to carry traffic on higher topology layers such as Ethernet and TDM. A second YANG data model describes bandwidth availability for a link.  It is an important characteristic of a microwave radio link, but it could also be applicable for other types of links.  A third YANG data model introduces a way to reference the information in a YANG data model for interface management {{!RFC8343}} from a termination point, which is useful for microwave termination points, but which could also be useful for other types of termination points.  All three models augment "YANG Data Model for Traffic Engineering (TE) Topologies" defined in {{!RFC8795}}, which is based on "A YANG Data Model for Network Topologies" defined in {{!RFC8345}}.

The microwave point-to-point radio technology provides connectivity on L0/L1 over a radio link between two termination points, using one or several supporting carriers in aggregated or protected configurations. That application of microwave technology cannot be used to perform cross-connection or switching of the traffic to create network connectivity across multiple microwave radio links. Instead, a payload of traffic on higher topology layers, normally L2 Ethernet, is carried over the microwave radio link and when the microwave radio link is terminated at the endpoints, cross-connection and switching can be performed on that higher layer creating connectivity across multiple supporting microwave radio links.

The microwave topology, the bandwidth availability, and the interface reference models are expected to be used between a Provisioning Network Controller (PNC) and a Multi Domain Service Coordinator(MDSC) {{?RFC8453}}. Examples of use cases that can be supported are:

1. Correlation between microwave radio links and the supported links on higher topology layers. e.g. an L2 Ethernet topology.  This information can be used to understand how changes in the performance/status of a microwave radio link affects traffic on higher layers.
2. Propagation of relevant characteristics of a microwave radio link, such as bandwidth, to higher topology layers, where it e.g. could be used as a criterion when configuring and optimizing a path for a connection/service through the network end to end.
3. Optimization of the microwave radio link configurations on a network level, e.g. with the purpose to minimize overall interference and/or maximize the overall capacity provided by the links.
4. A microwave radio link could dynamically adjust its bandwidth according to changes in the signal conditions. {{?RFC8330}} defines a mechanism to report bandwidth-availability information through OSPF-TE, but it could also be useful for a controller to access such bandwidth-availability information as part of the topology model when performing a path/route computation.

Different use cases require access to different attributes and in order not to restrict what use cases can be supported, all attributes supported by the microwave radio link interface management model is accessible from the topology model.

## Terminology and Definitions
The following acronyms are used in this document:

PNC Provisioning Network Controller

MDSC Multi Domain Service Coordinator

## Tree Structure
A simplified graphical representation of the data models is used in chapters 3.1, 4.1, and 5.1 of this document.  The meaning of the symbols in these diagrams is defined in {{?RFC8340}}.

# Requirements Language
The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14 {{!RFC2119}} {{!RFC8174}} when, and only when, they appear in all capitals, as shown here.

# Microwave Topology YANG Data Model

## YANG Tree
~~~~ ascii-art
{::include ./mw.tree}
~~~~
{: artwork-name="mw.tree"}

## Relationship between radio links and carriers
A microwave radio link is always an aggregate of one or multiple carries, in various configurations/modes.  The supporting carriers are identified by its termination points and are listed in the container bundled-links as part of the te-link-config in the YANG Data Model for Traffic Engineering (TE) Topologies {{!RFC8795}} for a radio-link.  The exact configuration of the included carriers is further specified in the leaf mode (1+0, 2+0, 1+1, etc.) for the radio-link.  Appendix A includes an JSON example of how such a relationship can be modelled.

## Relationship with client topology model
A microwave radio link carries a payload of traffic on higher topology layers, normally L2 Ethernet.  The leafs supporting-network, supporting-node, supporting-link, and supporting-termination-point in the generic YANG module for Network Topologies {{!RFC8345}} are expected to be used to model a relationship/dependency from higher topology layers to a supporting microwave radio link topology layer.  Appendix A includes an JSON example of an L2 Ethernet link transported over one supporting microwave link.

## Applicability of the Data Model for Traffic Engineering (TE) Topologies
Since microwave is a point-to-point radio technology providing connectivity on L0/L1 over a radio link between two termination points and cannot be used to perform cross-connection or switching of the traffic to create network connectivity across multiple microwave radio links, a majority of the leafs in the Data Model for Traffic Engineering (TE) Topologies augmented by the microwave topology model are not applicable.  An example of which leafs are considered applicable can be found in appendix "Examples of the application of the Topology Models" in this document. {{examples}}

More specifically, admin-status and oper-status are recommended to be reported for links only.  Status for termination points can be used when links are inter-domain and when the status of only one side of link is known, but since microwave is a point-to-point technology where both ends normally belong to the same domain it is not expected to be applicable in normal cases.  Furthermore, admin-status is not applicable for microwave radio links.  Enable and disable of a radio link is instead done in the constituent carriers.

## Model applicability to other technology
TBD

## Microwave Topology YANG Module
~~~~ yang
{::include ./ietf-microwave-topology@2021-10-20.yang}
~~~~
{: sourcecode-markers="true" sourcecode-name="ietf-microwave-topology@2021-10-20.yang"}

#Bandwidth Availability Topology YANG Data Model

## YANG Tree
~~~~ ascii-art
{::include ./bw.tree}
~~~~
{: artwork-name="bw.tree"}

## Bandwidth Availability Topology YANG Data Module
~~~~ yang
{::include ./ietf-bandwidth-availability-topology.yang}
~~~~
{: sourcecode-markers="true" sourcecode-name="ietf-bandwidth-availability-topology.yang"}

# Termination Point to Interface Reference YANG Data Model

## YANG Tree
~~~~ ascii-art
{::include ./if.tree}
~~~~
{: artwork-name="if.tree"}

## Termination Point to Interface Reference YANG Data Module
~~~~ yang
{::include ./ietf-tp-interface-reference-topology.yang}
~~~~
{: sourcecode-markers="true" sourcecode-name="ietf-tp-interface-reference-topology.yang"}

# Security Considerations

   The YANG modules specified in this document define schemas for data
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

   The YANG modules specified in this document import and augment the
   ietf-network and ietf-network-topology models defined in {{!RFC8345}}.
   The security considerations from {{!RFC8345}} are applicable to the
   modules in this document.

   There are a several data nodes defined in these YANG modules that are
   writable/creatable/deletable (i.e., config true, which is the
   default).  These data nodes may be considered sensitive or vulnerable
   in some network environments.  Write operations (e.g., edit-config)
   to these data nodes without proper protection can have a negative
   effect on network operations.  These are the subtrees and data nodes
   and their sensitivity/vulnerability:

   In the "ietf-microwave-topology" module:

   -  rlt-interface-path: A malicious client could set an arbitrary
      xpath that could allow a client to retrieve incorrect information.
      Troubleshooting would be difficult because the bad path would not
      be detectable until the client tries to use the leaf to identify
      to radio link terminal.

   In the "ietf-bandwidth-availability-topology" module:

   -  availability: A malicious client could attempt to modify the
      availability level which could modify the intended behavior.

   -  link-bandwidth: A malicious client could attempt to modify the
      link bandwidth which could either provide more or less link
      bandwidth at the indicated availability level, changing the
      resource allocation in unintended ways.

# IANA Considerations

   IANA is asked to assign a new URI from the "IETF XML Registry" {{!RFC3688}} as follows:

~~~~
URI: urn:ietf:params:xml:ns:yang:ietf-microwave-topology
Registrant Contact: The IESG
XML: N/A; the requested URI is an XML namespace.

URI: urn:ietf:params:xml:ns:yang:ietf-bandwidth-availability-topology
Registrant Contact: The IESG
XML: N/A; the requested URI is an XML namespace.
~~~~

   It is proposed that IANA should record YANG module names in the "YANG
   Module Names" registry {{!RFC6020}} as follows:

~~~~
    Name: ietf-microwave-topology
    Maintained by IANA?: N
    Namespace: urn:ietf:params:xml:ns:yang:ietf-microwave-topology
    Prefix: mwtopo
    Reference: RFC XXXX

    Name: ietf-bandwidth-availability-topology
    Maintained by IANA?: N
    Namespace:
    urn:ietf:params:xml:ns:yang:ietf-bandwidth-availability-topology
    Prefix: bwavtopo
    Reference: RFC XXXX
~~~~

--- back

# Examples of the application of the Topology Models {#examples}

   This appendix provides some examples and illustrations of how the
   Microwave Topology Model and the Bandwidth Availability Topology Model
   can be used.  There is one extended tree to illustrate the complete
   Microwave Topology Model and a JSON based instantiation of the
   Microwave Topology Model for a small network example.

## A tree for a complete Microwave Topology Model

   The tree below shows the leafs for a complete Microwave Topology
   Model including the augmented Network Topology Model defined in
   {{!RFC8345}}, Traffic Engineering (TE) Topologies model defined in
   {{!RFC8795}} and the associated Bandwidth Availability Model.

~~~~ ascii-art
{::include ./full.tree}
~~~~
{: artwork-name="full.tree"}

## A topology with single microwave radio link

   Microwave is a transport technology which can be used to transport
   client services, such as L2 Ethernet links.  When an L2 link is
   transported over a single supporting microwave radio link, the
   topologies could be as shown below.  Note that the figure
   just shows an example, there might be other possibilities to
   demonstrate such a topology.  The example of the instantiation encoded
   in JSON is using only a selected subset of the leafs from the L2
   topology model {{?RFC8944}} and the Microwave Interface Management Model
   {{?RFC8561}}.

~~~~ ascii-art
{::include ./example.txt}
~~~~
{: artwork-name="example.txt"}

This example shows a 2+0 mode for a bonded configuration.

~~~~ ascii-art
{::include ./example2plus0-f.json}
~~~~
{: artwork-name="example2plus0-f.json"}

This example shows a 1+1 mode for protection.

~~~~
{::include ./example1plus1-f.json}
~~~~
{: artwork-name="example1plus1-f.json"}

 Note that the examples above show one particular link
 (unidirectional) and not a complete network topology.

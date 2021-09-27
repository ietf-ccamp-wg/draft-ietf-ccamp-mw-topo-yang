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
smart_quotes: no
pi: [toc, sortrefs, symrefs]

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
   RFC2119:
   RFC3688:
   RFC6020:
   RFC6241:
   RFC6242:
   RFC8040:
   RFC8174:
   RFC8341:
   RFC8345:
   RFC8446:
   RFC8795:

informative:
   RFC8330:
   RFC8340:
   RFC8453:
   RFC8561:
   RFC8944:

--- abstract
This document defines a YANG data model to describe topologies of microwave/millimeter radio links and bandwidth availability for a link in general.

TODO

--- middle

# Introduction

This document defines a YANG data model to describe topologies of microwave/millimeter wave (hereafter microwave is used to simplify the text) comprising radio links and the supporting carrier(s). A carrier is a description of a link providing transport capacity over the air by a single carrier. It is typically defined by its transmitting and receiving frequencies. A radio link is a link providing the aggregated transport capacity of the supporting carriers in aggregated and/or protected configurations, which can be used to carry traffic on higher topology layers such as Ethernet and TDM. The document also defines a YANG data model to describe bandwidth availability for a link. It is an important characteristic of a microwave radio link but it could also be applicable for other types of links. Both models augment "YANG Data Model for Traffic Engineering (TE) Topologies" defined in {{RFC8795}}, which is based on "A YANG Data Model for Network Topologies" defined in {{RFC8345}}.

The microwave point-to-point radio technology provides connectivity on L0/L1 over a radio link between two termination points, using one or several supporting carriers in aggregated or protected configurations. That application of microwave technology cannot be used to perform cross-connection or switching of the traffic to create network connectivity across multiple microwave radio links. Instead a payload of traffic on higher topology layers, normally L2 Ethernet, is carried over the microwave radio link and when the microwave radio link is terminated at the end-points, cross-connection and switching can be performed on that higher layer creating connectivity across multiple supporting microwave radio links.

The microwave topology and the bandwidth availability models are expected to be used between a Provisioning Network Controller (PNC) and a Multi Domain Service Coordinator(MDSC) {{RFC8453}}. Examples of use cases that can be supported are:

- Correlation between microwave radio links and the supported links on higher topology layers. e.g. an L2 Ethernet topology.  This information can be used to understand how changes in the performance/status of a microwave radio link affects traffic on higher layers.
- Propagation of relevant characteristics of a microwave radio link, such as bandwidth, to higher topology layers, where it e.g. could be used as a criteria when configuring and optimizing a path for a connection/service through the network end to end.
- Optimization of the microwave radio link configurations on a network level, e.g. with the purpose to minimize overall interference and/or maximize the overall capacity provided by the links.
- A microwave radio link could dynamically adjust its bandwidth according to changes in the signal conditions. {{RFC8330}} defines a mechanism to report bandwidth-availability information through OSPF-TE, but it could also be useful for a controller to access such bandwidth-availability information as part of the topology model when performing a path/route computation.

Different use cases require access to different attributes and in order not to restrict what use cases can be supported, all attributes supported by the microwave radio link interface management model is accessible from the topology model.

## Terminology and Definitions
The following acronyms are used in this document:

PNC Provisioning Network Controller

MDSC Multi Domain Service Coordinator

## Tree Structure
A simplified graphical representation of the data models are used in chapters 3.1 and 4.1 of this document.  The meaning of the symbols in these diagrams is defined in {{RFC8340}}.

# Requirements Language
The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14 {{RFC2119}} {{RFC8174}} when, and only when, they appear in all capitals, as shown here.

# Microwave Topology YANG Data Model

## YANG Tree
~~~~~~~~~~
module: ietf-microwave-topology

  augment /nw:networks/nw:network/nw:network-types/
           tet:te-topology:
    +--rw mw-topology!
  augment /nw:networks/nw:network/nw:node/
           nt:termination-point/tet:te:
    +--rw mw-tp-choice
       +--rw (mw-tp-option)?
          +--:(microwave-rltp)
          |  +--rw microwave-rltp!
          +--:(microwave-ctp)
             +--rw microwave-ctp!
  augment /nw:networks/nw:network/nt:link/tet:te/
           tet:te-link-attributes:
    +--rw mw-link-choice
       +--rw (mw-link-option)?
          +--:(microwave-radio-link)
          |  +--rw microwave-radio-link!
          |     +--rw mode?   identityref
          +--:(microwave-carrier)
             +--rw microwave-carrier!
                +--rw tx-frequency?         uint32
                +--rw rx-frequency?         uint32
                +--rw channel-separation?   uint32
  augment /nw:networks/nw:network/nt:link/tet:te/
           tet:te-link-attributes/tet:max-link-bandwidth/
           tet:te-bandwidth/tet:technology:
    +--:(microwave)
       +--ro mw-bandwidth?   uint64
~~~~~~~~~~

## Relationship between radio links and carriers
A microwave radio link is always an aggregate of one or multiple carries, in various configurations/modes.  The supporting carriers are identified by its termination points and are listed in the container bundled-links as part of the te-link-config in the YANG Data Model for Traffic Engineering (TE) Topologies {{RFC8795}} for a radio-link.  The exact configuration of the included carriers are further specified in the leaf mode (1+0, 2+0, 1+1, etc.) for the radio-link.  Appendix A includes an JSON example of how such a relationship can be modelled.

## Relationship with client topology model
A microwave radio link carries a payload of traffic on higher topology layers, normally L2 Ethernet.  The leafs supporting-network, supporting-node, supporting-link, and supporting-termination-point in the generic YANG module for Network Topologies {{RFC8345}} are expected to be used to model a relationship/dependency from higher topology layers to a supporting microwave radio link topology layer.  Appendix A includes an JSON example of an L2 Ethernet link transported over one supporting microwave link.

## Model applicability to other technology
TBD

## Microwave Topology YANG Module
~~~~~~~~~~
  <CODE BEGINS> file "ietf-microwave-topology@2021-09-22.yang"
 module ietf-microwave-topology {
  yang-version 1.1;
   namespace
   "urn:ietf:params:xml:ns:yang:ietf-microwave-topology";

   prefix "mwtopo";

   import ietf-network {
     prefix "nw";
     reference "RFC 8345: A YANG Data Model for Network Topologies";
   }

   import ietf-network-topology {
     prefix "nt";
     reference "RFC 8345: A YANG Data Model for Network Topologies";
   }

   import ietf-te-topology {
     prefix "tet";
     reference "RFC 8795: YANG Data Model for Traffic Engineering
                (TE) Topologies";
   }

   import ietf-microwave-types {
     prefix mw-types;
     reference "RFC 8561";
   }

   organization
     "Internet Engineering Task Force (IETF) CCAMP WG";
   contact
   "
   WG List: <mailto:ccamp@ietf.org>

//[JonasA] Who would like to be on the list of editors/contributors?
   Editor: Jonas Ahlberg
        <mailto:jonas.ahlberg@ericsson.com>
   Editor: Scott Mansfield
        <mailto:scott.mansfield@ericsson.com>
   ";

 // Note to RFC Editor: replace XXXX with actual RFC number and
 // remove this note.
   description
     "This is a module for microwave topology.

     Copyright (c) 2019 IETF Trust and the persons identified as
     authors of the code.  All rights reserved.
     Redistribution and use in source and binary forms, with or
     without modification, is permitted pursuant to, and subject to
     the license terms contained in, the Simplified BSD License set
     forth in Section 4.c of the IETF Trust's Legal Provisions
     Relating to IETF Documents
     (http://trustee.ietf.org/license-info).
     This version of this YANG module is part of RFC XXXX
     (https://tools.ietf.org/html/rfcXXXX); see the RFC itself for
     full legal notices.";

   revision 2021-09-22 {
     description
     "Draft to be used as a basis for the continued microwave
      team discussions";
     reference "";
   }

   /*
    * Groupings
    */
   grouping microwave-rltp-attributes {
     description "Grouping used for attributes describing a microwave
                  radio link termination point.";

 //[JonasA] Any attributes to be incuded?
   }

   grouping microwave-ctp-attributes {
     description "Grouping used for attributes describing a microwave
                  carrier termination point.";

 //[JonasA] Any attributes to be incuded?
   }

   grouping microwave-radio-link-attributes {
     description "Grouping used for attributes describing a microwave
                  radio link.";
     leaf mode {
       type identityref {
         base mw-types:rlt-mode;
       }
       description
         "A description of the mode in which the radio link
          is configured.  The format is X plus Y.
          X represents the number of bonded carriers.
          Y represents the number of protecting carriers.";
     }
 //[JonasA] Any other attributes to be incuded?
   }
   grouping microwave-carrier-attributes {
     description "Grouping used for attributes describing a microwave
                  carrier.";
     leaf tx-frequency {
       type uint32;
       units "kHz";
       description
         "Selected transmitter frequency.";
     }
     leaf rx-frequency {
       type uint32;
       units "kHz";
       description
         "Selected receiver frequency.";
     }
     leaf channel-separation {
       type uint32;
       units "kHz";
       description
         "The amount of bandwidth allocated to a carrier.  The
          distance between adjacent channels in a radio
          frequency channels arrangement";
       reference
         "ETSI EN 302 217-1";
     }
 //[JonasA] Any other attributes to be incuded?
   }

   grouping microwave-bandwidth {
     description "Grouping used for microwave bandwidth.";
     leaf mw-bandwidth {
       type uint64;
       units "Kbps";
       config false;
       description "Microwave radio link and carrier bandwidth.";
     }
   }

   /*
    * Data nodes
    */
   augment "/nw:networks/nw:network/nw:network-types/"
           + "tet:te-topology" {
     description
       "Augment network types to define a microwave network
        topology type.";
     container mw-topology {
       presence "Indicates a topology type of microwave.";
       description "Microwave topology type";
     }
   }

   augment "/nw:networks/nw:network/nw:node/nt:termination-point/"
           + "tet:te" {
     when '../../../nw:network-types/tet:te-topology/'
          + 'mwtopo:mw-topology' {
       description
         "Augmentation parameters apply only for networks with an
          microwave network topology type.";
     }
     description
       "Augmentation to add microwave technology specific
        charactersitics to a termination point.";
     container mw-tp-choice {
       description "Specification of type of termination point.";
       choice mw-tp-option {
         description "Selection of type of termination point.";
         case microwave-rltp {
           container "microwave-rltp" {
             presence
               "Denotes a microwave radio link termination point.
                It corresponds to a microwave RLT interface as
                defined in RFC 8561.";
             uses microwave-rltp-attributes;
             description
               "Denotes and describes a microwave radio link
                termination point.";
           }
         }
         case microwave-ctp {
           container "microwave-ctp" {
             presence
               "Denotes a microwave carrier termination point.
                It corresponds to a microwave CT interface as
                defined in RFC 8561.";
             uses microwave-ctp-attributes;
             description
               "Denotes and describes a microwave carrier
                termination point.";
           }
         }
       }
     }
   }

   augment "/nw:networks/nw:network/nt:link/tet:te/"
           + "tet:te-link-attributes" {
     when '../../../nw:network-types/tet:te-topology/'
        + 'mwtopo:mw-topology' {
       description
         "Augmentation parameters apply only for networks with an
          microwave network topology type.";
     }
     description
       "Augmentation to add microwave technology specific
        charactersitics to a link.";
     container mw-link-choice {
       description "Specification of type of link.";
       choice mw-link-option {
         description "Selection of type of link.";
         case microwave-radio-link {
           container "microwave-radio-link" {
             presence
               "Denotes a microwave radio link";
             uses microwave-radio-link-attributes;
             description
               "Denotes and describes a microwave radio link";
           }
         }
         case microwave-carrier {
           container "microwave-carrier" {
             presence "Denotes a microwave carrier";
             uses microwave-carrier-attributes;
             description "Denotes and describes a microwave carrier";
           }
         }
       }
     }
   }

   augment "/nw:networks/nw:network/nt:link/tet:te/"
           + "tet:te-link-attributes/"
           + "tet:max-link-bandwidth/"
           + "tet:te-bandwidth/tet:technology" {
     when '../../../../../nw:network-types/tet:te-topology/'
          + 'mwtopo:mw-topology' {
       description
         "Augmentation parameters apply only for networks with an
          microwave network topology type.";
     }
     description
       "Augmentation for TE bandwidth.";
     case microwave {
      uses microwave-bandwidth;
     }
   }
 }
  <CODE ENDS>
~~~~~~~~~~

#Bandwidth Availability Topology YANG Data Model

## YANG Tree
~~~~~~~~~~
module: ietf-bandwidth-availability-topology

  augment /nw:networks/nw:network/nt:link/tet:te/
           tet:te-link-attributes:
    +--rw link-availability* [availability]
       +--rw availability      decimal64
       +--rw link-bandwidth?   uint64
~~~~~~~~~~

## Bandwidth Availability Topology YANG Data Module
~~~~~~~~~~
  <CODE BEGINS> file "ietf-bandwidth-availability-topology@2021-09-22.yang"
 module ietf-bandwidth-availability-topology {
  yang-version 1.1;
   namespace
 "urn:ietf:params:xml:ns:yang:ietf-bandwidth-availability-topology";

   prefix "bwatopo";

   import ietf-network {
     prefix "nw";
     reference "RFC 8345: A YANG Data Model for Network Topologies";
   }

   import ietf-network-topology {
     prefix "nt";
     reference "RFC 8345: A YANG Data Model for Network Topologies";
   }

   import ietf-te-topology {
     prefix "tet";
     reference "RFC 8795: YANG Data Model for Traffic Engineering
                (TE) Topologies";
   }

   organization
     "Internet Engineering Task Force (IETF) CCAMP WG";
   contact
   "
   WG List: <mailto:ccamp@ietf.org>

// [JonasA] Who would like to be on the list of editors/contributors?
   Editor: Jonas Ahlberg
        <mailto:jonas.ahlberg@ericsson.com>
   Editor: Scott Mansfield
        <mailto:scott.mansfield@ericsson.com>
   ";

   // Note to RFC Editor: replace XXXX with actual RFC number and
   // remove this note.
   description
     "This is a module for defining bandwidth availability matrix,
      for links in a topology. It is intended to be used in
      conjunction with an instance of ietf-network-topology and its
      augmentations.
      Example use cases include:
      - Defining bandwidth availability matrix for a microwave link
      - Defining bandwidth availability matrix for a LAG link
        comprising of two or more member links

      Copyright (c) 2020 IETF Trust and the persons identified as
      authors of the code.  All rights reserved.
      Redistribution and use in source and binary forms, with or
      without modification, is permitted pursuant to, and subject to
      the license terms contained in, the Simplified BSD License set
      forth in Section 4.c of the IETF Trust's Legal Provisions
      Relating to IETF Documents
      (http://trustee.ietf.org/license-info).
      This version of this YANG module is part of RFC XXXX
      (https://tools.ietf.org/html/rfcXXXX); see the RFC itself for
      full legal notices.";

   revision 2021-09-22 {
     description
     "First rough draft.";
     reference "";
   }

   /*
    * Groupings
    */
   grouping link-bw-availability-table {

     description "Grouping used for bandwidth availability.";

     list link-availability{
       key "availability";
       description
         "Table describing the bandwidths available at corresponding
          availbility level for a link.";

       leaf availability {
         type decimal64 {
           fraction-digits 4;
           range "0..99.9999";
         }
         description "Availability level";
       }

       leaf link-bandwidth {
         type uint64;
         units "Kbps";
         description
           "The link bandwidth corresponding to the availability
            level";
       }
     }
   }

   /*
    * Data nodes
    */

   augment "/nw:networks/nw:network/nt:link/tet:te/"
           + "tet:te-link-attributes" {
     description
       "Augmenting link with link bandwidth availability matrix.";
     uses link-bw-availability-table;
   }
 }
  <CODE ENDS>
~~~~~~~~~~

# Termination Point to Interface Reference YANG Data Model

## YANG Tree
~~~~~~~~~~
  augment /nw:networks/nw:network/nw:node/
           nt:termination-point/tet:te:
    +--rw tp-to-interface-path?
          -> /if:interfaces/if:interface/if:name
~~~~~~~~~~

## Termination Point to Interface Reference YANG Data Module
~~~~~~~~~~
  <CODE BEGINS> file "ietf-tp-interface-reference-topology@2021-09-22.yang"
 module ietf-tp-interface-reference-topology {
  yang-version 1.1;
   namespace
 "urn:ietf:params:xml:ns:yang:ietf-tp-interface-reference-topology";

   prefix "ifref";

   import ietf-network {
     prefix "nw";
     reference "RFC 8345: A YANG Data Model for Network Topologies";
   }

   import ietf-network-topology {
     prefix "nt";
     reference "RFC 8345: A YANG Data Model for Network Topologies";
   }

   import ietf-te-topology {
     prefix "tet";
     reference "RFC 8795: YANG Data Model for Traffic Engineering
                (TE) Topologies";
   }

   import ietf-interfaces {
     prefix if;
     reference
       "RFC 8343";
   }

   organization
     "Internet Engineering Task Force (IETF) CCAMP WG";
   contact
   "
   WG List: <mailto:ccamp@ietf.org>

// [JonasA] Who would like to be on the list of editors/contributors?
   Editor: Jonas Ahlberg
        <mailto:jonas.ahlberg@ericsson.com>
   Editor: Scott Mansfield
        <mailto:scott.mansfield@ericsson.com>
   ";

   // Note to RFC Editor: replace XXXX with actual RFC number and
   // remove this note.
   description
     "This is a module for defining a reference from a termination
      point in a te topology to a list element in interfaces
      as defined in RFC 8343.

      Copyright (c) 2020 IETF Trust and the persons identified as
      authors of the code.  All rights reserved.
      Redistribution and use in source and binary forms, with or
      without modification, is permitted pursuant to, and subject to
      the license terms contained in, the Simplified BSD License set
      forth in Section 4.c of the IETF Trust's Legal Provisions
      Relating to IETF Documents
      (http://trustee.ietf.org/license-info).
      This version of this YANG module is part of RFC XXXX
      (https://tools.ietf.org/html/rfcXXXX); see the RFC itself for
      full legal notices.";

   revision 2021-09-22 {
     description
     "First rough draft.";
     reference "";
   }

   /*
    * Groupings
    */
   grouping tp-to-interface-ref {

     description
       "Grouping used for reference between a termination point and
        an interface.";
     leaf tp-to-interface-path {
       type leafref {
         path '/if:interfaces/if:interface/if:name';
       }
       description
         "Leafref expression referencing a list element, identified
          by its name, in interfaces as defined in RFC 8343.";
     }
  }

   /*
    * Data nodes
    */

   augment "/nw:networks/nw:network/nw:node/nt:termination-point/"
           + "tet:te" {
     description
       "Augmentation to add possibility to reference an element
        in the list of interfaces as defined by RFC 8343.";
     uses tp-to-interface-ref;
   }
 }
  <CODE ENDS>
~~~~~~~~~~

# Security Considerations

   The YANG modules specified in this document define schemas for data
   that is designed to be accessed via network management protocols such
   as NETCONF {{RFC6241}} or RESTCONF {{RFC8040}}.  The lowest NETCONF layer
   is the secure transport layer, and the mandatory-to-implement secure
   transport is Secure Shell (SSH) {{RFC6242}}.  The lowest RESTCONF layer
   is HTTPS, and the mandatory-to-implement secure transport is TLS
   {{RFC8446}}.

   The NETCONF access control model {{RFC8341}} provides the means to
   restrict access for particular NETCONF or RESTCONF users to a
   preconfigured subset of all available NETCONF or RESTCONF protocol
   operations and content.

   The YANG modules specified in this document import and augment the
   ietf-network and ietf-network-topology models defined in {{RFC8345}}.
   The security considerations from {{RFC8345}} are applicable to the
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

   IANA is asked to assign a new URI from the "IETF XML Registry" {{RFC3688}} as follows:

~~~~~~~~~~
   URI: urn:ietf:params:xml:ns:yang:ietf-microwave-topology
   Registrant Contact: The IESG
   XML: N/A; the requested URI is an XML namespace.

   URI: urn:ietf:params:xml:ns:yang:ietf-bandwidth-availability-topology
   Registrant Contact: The IESG
   XML: N/A; the requested URI is an XML namespace.
~~~~~~~~~~

   It is proposed that IANA should record YANG module names in the "YANG
   Module Names" registry {{RFC6020}} as follows:

~~~~~~~~~~
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
~~~~~~~~~~

--- back

# Examples of the application of the Topology Models

   This appendix provides some examples and illustrations of how the
   Microwave Topology Model and the Bandwidth Availability Topology Model
   can be used.  There is one extended tree to illustrate the complete
   Microwave Topology Model and a JSON based instantiation of the
   Microwave Topology Model for a small network example.

## A tree for a complete Microwave Topology Model

   The tree below shows the leafs for a complete Microwave Topology
   Model including the augmented Network Topology Model defined in
   {{RFC8345}}, Traffic Engineering (TE) Topologies model defined in
   {{RFC8795}} and the associated Bandwidth Availability Model.

~~~~~~~~~~
module: ietf-network
  +--rw networks
  +--rw network* [network-id]
     +--rw network-id            network-id
     +--rw network-types
     |  +--rw tet:te-topology!
     |     +--rw mwtopo:mw-topology!
     +--rw supporting-network* [network-ref]
     |  +--rw network-ref    -> /networks/network/network-id
     +--rw node* [node-id]
     |  +--rw node-id                 node-id
     |  +--rw supporting-node* [network-ref node-ref]
     |  |  +--rw network-ref
     |  |  |           -> ../../../supporting-network/network-ref
     |  |  +--rw node-ref       -> /networks/network/node/node-id
     |  +--rw nt:termination-point* [tp-id]
     |     +--rw nt:tp-id                           tp-id
     |     +--rw nt:supporting-termination-point*
     |     |  |                     [network-ref node-ref tp-ref]
     |     |  +--rw nt:network-ref
     |     |  |        -> ../../../nw:supporting-node/network-ref
     |     |  +--rw nt:node-ref
     |     |  |           -> ../../../nw:supporting-node/node-ref
     |     |  +--rw nt:tp-ref
     |     |        -> /nw:networks/network[nw:network-id=current()
     |     |           /../network-ref]/node[nw:node-id=current()
     |     |           /../node-ref]/termination-point/tp-id
     |     +--rw tet:te!
     |        +--rw tet:admin-status?  te-types:te-admin-status
     |        +--rw tet:name?          string
     |        +--ro tet:oper-status?   te-types:te-oper-status
     |        +--ro tet:geolocation
     |        |  +--ro tet:altitude?   int64
     |        |  +--ro tet:latitude?   geographic-coordinate-degree
     |        |  +--ro tet:longitude?  geographic-coordinate-degree
     |        +--rw ifref:tp-to-interface-path?
     |        |       -> /if:interfaces/if:interface/if:name
     |        +--rw mwtopo:mw-tp-choice
     |           +--rw (mwtopo:mw-tp-option)?
     |              +--:(mwtopo:microwave-rltp)
     |              |  +--rw mwtopo:microwave-rltp!
     |              +--:(mwtopo:microwave-ctp)
     |                 +--rw mwtopo:microwave-ctp!
     +--rw nt:link* [link-id]
        +--rw nt:link-id                link-id
        +--rw nt:source
        |  +--rw nt:source-node?   -> ../../../nw:node/node-id
        |  +--rw nt:source-tp?
        |         -> ../../../nw:node[nw:node-id=current()
        |           /../source-node]/termination-point/tp-id
        +--rw nt:destination
        |  +--rw nt:dest-node?   -> ../../../nw:node/node-id
        |  +--rw nt:dest-tp?
        |        -> ../../../nw:node[nw:node-id=current()
        |          /../dest-node]/termination-point/tp-id
        +--rw tet:te!
           +--rw (tet:bundle-stack-level)?
           |  +--:(tet:bundle)
           |     +--rw tet:bundled-links
           |        +--rw tet:bundled-link* [sequence]
           |           +--rw tet:sequence      uint32
           |           +--rw tet:src-tp-ref?   -> ../../../../../
           |           |     nw:node[nw:node-id current()/../../..
           |           |     /../nt:source/source-node]/
           |           |     termination-point/tp-id
           |           +--rw tet:des-tp-ref?   -> ../../../../../
           |                 nw:node[nw:node-id = current()/../../
           |                 ../../nt:destination/dest-node]/
           |                 termination-point/tp-id
           +--rw tet:te-link-attributes
           |  +--rw tet:name?            string
           |  +--rw tet:admin-status?    te-types:te-admin-status
           |  +--rw tet:max-link-bandwidth
           |  |  +--rw tet:te-bandwidth
           |  |     +--rw (tet:technology)?
           |  |        +--:(mwtopo:microwave)
           |  |           +--ro mwtopo:mw-bandwidth? uint64
           |  +--rw mwtopo:mw-link-choice
           |  |   +--rw (mwtopo:mw-link-option)?
           |  |     +--:(mwtopo:microwave-radio-link)
           |  |     |  +--rw mwtopo:microwave-radio-link!
           |  |     |     +--rw mwtopo:mode?   identityref
           |  |     +--:(mwtopo:microwave-carrier)
           |  |        +--rw mwtopo:microwave-carrier!
           |  |           +--rw mwtopo:tx-frequency?       uint32
           |  |           +--rw mwtopo:rx-frequency?       uint32
           |  |           +--rw mwtopo:channel-separation? uint32
           |  +--rw bwatopo:link-availability* [availability]
           |     +--rw bwatopo:availability      decimal64
           |     +--rw bwatopo:link-bandwidth?   uint64
           +--ro tet:oper-status?         te-types:te-oper-status
~~~~~~~~~~

## A topology with single microwave radio link

   Microwave is a transport technology which can be used to transport
   client services, such as L2 Ethernet links.  When an L2 link is
   transported over a single supporting microwave radio link, the
   topologies could be as shown in Figure 3 below.  Note that the figure
   just shows an example, there might be other possibilities to
   demonstrate such a topology.  The example of the instantiation encoded
   in JSON is using only a selected subset of the leafs from the L2
   topology model {{RFC8944}} and the Microwave Interface Management Model
   {{RFC8561}}.

~~~~~~~~~~
     Node N1                          Node N2
+--------------+                 +--------------+
| +----------+ |                 | +----------+ | L2-network
| | L2-N1-   | |    L2-N1-N2     | |    L2-N2-| | -L2 topology
| | TP1      o---------------------o    TP2   | |
| +----------+ |        '        | +----------+ | Supporting
|          :   |        '        |   :          | ' mw link
|          :   |        '        |   :          | : TPs
| +----------+ |        '        | +----------+ |
| |mw-N1-    | |   mwrl-N1-N2    | |    mw-N2-| | MW-network
| |RLTP1     o---------------------o    RLTP2 | | -MW topology
| +----------+ |        *        | +----------+ |
|         ::   | *************** |   ::         |
|         ::   |**             **|   ::         | Supporting
| +-------:--+ * *             * * +--:-------+ | : TPs
| |mw-N1- :  |*| * mwc-N1-N2-A * |*|  : mw-N1-| | * carriers as
| |CTP1   :  o---------------------o  : CTP2  | |   bundled links
| +-------:--+ | *             * | +--:-------+ |
|         :    |*               *|    :         |
| +----------+ *                 * +----------+ |
| |mw-N1-    |*|   mwc-N1-N2-B   |*|    mw-N1-| |
| |CTP3      o---------------------o    CTP4  | |
| +----------+ |                 | +----------+ |
+--------------+                 +--------------+
 Figure 3: L2 transported over a (2+0) microwave radio link

     Node N1                            Interfaces
+---------------+                    +----------------+
| +-----------+ |tp-to-interface-path| +------------+ |
| | L2-N1-TP1 |************************|L2Interface1| |
| +-----------+ |                    | +------------+ |
|               |                    |                |
| +-----------+ |tp-to-interface-path| +------------+ |
| |mw-N1-RLTP1|************************|   RLT-1    | |
| +-----------+ |                    | +------------+ |
|               |                    |                |
| +-----------+ |tp-to-interface-path| +------------+ |
| |mw-N1-CTP1 |************************|    CT-1    | |
| +-----------+ |                    | +------------+ |
|               |                    |                |
| +-----------+ |tp-to-interface-path| +------------+ |
| |mw-N1-CTP3 |************************|    CT-3    | |
| +-----------+ |                    | +------------+ |
+---------------+                    +----------------+
 Figure 4: References from the topology model information to
 the associated interface management model information

   The example above, a L2 network with a supporting microwave
   network, including microwave-topology (MW) and
   bandwidth-availability-topology (BWA) models as well as
   the reference to the associated interface management
   information, is encoded in JSON as follows:

{
  "ietf-network:networks": {
    "network": [
      {
        "network-id": "L2-network",
        "network-types": {
          "ietf-l2-topology:l2-topology": {
          }
        },
        "supporting-network": [
          {
            "network-ref": "mw-network"
          }
        ],
        "node": [
          {
            "node-id": "L2-N1",
            "supporting-node": [
              {
                "network-ref": "mw-network",
                "node-ref": "mw-N1"
              }
            ],
            "ietf-network-topology:termination-point": [
              {
                "tp-id": "L2-N1-TP1",
                "supporting-termination-point": [
                  {
                    "network-ref": "mw-network",
                    "node-ref": "mw-N1",
                    "tp-ref": "mw-N1-RLTP1"
                  }
                ]
              }
            ]
          },
          {
            "node-id": "L2-N2",
            "supporting-node": [
              {
                "network-ref": "mw-network",
                "node-ref": "mw-N2"
              }
            ],
            "ietf-network-topology:termination-point": [
              {
                "tp-id": "L2-N2-TP2",
                "supporting-termination-point": [
                  {
                    "network-ref": "mw-network",
                    "node-ref": "mw-N2",
                    "tp-ref": "mw-N2-RLTP2"
                  }
                ]
              }
            ]
          }
        ],
        "ietf-network-topology:link": [
          {
            "link-id": "L2-N1-N2",
            "source": {
              "source-node": "L2-N1",
              "source-tp": "L2-N1-TP1"
            },
            "destination": {
              "dest-node": "L2-N2",
              "dest-tp": "L2-N2-TP2"
            },
            "supporting-link": [
              {
                "network-ref": "mw-network",
                "link-ref": "mwrl-N1-N2"
              }
            ]
          }
        ]
      },
      {
        "network-id": "mw-network",
        "network-types": {
          "ietf-te-topology:te-topology": {
            "ietf-microwave-topology:mw-topology": {
            }
          }
        },
        "node": [
          {
            "node-id": "mw-N1",
            "ietf-network-topology:termination-point": [
              {
                "tp-id": "mw-N1-RLTP1",
                "supporting-termination-point": [
                  {
                    "network-ref": "mw-network",
                    "node-ref": "mw-N1",
                    "tp-ref": "mw-N1-CTP1"
                  },
                  {
                    "network-ref": "mw-network",
                    "node-ref": "mw-N1",
                    "tp-ref": "mw-N1-CTP3"
                  }
                ],
                "ietf-te-topology:te-tp-id": "10.10.10.1",
                "ietf-te-topology:te": {
                  "ietf-microwave-topology:mw-tp-choice": {
                    "microwave-rltp": {}
                  },
                  "ietf-tp-interface-reference-topology:
                   tp-to-interface-path":"RLT-1"
                }
              },
              {
                "tp-id": "mw-N1-CTP1",
                "ietf-te-topology:te-tp-id": "1",
                "ietf-te-topology:te": {
                  "ietf-microwave-topology:mw-tp-choice": {
                    "microwave-ctp": {}
                  },
                  "ietf-tp-interface-reference-topology:
                  tp-to-interface-path":"CT-1"
                }
              },
              {
                "tp-id": "mw-N1-CTP3",
                "ietf-te-topology:te-tp-id": "2",
                "ietf-te-topology:te": {
                  "ietf-microwave-topology:mw-tp-choice": {
                    "microwave-ctp": {}
                  },
                  "ietf-tp-interface-reference-topology:
                  tp-to-interface-path":"CT-3"
                }
              }
            ]
          },
          {
            "node-id": "mw-N2",
            "ietf-network-topology:termination-point": [
              {
                "tp-id": "mw-N2-RLTP2",
                "supporting-termination-point": [
                  {
                    "network-ref": "mw-network",
                    "node-ref": "mw-N2",
                    "tp-ref": "mw-N2-CTP2"
                  },
                  {
                    "network-ref": "mw-network",
                    "node-ref": "mw-N2",
                    "tp-ref": "mw-N2-CTP4"
                  }
                ],
                "ietf-te-topology:te-tp-id": "10.10.10.1",
                "ietf-te-topology:te": {
                  "ietf-microwave-topology:mw-tp-choice": {
                    "microwave-rltp": {}
                  },
                  "ietf-tp-interface-reference-topology:
                  tp-to-interface-path":"RLT-2"
                }
              },
              {
                "tp-id": "mw-N2-CTP2",
                "ietf-te-topology:te-tp-id": 1,
                "ietf-te-topology:te": {
                  "ietf-microwave-topology:mw-tp-choice": {
                    "microwave-ctp": {}
                  },
                  "ietf-tp-interface-reference-topology:
                  tp-to-interface-path":"CT-2"
                }
              },
              {
                "tp-id": "mw-N2-CTP4",
                "ietf-te-topology:te-tp-id": 2,
                "ietf-te-topology:te": {
                  "ietf-microwave-topology:mw-tp-choice": {
                    "microwave-ctp": {}
                  },
                  "ietf-tp-interface-reference-topology:
                  tp-to-interface-path":"CT-4"
                }
              }
            ]
          }
        ],
        "ietf-network-topology:link": [
          {
            "link-id": "mwrl-N1-N2",
            "source": {
              "source-node": "mw-N1",
              "source-tp": "mw-N1-RLTP1"
            },
            "destination": {
              "dest-node": "mw-N2",
              "dest-tp": "mw-N2-RLTP2"
            },
            "ietf-te-topology:te": {
              "bundled-links": {
                "bundled-link": [
                  {
                    "sequence": 1,
                    "src-tp-ref": "mw-N1-CTP1",
                    "des-tp-ref": "mw-N2-CTP2"
                  },
                  {
                    "sequence": 2,
                    "src-tp-ref": "mw-N1-CTP3",
                    "des-tp-ref": "mw-N2-CTP4"
                  }
                ]
              },
              "te-link-attributes": {
                "ietf-bandwidth-availability-topology:
                link-availability": [
                  {
                    "availability": "0.999",
                    "link-bandwidth": "1572864"
                  },
                  {
                    "availability": "0.95",
                    "link-bandwidth": "2097152"
                  }
                ],
                "ietf-microwave-topology:mw-link-choice": {
                  "microwave-radio-link": {
                    "mode": "ietf-microwave-types:two-plus-zero"
                  }
                }
              }
            }
          },
          {
            "link-id": "mwc-N1-N2-A",
            "source": {
              "source-node": "mw-N1",
              "source-tp": "mw-N1-CTP1"
            },
            "destination": {
              "dest-node": "mw-N2",
              "dest-tp": "mw-N2-CTP2"
            },
            "ietf-te-topology:te": {
              "te-link-attributes": {
                "ietf-bandwidth-availability-topology:
                link-availability": [
                  {
                    "availability": "0.99",
                    "link-bandwidth": "1048576"
                  }
                ],
                "ietf-microwave-topology:mw-link-choice": {
                  "microwave-carrier": {
                    "tx-frequency": 10728000,
                    "rx-frequency": 10615000,
                    "channel-separation": 28000
                  }
                }
              }
            }
          },
          {
            "link-id": "mwc-N1-N2-B",
            "source": {
              "source-node": "mw-N1",
              "source-tp": "mw-N1-CTP3"
            },
            "destination": {
              "dest-node": "mw-N2",
              "dest-tp": "mw-N2-CTP4"
            },
            "ietf-te-topology:te": {
              "te-link-attributes": {
                "ietf-bandwidth-availability-topology:
                link-availability": [
                  {
                    "availability": "0.99",
                    "link-bandwidth": "1048576"
                  }
                ],
                "ietf-microwave-topology:mw-link-choice": {
                  "microwave-carrier": {
                    "tx-frequency": 10528000,
                    "rx-frequency": 10415000,
                    "channel-separation": 28000
                  }
                }
              }
            }
          }
        ]
      }
    ]
  },
  "ietf-interfaces:interfaces": {
    "interface": [
      {
        "name": "L2Interface1",
        "description": "'Ethernet Interface 1'",
        "type": "iana-if-type:ethernetCsmacd"
      },
      {
        "name": "L2Interface2",
        "description": "'Ethernet Interface 2'",
        "type": "iana-if-type:ethernetCsmacd"
      },
      {
        "name": "RLT-1",
        "description": "'Radio Link Terminal 1'",
        "type": "iana-if-type:microwaveRadioLinkTerminal",
        "ietf-microwave-radio-link:mode":
        "ietf-microwave-types:one-plus-zero",
        "ietf-microwave-radio-link:carrier-terminations": [
          "CT-1",
          "CT-3"
        ]
      },
      {
        "name": "RLT-2",
        "description": "'Radio Link Terminal 2'",
        "type": "iana-if-type:microwaveRadioLinkTerminal",
        "ietf-microwave-radio-link:mode":
        "ietf-microwave-types:one-plus-zero",
        "ietf-microwave-radio-link:carrier-terminations": [
          "CT-2",
          "CT-4"
        ]
      },
      {
        "name": "CT-1",
        "description": "'Carrier Termination 1'",
        "type": "iana-if-type:microwaveCarrierTermination",
        "ietf-microwave-radio-link:tx-frequency": 10728000,
        "ietf-microwave-radio-link:duplex-distance": 644000,
        "ietf-microwave-radio-link:channel-separation": 28000,
        "ietf-microwave-radio-link:rtpc": {
          "maximum-nominal-power": "20.0"
        },
        "ietf-microwave-radio-link:single": {
          "selected-cm": "ietf-microwave-types:qam-512"
        }
      },
      {
        "name": "CT-3",
        "description": "'Carrier Termination 3'",
        "type": "iana-if-type:microwaveCarrierTermination",
        "ietf-microwave-radio-link:tx-frequency": 10728000,
        "ietf-microwave-radio-link:duplex-distance": 644000,
        "ietf-microwave-radio-link:channel-separation": 28000,
        "ietf-microwave-radio-link:rtpc": {
          "maximum-nominal-power": "20.0"
        },
        "ietf-microwave-radio-link:single": {
          "selected-cm": "ietf-microwave-types:qam-512"
        }
      },
      {
        "name": "CT-2",
        "description": "'Carrier Termination 2'",
        "type": "iana-if-type:microwaveCarrierTermination",
        "ietf-microwave-radio-link:tx-frequency": 10728000,
        "ietf-microwave-radio-link:duplex-distance": 644000,
        "ietf-microwave-radio-link:channel-separation": 28000,
        "ietf-microwave-radio-link:rtpc": {
          "maximum-nominal-power": "20.0"
        },
        "ietf-microwave-radio-link:single": {
          "selected-cm": "ietf-microwave-types:qam-512"
        }
      },
      {
        "name": "CT-4",
        "description": "'Carrier Termination 4'",
        "type": "iana-if-type:microwaveCarrierTermination",
        "ietf-microwave-radio-link:tx-frequency": 10728000,
        "ietf-microwave-radio-link:duplex-distance": 644000,
        "ietf-microwave-radio-link:channel-separation": 28000,
        "ietf-microwave-radio-link:rtpc": {
          "maximum-nominal-power": "20.0"
        },
        "ietf-microwave-radio-link:single": {
          "selected-cm": "ietf-microwave-types:qam-512"
        }
      }
    ]
  }
}

 Note that the example above just shows one particular link
 (unidirectional) and not a complete network topology.
~~~~~~~~~~

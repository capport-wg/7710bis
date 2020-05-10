**Important:** Read CONTRIBUTING.md before submitting feedback or contributing
```




Network Working Group                                          W. Kumari
Internet-Draft                                                    Google
Obsoletes: 7710 (if approved)                                   E. Kline
Intended status: Standards Track                                    Loon
Expires: October 3, 2020                                   April 1, 2020


               Captive-Portal Identification in DHCP / RA
                    draft-ietf-capport-rfc7710bis-04

Abstract

   In many environments offering short-term or temporary Internet access
   (such as coffee shops), it is common to start new connections in a
   captive portal mode.  This highly restricts what the customer can do
   until the customer has authenticated.

   This document describes a DHCP option (and a Router Advertisement
   (RA) extension) to inform clients that they are behind some sort of
   captive-portal enforcement device, and that they will need to
   authenticate to get Internet access.  It is not a full solution to
   address all of the issues that clients may have with captive portals;
   it is designed to be used in larger solutions.  The method of
   authenticating to, and interacting with the captive portal is out of
   scope of this document.

   This document replaces RFC 7710.  RFC 7710 used DHCP code point 160.
   Due to a conflict, this document specifies 114.

   [ This document is being collaborated on in Github at:
   https://github.com/capport-wg/7710bis.  The most recent version of
   the document, open issues, etc should all be available here.  The
   authors (gratefully) accept pull requests.  Text in square brackets
   will be removed before publication. ]

Status of This Memo

   This Internet-Draft is submitted in full conformance with the
   provisions of BCP 78 and BCP 79.

   Internet-Drafts are working documents of the Internet Engineering
   Task Force (IETF).  Note that other groups may also distribute
   working documents as Internet-Drafts.  The list of current Internet-
   Drafts is at https://datatracker.ietf.org/drafts/current/.

   Internet-Drafts are draft documents valid for a maximum of six months
   and may be updated, replaced, or obsoleted by other documents at any




Kumari & Kline           Expires October 3, 2020                [Page 1]

Internet-Draft             DHCP Captive-Portal                April 2020


   time.  It is inappropriate to use Internet-Drafts as reference
   material or to cite them other than as "work in progress."

   This Internet-Draft will expire on October 3, 2020.

Copyright Notice

   Copyright (c) 2020 IETF Trust and the persons identified as the
   document authors.  All rights reserved.

   This document is subject to BCP 78 and the IETF Trust's Legal
   Provisions Relating to IETF Documents
   (https://trustee.ietf.org/license-info) in effect on the date of
   publication of this document.  Please review these documents
   carefully, as they describe your rights and restrictions with respect
   to this document.  Code Components extracted from this document must
   include Simplified BSD License text as described in Section 4.e of
   the Trust Legal Provisions and are provided without warranty as
   described in the Simplified BSD License.

Table of Contents

   1.  Introduction  . . . . . . . . . . . . . . . . . . . . . . . .   2
     1.1.  Requirements Notation . . . . . . . . . . . . . . . . . .   3
   2.  The Captive-Portal Option . . . . . . . . . . . . . . . . . .   3
     2.1.  IPv4 DHCP Option  . . . . . . . . . . . . . . . . . . . .   4
     2.2.  IPv6 DHCP Option  . . . . . . . . . . . . . . . . . . . .   5
     2.3.  The Captive-Portal IPv6 RA Option . . . . . . . . . . . .   5
   3.  Precedence of API URIs  . . . . . . . . . . . . . . . . . . .   6
   4.  IANA Considerations . . . . . . . . . . . . . . . . . . . . .   6
     4.1.  Captive Portal Unrestricted Identifier  . . . . . . . . .   7
     4.2.  BOOTP Vendor Extensions and DHCP Options Code Change  . .   7
   5.  Security Considerations . . . . . . . . . . . . . . . . . . .   8
   6.  Acknowledgements  . . . . . . . . . . . . . . . . . . . . . .   9
   7.  References  . . . . . . . . . . . . . . . . . . . . . . . . .   9
     7.1.  Normative References  . . . . . . . . . . . . . . . . . .   9
     7.2.  Informative References  . . . . . . . . . . . . . . . . .  10
   Appendix A.  Changes / Author Notes.  . . . . . . . . . . . . . .  11
   Appendix B.  Changes from RFC 7710  . . . . . . . . . . . . . . .  11
   Appendix C.  Observations From IETF 106 Network Experiment  . . .  11
   Authors' Addresses  . . . . . . . . . . . . . . . . . . . . . . .  12

1.  Introduction

   In many environments, users need to connect to a captive-portal
   device and agree to an Acceptable Use Policy (AUP) and / or provide
   billing information before they can access the Internet.  Regardless
   of how that mechanism operates, this document provides functionality



Kumari & Kline           Expires October 3, 2020                [Page 2]

Internet-Draft             DHCP Captive-Portal                April 2020


   to allow the client to know when it is behind a captive portal and
   how to contact it.

   In order to present users with the payment or AUP pages, presently a
   captive-portal enforcement device has to intercept the user's
   connections and redirect the user to a captive portal server, using
   methods that are very similar to man-in-the-middle (MITM) attacks.
   As increasing focus is placed on security, and end nodes adopt a more
   secure stance, these interception techniques will become less
   effective and/or more intrusive.

   This document describes a DHCP ([RFC2131]) option (Captive-Portal)
   and an IPv6 Router Advertisement (RA) ([RFC4861]) extension that
   informs clients that they are behind a captive-portal enforcement
   device and how to contact an API for more information.

   This document replaces RFC 7710 [RFC7710].

1.1.  Requirements Notation

   The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
   "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and
   "OPTIONAL" in this document are to be interpreted as described in BCP
   14 [RFC2119] [RFC8174] when, and only when, they appear in all
   capitals, as shown here.

2.  The Captive-Portal Option

   The Captive Portal DHCP / RA Option informs the client that it may be
   behind a captive portal and provides the URI to access an API as
   defined by [draft-ietf-capport-api].  This is primarily intended to
   improve the user experience by showing the user the captive portal
   information faster and more reliably.  Note that, for the foreseeable
   future, captive portals will still need to implement the interception
   techniques to serve legacy clients, and clients will need to perform
   probing to detect captive portals.

   Clients that support the Captive Portal DHCP option SHOULD include
   the option in the Parameter Request List in DHCPREQUEST messages.
   DHCP servers MAY send the Captive Portal option without any explicit
   request.

   In order to support multiple "classes" of clients (e.g.  IPv4 only,
   IPv6 only with DHCPv6 ([RFC8415]), and IPv6 only with RA) the captive
   network can provision the client with the URI via multiple methods
   (IPv4 DHCP, IPv6 DHCP, and IPv6 RA).  The captive portal operator
   SHOULD ensure that the URIs provisioned by each method are equivalent
   to reduce the chance of operational problems.  The maximum length of



Kumari & Kline           Expires October 3, 2020                [Page 3]

Internet-Draft             DHCP Captive-Portal                April 2020


   the URI that can be carried in IPv4 DHCP is 255 bytes, so URIs longer
   than 255 bytes should not be provisioned via IPv6 DHCP nor IPv6 RA
   options.

   In all variants of this option, the URI MUST be that of the captive
   portal API endpoint, conforming to the recommendations for such URIs
   [draft-ietf-capport-api].

   A captive portal MAY do content negotiation ([RFC7231] section 3.4)
   and attempt to redirect clients querying without an explicit
   indication of support for the captive portal API content type (i.e.
   without application/capport+json listed explicitly anywhere within an
   Accept header vis.  [RFC7231] section 5.3).  In so doing, the captive
   portal SHOULD redirect the client to the value associated with the
   "user-portal-url" API key.  When performing such content negotiation
   ([RFC7231] Section 3.4), implementors of captive portals need to keep
   in mind that such responses might be cached, and therefore SHOULD
   include an appropriate Vary header field ([RFC7231] Section 7.1.4) or
   mark them explicitly uncacheable (for example, using Cache-Control:
   no-store [RFC7234] Section 5.2.2.3).

   The URI SHOULD NOT contain an IP address literal.  Exceptions to this
   might include networks with only one operational IP address family
   where DNS is either not available or not fully functional until the
   captive portal has been satisfied.

   Networks with no captive portals MAY explicitly indicate this
   condition by using this option with the IANA-assigned URI for this
   purpose.  Clients observing the URI value
   "urn:ietf:params:capport:unrestricted" MAY forego time-consuming
   forms of captive portal detection.

2.1.  IPv4 DHCP Option

   The format of the IPv4 Captive-Portal DHCP option is shown below.

       0                   1                   2                   3
       0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
      +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
      | Code          | Len           | URI (variable length) ...     |
      +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
      .                   ...URI continued...                         .
      |                              ...                              |
      +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

   o  Code: The Captive-Portal DHCPv4 Option (114) (one octet)

   o  Len: The length (one octet), in octets of the URI.



Kumari & Kline           Expires October 3, 2020                [Page 4]

Internet-Draft             DHCP Captive-Portal                April 2020


   o  URI: The URI for the captive portal API endpoint to which the user
      should connect (encoded following the rules in [RFC3986]).

   See [RFC2132], Section 2 for more on the format of IPv4 DHCP options.

   Note that the URI parameter is not null terminated.

2.2.  IPv6 DHCP Option

   The format of the IPv6 Captive-Portal DHCP option is shown below.

       0                   1                   2                   3
       0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
      +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
      |          option-code          |          option-len           |
      +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
      .                      URI (variable length)                    .
      |                              ...                              |
      +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

   o  option-code: The Captive-Portal DHCPv6Option (103) (two octets)

   o  option-len: The unsigned 16-bit length, in octets, of the URI.

   o  URI: The URI for the captive portal API endpoint to which the user
      should connect (encoded following the rules in [RFC3986]).

   See [RFC7227], Section 5.7 for more examples of DHCP Options with
   URIs.  See [RFC8415], Section 21.1 for more on the format of IPv6
   DHCP options.

   Note that the URI parameter is not null terminated.

   The maximum length of the URI that can be carried in IPv4 DHCP is 255
   bytes, so URIs longer than 255 bytes should not be provisioned via
   IPv6 DHCP options.

2.3.  The Captive-Portal IPv6 RA Option

   This section describes the Captive-Portal Router Advertisement
   option.










Kumari & Kline           Expires October 3, 2020                [Page 5]

Internet-Draft             DHCP Captive-Portal                April 2020


       0                   1                   2                   3
       0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
      +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
      |     Type      |     Length    |              URI              .
      +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+                               .
      .                                                               .
      .                                                               .
      .                                                               .
      +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
               Figure 2: Captive-Portal RA Option Format

   Type  37

   Length  8-bit unsigned integer.  The length of the option (including
      the Type and Length fields) in units of 8 bytes.

   URI  The URI for the captive portal API endpoint to which the user
      should connect.  This MUST be padded with NULL (0x00) to make the
      total option length (including the Type and Length fields) a
      multiple of 8 bytes.

   Note that the URI parameter is not guaranteed to be null terminated.

   The maximum length of the URI that can be carried in IPv4 DHCP is 255
   bytes, so URIs longer than 255 bytes should not be provisioned via
   IPv6 RA options.

3.  Precedence of API URIs

   A device may learn about Captive Portal API URIs through more than
   one of (or indeed all of) the above options.  Implementations can
   select their own precedence order (e.g., prefer one of the IPv6
   options before the DHCPv4 option, or vice versa, et cetera).

   If the URIs learned via more than one option described in Section 2
   are not all identical, this condition should be logged for the device
   owner or administrator; it is a network configuration error if the
   learned URIs are not all identical.

4.  IANA Considerations

   This document requests one new IETF URN protocol parameter
   ([RFC3553]) entry.  This document also requests a reallocation of
   DHCPv4 option codes (see Appendix C for background).

   Thanks IANA!





Kumari & Kline           Expires October 3, 2020                [Page 6]

Internet-Draft             DHCP Captive-Portal                April 2020


4.1.  Captive Portal Unrestricted Identifier

   This document registers a new entry under the IETF URN Sub-namespace
   defined in [RFC3553]:

   Registry name:  Captive Portal Unrestricted Identifier

   URN:  urn:ietf:params:capport:unrestricted

   Specification:  RFC TBD (this document)

   Repository:  RFC TBD (this document)

   Index value:  Only one value is defined (see URN above).  No
      hierarchy is defined and therefore no sub-namespace registrations
      are possible.

4.2.  BOOTP Vendor Extensions and DHCP Options Code Change

   [ RFC Ed: Please remove before publication: RFC7710 uses DHCP Code
   160 -- unfortunately, it was discovered that this option code is
   already widely used by Polycom (see appendix).  Option 114 (URL) is
   currently assigned to Apple (RFC3679, Section 3.2.3 - Contact: Dieter
   Siegmund, dieter@apple.com - Reason to recover: Never published in an
   RFC) Tommy Pauly (Apple) and Dieter Siegmund confirm that this
   codepoint hasn't been used, and Apple is willing to relinquish it for
   use in CAPPORT.  Please see thread:
   https://mailarchive.ietf.org/arch/msg/captive-portals/
   TmqQz6Ma_fznD3XbhwkH9m2dB28 for more background. ]

   The IANA is requested to update the "BOOTP Vendor Extensions and DHCP
   Options" registry (https://www.iana.org/assignments/bootp-dhcp-
   parameters/bootp-dhcp-parameters.xhtml) as follows.

      Tag: 114
      Name: DHCP Captive-Portal
      Data Length: N
      Meaning: DHCP Captive-Portal
      Reference: [THIS-RFC]

      Tag: 160
      Name: REMOVED/Unassigned
      Data Length:
      Meaning:
      Reference: [THIS-RFC][RFC7710]






Kumari & Kline           Expires October 3, 2020                [Page 7]

Internet-Draft             DHCP Captive-Portal                April 2020


5.  Security Considerations

   By removing or reducing the need for captive portals to perform MITM
   hijacking, this mechanism improves security by making the portal and
   its actions visible, rather than hidden, and reduces the likelihood
   that users will disable useful security safeguards like DNSSEC
   validation, VPNs, etc.  In addition, because the system knows that it
   is behind a captive portal, it can know not to send cookies,
   credentials, etc.  By handing out a URI which is protected with TLS,
   the captive portal operator can attempt to reassure the user that the
   captive portal is not malicious.

   Each of the options described in this document is presented to a node
   using the same protocols used to provision other information critical
   to the node's successful configuration on a network.  The security
   considerations applicable to each of these provisioning mechanisms
   also apply when the node is attempting to learn the information
   conveyed in these options.  In the absence of security measures like
   RA Guard ([RFC6105], [RFC7113]) or DHCP Shield [RFC7610], an attacker
   could inject, modify, or block DHCP messages or RAs.

   An attacker with the ability to inject DHCP messages or RAs could
   include an option from this document to force users to contact an
   address of his choosing.  As an attacker with this capability could
   simply list themselves as the default gateway (and so intercept all
   the victim's traffic); this does not provide them with significantly
   more capabilities, but because this document removes the need for
   interception, the attacker may have an easier time performing the
   attack.

   However, as the operating systems and application that make use of
   this information know that they are connecting to a captive-portal
   device (as opposed to intercepted connections) they can render the
   page in a sandboxed environment and take other precautions, such as
   clearly labeling the page as untrusted.  The means of sandboxing and
   user interface presenting this information is not covered in this
   document - by its nature it is implementation specific and best left
   to the application and user interface designers.

   Devices and systems that automatically connect to an open network
   could potentially be tracked using the techniques described in this
   document (forcing the user to continually authenticate, or exposing
   their browser fingerprint).  However, similar tracking can already be
   performed with the presently common captive portal mechanisms, so
   this technique does not give the attackers more capabilities.

   Captive portals are increasingly hijacking TLS connections to force
   browsers to talk to the portal.  Providing the portal's URI via a



Kumari & Kline           Expires October 3, 2020                [Page 8]

Internet-Draft             DHCP Captive-Portal                April 2020


   DHCP or RA option is a cleaner technique, and reduces user
   expectations of being hijacked - this may improve security by making
   users more reluctant to accept TLS hijacking, which can be performed
   from beyond the network associated with the captive portal.

6.  Acknowledgements

   This document is a -bis of RFC7710.  Thanks to all of the original
   authors (Warren Kumari, Olafur Gudmundsson, Paul Ebersman, Steve
   Sheng), and original contributors.

   Also thanks to the CAPPORT WG for all of the discussion and
   improvements including contributions and review from Joe Clarke,
   Lorenzo Colitti, Dave Dolson, Hans Kuhn, Kyle Larose, Clemens
   Schimpe, Martin Thomson, Michael Richardson, Remi Nguyen Van, Subash
   Tirupachur Comerica, Bernie Volz, and Tommy Pauly.

7.  References

7.1.  Normative References

   [RFC2119]  Bradner, S., "Key words for use in RFCs to Indicate
              Requirement Levels", BCP 14, RFC 2119,
              DOI 10.17487/RFC2119, March 1997,
              <https://www.rfc-editor.org/info/rfc2119>.

   [RFC2131]  Droms, R., "Dynamic Host Configuration Protocol",
              RFC 2131, DOI 10.17487/RFC2131, March 1997,
              <https://www.rfc-editor.org/info/rfc2131>.

   [RFC2132]  Alexander, S. and R. Droms, "DHCP Options and BOOTP Vendor
              Extensions", RFC 2132, DOI 10.17487/RFC2132, March 1997,
              <https://www.rfc-editor.org/info/rfc2132>.

   [RFC3553]  Mealling, M., Masinter, L., Hardie, T., and G. Klyne, "An
              IETF URN Sub-namespace for Registered Protocol
              Parameters", BCP 73, RFC 3553, DOI 10.17487/RFC3553, June
              2003, <https://www.rfc-editor.org/info/rfc3553>.

   [RFC3986]  Berners-Lee, T., Fielding, R., and L. Masinter, "Uniform
              Resource Identifier (URI): Generic Syntax", STD 66,
              RFC 3986, DOI 10.17487/RFC3986, January 2005,
              <https://www.rfc-editor.org/info/rfc3986>.

   [RFC4861]  Narten, T., Nordmark, E., Simpson, W., and H. Soliman,
              "Neighbor Discovery for IP version 6 (IPv6)", RFC 4861,
              DOI 10.17487/RFC4861, September 2007,
              <https://www.rfc-editor.org/info/rfc4861>.



Kumari & Kline           Expires October 3, 2020                [Page 9]

Internet-Draft             DHCP Captive-Portal                April 2020


   [RFC7227]  Hankins, D., Mrugalski, T., Siodelski, M., Jiang, S., and
              S. Krishnan, "Guidelines for Creating New DHCPv6 Options",
              BCP 187, RFC 7227, DOI 10.17487/RFC7227, May 2014,
              <https://www.rfc-editor.org/info/rfc7227>.

   [RFC7231]  Fielding, R., Ed. and J. Reschke, Ed., "Hypertext Transfer
              Protocol (HTTP/1.1): Semantics and Content", RFC 7231,
              DOI 10.17487/RFC7231, June 2014,
              <https://www.rfc-editor.org/info/rfc7231>.

   [RFC7234]  Fielding, R., Ed., Nottingham, M., Ed., and J. Reschke,
              Ed., "Hypertext Transfer Protocol (HTTP/1.1): Caching",
              RFC 7234, DOI 10.17487/RFC7234, June 2014,
              <https://www.rfc-editor.org/info/rfc7234>.

   [RFC8174]  Leiba, B., "Ambiguity of Uppercase vs Lowercase in RFC
              2119 Key Words", BCP 14, RFC 8174, DOI 10.17487/RFC8174,
              May 2017, <https://www.rfc-editor.org/info/rfc8174>.

   [RFC8415]  Mrugalski, T., Siodelski, M., Volz, B., Yourtchenko, A.,
              Richardson, M., Jiang, S., Lemon, T., and T. Winters,
              "Dynamic Host Configuration Protocol for IPv6 (DHCPv6)",
              RFC 8415, DOI 10.17487/RFC8415, November 2018,
              <https://www.rfc-editor.org/info/rfc8415>.

7.2.  Informative References

   [RFC6105]  Levy-Abegnoli, E., Van de Velde, G., Popoviciu, C., and J.
              Mohacsi, "IPv6 Router Advertisement Guard", RFC 6105,
              DOI 10.17487/RFC6105, February 2011,
              <https://www.rfc-editor.org/info/rfc6105>.

   [RFC7113]  Gont, F., "Implementation Advice for IPv6 Router
              Advertisement Guard (RA-Guard)", RFC 7113,
              DOI 10.17487/RFC7113, February 2014,
              <https://www.rfc-editor.org/info/rfc7113>.

   [RFC7610]  Gont, F., Liu, W., and G. Van de Velde, "DHCPv6-Shield:
              Protecting against Rogue DHCPv6 Servers", BCP 199,
              RFC 7610, DOI 10.17487/RFC7610, August 2015,
              <https://www.rfc-editor.org/info/rfc7610>.

   [RFC7710]  Kumari, W., Gudmundsson, O., Ebersman, P., and S. Sheng,
              "Captive-Portal Identification Using DHCP or Router
              Advertisements (RAs)", RFC 7710, DOI 10.17487/RFC7710,
              December 2015, <https://www.rfc-editor.org/info/rfc7710>.





Kumari & Kline           Expires October 3, 2020               [Page 10]

Internet-Draft             DHCP Captive-Portal                April 2020


7.3.  URIs

   [1] https://tickets.meeting.ietf.org/wiki/IETF106network#Experiments

   [2] https://tickets.meeting.ietf.org/wiki/CAPPORT

   [3] https://community.polycom.com/t5/VoIP-SIP-Phones/DHCP-
       Standardization-160-vs-66/td-p/72577

Appendix A.  Changes / Author Notes.

   [RFC Editor: Please remove this section before publication ]

   From initial to -00.

   o  Import of RFC7710.

   From -00 to -01.

   o  Remove link-relation text.

   o  Clarify option should be in DHCPREQUEST parameter list.

   o  Uppercase some SHOULDs.

Appendix B.  Changes from RFC 7710

   This document incorporates the following changes from [RFC7710].

   1.  Clarify that IP string literals are NOT RECOMMENDED.

   2.  Clarify that the option URI SHOULD be that of the captive portal
       API endpoint.

   3.  Clarify that captive portals MAY do content negotiation.

   4.  Added text about Captive Portal API URI precedence in the event
       of a network configuration error.

   5.  Added urn:ietf:params:capport:unrestricted URN.

   6.  Notes that the DHCP Code changed from 160 to 114.

Appendix C.  Observations From IETF 106 Network Experiment

   During IETF 106 in Singapore an experiment [1] enabling Captive
   Portal API compatible clients to discover a venue-info-url (see
   experiment description [2] for more detail) revealed that some



Kumari & Kline           Expires October 3, 2020               [Page 11]

Internet-Draft             DHCP Captive-Portal                April 2020


   Polycom devices on the same network made use of DHCPv4 option code
   160 for other purposes [3].

   The presence of DHCPv4 Option code 160 holding a value indicating the
   Captive Portal API URL caused these devices to not function as
   desired.  For this reason, this document requests IANA deprecate
   option code 160 and reallocate different value to be used for the
   Captive Portal API URL.

Authors' Addresses

   Warren Kumari
   Google
   1600 Amphitheatre Parkway
   Mountain View, CA  94043
   US

   Email: warren@kumari.net


   Erik Kline
   Loon
   1600 Amphitheatre Parkway
   Mountain View, CA  94043
   US

   Email: ek@loon.com
























Kumari & Kline           Expires October 3, 2020               [Page 12]
```

---
v: 3
docname: draft-brockhaus-lamps-eu-rail-keyusages-latest
cat: std
consensus: 'true'
submissiontype: IETF
lang: en
pi:
  toc: 'true'
  tocdepth: '3'
  symrefs: 'true'
  sortrefs: 'false'
title: X.509 Certificate Extended Key Usage (EKU) for ERJU System Pillar
abbrev: EKU for ERJU System Pillar
area: "Security"
wg: LAMPS Working Group
keyword:
- ERJU
- extended key usage
- extension
- PKI
date: 2024
github: "HBrock/eu-rail-keyusages"
latest: "https://HBrock.github.io/eu-rail-keyusages/draft-brockhaus-lamps-eu-rail-keyusages.html"
author:
- name: Hendrik Brockhaus
  org: Siemens
  abbrev: Siemens
  street: Werner-von-Siemens-Strasse 1
  code: '80333'
  city: Munich
  country: Germany
  email: hendrik.brockhaus@siemens.com
  uri: https://www.siemens.com
- name: David Goltzsche
  org: Siemens Mobility
  street: Ackerstraße 22
  code: '38126'
  city: Braunschweig
  country: Germany
  email: david.goltzsche@siemens.com
  uri: https://www.mobility.siemens.com

normative:
  RFC2119:
  RFC5280:
  RFC8174:
  X.680:
    target: https://www.itu.int/rec/T-REC.X.680
    title: >
      Information Technology - Abstract Syntax Notation One (ASN.1):
      Specification of basic notation
    author:
      org: ITU-T
    date: '2021-02'
    seriesinfo:
      ITU-T Recommendation X.680
  X.690:
    target: https://www.itu.int/rec/T-REC.X.690
    title: >
      Information Technology - ASN.1 encoding rules:
      Specification of Basic Encoding Rules (BER),
      Canonical Encoding Rules (CER)
      and Distinguished Encoding Rules (DER)
    author:
      org: ITU-T
    date: '2021-02'
    seriesinfo:
      ITU-T Recommendation X.690

informative:
  RFC5246:
  RFC7299:
  RFC8446:
  RFC9162:
  RFC9336:
  RFC9509:
  Directive-2016/797:
    target: https://eur-lex.europa.eu/eli/dir/2016/797/2020-05-28
    title: 'Directive 2016/797 - Interoperability of the rail system within the EU'
    author:
    - org: European Parliament, Council of the European Union
    date: '2020-05-28'
  ERJU:
    target: https://rail-research.europa.eu/wp-content/uploads/2023/10/ERJU_SP_CyberSecurity_Review3_Files.zip
    title: 'SP-Cybersecurity-SharedCybersecurityServices - Review 3 Final Draft Specs (V0.90)'
    author:
    - org: Europe's Rail Joint Undertaking
    date: '2024-09-23'

--- abstract


RFC 5280 specifies several extended key purpose identifiers (KeyPurposeIds) for X.509 certificates. This document defines KeyPurposeIds for general-purpose and trust anchor configuration files, for software and firmware update packages, and for safety-critical communication to be included in the Extended Key Usage (EKU) extension of X.509 v3 public key certificates used by the ERJU System Pillar.

--- middle

# Introduction {#Intro}

The [Europe's Rail Joint Undertaking System Pillar](#ERJU) will deliver a unified operational concept and a functional, safe and secure system architecture alongside with system requirements. The deliverables include due consideration of cyber-security aspects, focused on the European railway network to which [Directive 2016/797 - Interoperability of the rail system within the EU](#Directive-2016/797) applies.

The ERJU System Pillar Cyber Security Working Group makes use of an internal PKI to generate X.509 PKI certificates. The certificates are used for the following purposes, among others:

* Validating signatures of general-purpose software configuration files.

* Validating signatures of trust anchor configuration files.

* Validating signatures of software and firmware update packages.

* Authenticating communication endpoints authorized for safety-critical communication.

{{RFC5280}} specifies several key usage extensions, defined via KeyPurposeIds, for X.509 certificates. Key usage extensions added to a certificate are meant to express intent as to the purpose of the named usage, for humans and for complying libraries. In addition, the IANA registry "SMI Security for PKIX Extended Key Purpose" {{RFC7299}} contains additional KeyPurposeIds. The use of the anyExtendedKeyUsage KeyPurposeId, as defined in {{Section 4.2.1.12 of RFC5280}}, is generally considered a poor practice. This is especially true for certificates, whether they are multi-purpose or single-purpose, within the context of EURJU System Pillar.

If the purpose of the issued certificates is not restricted, i.e., the type of operations for which a public key contained in the certificate can be used are not specified, those certificates could be used for another purpose than intended, increasing the risk of cross-protocol attacks. Failure to ensure proper segregation of duties means that an application or system that generates the public/private keys and applies for a certificate to the operator certification authority could obtain a certificate that can be misused for tasks that this application or system is not entitled to perform. For example, management of trust anchor is a particularly critical task. A device could potentially accept a trust anchor configuration file signed by a service that uses a certificate with no EKU or with the is KeyPurposeId id-kp-codeSigning ({{Section 4.2.1.12 of RFC5280}}) or id-kp-documentSigning {{RFC9336}}. A device should only accept trust anchor configuration files if the file is signed with a certificate that has been explicitly issued for this purpose.

The KeyPurposeId id-kp-serverAuth ({{Section 4.2.1.12 of RFC5280}}) can be used to identify that the certificate is for a TLS server, and the KeyPurposeId id-kp-clientAuth ({{Section 4.2.1.12 of RFC5280}}) can be used to identify that the certificate is for a TLS client. However, there are currently no KeyPurposeIds for usage with X.509 certificates in EURJU documents for safety-critical communication.

This document addresses the above problems by defining the EKU extension of X.509 public key certificates. Certificates are either used for signing files (general-purpose configuration and trust anchor configuration files, software and firmware update packages) or are used for safety-critical communication.

Vendor-defined KeyPurposeIds used within a PKI governed by the vendor or a group of vendors typically do not pose interoperability concerns, as non-critical extensions can be safely ignored if unrecognized. However, using or misusing KeyPurposeIds outside of their intended vendor-controlled environment can lead to interoperability issues. Therefore, it is advisable not to rely on vendor-defined KeyPurposeIds. Instead, the specification defines standard KeyPurposeIds to ensure interoperability across various implementations.

Although the specification focuses on the the use within ERJU documents, the standard KeyPurposeIds defined in this document can be used in other deployments.


# Conventions and Definitions {#conventions}

{::boilerplate bcp14-tagged}


# Extended Key Purpose for ERJU System Pillar {#EKU}

This specification defines the KeyPurposeIds id-kp-configSigning, id-kp-trustanchorSigning, id-kp-updateSigning, and id-kp-safetyCommunication and uses these, respectively, for: signing general-purpose or trust anchor configuration files, or signing software or firmware update packages, or authenticating communication peers for safety-critical communication. As described in {{Section 4.2.1.12 of RFC5280}}, "\[i\]f the \[extended key usage\] extension is present, then the certificate MUST only be used for one of the purposes indicated" and "\[i\]f multiple \[key\] purposes are indicated the application need not recognize all purposes indicated, as long as the intended purpose is present".

Systems or applications that verify the signature of a general-purpose or trust anchor configuration file, the signature of a software or firmware update package, or the authentication of a communication peer for safety-critical communication SHOULD require that corresponding KeyPurposeIds be specified by the EKU extension. If the certificate requester knows the certificate users are mandated to use these KeyPurposeIds, it MUST enforce their inclusion. Additionally, such a certificate requester MUST ensure that the KeyUsage extension be set to digitalSignature or nonRepudiation (also designated as contentCommitment) for signature verification and/or to keyEncipherment for secret key encryption.


# Including the Extended Key Purpose in Certificates {#include-EKU}

{{RFC5280}} specifies the EKU X.509 certificate extension for use on end entity certificates. The extension indicates one or more purposes for which the certified public key is valid. The EKU extension can be used in conjunction with the Key Usage (KU) extension, which indicates the set of basic cryptographic operations for which the certified key may be used. The EKU extension syntax is repeated here for convenience:

~~~
ExtKeyUsageSyntax  ::=  SEQUENCE SIZE (1..MAX) OF KeyPurposeId

KeyPurposeId  ::=  OBJECT IDENTIFIER
~~~

As described in {{RFC5280}}, the EKU extension may, at the option of the certificate issuer, be either critical or non-critical. The inclusion of KeyPurposeIds id-kp-configSigning, id-kp-trustanchorSigning, id-kp-updateSigning, and id-kp-safetyCommunication in a certificate indicates that the public key encoded in the certificate has been certified for the following usages:

* id-kp-configSigning

> A public key contained in a certificate containing the KeyPurposeId id-kp-configSigning may be used for verifying signatures of general-purpose configuration files (for example XML or JSON files). Configuration files are used to configure software. The KU extension is set to digitalSignature or nonRepudiation.

* id-kp-trustanchorSigning

> A public key contained in a certificate containing the KeyPurposeId id-kp-trustanchorSigning may be used for verifying signatures of trust anchor configuration files. Trust anchor configuration files are used to add or remove trust anchors to the trust store of a device. The KU extension is set to digitalSignature or nonRepudiation.

* id-kp-updateSigning

> A public key contained in a certificate containing the KeyPurposeId id-kp-updateSigning may be used for verifying signatures of software or firmware update packages. Update packages are used to install software (including firmware, safety-related applications and others) on systems. The KU extension is set to digitalSignature or nonRepudiation.

* id-kp-safetyCommunication

> A public key contained in a certificate containing the KeyPurposeId id-kp-safetyCommunication may be used to authenticate a communication peer for safety-critical communication based on TLS or other protocols. Depending on the key type used, the KU is set to digitalSignature or keyEncipherment.

~~~
id-kp  OBJECT IDENTIFIER  ::=
    { iso(1) identified-organization(3) dod(6) internet(1)
      security(5) mechanisms(5) pkix(7) 3 }

id-kp-configSigning        OBJECT IDENTIFIER ::= { id-kp TBD2 }
id-kp-trustanchorSigning   OBJECT IDENTIFIER ::= { id-kp TBD3 }
id-kp-updateSigning        OBJECT IDENTIFIER ::= { id-kp TBD4 }
id-kp-safetyCommunication  OBJECT IDENTIFIER ::= { id-kp TBD5 }
~~~


# Implications for a Certification Authority {#ca-implication}

The procedures and practices employed by a certification authority MUST ensure that the correct values for the EKU extension as well as the KU extension are inserted in each certificate that is issued. The inclusion of the id-kp-configSigning, id-kp-trustanchorSigning, id-kp-updateSigning, and id-kp-safetyCommunication KeyPurposeIds does not preclude the inclusion of other KeyPurposeIds.


# Security Considerations {#security}

The Security Considerations of {{RFC5280}} are applicable to this document. This extended key purpose does not introduce new security risks but instead reduces existing security risks by providing the means to identify if the certificate is generated to verify the signature of a general-purpose or trust anchor configuration file, the signature of a software or firmware update package, or the authentication of a communication peer for safety-critical communication.

To reduce the risk of specific cross-protocol attacks, the relying party or the relying party software may additionally prohibit use of specific combinations of KeyPurposeIds. The procedure for allowing or disallowing combinations of KeyPurposeIds using Excluded KeyPurposeId and Permitted KeyPurposeId, as carried out by a relying party, is defined in {{Section 4 of RFC9336}}. Examples of Excluded KeyPurposeIds include the presence of the anyExtendedKeyUsage KeyPurposeId or the complete absence of the EKU extension in a certificate. Examples of Permitted KeyPurposeIds include the presence of id-kp-configSigning, id-kp-trustanchorSigning, id-kp-updateSigning, and id-kp-safetyCommunication KeyPurposeIds.

# Privacy Considerations {#privacy}

In some security protocols, such as [TLS 1.2](#RFC5246), certificates are exchanged in the clear. In other security protocols, such as [TLS 1.3](#RFC8446), the certificates are encrypted. The inclusion of the EKU extension can help an observer determine the purpose of the certificate. In addition, if the certificate is issued by a public certification authority, the inclusion of an EKU extension can help an attacker to monitor the Certificate Transparency logs {{RFC9162}} to identify the purpose of the certificate.


# IANA Considerations {#iana}

IANA is also requested to register the following ASN.1 {{X.680}}
module OID in the "SMI Security for PKIX Module Identifier" registry
(1.3.6.1.5.5.7.0). This OID is defined in {{asn1}}.

| Decimal | Description         | References |
|:--------|:--------------------|:-----------|
| TBD1    | id-mod-eu-rail-eku  | This-RFC   |


IANA is requested to register the following OIDs in the "SMI Security
for PKIX Extended Key Purpose" registry (1.3.6.1.5.5.7.3).  These
OIDs are defined in {{include-EKU}}.

| Decimal | Description                | References |
|:--------|:---------------------------|:-----------|
| TBD2    | id-kp-configSigning        | This-RFC   |
| TBD3    | id-kp-trustanchorSigning   | This-RFC   |
| TBD4    | id-kp-updateSigning        | This-RFC   |
| TBD5    | id-kp-safetyCommunication  | This-RFC   |


# Acknowledgments {#acknow}

We would like to thank the authors of {{RFC9336}} and {{RFC9509}} for  their excellent template.

We also thank all reviewers of this document for their valuable feedback.


--- back

# ASN.1 Module {#asn1}

The following module adheres to ASN.1 specifications {{X.680}} and
{{X.690}}.

~~~ asn1
<CODE BEGINS>

EU-Rail-EKU
  { iso(1) identified-organization(3) dod(6) internet(1)
  security(5) mechanisms(5) pkix(7) id-mod(0)
  id-mod-eu-rail-eku (TBD1) }

DEFINITIONS IMPLICIT TAGS ::=
BEGIN

-- OID Arc

id-kp OBJECT IDENTIFIER ::=
  { iso(1) identified-organization(3) dod(6) internet(1)
    security(5) mechanisms(5) pkix(7) kp(3) }

-- Extended Key Usage Values

id-kp-configSigning        OBJECT IDENTIFIER ::= { id-kp TBD2 }
id-kp-trustanchorSigning   OBJECT IDENTIFIER ::= { id-kp TBD3 }
id-kp-updateSigning        OBJECT IDENTIFIER ::= { id-kp TBD4 }
id-kp-safetyCommunication  OBJECT IDENTIFIER ::= { id-kp TBD5 }

END


<CODE ENDS>
~~~


# History of Changes {#history}

[RFC Editor: Please remove this appendix in the release version of the document.]

Version 00:

Initial version of the document following best practices from RFC 9336 and RFC 9509

---
title: "Delegated http-01 Validation in ACME Protocol"
abbrev: "ACME Delegated HTTP Validation"
category: info

docname: draft-crawford-acme-delegated-http-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: ""
workgroup: "Automated Certificate Management Environment"
keyword:
 - next generation
 - unicorn
 - sparkling distributed ledger
venue:
  group: "Automated Certificate Management Environment"
  type: ""
  mail: "acme@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/acme/"
  github: "jmcrawford45/draft-crawford-acme-delegated-http"
  latest: "https://jmcrawford45.github.io/draft-crawford-acme-delegated-http/draft-crawford-acme-delegated-http.html"

author:
 -
    fullname: "Jared Crawford"
    organization: Netflix
    email: "jmcrawford45@gmail.com"
 -
    fullname: "Andrew Chen"
    organization: Netflix
    email: "achen.code@gmail.com"
 -
    fullname: "Hoss Shafagh"
    organization: Netflix
    email: "hoss.sha@gmail.com"

normative:

informative:


--- abstract

This document proposes an extension to the Automated Certificate Management Environment (ACME) !RFC8555 protocol to enhance the http-01 challenge type (see {{Section 8.3 of !RFC8555}}) by allowing for delegation, enabling validation requests to be directed to a designated server. This approach mirrors the functionality available with dns-01 (see {{Section 8.4 of !RFC8555}}) challenges via DNS CNAME records, aiming to improve the flexibility and scalability of the http-01 validation method.

--- middle

# Introduction

The ACME protocol provides mechanisms for validating domain control using several challenge types, including http-01 and dns-01. While dns-01 challenges allow for delegation using DNS CNAME records, http-01 challenges require direct hosting of challenge responses on the domain being validated.
This document introduces a mechanism to delegate http-01 validation requests to a designated server. By leveraging DNS records to redirect HTTP validation, this proposal enables http-01 validation while addressing challenges associated with validating domains hosted on corporate networks, on load balancers, or behind firewalls.
Delegated HTTP validation combines the following benefits from the existing dns-01 and http-01 validation methods.

- **Centralized Management**: Enables centralized management of challenge responses, reducing the complexity of managing multiple domains.
- **Reduced Exposure**: Eliminates the need for direct access to the primary web server for validation.
- **Performance**: Reduces latency compared to dns-01 challenges by allowing synchronous validation.
- **Security**: Avoids risks associated with exposing DNS API credentials.
- **Scalability**: Enables parallelized validation of domains on distributed load balancers.


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Delegated HTTP Validation

This proposal extends the http-01 challenge type to support delegation using a DNS record, similar to the approach used for dns-01 challenges. The process for validating example.com is as follows:

1. **DNS Configuration**: The domain owner creates a DNS record for `_acme-challenge.example.com` pointing to a server capable of serving HTTP validation requests. For example:

    ```
_acme-challenge.example.com. CNAME validation-server.example.net.
    ```
2. **Challenge File Provisioning**: The designated server hosts the challenge file at the path `/.well-known/acme-challenge/{token}`, containing the domain authorization string.
3. **Validation Request**: The ACME server attempts validation in the following order:
    - http://\_acme-challenge.example.com/.well-known/acme-challenge/{token}
    - http://example.com/.well-known/acme-challenge/{token}

    An ACME server MUST try the undelegated URL if the delegated URL fails.
4. **Validation Response**: The designated server responds with the authorization string, which the ACME server verifies against the expected value.
5. **Challenge Completion**: If the validation succeeds, the challenge status is updated to “valid.” If it fails, the status is updated to “invalid,” with diagnostic information provided.


# Security Considerations

- Relies on the security of both DNS and the delegated server.



# IANA Considerations

TODO: https://github.com/jmcrawford45/draft-crawford-acme-delegated-http/issues/1


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.

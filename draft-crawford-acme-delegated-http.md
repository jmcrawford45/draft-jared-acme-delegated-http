---
title: "Delegated HTTP-01 Validation in ACME Protocol"
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

normative:

informative:


--- abstract

This document proposes an extension to the ACME protocol to enhance the HTTP-01 challenge type by allowing for delegation, enabling validation requests to be directed to a designated server. This approach mirrors the functionality currently possible with DNS-01 challenges via DNS CNAME records, aiming to improve the flexibility and scalability of the HTTP-01 validation method.

--- middle

# Introduction

The Automated Certificate Management Environment (ACME) protocol provides mechanisms for validating domain control using several challenge types, including HTTP-01 and DNS-01. While DNS-01 challenges allow for delegation using DNS CNAME records, HTTP-01 challenges currently require direct hosting of challenge responses on the domain being validated.
This document introduces a mechanism to delegate HTTP-01 validation requests to a designated server. By leveraging DNS records to redirect HTTP validation, this proposal enables centralized management of validation requests while addressing challenges associated with validating domains hosted on corporate networks or behind firewalls.
Delegated HTTP validation combines the following benefits from the existing DNS-01 and HTTP-01 validation methods.

- **Centralized Management**: Enables centralized management of challenge responses, reducing the complexity of managing multiple domains.
- **Reduced Exposure**: Eliminates the need for direct access to the primary web server for validation.
- **Performance**: Reduces latency compared to DNS-01 challenges by allowing synchronous validation.
- **Security**: Avoids risks associated with exposing DNS API credentials.


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Delegated HTTP Validation

This proposal extends the HTTP-01 challenge type to support delegation using a DNS record, similar to the approach used for DNS-01 challenges. The process is as follows:

1. **DNS Configuration**: The domain owner creates a DNS record for `_acme-challenge.<YOUR_DOMAIN>` pointing to a server capable of serving HTTP requests. For example:
```
_acme-challenge.example.com CNAME validation-server-example.net
```
2. **Challenge File Provisioning**: The designated server hosts the challenge file at the path `/.well-known/acme-challenge/<TOKEN>`, containing the domain authorization string.
3. **Validation Request**: The ACME server performs the following steps:

    - Attempts validation using the standard HTTP-01 method.
    - If the HTTP-01 method fails or if delegation is detected, constructs a URL using `_acme-challenge.<YOUR_DOMAIN>` and sends an HTTP GET request to the designated server.

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

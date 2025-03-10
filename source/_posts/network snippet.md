---
title: network snippet
date: 2024-12-05 15:50:13
tags: snippet
categories: network
---

# basic

**Common Name (CN)**

**Alternative Name (SAN)**

**Transport Layer Security (TLS)**

# A VS B

## SSL VS TLS

> TLS (Transport Layer Security) and SSL (Secure Sockets Layer) are both cryptographic protocols used for secure communication over the internet. However, they have some key differences:

**SSL (Secure Sockets Layer)**

- Developed by Netscape in 1994 as a predecessor to TLS
- Primarily focused on web browsing security
- Used for encrypting data between a client's browser and a server
- Typically used with port 443 (HTTPS)
- Considered insecure due to weaknesses in its encryption algorithms and key exchange mechanisms

**TLS (Transport Layer Security)**

- Developed by the Internet Engineering Task Force (IETF) as a successor to SSL
- Focuses on secure communication between any two endpoints, not just web browsing
- Uses more robust encryption algorithms, such as AES-256, and improved key exchange mechanisms
- Supports multiple versions of the protocol (TLS 1.0, TLS 1.1, TLS 1.2)
- Now considered a standard for secure internet communications

**Key differences:**

1. **Name**: SSL was originally used for web browsing security, while TLS is more general-purpose.
2. **Encryption algorithms**: TLS uses stronger encryption algorithms than SSL.
3. **Key exchange mechanisms**: TLS has improved key exchange mechanisms compared to SSL.
4. **Protocol versions**: TLS supports multiple protocol versions (TLS 1.0, TLS 1.1, TLS 1.2), while SSL is typically associated with a single version.

**When to use each:**

- Use SSL when you need to maintain compatibility with older systems or browsers that only support the original SSL protocol.
- Use TLS for new applications and systems where security is paramount.

Keep in mind that most modern systems, including web servers and clients, have already adopted TLS as the standard for secure communication. If you're developing a new application or system, it's recommended to use TLS to ensure the highest level of security.


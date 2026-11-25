# TLS Decryption with Wireshark

**Tools:** Kali Linux, Wireshark, OpenSSL  
**Focus:** TLS traffic analysis, RSA key exchange, and HTTP stream reconstruction  
**Environment:** Personal lab with the client and server on one Kali Linux VM

## Overview

I generated a self-signed certificate, started an OpenSSL test server, and captured local TLS traffic in Wireshark. After configuring Wireshark with the matching server private key, I inspected the decrypted HTTP request and response.

**Result:** The screenshots show TLS 1.2 traffic on loopback, an HTTP `GET / HTTP/1.0` request, and an `HTTP/1.0 200 ok` response containing the OpenSSL server's HTML status page.

This was a controlled protocol-analysis exercise using my own service and key material.

## Objectives

- Create a certificate and private key for a local test service.
- Capture the TLS handshake and encrypted application traffic.
- Configure RSA private-key decryption in Wireshark.
- Reconstruct an HTTP exchange using **Follow TLS Stream**.
- Explain why the same decryption method does not apply to every TLS connection.

## Lab environment

| Component | Purpose |
| --- | --- |
| Kali Linux VM | Hosts the client, test server, and packet capture |
| OpenSSL `s_server` | Serves an HTML status page over TLS |
| Wireshark | Captures packets and displays the decrypted stream |
| Loopback interface `lo` | Captures communication within the VM |
| TCP port `4433` | Test service port shown in the capture |
| `server.crt` | Self-signed certificate |
| `server.pem` | Matching private key, kept outside this repository |

The packet screenshots show IPv6 loopback (`::1`). The older RSA configuration dialog contains an IPv4 loopback entry (`127.0.0.1`). Both refer to the local machine; the screenshots document those settings as captured.

## Investigation workflow

### 1. Generate the certificate and key

I used the following command, preserved in the original screenshot:

```bash
openssl req -new -x509 -nodes \
  -out server.crt \
  -keyout server.pem \
  -subj "/CN=localhost"
```

The command created the certificate and a private key without a passphrase. The lack of a passphrase was a lab convenience, rather than a universal Wireshark requirement.

![OpenSSL command generating the localhost certificate and private key](images/01-generate-certificate.png)

### 2. Start the TLS test server

I started OpenSSL's test server with the certificate, private key, and `AES256-SHA` cipher setting:

```bash
openssl s_server -www -cipher AES256-SHA \
  -key server.pem \
  -cert server.crt
```

The terminal displayed `ACCEPT`, and the subsequent capture showed connections to port `4433`. The `-www` option produces the HTML status response examined later.

The cipher setting applies to TLS 1.2 and earlier; it does not itself disable TLS 1.3. My original notes describe disabling TLS 1.3 on the client. The capture provides the evidence that this exchange used TLS 1.2.

![OpenSSL test server started and waiting for connections](images/03-start-tls-server.png)

### 3. Capture the local connection

I captured on `lo` because the client and server ran on the same VM. My notes describe generating traffic through a browser and OpenSSL `s_client`; the exact client command was not retained in the screenshots.

The capture shows the TCP connection, TLS handshake messages, and TLS application-data records. The handshake includes **Client Key Exchange**. Before decryption, the HTTP payload is not readable in the packet list, although connection and handshake information remains visible.

![Loopback capture showing the TLS 1.2 handshake and encrypted application data](images/04-encrypted-tls-capture.png)

### 4. Configure the matching private key

In the Wireshark version used for the lab, I opened **Edit → Preferences → Protocols → TLS → RSA keys list** and selected `/home/kali/server.pem`.

Current versions also provide **Preferences → RSA Keys**. The screenshot records the older dialog used in this exercise.

![Wireshark RSA key configuration referencing the local server private key](images/05-wireshark-rsa-key-settings.png)

### 5. Verify application-layer visibility

After configuring the key, Wireshark identified the HTTP request in the captured session. The highlighted packet shows `GET / HTTP/1.0`.

![Wireshark packet list displaying the decrypted HTTP GET request](images/06-decrypted-http-packet.png)

I then used **Follow → TLS Stream** to inspect both sides of the exchange:

| Evidence | Observation |
| --- | --- |
| Client request | `GET / HTTP/1.0` |
| Server response | `HTTP/1.0 200 ok` |
| Content type | `text/html` |
| Response body | OpenSSL test-server status page |
| Cipher information | `AES256-SHA` appears under ciphers common to the endpoints |

![Follow TLS Stream showing the HTTP request, successful response, and server HTML](images/07-follow-tls-stream.png)

The response also lists TLS 1.3 cipher suites supported by the server. That capability list does not mean this connection used TLS 1.3; the packet capture identifies the exchange as TLS 1.2.

## Why decryption worked

RSA private-key decryption requires a matching server key, a supported protocol version, a non-resumed session, and an RSA key-exchange handshake. The original server command, captured handshake, and readable HTTP stream are consistent with that setup.

TLS 1.2 connections using ephemeral Diffie-Hellman, such as ECDHE, cannot be decrypted using the server private key alone. Neither can TLS 1.3. With suitable session secrets exported from an endpoint, Wireshark can decrypt those connections through a separate key-log method.

## Findings and defensive relevance

- **Decryption was demonstrated:** The HTTP request and server response are visible in the reconstructed stream.
- **Traffic stayed encrypted in transit:** Wireshark used the supplied key to display decrypted application data locally.
- **Key ownership alone is not enough:** The negotiated key exchange and available handshake evidence also matter.
- **A prior compromise is not required:** In this lab, I controlled the endpoints and provided my own key material.
- **The scope was limited:** This exercise did not test TLS 1.3 decryption, an enterprise inspection proxy, or malicious traffic.

For defensive investigations, this helps me determine whether packet contents can be examined with the available key material and when to turn to endpoint logs or network metadata instead.

## Skills demonstrated

- Local TLS service setup with OpenSSL
- Loopback packet capture
- TLS handshake interpretation
- Wireshark RSA key configuration
- HTTP request and response analysis
- Evidence-based technical documentation

## Additional original evidence

<details>
<summary>Certificate files and additional server response details</summary>

The file listing confirms that `server.crt` and `server.pem` were created. The second screenshot continues the HTML response with session and server statistics.

![Terminal listing of the generated certificate and private-key files](images/02-certificate-files.png)

![Additional server session details visible in Follow TLS Stream](images/08-server-session-details.png)

</details>

## References

- [Wireshark TLS decryption documentation](https://wiki.wireshark.org/TLS)
- [OpenSSL s_server documentation](https://docs.openssl.org/3.5/man1/openssl-s_server/)
- [OpenSSL s_client documentation](https://docs.openssl.org/3.5/man1/openssl-s_client/)

The screenshots are original evidence extracted from my lab document. This repository contains the writeup and screenshots; private keys, session-secret logs, and raw capture files are not included.

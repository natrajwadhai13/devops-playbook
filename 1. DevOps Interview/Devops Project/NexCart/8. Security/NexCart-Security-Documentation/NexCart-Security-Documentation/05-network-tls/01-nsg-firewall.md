---
title: "• 05-network-tls"
parent: "• Security_Documentation"
grand_parent: "8. Security"
grand_grand_parent: "NexCart"
grand_grand_grand_parent: "• Devops Project"
grand_grand_grand_grand_parent: "1. DevOps"
nav_order: 6
---

- 01-nsg-firewall
- 02-tls-ssl

# NSG and Firewall

## THEORY

Azure Network Security Groups control inbound/outbound network traffic at supported network interfaces/subnets. Firewalls provide centralized traffic control.

Principles:
- least exposure
- required ports only
- restrict source ranges
- restrict direction
- monitor denied traffic

## NEXCART
```text
Internet -> Application Gateway -> NGINX -> AKS -> Services
```

Databases should not be directly exposed to the Internet.

## INTERVIEW
"I use controlled ingress paths and network rules to reduce public exposure. Internal databases and services should not be directly Internet-facing."

===============================================


# TLS / SSL

## THEORY

TLS provides encryption, integrity and endpoint authentication. SSL is the older predecessor.

Key terms:
- certificate
- private key
- public key
- CA
- CSR
- certificate chain
- TLS handshake
- expiry

## NEXCART

```text
Client -> HTTPS -> Application Gateway/NGINX -> application
```

Kubernetes TLS secret:
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: nexcart-tls
  namespace: nexcart
type: kubernetes.io/tls
```

Never commit private keys.

## VERIFICATION
```bash
openssl s_client -connect <host>:443
```

Check certificate issuer, expiry, SAN/hostname and chain.

## TROUBLESHOOTING
Expired certificate -> renew and verify.
Hostname mismatch -> certificate SAN must match hostname.
Incomplete chain -> provide required intermediate certificates.

## INTERVIEW
"TLS protects communication and authenticates endpoints using certificates. Private keys must be protected and never committed to Git."

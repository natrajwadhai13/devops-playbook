---
title: "• Fortify SAST Implementation"
parent: "• Security_Implementation"
grand_parent: "8. Security"
grand_grand_parent: "NexCart"
grand_grand_grand_parent: "• Devops Project"
grand_grand_grand_grand_parent: "1. DevOps"
nav_order: 6
---

# 06 - Fortify SAST Implementation

## 1. Implementation Status

**Not implemented.**

This note is intentionally included so the project documentation records the exact state rather than falsely claiming a Fortify implementation.

## 2. What Was Checked

On the fresh `jenkins-node` VM:

```bash
sourceanalyzer -version
```

Result:

```text
sourceanalyzer: command not found
```

Also:

```bash
fortifyclient -version
```

Result:

```text
fortifyclient: command not found
```

The expected installer files were also not present:

```bash
ls -lh /tmp/Fortify*
```

Result:

```text
ls: cannot access '/tmp/Fortify*': No such file or directory
```

## 3. Package Manager Check

Attempted:

```bash
apt install fortify
```

Result:

```text
No apt package "fortify"
E: Unable to locate package fortify
```

This is not the correct way to install licensed Fortify SCA.

## 4. Internet Connectivity Check

The VM was able to reach the vendor website:

```bash
curl -I https://www.microfocus.com
```

The request returned an HTTP redirect, confirming Internet connectivity to the site.

This does **not** mean Fortify SCA is freely downloadable or that the licensed installer is available to the VM.

## 5. Docker Check

An attempt was made to use:

```bash
docker pull fortify/sca
```

Result:

```text
pull access denied for fortify/sca, repository does not exist or may require 'docker login'
```

No Fortify SCA installation was completed from Docker.

## 6. Correct Project Decision

Fortify is **parked for now**.

Do not:

- claim Fortify was implemented
- use an unverified third-party image
- put a fake Fortify package in the project
- invent an installation command
- add an unverified SSC upload command

For a real enterprise implementation, use the organization's licensed Fortify/OpenText distribution and the organization's Fortify SSC/ScanCentral environment.

## 7. Future Implementation Target

When licensed access becomes available:

```text
GitLab
   |
   v
GitLab Runner
   |
   v
Fortify SCA
   |
   +--> sourceanalyzer translation
   |
   +--> sourceanalyzer scan
   |
   v
NexCart.fpr
   |
   v
Fortify SSC / ScanCentral
```

## 8. Information Required Before Installation

Before writing the real installation procedure, collect:

```text
1. Fortify SCA installer/package
2. Fortify SCA version
3. License method
4. Fortify SSC URL/version if used
5. SSC authentication method
6. Whether the company uses ScanCentral
7. Whether CI runs SCA locally or submits to ScanCentral
```

## 9. GitLab Variables - Future

Potential variable names:

```text
FORTIFY_SSC_URL
FORTIFY_SSC_TOKEN
FORTIFY_APP_NAME
FORTIFY_APP_VERSION
```

Do not create or use these until the actual company Fortify architecture is known.

## 10. Future GitLab File

When implemented, keep it separate:

```text
ci/fortify.yml
```

Then enable from the main pipeline:

```yaml
include:
  - local: "ci/fortify.yml"
```

## 11. Project Statement

> "Fortify is not currently implemented in NexCart. I attempted to establish the installation path on the fresh Jenkins-node VM, but no licensed Fortify SCA package or Fortify environment was available. I therefore parked the integration rather than using an unverified package or claiming an implementation that was not performed."

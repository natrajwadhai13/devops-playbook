---
title: "• security-errors"
parent: "• Security_Documentation"
grand_parent: "8. Security"
grand_grand_parent: "NexCart"
grand_grand_grand_parent: "• Devops Project"
grand_grand_grand_grand_parent: "1. DevOps"
nav_order: 8
---


# NexCart Security Troubleshooting

## Dependency-Check
Problem -> data directory permission denied.
Root Cause -> gitlab-runner lacked permission.
Solution -> `sudo mkdir -p /opt/dependency-check/data` and `sudo chown -R gitlab-runner:gitlab-runner /opt/dependency-check/data`.
Verification -> CI job passes and reports are generated.

## SonarQube
Problem -> No route to host.
Root Cause -> SonarQube VM was powered off.
Solution -> start VM when SonarQube practice is needed; current job uses `allow_failure: true`.
Verification -> `curl -I http://192.168.16.131:9000`.

## Nexus
Problem -> username is empty.
Root Cause -> protected CI variables unavailable to branch.
Solution -> review variable protection/environment/name.
Verification -> nexus-push succeeds without printing secret.

Problem -> HTTP response to HTTPS client.
Root Cause -> HTTP Nexus registry vs Docker HTTPS default.
Solution -> configure `192.168.16.130:8082` as an insecure registry and restart Docker.
Verification -> `docker login 192.168.16.130:8082`.

## ZAP
Problem -> report permission error.
Root Cause -> mounted directory not writable.
Solution -> `mkdir -p zap-reports; chmod 777 zap-reports`.
Verification -> report appears in artifacts.

Problem -> ZAP cannot reach frontend.
Root Cause -> container-to-host routing.
Solution -> use Docker bridge gateway as target.
Verification -> curl target from a container.

## Trivy
Problem -> image not found.
Root Cause -> image absent/different tag.
Solution -> `docker images | grep nexcart`.
Verification -> `docker image inspect <image>`.

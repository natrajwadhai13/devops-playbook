---
title: "• SonarQube"
parent: "• Security_Documentation"
grand_parent: "8. Security"
grand_grand_parent: "NexCart"
grand_grand_grand_parent: "• Devops Project"
grand_grand_grand_grand_parent: "1. DevOps"
nav_order: 3
---
# SonarQube

## THEORY

SonarQube performs static analysis and code-quality analysis.

Key concepts:
- Project
- Quality Profile
- Quality Gate
- Bug
- Vulnerability
- Code Smell
- Security Hotspot
- Coverage
- Duplication
- Security/maintainability/reliability ratings

## NEXCART IMPLEMENTATION

```text
GitLab -> GitLab Runner -> SonarScanner -> SonarQube -> NexCart
```

Configuration:
```properties
sonar.projectKey=NexCart
sonar.projectName=NexCart
sonar.projectVersion=1.0
sonar.sources=application
sonar.exclusions=**/node_modules/**,**/target/**,**/.venv/**,**/__pycache__/**,**/dist/**
sonar.sourceEncoding=UTF-8
```

Current job:
```yaml
sonarqube:
  stage: security
  tags:
    - nexcart-runner
  allow_failure: true
  script:
    - /opt/sonar-scanner/bin/sonar-scanner
      -Dsonar.host.url=http://192.168.16.131:9000
      -Dsonar.login="$SONAR_TOKEN"
      # -Dsonar.qualitygate.wait=true
```

Current lab decision: Quality Gate enforcement is disabled and `allow_failure` remains true because the SonarQube VM is not always running.

## TROUBLESHOOTING

**Problem:** `No route to host`  
**Root Cause:** SonarQube is on `k8s-master`, which may be powered off.  
**Solution:** start the VM when Sonar practice is needed.  
**Verification:**
```bash
curl -I http://192.168.16.131:9000
```

## INTERVIEW
"I use SonarQube for static analysis and code quality. GitLab Runner runs SonarScanner and sends the analysis to SonarQube. In this lab it is optional because the SonarQube VM is not always running."

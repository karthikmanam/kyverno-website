---
title: "Verify Image GCP KMS"
category: Software Supply Chain Security
version: 1.8.1
subject: Pod
policyType: "verifyImages"
description: >
    Using the Cosign project, OCI images may be signed to ensure supply chain security is maintained. Those signatures can be verified before pulling into a cluster. This policy checks the signature of an image repo called ghcr.io/kyverno/test-verify-image to ensure it has been signed by verifying its signature against the provided public key. This policy serves as an illustration for how to configure a similar rule and will require replacing with your image(s) and keys.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/verify-image-gcpkms/verify-image-gcpkms.yaml" target="-blank">/other/verify-image-gcpkms/verify-image-gcpkms.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: verify-image-gcpkms
  annotations:
    policies.kyverno.io/title: Verify Image GCP KMS
    policies.kyverno.io/category: Software Supply Chain Security
    policies.kyverno.io/severity: medium
    policies.kyverno.io/subject: Pod
    policies.kyverno.io/minversion: 1.8.1
    policies.kyverno.io/description: >-
      Using the Cosign project, OCI images may be signed to ensure supply chain
      security is maintained. Those signatures can be verified before pulling into
      a cluster. This policy checks the signature of an image repo called
      ghcr.io/kyverno/test-verify-image to ensure it has been signed by verifying
      its signature against the provided public key. This policy serves as an illustration for
      how to configure a similar rule and will require replacing with your image(s) and keys.
spec:
  validationFailureAction: Audit
  background: false
  rules:
    - name: verify-image
      match:
        any:
        - resources:
            kinds:
            - Pod
      verifyImages:
      - imageReferences:
        - "ghcr.io/kyverno/test-verify-image:*"
        attestors:
        - count: 1
          entries:
          - keys:
              kms: gcpkms://projects/omni-163105/locations/asia-south1/keyRings/first-ring/cryptoKeys/jerry/versions/1
```

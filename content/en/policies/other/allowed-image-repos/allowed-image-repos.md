---
title: "Allowed Image Repositories"
category: Other
version: 
subject: Pod
policyType: "validate"
description: >
    In addition to restricting the image registry from which images are pulled, in some cases and environments it may be required to also restrict which image repositories are used,  for example in some restricted Namespaces. This policy ensures that the only allowed image repositories present in a given Pod, across any container type, come from the designated list.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/allowed-image-repos/allowed-image-repos.yaml" target="-blank">/other/allowed-image-repos/allowed-image-repos.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: allowed-image-repos
  annotations:
    policies.kyverno.io/title: Allowed Image Repositories
    policies.kyverno.io/category: Other
    policies.kyverno.io/severity: medium
    kyverno.io/kyverno-version: 1.9.0
    kyverno.io/kubernetes-version: "1.24"
    policies.kyverno.io/subject: Pod
    policies.kyverno.io/description: >-
      In addition to restricting the image registry from which images are pulled, in some cases
      and environments it may be required to also restrict which image repositories are used, 
      for example in some restricted Namespaces. This policy ensures that the only allowed
      image repositories present in a given Pod, across any container type, come from the
      designated list.
spec:
  validationFailureAction: Audit
  background: false
  rules:
    - name: good-repos
      match:
        any:
        - resources:
            kinds:
              - Pod
      validate:
        message: >-
          All images in this Pod must come from an authorized repository.
        deny:
          conditions:
            all:
            - key: "{{ images.[containers, initContainers, ephemeralContainers][].*.name[] }}"
              operator: AnyNotIn
              value:
              - myknownimage
              - kyverno

```

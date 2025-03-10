---
title: "Require Image Source"
category: Other
version: 1.6.0
subject: Pod
policyType: "validate"
description: >
    Images can be built from a variety of source control locations and the name does not necessarily indicate this mapping. Ensuring that known good repositories are the source of images helps ensure supply chain security. This policy checks the container images and ensures that they specify the source in either a label `org.opencontainers.image.source` or a newer annotation in the manifest of the same name.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/require-image-source/require-image-source.yaml" target="-blank">/other/require-image-source/require-image-source.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-image-source
  annotations:
    policies.kyverno.io/title: Require Image Source
    policies.kyverno.io/category: Other
    policies.kyverno.io/severity: medium
    kyverno.io/kyverno-version: 1.7.0
    policies.kyverno.io/minversion: 1.6.0
    kyverno.io/kubernetes-version: "1.23"
    policies.kyverno.io/subject: Pod
    policies.kyverno.io/description: >-
      Images can be built from a variety of source control locations and
      the name does not necessarily indicate this mapping. Ensuring that known good
      repositories are the source of images helps ensure supply chain security. This
      policy checks the container images and ensures that they specify the source in
      either a label `org.opencontainers.image.source` or a newer annotation in the
      manifest of the same name.
spec:
  validationFailureAction: Audit
  rules:
  - name: check-source
    match:
      any:
      - resources:
          kinds:
          - Pod
    preconditions:
      all:
      - key: "{{request.operation || 'BACKGROUND'}}"
        operator: NotEquals
        value: DELETE
    validate:
      message: "The image source must be specified in a label or annotation."
      foreach:
      - list: "request.object.spec.containers"
        context: 
        - name: imageData
          imageRegistry: 
            reference: "{{ element.image }}"
            jmesPath: "{labels: configData.config.Labels, annotations: manifest.annotations}"
        deny:
          conditions:
            all:
              - key: "{{ imageData.labels.\"org.opencontainers.image.source\" || '' }}"
                operator: NotEquals
                value: "*?"
              - key: "{{ imageData.annotations.\"org.opencontainers.image.source\" || '' }}"
                operator: NotEquals
                value: "*?"
```

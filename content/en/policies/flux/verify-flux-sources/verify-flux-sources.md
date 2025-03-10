---
title: "Verify Flux Sources"
category: Flux
version: 1.6.0
subject: GitRepository, Bucket, HelmRepository, ImageRepository
policyType: "validate"
description: >
    Flux source APIs include a number of different sources such as GitRepository, Bucket, HelmRepository, and ImageRepository resources. Each of these by default can be pointed to any location. In a production environment, it may be desired to restrict these to only known sources to prevent accessing outside sources. This policy verifies that each of the Flux sources comes from a trusted location.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//flux/verify-flux-sources/verify-flux-sources.yaml" target="-blank">/flux/verify-flux-sources/verify-flux-sources.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: verify-flux-sources
  annotations:
    policies.kyverno.io/title: Verify Flux Sources
    policies.kyverno.io/category: Flux
    policies.kyverno.io/severity: medium
    kyverno.io/kyverno-version: 1.6.2
    policies.kyverno.io/minversion: 1.6.0
    kyverno.io/kubernetes-version: "1.23"
    policies.kyverno.io/subject: GitRepository, Bucket, HelmRepository, ImageRepository
    policies.kyverno.io/description: >-
      Flux source APIs include a number of different sources such as
      GitRepository, Bucket, HelmRepository, and ImageRepository resources. Each of these
      by default can be pointed to any location. In a production environment,
      it may be desired to restrict these to only known sources to prevent
      accessing outside sources. This policy verifies that each of the Flux
      sources comes from a trusted location.
spec:  
  validationFailureAction: Audit
  rules:
    - name: flux-github-repositories
      match:
        any:
        - resources:
            kinds:
              - GitRepository
      exclude:
        any:
        - resources:
            namespaces:
              - flux-system
      validate:
        message: ".spec.url must be from a repository within the myorg organization."
        pattern:
          spec:
            url: "https://github.com/myorg/?* | ssh://git@github.com:myorg/?*"
    - name: flux-buckets
      match:
        any:
        - resources:
            kinds:
              - Bucket
      exclude:
        any:
        - resources:
            namespaces:
              - flux-system
      validate:
        message: ".spec.endpoint must reference an address within the myorg organization."
        pattern:
          spec:
            endpoint: "*.myorg.com"
    - name: flux-helm-repositories
      match:
        any:
        - resources:
            kinds:
              - HelmRepository
      exclude:
        any:
        - resources:
            namespaces:
              - flux-system
      validate:
        message: ".spec.url must be from a repository within the myorg organization."
        pattern:
          spec:
            url: "https://?*.myorg.com/*"
    - name: flux-image-repositories
      match:
        any:
        - resources:
            kinds:
              - ImageRepository
      exclude:
        any:
        - resources:
            namespaces:
              - flux-system
      validate:
        message: ".spec.image must be from an image repository within the myorg organization."
        pattern:
          spec:
            image: "ghcr.io/myorg/*"

```

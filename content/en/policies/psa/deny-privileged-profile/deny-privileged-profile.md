---
title: "Deny Privileged Profile"
category: Pod Security Admission
version: 1.6.0
subject: Namespace
policyType: "validate"
description: >
    When Pod Security Admission (PSA) is enforced at the cluster level via an AdmissionConfiguration file which defines a default level at baseline or restricted, setting of a label at the `privileged` profile will effectively cause unrestricted workloads in that Namespace, overriding the cluster default. This may effectively represent a circumvention attempt and should be closely controlled. This policy ensures that only those holding the cluster-admin ClusterRole may create Namespaces which assign the label `pod-security.kubernetes.io/enforce=privileged`.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//psa/deny-privileged-profile/deny-privileged-profile.yaml" target="-blank">/psa/deny-privileged-profile/deny-privileged-profile.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: deny-privileged-profile
  annotations:
    policies.kyverno.io/title: Deny Privileged Profile
    policies.kyverno.io/category: Pod Security Admission
    policies.kyverno.io/severity: medium
    kyverno.io/kyverno-version: 1.7.1
    policies.kyverno.io/minversion: 1.6.0
    kyverno.io/kubernetes-version: "1.24"
    policies.kyverno.io/subject: Namespace
    policies.kyverno.io/description: >-
      When Pod Security Admission (PSA) is enforced at the cluster level
      via an AdmissionConfiguration file which defines a default level at
      baseline or restricted, setting of a label at the `privileged` profile
      will effectively cause unrestricted workloads in that Namespace, overriding
      the cluster default. This may effectively represent a circumvention attempt
      and should be closely controlled. This policy ensures that only those holding
      the cluster-admin ClusterRole may create Namespaces which assign the label
      `pod-security.kubernetes.io/enforce=privileged`.
spec:
  validationFailureAction: Audit
  background: false
  rules:
  - name: check-privileged
    match:
      any:
      - resources:
          kinds:
            - Namespace
          selector:
            matchLabels:
              pod-security.kubernetes.io/enforce: privileged
    exclude:
      any:
      - clusterRoles:
        - cluster-admin
    validate:
      message: Only cluster-admins may create Namespaces that allow setting the privileged level.
      deny: {}


```

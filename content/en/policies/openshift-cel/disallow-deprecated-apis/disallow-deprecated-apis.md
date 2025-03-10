---
title: "Disallow deprecated APIs in CEL expressions"
category: OpenShift in CEL
version: 1.11.0
subject: ClusterRole,ClusterRoleBinding,Role,RoleBinding,RBAC
policyType: "validate"
description: >
    OpenShift APIs are sometimes deprecated and removed after a few releases. As a best practice, older API versions should be replaced with newer versions. This policy validates for APIs that are deprecated or scheduled for removal. Note that checking for some of these resources may require modifying the Kyverno ConfigMap to remove filters.      
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//openshift-cel/disallow-deprecated-apis/disallow-deprecated-apis.yaml" target="-blank">/openshift-cel/disallow-deprecated-apis/disallow-deprecated-apis.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: disallow-deprecated-apis
  annotations:
    policies.kyverno.io/title: Disallow deprecated APIs in CEL expressions
    policies.kyverno.io/category: OpenShift in CEL 
    policies.kyverno.io/severity: medium
    kyverno.io/kyverno-version: 1.11.0
    policies.kyverno.io/minversion: 1.11.0
    kyverno.io/kubernetes-version: "1.26-1.27"
    policies.kyverno.io/subject: ClusterRole,ClusterRoleBinding,Role,RoleBinding,RBAC
    policies.kyverno.io/description: >-
      OpenShift APIs are sometimes deprecated and removed after a few releases.
      As a best practice, older API versions should be replaced with newer versions.
      This policy validates for APIs that are deprecated or scheduled for removal.
      Note that checking for some of these resources may require modifying the Kyverno
      ConfigMap to remove filters.      
spec:
  validationFailureAction: Enforce
  background: true
  rules:
  - name: check-deprecated-apis
    match:
      any:
      - resources:
          kinds:
          - authorization.openshift.io/v1/ClusterRole
          - authorization.openshift.io/v1/ClusterRoleBinding
          - authorization.openshift.io/v1/Role
          - authorization.openshift.io/v1/RoleBinding
          operations:
          - CREATE
          - UPDATE
    validate:
      cel:
        expressions:
          - expression: "false"
            messageExpression: >-
              object.apiVersion + '/' + object.kind + ' is deprecated.'


```

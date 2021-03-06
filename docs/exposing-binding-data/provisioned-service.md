---
sidebar_position: 2
---

# Provisioned Service

As a backing service author or provider, you can use this method to be compliant
with the Service Binding specification.

[Provisioned Service][provisioned-service] represents a backing service custom
resource with a reference to a Secret resource.  The Secret resource provides
all the binding metadata values required for an application to connect to the
backing service.  You must provide the reference to the Secret service resource
in the `.status.binding.name` attribute of the custom resource.

The following examples show an `AccountService` resource that represents a
backing service and a `Secret` service resource. As a backing service author,
you can create a `Secret` service resource with all the necessary connection
details and refer them using the `.status.binding.name` field.

## Example: AccountService resource

This example shows an `AccountService` resource with relavant details:

```
apiVersion: example.com/v1alpha1
kind: AccountService
name: prod-account-service
spec:
  ...
status:
  binding:
    name: production-db-secret
```

This is the referenced Secret:

```
apiVersion: v1
kind: Secret
metadata:
  name: production-db-secret
data:
  password: "MTBz"
  username: "Z3Vlc3Q="
```

When creating service binding, directly give the details of the `AccountService`
resource in the following `ServiceBinding` specifications:

## Example: Specification with the binding.operators.coreos.com API

```
apiVersion: binding.operators.coreos.com/v1alpha1
kind: ServiceBinding
metadata:
  name: account-service
spec:
  ...
  services:
  - group: "example.com"
    version: v1alpha1
    kind: AccountService
    name: prod-account-service
  application:
    name: nodejs-app
    group: apps
    version: v1
    resource: deployments
```

## Example: Specification that is compliant with the service.binding API

```
apiVersion: service.binding/v1alpha2
kind: ServiceBinding
metadata:
  name: account-service

spec:
  ...
  service:
    apiVersion: example.com/v1alpha1
    kind: AccountService
    name: prod-account-service
  application:
    apiVersion: apps/v1
    kind: Deployment
    name: nodejs-app
```

All the keys in the `Secret` resource are exposed as binding metadata and
projected into the application.

[provisioned-service]: https://github.com/k8s-service-bindings/spec#provisioned-service

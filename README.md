# BigQuery-Dataset-name-constraints-clash

CRD: 
- apiVersion: bigquery.gcp.upbound.io/v1beta1
- kind: Dataset
- doc: https://marketplace.upbound.io/providers/upbound/provider-gcp/v0.35.0/resources/bigquery.gcp.upbound.io/Dataset/v1beta1

Problem:
- It appears the Dataset name is derived from the manifest's "metadata.name" field and not a field in the CRD's spec (something like "spec.forProvider.name")
- Using the "metadata.name" could work if K8s and Google didn't have different name constraints that clash.

Google contraint error when using dash:
```
apply failed: Error creating Dataset: googleapi: Error 400: Invalid dataset ID "example-dataset". 
Dataset IDs must be alphanumeric (plus underscores) and must be at most 1024 characters long.
```
Google constraint error when using period:
```
observe failed: cannot run refresh: refresh failed: Invalid resource name: 
A name must start with a letter or underscore and may contain only letters, digits, underscores, and dashes.
```
K8s constraint error when using underscore:
```
The Dataset "example_dataset" is invalid: metadata.name: Invalid value: "example_dataset": a lowercase RFC 1123 subdomain 
must consist of lower case alphanumeric characters, '-' or '.', and must start and end with an alphanumeric character 
(e.g. 'example.com', regex used for validation is '[a-z0-9]([-a-z0-9]*[a-z0-9])?(\.[a-z0-9]([-a-z0-9]*[a-z0-9])?)*')
```
Possible Solution:
- Add a "spec.forProvider.name" field so that the Dataset name is not derived from "metadata.name"
- There is a "spec.forProvider.friendlyName" field currently, but filling it out does not solve the issue.
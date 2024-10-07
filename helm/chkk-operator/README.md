# Chkk Operator

This Helm chart is used to deploy the Chkk Operator on a Kubernetes cluster.

## Prerequisites

- Kubernetes 1.16+
- Helm 3.0+
- Access to a Kubernetes cluster
- `kubectl` configured to communicate with your cluster
- `oc` CLI configured to communicate with your OpenShift cluster (if using OpenShift)

## Get Repository Info

To add the Chkk Helm repository, use the following command:

```bash
helm repo add chkk https://helm.chkk.io
helm repo update
```

## Install Chart

To install the chart with the release name `chkk-operator`, use the following command:

```bash
helm install chkk-operator chkk/chkk-operator --set secret.create=true --set secret.chkkAccessToken=<ACCESS-TOKEN>
```

Login to your [Chkk Dashboard](https://app.chkk.io) and get your access token.

The command deploys the Chkk Operator on the Kubernetes cluster with the default configuration. The [configuration](#configuration) section lists the parameters that can be configured during installation.

## Configuration

The following table lists the configurable parameters of the Chkk Operator chart and their default values.

| Parameter                  | Description                        | Default                        |
| -------------------------- | ---------------------------------- | ------------------------------ |
| `image.repository`         | Image repository                   | `public.ecr.aws/chkk/operator` |
| `image.tag`                | Image tag                          | `v0.0.12`                      |
| `image.pullPolicy`         | Image pull policy                  | `Always`                       |
| `replicaCount`             | Number of replicas                 | `1`                            |
| `revisionHistoryLimit`     | Revision history limit             | `2`                            |
| `secret.create`            | Create a new secret                | `true`                         |
| `secret.chkkAccessToken`   | Chkk access token                  | `CHKK-ACCESS-TOKEN`            |
| `serviceAccount.create`    | Create a service account           | `true`                         |
| `serviceAccount.name`      | Service account name               | `chkk-operator-sa`             |
| `disableAnalytics`         | Disable analytics                  | `false`                        |
| `proxy.http_proxy`         | HTTP proxy                         | `""`                           |
| `proxy.https_proxy`        | HTTPS proxy                        | `""`                           |
| `proxy.no_proxy`           | No proxy                           | `""`                           |
| `tolerations`              | Node tolerations                   | See `values.yaml`              |
| `nodeSelector`             | Node labels for pod assignment     | `{}`                           |
| `affinity`                 | Pod affinity                       | See `values.yaml`              |
| `securityContext`          | Security Context for the pod       | See `values.yaml`              |
| `containerSecurityContext` | Security Context for the container | See `values.yaml`              |

### Custom Secret

To use a custom secret, set `secret.create` to `false` and provide the `secret.ref.secretName` and `secret.ref.keyName` in the `values.yaml` file.

**Example:**

```yaml
secret:
  create: false
  ref:
    secretName: secret-name
    keyName: CHKK_ACCESS_TOKEN
```

### Custom RBAC

To customize the RBAC settings, modify the `serviceAccount` parameters in the `values.yaml` file. You can specify whether to create a new service account and provide a custom name.

**Example:**

```yaml
serviceAccount:
  create: false
  name: chkkagent-custom-sa
```

### Custom Image

To use a custom image, update the `image.repository` and `image.tag` in the `values.yaml` file. You can also set the `image.pullPolicy` to control when the image is pulled.

**Example:**

```yaml
image:
  repository: custom-repo/chkk/operator
  tag: v0.1.0
  pullPolicy: IfNotPresent
```

### Tolerations

To schedule the Chkk Operator on nodes with specific taints, configure the `tolerations` section in the `values.yaml` file. You can specify the key, operator, value, and effect for each toleration.

**Example:**

```yaml
tolerations:
  - key: "example.com/special-taint"
    operator: "Equal"
    value: "true"
    effect: "NoSchedule"
```

For more detailed information, refer to the [Kubernetes documentation on taints and tolerations](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/).

Once the Chkk Operator is installed, you can proceed to deploying a ChkkAgent and configure the Chkk Kubernetes Connector.

# ChkkAgent

## Overview

The `ChkkAgent` is a custom resource that is managed by the Chkk Operator. It is used to configure and manage the Chkk Kubernetes Connector.

By deploying the `ChkkAgent` resource, you can control key aspects of the Chkk Kubernetes Connector, such as image configuration, Kubernetes resource filtering, and security contexts.

## Specification

### API Version

- **`apiVersion: k8s.chkk.io/v1beta1`**

### Kind

- **`kind: ChkkAgent`**

### Spec Fields

#### 1. `agentOverride`

Allows overriding the default agent configuration settings, offering flexibility to adapt to various cluster requirements:

- **`activeDeadlineSeconds`**: Time (in seconds) after which the job will be terminated if it hasn't finished.
- **`backoffLimit`**: Maximum number of retries for failed jobs before considering them failed.
- **`completionMode`**: Method to track pod completions (`NonIndexed` or `Indexed`).
- **`completions`**: Number of pods that must successfully finish the job.
- **`concurrencyPolicy`**: Defines how concurrent executions of a job are handled (`Allow`, `Forbid`, or `Replace`).
- **`createRbac`**: Automatically create the necessary RBAC resources (Roles/ClusterRoles).
- **`failedJobsHistoryLimit`**: Specifies how many failed job executions to retain for reference.
- **`image`**: Configuration of the Chkk Agent's container image.
- **`managerImage`**: Configuration of the Chkk Agent Manager's container image.
- **`template`**: Pod template for the Chkk Agent, allowing detailed security and resource specifications.

#### 2. `global`

Global settings for configuring Chkk Agent operations across the cluster:

- **`clusterEnvironment`**: The environment name (e.g., production, development).
- **`clusterName`**: A unique identifier for the cluster.
- **`credentials`**: API credentials for authenticating the agent with the Chkk service.
- **`endpoint`**: URL of the Chkk API.
- **`filter`**: Rules for including or excluding Kubernetes resources during scans.
- **`logLevel`**: Sets the verbosity of logging (e.g., `trace`, `debug`, `info`, `warn`, `error`, `critical`, `off`).
- **`podAnnotationsAsTags`**: Mapping of Kubernetes pod annotations to Chkk tags for better traceability.
- **`podLabelsAsTags`**: Mapping of Kubernetes pod labels to Chkk tags.
- **`tags`**: Custom tags to apply across clusters and associated resources.
- **`updates`**: Controls agent auto-updates, allowing for specification of update frequency.

### Status Fields

The `status` section offers insight into the operational status of the ChkkAgent. Key fields include:

- **`agent`**: Indicates the current state of the associated CronJob.
- **`conditions`**: Provides a list of conditions describing the ChkkAgent's state.
- **`lastScanTime`**: Timestamp of the last scan performed by the agent.
- **`latestUpdateState`**: Information about the last update applied to the ChkkAgent.

For the full field definition, please refer to the [ChkkAgent CRD YAML](crds.yaml).

## Deployment

To deploy the `ChkkAgent`, create a YAML file containing the necessary configuration and apply it to your Kubernetes cluster:

### Example: Basic ChkkAgent Deployment

```yaml
apiVersion: k8s.chkk.io/v1beta1
kind: ChkkAgent
metadata:
  name: chkk-agent
  namespace: chkk-system
```

## Example: Custom Image and Security Context

```yaml
apiVersion: k8s.chkk.io/v1beta1
kind: ChkkAgent
metadata:
  name: chkk-agent
  namespace: chkk-system
spec:
  agentOverride:
    image:
      name: public.ecr.aws/chkk/chkk-agent:v0.1.12
    managerImage:
      name: public.ecr.aws/chkk/chkk-agent-manager:v0.1.12
    template:
      securityContext:
        runAsNonRoot: true
        runAsUser: 12000
        runAsGroup: 12000
        fsGroup: 12000
        seccompProfile:
          type: RuntimeDefault
      container:
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop:
              - ALL
          readOnlyRootFilesystem: true
          runAsNonRoot: true
```

### Example: Custom Credentials with Secret

```yaml
apiVersion: k8s.chkk.io/v1beta1
kind: ChkkAgent
metadata:
  name: chkk-agent
  namespace: chkk-system
spec:
  global:
    credentials:
      accessTokenSecret:
        secretName: chkk-agent-token
        keyName: CHKK_ACCESS_TOKEN
```

### Example: Custom RBAC Configuration

```yaml
apiVersion: k8s.chkk.io/v1beta1
kind: ChkkAgent
metadata:
  name: chkk-agent
  namespace: chkk-system
spec:
  agentOverride:
    createRbac: true
    serviceAccountName: chkk-agent-custom-sa
```

The above configuration uses the ServiceAccount “chkk-agent-custom-sa“ for ChkkAgent. If you are planning to use a custom service account for ChkkAgent, make sure the associated ClusterRole has the following RBAC permissions:

```yaml
- apiGroups: ["batch"]
  resources: ["jobs", "cronjobs"]
  verbs: ["get", "create", "list", "update"]
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["get", "list", "watch"]
```

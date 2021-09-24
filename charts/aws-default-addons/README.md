# aws-default-addons

![Version: 0.1.0](https://img.shields.io/badge/Version-0.1.0-informational?style=flat-square) ![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square) ![AppVersion: 0.1.0](https://img.shields.io/badge/AppVersion-0.1.0-informational?style=flat-square)

A Helm chart for Kubernetes

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| accountID | string | `"XXXXXXXXXX"` |  |
| apiGateway.glooEdge.enabled | bool | `false` |  |
| apiGateway.kong.enabled | bool | `false` |  |
| apiGateway.krakend.enabled | bool | `false` |  |
| apiGateway.tyk.enabled | bool | `false` |  |
| domain | string | `"test.domain.com"` |  |
| ebs[0].name | string | `"gp3"` |  |
| ebs[0].parameters."csi.storage.k8s.io/fstype" | string | `"ext4"` |  |
| ebs[0].parameters.type | string | `"gp3"` |  |
| ebs[1].name | string | `"high-iops-ssd"` |  |
| ebs[1].parameters."csi.storage.k8s.io/fstype" | string | `"ext4"` |  |
| ebs[1].parameters.iopsPerGB | string | `"50"` |  |
| ebs[1].parameters.type | string | `"io1"` |  |
| ebs[2].name | string | `"medium-iops-ssd"` |  |
| ebs[2].parameters."csi.storage.k8s.io/fstype" | string | `"ext4"` |  |
| ebs[2].parameters.iopsPerGB | string | `"25"` |  |
| ebs[2].parameters.type | string | `"io1"` |  |
| ebs[3].name | string | `"throughput"` |  |
| ebs[3].parameters."csi.storage.k8s.io/fstype" | string | `"ext4"` |  |
| ebs[3].parameters.type | string | `"st1"` |  |
| ebs[4].name | string | `"cold-storage"` |  |
| ebs[4].parameters."csi.storage.k8s.io/fstype" | string | `"ext4"` |  |
| ebs[4].parameters.type | string | `"sc1"` |  |
| environmentName | string | `"aws-k8s-stack"` |  |
| monitoring.enabled | bool | `true` |  |
| nameOverride | string | `"aws-k8s-stack"` |  |
| openIDConnectProviderURL | string | `"https://oidc.eks.region.amazonaws.com/id/XXXXXXXXXXXXXX"` |  |
| region | string | `"us-east-1"` |  |

----------------------------------------------
Autogenerated from chart metadata using [helm-docs v1.5.0](https://github.com/norwoodj/helm-docs/releases/v1.5.0)
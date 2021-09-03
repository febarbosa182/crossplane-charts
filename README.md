# poc-automated-infrastructure

# Local 

## Requirements

 - [Kubectl](https://kubernetes.io/docs/tasks/tools/ "kubectl install page")
 - [Helm 3](https://helm.sh/docs/intro/install/ "Helm 3 install page")
 - [Minikube](https://minikube.sigs.k8s.io/docs/start/ "Minikube install page")
 - [AWS Account IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html "AWS IAM User Credentials file")

> **NOTE:**AWS User should have administrator access

## Base Configuration

Starts minikube and update context
```sh
# Starts minikube with default configuration
minikube start
# Set minikube to your current kubectl context
minikube update-context
```

Add necessary helm repositories
```sh
# Adding crossplane chart repository
helm repo add crossplane-stable https://charts.crossplane.io/stable
# Update chart repository index
helm repo update
# Install crossplane with necessary provider enabled
helm install crossplane \
    -n crossplane-system \
    crossplane-stable/crossplane \
    --version 1.3.1 \
    --set provider.packages={crossplane/provider-aws:v0.19.0}
```

Create a credentials file with your IAM User information:
```sh
[default]
aws_access_key_id=XXXXXXXXX
aws_secret_access_key=XXXXXXXXXXXXXXXXXXXXXXXXXXX
```
> Form more information [access here](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html)

## Stacks

### AWS Simple Stack
![Architecture](./diagrams/architecture.png)

Creating definied stack.
Replace the environment variables by the path of your previously create credentials file, desired [AWS Region](https://docs.aws.amazon.com/pt_br/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#concepts-available-regions) and especific environment name.
```sh
# This command use local aws chart stach
helm upgrade --install $ENVIRONMENT_NAME ./charts/aws-k8s-stack \
    --set-file creds=$YOUR_CREDENTIALS_FILE_PATH \
    --set region=$REGION \
    --set fullnameOverride=$ENVIRONMENT_NAME \ 
    --set nameOverride=$ENVIRONMENT_NAME
```

Getting created resources:
```sh
# Install crossplane kubectl addon
curl -sL https://raw.githubusercontent.com/crossplane/crossplane/master/install.sh | sh
# Getting managed resources
kubectl get managed
```

> To get managed resources in JSON format run "kubectl get managed -o json"

Deleting stack:
```sh
helm delete aws-k8s-stack
```

After cluster creation, addons instalation are enabled.
First get configuration and connection informations:
```sh
# Associate OIDC with cluster
eksctl utils associate-iam-oidc-provider --cluster aws-k8s-stack --approve
# Get OIDC URL
openIDConnectProviderURL=$(aws eks describe-cluster --name aws-k8s-stack --query "cluster.identity.oidc.issuer" --output text)
# Get OIDC ARN
```
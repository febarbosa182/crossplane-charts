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
helm upgrade --install crossplane \
    crossplane-stable/crossplane \
    -n crossplane-system \
    --version "1.3.1" \
    --set provider.packages="{crossplane/provider-aws:v0.19.0,crossplane/provider-helm:v0.8.0}"
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
# This command use local aws chart stack
helm upgrade --install $ENVIRONMENT_NAME ./charts/aws-k8s-stack \
    --set-file creds=$YOUR_CREDENTIALS_FILE_PATH \
    --set region=$REGION \
    --set fullnameOverride=$ENVIRONMENT_NAME \ 
    --set nameOverride=$ENVIRONMENT_NAME \
    --set route53.domain=$DOMAIN
```

Getting created resources:
```sh
# Install crossplane kubectl addon
curl -sL https://raw.githubusercontent.com/crossplane/crossplane/master/install.sh | sh
# Getting managed resources
kubectl get managed
```

> To get managed resources in JSON format run "kubectl get managed -o json"

Deleting stack (only needed if you dont proceed to addons install):
```sh
helm delete aws-k8s-stack
```

### AWS Addons Stack

After cluster creation, addons instalation are enabled.

 - [EBS CSI Driver](https://github.com/kubernetes-sigs/aws-ebs-csi-driver) 1.2.0

First get configuration and connection informations:
```sh
# Associate OIDC with cluster
eksctl utils associate-iam-oidc-provider --cluster aws-k8s-stack --approve --region $REGION 
# Get OIDC URL
OIDC_URL=$(aws eks describe-cluster --name aws-k8s-stack --query "cluster.identity.oidc.issuer" --region $REGION --output text)
# Get account ID
ACCOUNT_ID=$(aws sts get-caller-identity --query "Account")
```

Install definied addons stack
```sh
# This command use local aws default addons chart
helm upgrade --install $ENVIRONMENT_NAME-addons ./charts/aws-default-addons \
    --set nameOverride=$ENVIRONMENT_NAME \
    --set environmentName=$ENVIRONMENT_NAME \
    --set openIDConnectProviderURL=$OIDC_URL \
    --set region=$REGION \
    --set domain=$DOMAIN
```

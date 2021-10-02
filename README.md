# Amazon EKS
<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Contents**

  - [EKS CLUSTER CREATION WORKFLOW](#eks-cluster-creation-workflow)
- [Getting Started](#getting-started)
  - [Create an AWS Account](#create-an-aws-account)
  - [Create a Workspace](#create-a-workspace)
  - [Install Kubernetes Tools](#install-kubernetes-tools)
  - [Create an IAM role for your Workspace](#create-an-iam-role-for-your-workspace)
  - [Attach the IAM role to your Workspace](#attach-the-iam-role-to-your-workspace)
  - [Update IAM Settings for your Workspace](#update-iam-settings-for-your-workspace)
  - [Clone the service repos](#clone-the-service-repos)
  - [Create an AWS KMS Customer Managed Key](#create-an-aws-kms-customer-managed-key)
- [Launch Using EKSCTL](#launch-using-eksctl)
  - [Prerequisites](#prerequisites)
  - [Launch EKS](#launch-eks)
    - [Create an EKS Cluster](#create-an-eks-cluster)
  - [Test the Cluster](#test-the-cluster)
  - [Console Credentials](#console-credentials)
    - [Import your EKS Console credentials to your new cluster:](#import-your-eks-console-credentials-to-your-new-cluster)
- [Deploy the Kubernetes Dashboard](#deploy-the-kubernetes-dashboard)
  - [Deploy the dasboard](#deploy-the-dasboard)
  - [Access the Dashboard](#access-the-dashboard)
  - [Cleanup](#cleanup)
- [Deploy the example Microservices](#deploy-the-example-microservices)
  - [Deploy our sample applications](#deploy-our-sample-applications)
  - [Deploy NodeJS Backend api](#deploy-nodejs-backend-api)
  - [Deploy Crystal Backend API](#deploy-crystal-backend-api)
  - [Let's check Service Types](#lets-check-service-types)
  - [Ensure the ELB Service Role Exists](#ensure-the-elb-service-role-exists)
  - [Deploy Frontend Service](#deploy-frontend-service)
  - [Find the service address](#find-the-service-address)
  - [Scale the Backend Services](#scale-the-backend-services)
  - [Scale the Frontend](#scale-the-frontend)
  - [Cleanup the applications](#cleanup-the-applications)
- [Helm](#helm)
  - [Introduction](#introduction)
  - [Install Helm CLI](#install-helm-cli)
  - [Deploy nginx with Helm](#deploy-nginx-with-helm)
    - [Update the Chart repository](#update-the-chart-repository)
    - [Search Chart repositories](#search-chart-repositories)
    - [Add the Bitnami Repository](#add-the-bitnami-repository)
    - [Install bitname/nginx](#install-bitnamenginx)
    - [Clean Up](#clean-up)
  - [Deploy Example Microservices using Helm](#deploy-example-microservices-using-helm)
    - [Create a Chart](#create-a-chart)
    - [Customize Defaults](#customize-defaults)
      - [Replace hard-coded values with template directives](#replace-hard-coded-values-with-template-directives)
      - [Create a values.yaml file with our template defaults](#create-a-valuesyaml-file-with-our-template-defaults)
    - [Deploy the EKSDemo Chart](#deploy-the-eksdemo-chart)
      - [Use the dry-run flag to test our templates](#use-the-dry-run-flag-to-test-our-templates)
      - [Deploy the chart](#deploy-the-chart)
    - [Testing the service](#testing-the-service)
    - [Rolling Back](#rolling-back)
      - [Update the demo application chart with a breaking change](#update-the-demo-application-chart-with-a-breaking-change)
      - [Deploy the updated demo application chart:](#deploy-the-updated-demo-application-chart)
      - [Rollback the failed upgrade](#rollback-the-failed-upgrade)
    - [Clean Up](#clean-up-1)
- [Health Checks](#health-checks)
  - [Configure Liveness Probe](#configure-liveness-probe)
    - [Configure the Probe](#configure-the-probe)
    - [Introduce a Failure](#introduce-a-failure)
    - [Challenge:](#challenge)
  - [Configure Readiness probe](#configure-readiness-probe)
    - [Configure the Probe](#configure-the-probe-1)
    - [Introduce a Failure](#introduce-a-failure-1)
    - [Challenge:](#challenge-1)
  - [Clean Up](#clean-up-2)
- [Autoscaling our Applications and Cluster](#autoscaling-our-applications-and-cluster)
  - [Install kube-ops-view](#install-kube-ops-view)
  - [Configure Horizontal Pod Autoscaler (HPA)](#configure-horizontal-pod-autoscaler-hpa)
    - [Deploy the Metrics Server](#deploy-the-metrics-server)
  - [Scale and application with HPA](#scale-and-application-with-hpa)
    - [Deploy a Sample App](#deploy-a-sample-app)
    - [Create an HPA resource](#create-an-hpa-resource)
    - [Generate load to trigger scaling](#generate-load-to-trigger-scaling)
  - [Configure Cluster Autoscaler (CA)](#configure-cluster-autoscaler-ca)
    - [Configure the ASG](#configure-the-asg)
    - [IAM roles for service accounts](#iam-roles-for-service-accounts)
    - [Deploy the Cluster Autoscaler (CA)](#deploy-the-cluster-autoscaler-ca)
  - [Scale a cluster with CA](#scale-a-cluster-with-ca)
    - [Deploy a Sample App](#deploy-a-sample-app-1)
    - [Scale our ReplicaSet](#scale-our-replicaset)
  - [Clean Up](#clean-up-3)
- [Introduction to RBAC](#introduction-to-rbac)
  - [What is RBAC?](#what-is-rbac)
    - [Objectives for this module](#objectives-for-this-module)
  - [Install the test Pods](#install-the-test-pods)
  - [Create a user](#create-a-user)
  - [Map an IAM User to K8s](#map-an-iam-user-to-k8s)
  - [Test the new user](#test-the-new-user)
  - [Create the role and the role binding](#create-the-role-and-the-role-binding)
  - [Verify the role and binding](#verify-the-role-and-binding)
  - [Clean up](#clean-up)
- [Using IAM groups to manage Kubernetes cluster access](#using-iam-groups-to-manage-kubernetes-cluster-access)
  - [Kubernetes authenticationIn the intro to RBAC module, we have seen how we can give access to individual users to Kubernetes.](#kubernetes-authenticationin-the-intro-to-rbac-module-we-have-seen-how-we-can-give-access-to-individual-users-to-kubernetes)
  - [Create IAM Roles](#create-iam-roles)
  - [Create IAM Groups](#create-iam-groups)
    - [Create k8sAdmin IAM Group](#create-k8sadmin-iam-group)
    - [Create k8sDev IAM Group](#create-k8sdev-iam-group)
    - [Create k8sInteg IAM Group](#create-k8sinteg-iam-group)
  - [Create IAM Users](#create-iam-users)
  - [Configure Kubernetes RBAC](#configure-kubernetes-rbac)
    - [Create kubernetes namespaces](#create-kubernetes-namespaces)
    - [Configuring access to development namespace](#configuring-access-to-development-namespace)
    - [Configuring access to integration namespace](#configuring-access-to-integration-namespace)
  - [Configure Kubernetes Role Access](#configure-kubernetes-role-access)
    - [Gives Access to our IAM Roles to EKS Cluster](#gives-access-to-our-iam-roles-to-eks-cluster)
  - [Test EKS Access](#test-eks-access)
    - [Automate assumerole with aws cli](#automate-assumerole-with-aws-cli)
    - [Using AWS profiles with the Kubectl config file](#using-aws-profiles-with-the-kubectl-config-file)
      - [With dev profile](#with-dev-profile)
      - [Test with integ profile](#test-with-integ-profile)
      - [Test with admin profile](#test-with-admin-profile)
  - [Conclusion](#conclusion)
  - [Clean Up](#clean-up-4)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## EKS CLUSTER CREATION WORKFLOW
![Cluster Creation Workflow](imgs/1.svg "Cluster Creation Workflow")
Let's start with the first bullet point "Create EKS cluster".
- Here we're reducing all of the necessary steps to setup a Kubernetes cluster to one single bullet point.
- Rest of the steps are the same whether you use a DIY cluster or an EKS cluster.
- Creating an EKS cluster is for us simply running an command. You can compare this with the DIY steps, we've to bring up the ETCD nodes, control plane nodes, ETCD nodes will build a quoram and then the rest of the control place components will start working.
- Only then, the worker nodes can be added and services deployed.
- All of this happens by running a single command in EKS.
- What happens when you create your EKS cluster?
  ![Cluster Control Plane](imgs/2.svg "Control Plane")

- When you look at the EKS cluster you'll build, you'll not see the control plane nodes. You'll probably see the ENIs that are attached to different components of the cluster.

- EKS Architecture for Control Plane and Worker node communication
  ![EKS Architecture](imgs/3.svg "EKS Architecture")

- In the above architecture diagram, we use 2 VPCs
  - One that will consist of all of the control plane nodes (the one on the right)
  - The other will consist of all of the worker nodes.

- All of the communication happens using ENIs which makes it seem like the communication is all happening in the same VPC.

- The Network Load Balancer ensures that the communication between the Kubelet and the API server is appropriately distributed.

- Once your EKS cluster is ready, you get an API endpoint and you’d use [Kubectl](https://kubernetes.io/docs/reference/kubectl/kubectl/), community developed tool to interact with your cluster.
  ![High Level](imgs/4.svg "High Level")

- When you use the `kubectl` commands, the request essentially goes to the AWS managed EKS control plane and then distributes the traffic to the relevant worker nodes in the customer managed VPC.


# Getting Started
## Create an AWS Account
-  If you don’t already have an AWS account with Administrator access: [create one now by clicking here](https://aws.amazon.com/getting-started/)

- Once you have an AWS account, ensure you are following the remaining workshop steps as an IAM user with administrator access to the AWS account: [Create a new IAM user to use for the workshop](https://console.aws.amazon.com/iam/home?#/users$new).

- Enter the user details:
  ![User Details](imgs/5.png "User Details")

- Attach the AdministratorAccess IAM Policy:
  ![User Details](imgs/6.png "User Details")

- Click to create the new user:
  ![User Details](imgs/7.png "User Details")

- Take note of the login URL and save:
  ![User Details](imgs/8.png "User Details")


## Create a Workspace
- Launch Cloud9 in your closest region:
  - Oregon: https://us-west-2.console.aws.amazon.com/cloud9/home?region=us-west-2
  - Ireland: https://eu-west-1.console.aws.amazon.com/cloud9/home?region=eu-west-1
  - Ohio: https://us-east-2.console.aws.amazon.com/cloud9/home?region=us-east-2
  - Singapore: https://ap-southeast-1.console.aws.amazon.com/cloud9/home?region=ap-southeast-1

- Select Create environment
- Name it eksworkshop, click Next.
- Choose t3.small for instance type, take all default values and click Create environment
- When it comes up, customize the environment by:

  - Closing the Welcome tab
  ![Cloud9](imgs/cloud9-1.png "Cloud9")

- Opening a new terminal tab in the main work area
  ![Cloud9](imgs/cloud9-2.png "Cloud9")

- Closing the lower work area
  ![Cloud9](imgs/cloud9-3.png "Cloud9")
- Your workspace should now look like this
  ![Cloud9](imgs/cloud9-4.png "Cloud9")

- Increase the disk size on the Cloud9 instance.
  - __The following command adds more disk space to the root volume of the EC2 instance that Cloud9 runs on. Once the command completes, we reboot the instance and it could take a minute or two for the IDE to come back online.__
  ```python
  pip3 install --user --upgrade boto3
  export instance_id=$(curl -s http://169.254.169.254/latest/meta-data/instance-id)
  python -c "import boto3
  import os
  from botocore.exceptions import ClientError
  ec2 = boto3.client('ec2')
  volume_info = ec2.describe_volumes(
    Filters=[
        {
            'Name': 'attachment.instance-id',
            'Values': [
                os.getenv('instance_id')
            ]
        }
    ]
  )
  volume_id = volume_info['Volumes'][0]['VolumeId']
  try:
    resize = ec2.modify_volume(    
            VolumeId=volume_id,    
            Size=30
    )
    print(resize)
  except ClientError as e:
    if e.response['Error']['Code'] == 'InvalidParameterValue':
        print('ERROR MESSAGE: {}'.format(e))"
  if [ $? -eq 0 ]; then
    sudo reboot
  fi

  ```

## Install Kubernetes Tools
- Install kubectl
```
sudo curl --silent --location -o /usr/local/bin/kubectl \
   https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl

sudo chmod +x /usr/local/bin/kubectl
```

- Update awscli
Upgrade AWS CLI according to guidance in [AWS documentation](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-linux.html).
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```
- Install jq, envsubst (from GNU gettext utilities) and bash-completion
```
sudo yum -y install jq gettext bash-completion moreutils
```

- Install yq for yaml processing
```
echo 'yq() {
  docker run --rm -i -v "${PWD}":/workdir mikefarah/yq "$@"
}' | tee -a ~/.bashrc && source ~/.bashrc
```

- Verify the binaries are in the path and executable
```
for command in kubectl jq envsubst aws
  do
    which $command &>/dev/null && echo "$command in path" || echo "$command NOT FOUND"
  done
```

- Enable kubectl bash_completion
```
kubectl completion bash >>  ~/.bash_completion
. /etc/profile.d/bash_completion.sh
. ~/.bash_completion
```

- Set the AWS Load Balancer Controller version
```
echo 'export LBC_VERSION="v2.2.0"' >>  ~/.bash_profile
.  ~/.bash_profile
```


## Create an IAM role for your Workspace
- Follow [this](https://console.aws.amazon.com/iam/home#/roles$new?step=review&commonUseCase=EC2%2BEC2&selectedUseCase=EC2&policies=arn:aws:iam::aws:policy%2FAdministratorAccess&roleName=eksworkshop-admin) deep link to create an IAM role with Administrator access.
- Confirm that `AWS service` and `EC2` are selected, then click `Next: Permissions` to view permissions.
- Confirm that `AdministratorAccess` is checked, then click `Next: Tags` to assign tags.
- Take the defaults, and click `Next: Review` to review.
- Enter __eksworkshop-admin__ for the Name, and click `Create role`
  ![Create Role](imgs/createrole.png "Create Role")


## Attach the IAM role to your Workspace
- Click the grey circle button (in top right corner) and select `Manage EC2 Instance.`
  ![Cloud9 Role](imgs/cloud9-role.png "Create Role")

- Select the instance, then choose `Actions / Security / Modify IAM Role`
  ![Cloud9 Role](imgs/c9instancerole.png "Create Role")

- Choose `eksworkshop-admin` from the` IAM Role` drop down, and select `Save`
  ![Cloud9 Attach Role](imgs/c9attachrole.png "Create Role")

## Update IAM Settings for your Workspace
Cloud9 normally manages IAM credentials dynamically. This isn’t currently compatible with the EKS IAM authentication, so we will disable it and rely on the IAM role instead.

- Return to your Cloud9 workspace and click the gear icon (in top right corner)
- Select `AWS SETTINGS`
- Turn off `AWS managed temporary credentials`
- Close the Preferences tab
  ![Cloud9 Disable IAM role Role](imgs/c9disableiam.png "Create Role")

- To ensure temporary credentials aren’t already in place we will also remove any existing credentials file:
```
rm -vf ${HOME}/.aws/credentials
```
We should configure our aws cli with our current region as default.
```
export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
export AZS=($(aws ec2 describe-availability-zones --query 'AvailabilityZones[].ZoneName' --output text --region $AWS_REGION))
```

- Check if AWS_REGION is set to desired region
```
test -n "$AWS_REGION" && echo AWS_REGION is "$AWS_REGION" || echo AWS_REGION is not set
```

- Let’s save these into bash_profile
```
echo "export ACCOUNT_ID=${ACCOUNT_ID}" | tee -a ~/.bash_profile
echo "export AWS_REGION=${AWS_REGION}" | tee -a ~/.bash_profile
echo "export AZS=(${AZS[@]})" | tee -a ~/.bash_profile
aws configure set default.region ${AWS_REGION}
aws configure get default.region
```
- Validate the IAM role
Use the [GetCallerIdentity](https://docs.aws.amazon.com/cli/latest/reference/sts/get-caller-identity.html) CLI command to validate that the Cloud9 IDE is using the correct IAM role.
```
aws sts get-caller-identity --query Arn | grep eksworkshop-admin -q && echo "IAM role valid" || echo "IAM role NOT valid"
```

__If the IAM role is not valid, DO NOT PROCEED. Go back and confirm the steps on this page.__

## Clone the service repos
```
cd ~/environment
git clone https://github.com/aws-containers/ecsdemo-frontend.git
git clone https://github.com/saikiranchalla1/ecsdemo-nodejs.git
git clone https://github.com/saikiranchalla1/ecsdemo-crystal.git
```

## Create an AWS KMS Customer Managed Key
Create a CMK for the EKS cluster to use when encrypting your Kubernetes secrets:
```
aws kms create-alias --alias-name alias/eksworkshop --target-key-id $(aws kms create-key --query KeyMetadata.Arn --output text)
```

Let’s retrieve the ARN of the CMK to input into the create cluster command.
```
export MASTER_ARN=$(aws kms describe-key --key-id alias/eksworkshop --query KeyMetadata.Arn --output text)
```

We set the MASTER_ARN environment variable to make it easier to refer to the KMS key later.

Now, let’s save the MASTER_ARN environment variable into the bash_profile
```
echo "export MASTER_ARN=${MASTER_ARN}" | tee -a ~/.bash_profile
```

# Launch Using [EKSCTL](https://eksctl.io/)
[eksctl](https://eksctl.io/) is a tool jointly developed by AWS and [Weaveworks](https://weave.works/) that automates much of the experience of creating EKS clusters.

In this module, we will use eksctl to launch and configure our EKS cluster and nodes.

## Prerequisites
For this module, we need to download the eksctl binary:
```
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp

sudo mv -v /tmp/eksctl /usr/local/bin
```

Confirm the eksctl command works:
```
eksctl version
```

Enable eksctl bash-completion

```
eksctl completion bash >> ~/.bash_completion
. /etc/profile.d/bash_completion.sh
. ~/.bash_completion
```

## Launch EKS
__DO NOT PROCEED with this step unless you have validated the IAM role in use by the Cloud9 IDE. You will not be able to run the necessary kubectl commands in the later modules unless the EKS cluster is built using the IAM role.__

Challenge: How do I check the IAM role on the workspace?
Run `aws sts get-caller-identity` and validate that your Arn contains eksworkshop-adminand an Instance Id.
```
{
    "Account": "123456789012",
    "UserId": "AROA1SAMPLEAWSIAMROLE:i-01234567890abcdef",
    "Arn": "arn:aws:sts::123456789012:assumed-role/eksworkshop-admin/i-01234567890abcdef"
}
```
If you do not see the correct role, please go back and validate the IAM role for troubleshooting.

If you do see the correct role, proceed to next step to create an EKS cluster.

### Create an EKS Cluster
Create an eksctl deployment file (eksworkshop.yaml) use in creating your cluster using the following syntax:
```
cat << EOF > eksworkshop.yaml
---
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: eksworkshop-eksctl
  region: ${AWS_REGION}
  version: "1.19"

availabilityZones: ["${AZS[0]}", "${AZS[1]}", "${AZS[2]}"]

managedNodeGroups:
- name: nodegroup
  desiredCapacity: 3
  instanceType: t3.small
  ssh:
    enableSsm: true

# To enable all of the control plane logs, uncomment below:
# cloudWatch:
#  clusterLogging:
#    enableTypes: ["*"]

secretsEncryption:
  keyARN: ${MASTER_ARN}
EOF
```

Next, use the file you created as the input for the eksctl cluster creation.
__We are deliberatly launching at least one Kubernetes version behind the latest available on Amazon EKS. This allows you to perform the cluster upgrade lab.__

```
eksctl create cluster -f eksworkshop.yaml
```
__Launching EKS and all the dependencies will take approximately 15 minutes__


## Test the Cluster
Confirm your nodes:
```
kubectl get nodes # if we see our 3 nodes, we know we have authenticated correctly
```

Export the Worker Role Name for use throughout the workshop:
```
STACK_NAME=$(eksctl get nodegroup --cluster eksworkshop-eksctl -o json | jq -r '.[].StackName')
ROLE_NAME=$(aws cloudformation describe-stack-resources --stack-name $STACK_NAME | jq -r '.StackResources[] | select(.ResourceType=="AWS::IAM::Role") | .PhysicalResourceId')
echo "export ROLE_NAME=${ROLE_NAME}" | tee -a ~/.bash_profile
```

__Congratulations!__
You now have a fully working Amazon EKS Cluster that is ready to use! Before you move on to any other labs, make sure to complete the steps on the next page to update the EKS Console Credentials.

## Console Credentials
This step is optional, as nearly all of the workshop content is CLI-driven. But, if you’d like full access to your workshop cluster in the EKS console this step is recommended.

The EKS console allows you to see not only the configuration aspects of your cluster, but also to view Kubernetes cluster objects such as Deployments, Pods, and Nodes. For this type of access, the console IAM User or Role needs to be granted permission within the cluster.

By default, the credentials used to create the cluster are automatically granted these permissions. Following along in the workshop, you’ve created a cluster using temporary IAM credentials from within Cloud9. This means that you’ll need to add your AWS Console credentials to the cluster.

### Import your EKS Console credentials to your new cluster:
IAM Users and Roles are bound to an EKS Kubernetes cluster via a ConfigMap named `aws-auth`. We can use `eksctl` to do this with one command.

You’ll need to determine the correct credential to add for your AWS Console access. If you know this already, you can skip ahead to the `eksctl create iamidentitymapping` step below.

If you’ve built your cluster from Cloud9 as part of this tutorial, invoke the following within your environment to determine your IAM Role or User ARN.
```
c9builder=$(aws cloud9 describe-environment-memberships --environment-id=$C9_PID | jq -r '.memberships[].userArn')
if echo ${c9builder} | grep -q user; then
	rolearn=${c9builder}
        echo Role ARN: ${rolearn}
elif echo ${c9builder} | grep -q assumed-role; then
        assumedrolename=$(echo ${c9builder} | awk -F/ '{print $(NF-1)}')
        rolearn=$(aws iam get-role --role-name ${assumedrolename} --query Role.Arn --output text)
        echo Role ARN: ${rolearn}
fi
```

With your ARN in hand, you can issue the command to create the identity mapping within the cluster.
```
eksctl create iamidentitymapping --cluster eksworkshop-eksctl --arn ${rolearn} --group system:masters --username admin
```

Note that permissions can be restricted and granular but as this is a workshop cluster, you’re adding your console credentials as administrator.

Now you can verify your entry in the AWS auth map within the console.
```
kubectl describe configmap -n kube-system aws-auth
```

Now you’re all set to move on. For more information, check out the [EKS documentation](https://docs.aws.amazon.com/eks/latest/userguide/add-user-role.html) on this topic.


# Deploy the Kubernetes Dashboard
In this module, we will deploy the official Kubernetes dashboard, and connect through our Cloud9 Workspace.
![Dashboard](imgs/dashboard.png "K8s Dashboard")

## Deploy the dasboard
The official Kubernetes dashboard is not deployed by default, but there are instructions in [the official documentation](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)

We can deploy the dashboard with the following command:
```
export DASHBOARD_VERSION="v2.0.0"

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/${DASHBOARD_VERSION}/aio/deploy/recommended.yaml
```

Since this is deployed to our private cluster, we need to access it via a proxy. kube-proxy is available to proxy our requests to the dashboard service. In your workspace, run the following command:
```
kubectl proxy --port=8080 --address=0.0.0.0 --disable-filter=true &
```

This will start the proxy, listen on port 8080, listen on all interfaces, and will disable the filtering of non-localhost requests.

This command will continue to run in the background of the current terminal’s session.

__We are disabling request filtering, a security feature that guards against XSRF attacks. This isn’t recommended for a production environment, but is useful for our dev environment.__

## Access the Dashboard
Now we can access the Kubernetes Dashboard

1. In your Cloud9 environment, click Tools / Preview / Preview Running Application
2. Scroll to the end of the URL and append:
```
/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

The Cloud9 Preview browser doesn’t appear to support the token authentication, so once you have the login screen in the cloud9 preview browser tab, press the Pop Out button to open the login screen in a regular browser tab, like below:
  ![Popout](imgs/[popout].png "K8s Dashboard")

- Open a New Terminal Tab and enter
```
aws eks get-token --cluster-name eksworkshop-eksctl | jq -r '.status.token'
```

- Copy the output of this command and then click the radio button next to Token then in the text field below paste the output from the last command.
  ![Popout](imgs/[dashboard-connect].png "K8s Dashboard")

- Then press Sign In.

## Cleanup

Stop the proxy and delete the dashboard deployment
```
# kill proxy
pkill -f 'kubectl proxy --port=8080'

# delete dashboard
kubectl delete -f https://raw.githubusercontent.com/kubernetes/dashboard/${DASHBOARD_VERSION}/aio/deploy/recommended.yaml

unset DASHBOARD_VERSION
```

# Deploy the example Microservices
## Deploy our sample applications
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ecsdemo-nodejs
  labels:
    app: ecsdemo-nodejs
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ecsdemo-nodejs
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: ecsdemo-nodejs
    spec:
      containers:
      - image: brentley/ecsdemo-nodejs:latest
        imagePullPolicy: Always
        name: ecsdemo-nodejs
        ports:
        - containerPort: 3000
          protocol: TCP
```
In the sample file above, we describe the service and how it should be deployed. We will write this description to the kubernetes api using kubectl, and kubernetes will ensure our preferences are met as the application is deployed.

The containers listen on port 3000, and native service discovery will be used to locate the running containers and communicate with them.

## Deploy NodeJS Backend api
Let’s bring up the NodeJS Backend API!

Copy/Paste the following commands into your Cloud9 workspace:
```
cd ~/environment/ecsdemo-nodejs
kubectl apply -f kubernetes/deployment.yaml
kubectl apply -f kubernetes/service.yaml
```

We can watch the progress by looking at the deployment status:
```
kubectl get deployment ecsdemo-nodejs
```

## Deploy Crystal Backend API
Let’s bring up the Crystal Backend API!

Copy/Paste the following commands into your Cloud9 workspace:
```
cd ~/environment/ecsdemo-crystal
kubectl apply -f kubernetes/deployment.yaml
kubectl apply -f kubernetes/service.yaml
```

We can watch the progress by looking at the deployment status:

```
kubectl get deployment ecsdemo-crystal
```

## Let's check Service Types
Before we bring up the frontend service, let’s take a look at the service types we are using: This is kubernetes/service.yaml for our frontend service:

```
apiVersion: v1
kind: Service
metadata:
  name: ecsdemo-frontend
spec:
  selector:
    app: ecsdemo-frontend
  type: LoadBalancer
  ports:
   -  protocol: TCP
      port: 80
      targetPort: 3000
```

Notice type: LoadBalancer: This will configure an ELB to handle incoming traffic to this service.

Compare this to kubernetes/service.yaml for one of our backend services:

```
apiVersion: v1
kind: Service
metadata:
  name: ecsdemo-nodejs
spec:
  selector:
    app: ecsdemo-nodejs
  ports:
   -  protocol: TCP
      port: 80
      targetPort: 3000
```

Notice there is no specific service type described. When we check [the kubernetes documentation](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) we find that the default type is ClusterIP. This Exposes the service on a cluster-internal IP. Choosing this value makes the service only reachable from within the cluster.

## Ensure the ELB Service Role Exists

In AWS accounts that have never created a load balancer before, it’s possible that the service role for ELB might not exist yet.

We can check for the role, and create it if it’s missing.

Copy/Paste the following commands into your Cloud9 workspace:
```
aws iam get-role --role-name "AWSServiceRoleForElasticLoadBalancing" || aws iam create-service-linked-role --aws-service-name "elasticloadbalancing.amazonaws.com"
```

## Deploy Frontend Service
Let’s bring up the Ruby Frontend!

Copy/Paste the following commands into your Cloud9 workspace:
```
cd ~/environment/ecsdemo-frontend
kubectl apply -f kubernetes/deployment.yaml
kubectl apply -f kubernetes/service.yaml

```

We can watch the progress by looking at the deployment status:
```
kubectl get deployment ecsdemo-frontend
```

## Find the service address
Now that we have a running service that is `type: LoadBalancer` we need to find the ELB’s address. We can do this by using the `get services` operation of kubectl:
```
kubectl get service ecsdemo-frontend
```

Notice the field isn’t wide enough to show the FQDN of the ELB. We can adjust the output format with this command:
```
kubectl get service ecsdemo-frontend -o wide
```

If we wanted to use the data programatically, we can also output via json. This is an example of how we might be able to make use of json output:
```
ELB=$(kubectl get service ecsdemo-frontend -o json | jq -r '.status.loadBalancer.ingress[].hostname')

curl -m3 -v $ELB
```

__It will take several minutes for the ELB to become healthy and start passing traffic to the frontend pods.__

You should also be able to copy/paste the loadBalancer hostname into your browser and see the application running. Keep this tab open while we scale the services up on the next page.

## Scale the Backend Services
When we launched our services, we only launched one container of each. We can confirm this by viewing the running pods:
```
kubectl get deployments
```

Now let’s scale up the backend services:
```
kubectl scale deployment ecsdemo-nodejs --replicas=3
kubectl scale deployment ecsdemo-crystal --replicas=3
```
Confirm by looking at deployments again:
```
kubectl get deployments
```

Also, check the browser tab where we can see our application running. You should now see traffic flowing to multiple backend services.

## Scale the Frontend
Let’s also scale our frontend service!
```
kubectl get deployments
kubectl scale deployment ecsdemo-frontend --replicas=3
kubectl get deployments
```

Check the browser tab where we can see our application running. You should now see traffic flowing to multiple frontend services.

## Cleanup the applications
To delete the resources created by the applications, we should delete the application deployments:

Undeploy the applications:
```
cd ~/environment/ecsdemo-frontend
kubectl delete -f kubernetes/service.yaml
kubectl delete -f kubernetes/deployment.yaml

cd ~/environment/ecsdemo-crystal
kubectl delete -f kubernetes/service.yaml
kubectl delete -f kubernetes/deployment.yaml

cd ~/environment/ecsdemo-nodejs
kubectl delete -f kubernetes/service.yaml
kubectl delete -f kubernetes/deployment.yaml
```

# Helm
Helm is a package manager for Kubernetes that packages multiple Kubernetes resources into a single logical deployment unit called a Chart. Charts are easy to create, version, share, and publish.

In this module, we’ll cover installing Helm. Once installed, we’ll demonstrate how Helm can be used to deploy a simple nginx webserver, and a more sophisticated microservice.

![Helm](imgs/helm-logo.svg "Helm")

## Introduction
[Helm](https://helm.sh/) is a package manager and application management tool for Kubernetes that packages multiple Kubernetes resources into a single logical deployment unit called a Chart.

Helm helps you to:

- Achieve a simple (one command) and repeatable deployment
- Manage application dependency, using specific versions of other application and services
- Manage multiple deployment configurations: test, staging, production and others
- Execute post/pre deployment jobs during application deployment
- Update/rollback and test application deployments

## Install Helm CLI
Before we can get started configuring Helm, we’ll need to first install the command line tools that you will interact with. To do this, run the following:
```
curl -sSL https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```

We can verify the version
```
helm version --short
```

Let’s configure our first Chart repository. Chart repositories are similar to APT or yum repositories that you might be familiar with on Linux, or Taps for Homebrew on macOS.

Download the stable repository so we have something to start with:
```
helm repo add stable https://charts.helm.sh/stable
```

Once this is installed, we will be able to list the charts you can install:
```
helm search repo stable
```
Finally, let’s configure Bash completion for the helm command:

```
helm completion bash >> ~/.bash_completion
. /etc/profile.d/bash_completion.sh
. ~/.bash_completion
source <(helm completion bash)
```

## Deploy nginx with Helm
### Update the Chart repository
Helm uses a packaging format called Charts. A Chart is a collection of files and templates that describes Kubernetes resources.

Charts can be simple, describing something like a standalone web server (which is what we are going to create), but they can also be more complex, for example, a chart that represents a full web application stack, including web servers, databases, proxies, etc.

Instead of installing Kubernetes resources manually via kubectl, one can use Helm to install pre-defined Charts faster, with less chance of typos or other operator errors.

Chart repositories change frequently due to updates and new additions. To keep Helm’s local list updated with all these changes, we need to occasionally run the repository update command.

To update Helm’s local list of Charts, run:

```
# first, add the default repository, then update
helm repo add stable https://charts.helm.sh/stable
helm repo update
```

And you should see something similar to:

```
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

Next, we’ll search for the `nginx` web server Chart.

### Search Chart repositories
Now that our repository Chart list has been updated, we can [search for Charts](https://helm.sh/docs/helm/helm_search/).

To list all Charts:
```
helm search repo
```
That should output something similar to:

```
NAME                                    CHART VERSION   APP VERSION                     DESCRIPTION
stable/acs-engine-autoscaler            2.2.2           2.1.1                           Scales worker...
stable/aerospike                        0.3.2           v4.5.0.5                        A Helm chart...
...
```

You can see from the output that it dumped the list of all Charts we have added. In some cases that may be useful, but an even more useful search would involve a keyword argument. So next, we’ll search just for nginx:
```
helm search repo nginx
```

That results in:

```
NAME                            CHART VERSION   APP VERSION     DESCRIPTION
stable/nginx-ingress            1.41.3          v0.34.1         DEPRECATED! An nginx Ingress controller ...
stable/nginx-ldapauth-proxy     0.1.6           1.13.5          DEPRECATED - nginx proxy with ldapauth ...
stable/nginx-lego               0.3.1                           Chart for...
stable/gcloud-endpoints         0.1.2           1               DEPRECATED Develop...
...
```

This new list of Charts are specific to nginx, because we passed the nginx argument to the `helm search repo` command.

Further information on the command can be found [here](https://helm.sh/docs/helm/helm_search_repo/).


### Add the Bitnami Repository
In the last slide, we saw that `nginx` offers many different products via the default Helm Chart repository, but the nginx standalone web server is not one of them.

After a quick web search, we discover that there is a Chart for the nginx standalone web server available via the [Bitnami Chart repository](https://github.com/bitnami/charts).

To add the Bitnami Chart repo to our local list of searchable charts:
```
helm repo add bitnami https://charts.bitnami.com/bitnami
```

Once that completes, we can search all Bitnami Charts:
```
helm search repo bitnami
```

Which results in:

```
NAME                     CHART VERSION   APP VERSION             DESCRIPTION
bitnami/bitnami-common   0.0.9           0.0.9           DEPRECATED Chart with custom templates used in ...
bitnami/airflow          10.2.5          2.1.2           Apache Airflow is a platform to programmaticall...
bitnami/apache           8.5.8           2.4.48          Chart for Apache HTTP Server                      
...
```

Search once again for nginx
```
helm search repo nginx
```

Now we are seeing more nginx options, across both repositories:

```
NAME                                    CHART VERSION   APP VERSION     DESCRIPTION
bitnami/nginx                           9.3.7           1.21.1          Chart for the nginx server                        
bitnami/nginx-ingress-controller        7.6.16          0.48.1          Chart for the nginx Ingress controller            
stable/nginx-ingress                    1.41.3          v0.34.1         DEPRECATED! An nginx Ingress controller that us...
```

Or even search the Bitnami repo, just for nginx:
```
helm search repo bitnami/nginx
```

Which narrows it down to nginx on Bitnami:

```
NAME                                    CHART VERSION   APP VERSION     DESCRIPTION
bitnami/nginx                           9.3.7           1.21.1          Chart for the nginx server            
bitnami/nginx-ingress-controller        7.6.16          0.48.1          Chart for the nginx Ingress controller
```

In both of those last two searches, we see

```
bitnami/nginx
```

as a search result. That’s the one we’re looking for, so let’s use Helm to install it to the EKS cluster.

### Install bitname/nginx
Installing the Bitnami standalone nginx web server Chart involves us using the [helm install](https://helm.sh/docs/helm/helm_install/) command.

A Helm Chart can be installed multiple times inside a Kubernetes cluster. This is because each installation of a Chart can be customized to suit a different purpose.

For this reason, you must supply a unique name for the installation, or ask Helm to generate a name for you.

Challenge:
How can you use Helm to deploy the bitnami/nginx chart?

HINT: Use the helm utility to install the bitnami/nginx chart and specify the name mywebserver for the Kubernetes deployment. Consult the helm install documentation or run the helm install --help command to figure out the syntax.

Solution:
```
helm install mywebserver bitnami/nginx
```

Once you run this command, the output will contain the information about the deployment status, revision, namespace, etc, similar to:

```
NAME: mywebserver
LAST DEPLOYED: Thu Jul 15 13:52:34 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
** Please be patient while the chart is being deployed **

NGINX can be accessed through the following DNS name from within your cluster:

    mywebserver-nginx.default.svc.cluster.local (port 80)

To access NGINX from outside the cluster, follow the steps below:

1. Get the NGINX URL by running these commands:

  NOTE: It may take a few minutes for the LoadBalancer IP to be available.
        Watch the status with: 'kubectl get svc --namespace default -w mywebserver-nginx'

    export SERVICE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].port}" services mywebserver-nginx)
    export SERVICE_IP=$(kubectl get svc --namespace default mywebserver-nginx -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
    echo "http://${SERVICE_IP}:${SERVICE_PORT}"
```

In order to review the underlying Kubernetes services, pods and deployments, run:
```
kubectl get svc,po,deploy
```
__In the following kubectl command examples, it may take a minute or two for each of these objects' DESIRED and CURRENT values to match; if they don’t match on the first try, wait a few seconds, and run the command again to check the status.__

The first object shown in this output is a Deployment. A Deployment object manages rollouts (and rollbacks) of different versions of an application.

You can inspect this Deployment object in more detail by running the following command:
```
kubectl describe deployment mywebserver
```

The next object shown created by the Chart is a Pod. A Pod is a group of one or more containers.

To verify the Pod object was successfully deployed, we can run the following command:
```
kubectl get pods -l app.kubernetes.io/name=nginx
```

And you should see output similar to:

```
NAME                                 READY     STATUS    RESTARTS   AGE
mywebserver-nginx-85985c8466-tczst   1/1       Running   0          10s
```

The third object that this Chart creates for us is a Service. A Service enables us to contact this nginx web server from the Internet, via an Elastic Load Balancer (ELB).

To get the complete URL of this Service, run:
```
kubectl get service mywebserver-nginx -o wide
```

That should output something similar to:

```
NAME                TYPE           CLUSTER-IP      EXTERNAL-IP
mywebserver-nginx   LoadBalancer   10.100.223.99   abc123.amazonaws.com
```

Copy the value for EXTERNAL-IP, open a new tab in your web browser, and paste it in.

__It may take a couple minutes for the ELB and its associated DNS name to become available; if you get an error, wait one minute, and hit reload.__

When the Service does come online, you should see a welcome message similar to:

![Welcome to Nginx](imgs/welcome_to_nginx.png "Welcome to Nginx")

Congratulations! You’ve now successfully deployed the nginx standalone web server to your EKS cluster!

### Clean Up
To remove all the objects that the Helm Chart created, we can use [Helm uninstall](https://helm.sh/docs/helm/helm_uninstall/).

Before we uninstall our application, we can verify what we have running via the [Helm list](https://helm.sh/docs/helm/helm_list/) command:

```
helm list
```

You should see output similar to below, which show that mywebserver is installed:

```
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
mywebserver     default         1               2021-07-15 13:52:34.563653342 +0000 UTC deployed        nginx-9.3.7     1.21.1   
```

It was a lot of fun; we had some great times sending HTTP back and forth, but now its time to uninstall this deployment. To uninstall:
```
helm uninstall mywebserver
```
And you should be met with the output:

```
release "mywebserver" uninstalled
```

kubectl will also demonstrate that our pods and service are no longer available:
```
kubectl get pods -l app.kubernetes.io/name=nginx
kubectl get service mywebserver-nginx -o wide
```
As would trying to access the service via the web browser via a page reload.

With that, cleanup is complete.


## Deploy Example Microservices using Helm
In this module, we will demonstrate how to deploy microservices using a custom Helm Chart, instead of doing everything manually using kubectl.

For detailed information on working with chart templates, refer to the Helm [docs](https://docs.helm.sh/chart_template_guide/).

### Create a Chart
Helm charts have a structure similar to:

```
/eksdemo
├── charts/
├── Chart.yaml
├── templates/
│   ├── deployment.yaml
│   ├── _helpers.tpl
│   ├── hpa.yaml
│   ├── ingress.yaml
│   ├── NOTES.txt
│   ├── serviceaccount.yaml
│   ├── service.yaml
│   └── tests
│       └── test-connection.yaml
└── values.yaml
```

We’ll follow this template, and create a new chart called eksdemo with the following commands:
```
cd ~/environment
helm create eksdemo
cd eksdemo
```

### Customize Defaults
If you look in the newly created eksdemo directory, you’ll see several files and directories.

The table below outlines the purpose of each component in the Helm chart structure.

|File or Directory|	Description|
|-----------------|------------|
|charts/ |	Sub-charts that the chart depends on|
|Chart.yaml	|Information about your chart|
|values.yaml|	The default values for your templates|
|template/|	The template files|
|template/deployment.yaml|	Basic manifest for creating Kubernetes Deployment objects|
|template/_helpers.tpl|	Used to define Go template helpers|
|template/hpa.yaml |	Basic manifest for creating Kubernetes Horizontal Pod Autoscaler objects|
|template/ingress.yaml |	Basic manifest for creating Kubernetes Ingress objects|
|template/NOTES.txt|	A plain text file to give users detailed information about how to use the newly installed chart|
|template/serviceaccount.yaml |	Basic manifest for creating Kubernetes ServiceAccount objects|
|template/service.yaml|	Basic manifest for creating Kubernetes Service objects|
|tests/	| Directory of Test files |
| tests/test-connections.yaml	 | Tests that validate that your chart works as expected when it is installed|

We’re actually going to create our own files, so we’ll delete these boilerplate files
```
rm -rf ~/environment/eksdemo/templates/
rm ~/environment/eksdemo/Chart.yaml
rm ~/environment/eksdemo/values.yaml
```

Run the following code block to create a new Chart.yaml file which will describe the chart
```
cat <<EoF > ~/environment/eksdemo/Chart.yaml
apiVersion: v2
name: eksdemo
description: A Helm chart for EKS Workshop Microservices application
version: 0.1.0
appVersion: 1.0
EoF
```

Next we’ll copy the manifest files for each of our microservices into the templates directory as servicename.yaml
```
#create subfolders for each template type
mkdir -p ~/environment/eksdemo/templates/deployment
mkdir -p ~/environment/eksdemo/templates/service

# Copy and rename frontend manifests
cp ~/environment/ecsdemo-frontend/kubernetes/deployment.yaml ~/environment/eksdemo/templates/deployment/frontend.yaml
cp ~/environment/ecsdemo-frontend/kubernetes/service.yaml ~/environment/eksdemo/templates/service/frontend.yaml

# Copy and rename crystal manifests
cp ~/environment/ecsdemo-crystal/kubernetes/deployment.yaml ~/environment/eksdemo/templates/deployment/crystal.yaml
cp ~/environment/ecsdemo-crystal/kubernetes/service.yaml ~/environment/eksdemo/templates/service/crystal.yaml

# Copy and rename nodejs manifests
cp ~/environment/ecsdemo-nodejs/kubernetes/deployment.yaml ~/environment/eksdemo/templates/deployment/nodejs.yaml
cp ~/environment/ecsdemo-nodejs/kubernetes/service.yaml ~/environment/eksdemo/templates/service/nodejs.yaml
```


All files in the templates directory are sent through the template engine. These are currently plain YAML files that would be sent to Kubernetes as-is.

#### Replace hard-coded values with template directives

Let’s replace some of the values with template directives to enable more customization by removing hard-coded values.

Open ~/environment/eksdemo/templates/deployment/frontend.yaml in your Cloud9 editor.

__The following steps should be completed seperately for frontend.yaml, crystal.yaml, and nodejs.yaml.__

Under `spec`, find __replicas: 1__ and replace with the following:

```
replicas: {{ .Values.replicas }}
```

Under `spec.template.spec.containers.image`, replace the image with the correct template value from the table below:

|Filename |	Value|
|---------|------|
|frontend.yaml|	- image: {{ .Values.frontend.image }}:{{ .Values.version }}|
|crystal.yaml |	- image: {{ .Values.crystal.image }}:{{ .Values.version }}|
|nodejs.yaml|	- image: {{ .Values.nodejs.image }}:{{ .Values.version }}|

#### Create a values.yaml file with our template defaults

Run the following code block to populate our template directives with default values.
```
cat <<EoF > ~/environment/eksdemo/values.yaml
# Default values for eksdemo.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

# Release-wide Values
replicas: 3
version: 'latest'

# Service Specific Values
nodejs:
  image: brentley/ecsdemo-nodejs
crystal:
  image: brentley/ecsdemo-crystal
frontend:
  image: brentley/ecsdemo-frontend
EoF
```

### Deploy the EKSDemo Chart
#### Use the dry-run flag to test our templates
To test the syntax and validity of the Chart without actually deploying it, we’ll use the `--dry-run` flag.

The following command will build and output the rendered templates without installing the Chart:
```
helm install --debug --dry-run workshop ~/environment/eksdemo
```

Confirm that the values created by the template look correct.

#### Deploy the chart
Now that we have tested our template, let’s install it.
```
helm install workshop ~/environment/eksdemo
```

Similar to what we saw previously in the nginx Helm Chart example, an output of the command will contain the information about the deployment status, revision, namespace, etc, similar to:

```
NAME: workshop
LAST DEPLOYED: Sat Jul 17 08:47:32 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

In order to review the underlying services, pods and deployments, run:
```
kubectl get svc,po,deploy
```

### Testing the service
To test the service our eksdemo Chart created, we’ll need to get the name of the ELB endpoint that was generated when we deployed the Chart:
```
kubectl get svc ecsdemo-frontend -o jsonpath="{.status.loadBalancer.ingress[*].hostname}"; echo
```

Copy that address, and paste it into a new tab in your browser. You should see something similar to:

![EKS Demo UI](imgs/micro_example.png "Cluster Creation Workflow")

### Rolling Back
Mistakes will happen during deployment, and when they do, Helm makes it easy to undo, or “roll back” to the previously deployed version.

#### Update the demo application chart with a breaking change
Open `values.yaml` and modify the image name under `nodejs.image` to brentley/ecsdemo-nodejs-non-existing. This image does not exist, so this will break our deployment.

#### Deploy the updated demo application chart:
```
helm upgrade workshop ~/environment/eksdemo
```

The rolling upgrade will begin by creating a new nodejs pod with the new image. The new `ecsdemo-nodejs` Pod should fail to pull non-existing image. Run `kubectl get pods` to see the `ImagePullBackOff` error.

```
kubectl get pods
```
```
NAME                                READY   STATUS             RESTARTS   AGE
ecsdemo-crystal-56976b4dfd-9f2rf    1/1     Running            0          2m10s
ecsdemo-frontend-7f5ddc5485-8vqck   1/1     Running            0          2m10s
ecsdemo-nodejs-56487f6c95-mv5xv     0/1     ImagePullBackOff   0          6s
ecsdemo-nodejs-58977c4597-r6hvj     1/1     Running            0          2m10s
```

Run helm status workshop to verify the __LAST DEPLOYED__ timestamp.

```
helm status workshop
```
```

NAME: workshop
LAST DEPLOYED: Fri Jul 16 13:53:22 2021
NAMESPACE: default
STATUS: deployed
REVISION: 2
TEST SUITE: None
...
```

This should correspond to the last entry on `helm history workshop`

```
helm history workshop
```

#### Rollback the failed upgrade
Now we are going to rollback the application to the previous working release revision.

First, list Helm release revisions:
```
helm history workshop
```
Then, rollback to the previous application revision (can rollback to any revision too):
```
# rollback to the 1st revision
helm rollback workshop 1
```

Validate workshop release status and you will see a new revision that is based on the rollback.

```
helm status workshop
```
```
NAME: workshop
LAST DEPLOYED: Fri Jul 16 13:55:27 2021
NAMESPACE: default
STATUS: deployed
REVISION: 3
TEST SUITE: None
```

Verify that the error is gone

```
kubectl get pods
```
```
NAME                                READY   STATUS    RESTARTS   AGE
ecsdemo-crystal-56976b4dfd-9f2rf    1/1     Running   0          6m
ecsdemo-frontend-7f5ddc5485-8vqck   1/1     Running   0          6m
ecsdemo-nodejs-58977c4597-r6hvj     1/1     Running   0          6m
```

### Clean Up
To delete the workshop release, run:

```
helm uninstall workshop
```

# Health Checks
By default, Kubernetes will restart a container if it crashes for any reason. It uses Liveness and Readiness probes which can be configured for running a robust application by identifying the healthy containers to send traffic to and restarting the ones when required.

In this section, we will understand how [liveness and readiness probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) are defined and test the same against different states of a pod. Below is the high level description of how these probes work.

__Liveness probes__ are used in Kubernetes to know when a pod is alive or dead. A pod can be in a dead state for a variety of reasons; Kubernetes will kill and recreate the pod when a liveness probe does not pass.

__Readiness probes__ are used in Kubernetes to know when a pod is ready to serve traffic. Only when the readiness probe passes will a pod receive traffic from the service; if a readiness probe fails traffic will not be sent to the pod.

We will review some examples in this module to understand different options for configuring liveness and readiness probes.

## Configure Liveness Probe
### Configure the Probe
Use the command below to create a directory
```
mkdir -p ~/environment/healthchecks
```

Run the following code block to populate the manifest file __~/environment/healthchecks/liveness-app.yaml__. In the configuration file, the `livenessProbe` field determines how `kubelet` should check the container in order to consider whether it is healthy or not. `kubelet` uses the periodSeconds field to do frequent check on the Container. In this case, `kubelet` checks the liveness probe every 5 seconds. The `initialDelaySeconds` field is used to tell kubelet that it should wait for 5 seconds before doing the first probe. To perform a probe, `kubelet` sends a HTTP GET request to the server hosting this pod and if the handler for the servers` /health` returns a success code, then the container is considered healthy. If the handler returns a failure code, the `kubelet` kills the container and restarts it.
```
cat <<EoF > ~/environment/healthchecks/liveness-app.yaml
apiVersion: v1
kind: Pod
metadata:
  name: liveness-app
spec:
  containers:
  - name: liveness
    image: brentley/ecsdemo-nodejs
    livenessProbe:
      httpGet:
        path: /health
        port: 3000
      initialDelaySeconds: 5
      periodSeconds: 5
EoF
```

Let’s create the pod using the manifest:

```
kubectl apply -f ~/environment/healthchecks/liveness-app.yaml
```

The above command creates a pod with liveness probe.
```
kubectl get pod liveness-app
```

The output looks like below. Notice the __RESTARTS__

```
NAME           READY     STATUS    RESTARTS   AGE
liveness-app   1/1       Running   0          11s
```

The `kubectl describe` command will show an event history which will show any probe failures or restarts.

```
kubectl describe pod liveness-app
```
```
Events:
  Type    Reason                 Age   From                                    Message
  ----    ------                 ----  ----                                    -------
  Normal  Scheduled              38s   default-scheduler                       Successfully assigned default/liveness-app to ip-192-168-18-63.ec2.internal
  Normal  Pulling                37s   kubelet                                 Pulling image "brentley/ecsdemo-nodejs"
  Normal  Pulled                 37s   kubelet                                 Successfully pulled image "brentley/ecsdemo-nodejs" in 108.556215ms
  Normal  Created                37s   kubelet                                 Created container liveness
  Normal  Started                37s   kubelet                                 Started container liveness
```

### Introduce a Failure
We will run the next command to send a SIGUSR1 signal to the nodejs application. By issuing this command we will send a kill signal to the application process in the docker runtime.

```
kubectl exec -it liveness-app -- /bin/kill -s SIGUSR1 1
```

Describe the pod after waiting for 15-20 seconds and you will notice the kubelet actions of killing the container and restarting it.

```
Events:
  Type     Reason         Age                From                  Message
  ----     ------         ----               ----                  -------
  Normal   Scheduled      72s                default-scheduler     Successfully assigned default/liveness-app to ip-192-168-18-63.ec2.internal
  Normal   Pulled         71s                kubelet               Successfully pulled image "brentley/ecsdemo-nodejs" in 100.615806ms
  Warning  Unhealthy      37s (x3 over 47s)  kubelet               Liveness probe failed: Get http://192.168.13.176:3000/health: context deadline exceeded (Client.Timeout exceeded while awaiting headers)
  Normal   Killing        37s                kubelet               Container liveness failed liveness probe, will be restarted
  Normal   Pulling        6s (x2 over 71s)   kubelet               Pulling image "brentley/ecsdemo-nodejs"
  Normal   Created        6s (x2 over 71s)   kubelet               Created container liveness
  Normal   Started        6s (x2 over 71s)   kubelet               Started container liveness
  Normal   Pulled         6s                 kubelet               Successfully pulled image "brentley/ecsdemo-nodejs" in 118.19123ms
```

When the nodejs application entered a debug mode with SIGUSR1 signal, it did not respond to the health check pings and kubelet killed the container. The container was subject to the default restart policy.

```
kubectl get pod liveness-app
```

The output looks like below:

```
NAME           READY     STATUS    RESTARTS   AGE
liveness-app   1/1       Running   1          12m
```

### Challenge:
How can we check the status of the container health checks?
Solution: `kubectl logs liveness-app`

## Configure Readiness probe
### Configure the Probe
Run the following code block to populate `~/environment/healthchecks/readiness-deployment.yaml`. The readinessProbe definition explains how a linux command can be configured as `healthcheck`. We create an empty file `/tmp/healthy` to configure readiness probe and use the same to understand how `kubelet` helps to update a deployment with only healthy pods.
```
cat <<EoF > ~/environment/healthchecks/readiness-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: readiness-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: readiness-deployment
  template:
    metadata:
      labels:
        app: readiness-deployment
    spec:
      containers:
      - name: readiness-deployment
        image: alpine
        command: ["sh", "-c", "touch /tmp/healthy && sleep 86400"]
        readinessProbe:
          exec:
            command:
            - cat
            - /tmp/healthy
          initialDelaySeconds: 5
          periodSeconds: 3
EoF
```

We will now create a deployment to test readiness probe:
```
kubectl apply -f ~/environment/healthchecks/readiness-deployment.yaml
```

The above command creates a deployment with 3 replicas and readiness probe as described in the beginning.
```
kubectl get pods -l app=readiness-deployment
```
The output looks similar to below:


```
NAME                                    READY     STATUS    RESTARTS   AGE
readiness-deployment-7869b5d679-922mx   1/1       Running   0          31s
readiness-deployment-7869b5d679-vd55d   1/1       Running   0          31s
readiness-deployment-7869b5d679-vxb6g   1/1       Running   0          31s
```

Let us also confirm that all the replicas are available to serve traffic when a service is pointed to this deployment.
```
kubectl describe deployment readiness-deployment | grep Replicas:
```

The output looks like below:

```
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
```

### Introduce a Failure
Pick one of the pods from above 3 and issue a command as below to delete the /tmp/healthy file which makes the readiness probe fail.

```
kubectl exec -it <YOUR-READINESS-POD-NAME> -- rm /tmp/healthy
```

`readiness-deployment-7869b5d679-922mx` was picked in our example cluster. The `/tmp/healthy` file was deleted. This file must be present for the readiness check to pass. Below is the status after issuing the command.

```
kubectl get pods -l app=readiness-deployment
```

The output looks similar to below:

```
NAME                                    READY     STATUS    RESTARTS   AGE
readiness-deployment-7869b5d679-922mx   0/1       Running   0          4m
readiness-deployment-7869b5d679-vd55d   1/1       Running   0          4m
readiness-deployment-7869b5d679-vxb6g   1/1       Running   0          4m
```

Traffic will not be routed to the first pod in the above deployment. The ready column confirms that the readiness probe for this pod did not pass and hence was marked as not ready.

We will now check for the replicas that are available to serve traffic when a service is pointed to this deployment.

```
kubectl describe deployment readiness-deployment | grep Replicas:
```

The output looks like below:

```
Replicas:               3 desired | 3 updated | 3 total | 2 available | 1 unavailable
```

When the readiness probe for a pod fails, the endpoints controller removes the pod from list of endpoints of all services that match the pod.

### Challenge:
How would you restore the pod to Ready status?
Solution: Run the below command with the name of the pod to recreate the __/tmp/healthy__ file. Once the pod passes the probe, it gets marked as ready and will begin to receive traffic again.
```
kubectl exec -it <YOUR-READINESS-POD-NAME> -- touch /tmp/healthy
kubectl get pods -l app=readiness-deployment
```

## Clean Up
Our Liveness Probe example used HTTP request and Readiness Probe executed a command to check health of a pod. Same can be accomplished using a TCP request as described in the [documentation](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).

```
kubectl delete -f ~/environment/healthchecks/liveness-app.yaml
kubectl delete -f ~/environment/healthchecks/readiness-deployment.yaml
```

# Autoscaling our Applications and Cluster
In this module, we will show patterns for scaling your worker nodes and applications deployments automatically.

Automatic scaling in K8s comes in two forms:

__Horizontal Pod Autoscaler (HPA)__ scales the pods in a deployment or replica set. It is implemented as a K8s API resource and a controller. The controller manager queries the resource utilization against the metrics specified in each HorizontalPodAutoscaler definition. It obtains the metrics from either the resource metrics API (for per-pod resource metrics), or the custom metrics API (for all other metrics).

__Cluster Autoscaler (CA)__ a component that automatically adjusts the size of a Kubernetes Cluster so that all pods have a place to run and there are no unneeded nodes.

## Install kube-ops-view
Before starting to learn about the various auto-scaling options for your EKS cluster we are going to install [Kube-ops-view](https://github.com/hjacobs/kube-ops-view) from [Henning Jacobs](https://github.com/hjacobs).

Kube-ops-view provides a common operational picture for a Kubernetes cluster that helps with understanding our cluster setup in a visual way.

__We will deploy kube-ops-view using Helm configured in a previous module__

The following line updates the stable helm repository and then installs kube-ops-view using a LoadBalancer Service type and creating a RBAC (Resource Base Access Control) entry for the read-only service account to read nodes and pods information from the cluster.
```
helm install kube-ops-view \
stable/kube-ops-view \
--set service.type=LoadBalancer \
--set rbac.create=True
```

The execution above installs kube-ops-view exposing it through a Service using the LoadBalancer type. A successful execution of the command will display the set of resources created and will prompt some advice asking you to use `kubectl proxy` and a local URL for the service. Given we are using the type LoadBalancer for our service, we can disregard this; Instead we will point our browser to the external load balancer.

__Monitoring and visualization shouldn’t be typically be exposed publicly unless the service is properly secured and provide methods for authentication and authorization. You can still deploy kube-ops-view using a Service of type ClusterIP by removing the --set service.type=LoadBalancer section and using kubectl proxy. Kube-ops-view does also support Oauth 2__

To check the chart was installed successfully:
```
helm list
```

should display :
```
NAME            REVISION        UPDATED                         STATUS          CHART                   APP VERSION     NAMESPACE
kube-ops-view   1               Sun Sep 22 11:47:31 2019        DEPLOYED        kube-ops-view-1.1.0     0.11            default  
```

With this we can explore kube-ops-view output by checking the details about the newly service created.
```
kubectl get svc kube-ops-view | tail -n 1 | awk '{ print "Kube-ops-view URL = http://"$4 }'
```

This will display a line similar to `Kube-ops-view URL = http://<URL_PREFIX_ELB>.amazonaws.com `. Opening the URL in your browser will provide the current state of our cluster.

__You may need to refresh the page and clean your browser cache. The creation and setup of the LoadBalancer may take a few minutes; usually in two minutes you should see kub-ops-view.__

![Kube Ops View](imgs/kube-ops-view.pmg "kube-ops-view")

As this workshop moves along and you perform scale up and down actions, you can check the effects and changes in the cluster using kube-ops-view. Check out the different components and see how they map to the concepts that we have already covered during this workshop.

__Spend some time checking the state and properties of your EKS cluster.__

![Kube Ops View](imgs/kube-ops-view-legend.pmg "kube-ops-view")

## Configure Horizontal Pod Autoscaler (HPA)
### Deploy the Metrics Server
Metrics Server is a scalable, efficient source of container resource metrics for Kubernetes built-in autoscaling pipelines.

These metrics will drive the scaling behavior of the [deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/).

We will deploy the metrics server using [Kubernetes Metrics Server](https://github.com/kubernetes-sigs/metrics-server).

```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.5.0/components.yaml
```

Lets' verify the status of the metrics-server APIService (it could take a few minutes).

```
kubectl get apiservice v1beta1.metrics.k8s.io -o json | jq '.status'
```

```
{
  "conditions": [
    {
      "lastTransitionTime": "2020-11-10T06:39:13Z",
      "message": "all checks passed",
      "reason": "Passed",
      "status": "True",
      "type": "Available"
    }
  ]
}
```

We are now ready to scale a deployed application

## Scale and application with HPA
### Deploy a Sample App
We will deploy an application and expose as a service on TCP port 80.

The application is a custom-built image based on the php-apache image. The index.php page performs calculations to generate CPU load. More information can be found [here](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/#run-expose-php-apache-server).
```
kubectl create deployment php-apache --image=us.gcr.io/k8s-artifacts-prod/hpa-example
kubectl set resources deploy php-apache --requests=cpu=200m
kubectl expose deploy php-apache --port 80

kubectl get pod -l app=php-apache
```

### Create an HPA resource
This HPA scales up when CPU exceeds 50% of the allocated container resource.
```
kubectl autoscale deployment php-apache `#The target average CPU utilization` \
    --cpu-percent=50 \
    --min=1 `#The lower limit for the number of pods that can be set by the autoscaler` \
    --max=10 `#The upper limit for the number of pods that can be set by the autoscaler`
```

View the HPA using kubectl. You probably will see `<unknown>/50%` for 1-2 minutes and then you should be able to see `0%/50%`

```
kubectl get hpa
```

### Generate load to trigger scaling
Open a new terminal in the Cloud9 Environment and run the following command to drop into a shell on a new container
```
kubectl --generator=run-pod/v1 run -i --tty load-generator --image=busybox /bin/sh
```

Execute a while loop to continue getting http:///php-apache
```
while true; do wget -q -O - http://php-apache; done
```

In the previous tab, watch the HPA with the following command

```
kubectl get hpa -w
```

You will see HPA scale the pods from 1 up to our configured maximum (10) until the CPU average is below our target (50%)

![HPA Results](imgs/scaling-hpa-results.png "Scaling HPA")

You can now stop (Ctrl + C) load test that was running in the other terminal. You will notice that HPA will slowly bring the replica count to min number based on its configuration. You should also get out of load testing application by pressing Ctrl + D.

## Configure Cluster Autoscaler (CA)
Cluster Autoscaler for AWS provides integration with Auto Scaling groups. It enables users to choose from four different options of deployment:

- One Auto Scaling group
- Multiple Auto Scaling groups
- Auto-Discovery
- Control-plane Node setup

Auto-Discovery is the preferred method to configure Cluster Autoscaler. Click [here](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler/cloudprovider/aws) for more information.

Cluster Autoscaler will attempt to determine the CPU, memory, and GPU resources provided by an Auto Scaling Group based on the instance type specified in its Launch Configuration or Launch Template.

### Configure the ASG
You configure the size of your Auto Scaling group by setting the minimum, maximum, and desired capacity. When we created the cluster we set these settings to 3.
```
aws autoscaling \
    describe-auto-scaling-groups \
    --query "AutoScalingGroups[? Tags[? (Key=='eks:cluster-name') && Value=='eksworkshop-eksctl']].[AutoScalingGroupName, MinSize, MaxSize,DesiredCapacity]" \
    --output table
```

```
-------------------------------------------------------------
|                 DescribeAutoScalingGroups                 |
+-------------------------------------------+----+----+-----+
|  eks-1eb9b447-f3c1-0456-af77-af0bbd65bc9f |  2 |  4 |  3  |
+-------------------------------------------+----+----+-----+

```

Now, increase the maximum capacity to 4 instances

```
# we need the ASG name
export ASG_NAME=$(aws autoscaling describe-auto-scaling-groups --query "AutoScalingGroups[? Tags[? (Key=='eks:cluster-name') && Value=='eksworkshop-eksctl']].AutoScalingGroupName" --output text)

# increase max capacity up to 4
aws autoscaling \
    update-auto-scaling-group \
    --auto-scaling-group-name ${ASG_NAME} \
    --min-size 3 \
    --desired-capacity 3 \
    --max-size 4

# Check new values
aws autoscaling \
    describe-auto-scaling-groups \
    --query "AutoScalingGroups[? Tags[? (Key=='eks:cluster-name') && Value=='eksworkshop-eksctl']].[AutoScalingGroupName, MinSize, MaxSize,DesiredCapacity]" \
    --output table
```

### IAM roles for service accounts

With IAM roles for service accounts on Amazon EKS clusters, you can associate an IAM role with a [Kubernetes service account](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/). This service account can then provide AWS permissions to the containers in any pod that uses that service account. With this feature, you no longer need to provide extended permissions to the node IAM role so that pods on that node can call AWS APIs.

Enabling IAM roles for service accounts on your cluster
```
eksctl utils associate-iam-oidc-provider \
    --cluster eksworkshop-eksctl \
    --approve
```

Creating an IAM policy for your service account that will allow your CA pod to interact with the autoscaling groups.
```
mkdir ~/environment/cluster-autoscaler

cat <<EoF > ~/environment/cluster-autoscaler/k8s-asg-policy.json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "autoscaling:DescribeAutoScalingGroups",
                "autoscaling:DescribeAutoScalingInstances",
                "autoscaling:DescribeLaunchConfigurations",
                "autoscaling:DescribeTags",
                "autoscaling:SetDesiredCapacity",
                "autoscaling:TerminateInstanceInAutoScalingGroup",
                "ec2:DescribeLaunchTemplateVersions"
            ],
            "Resource": "*",
            "Effect": "Allow"
        }
    ]
}
EoF

aws iam create-policy   \
  --policy-name k8s-asg-policy \
  --policy-document file://~/environment/cluster-autoscaler/k8s-asg-policy.json

```

Finally, create an IAM role for the cluster-autoscaler Service Account in the kube-system namespace.

```
eksctl create iamserviceaccount \
    --name cluster-autoscaler \
    --namespace kube-system \
    --cluster eksworkshop-eksctl \
    --attach-policy-arn "arn:aws:iam::${ACCOUNT_ID}:policy/k8s-asg-policy" \
    --approve \
    --override-existing-serviceaccounts
```

Make sure your service account with the ARN of the IAM role is annotated
```
kubectl -n kube-system describe sa cluster-autoscaler
```

Output

```
Name:                cluster-autoscaler
Namespace:           kube-system
Labels:              <none>
Annotations:         eks.amazonaws.com/role-arn: arn:aws:iam::197520326489:role/eksctl-eksworkshop-eksctl-addon-iamserviceac-Role1-12LNPCGBD6IPZ
Image pull secrets:  <none>
Mountable secrets:   cluster-autoscaler-token-vfk8n
Tokens:              cluster-autoscaler-token-vfk8n
Events:              <none>
```

### Deploy the Cluster Autoscaler (CA)
Deploy the Cluster Autoscaler to your cluster with the following command.
__The required YAML manifest file is in the `yamls` directory of the repository__

```
kubectl apply -f yamls/cluster-autoscaler-autodiscover.yaml
```

To prevent CA from removing nodes where its own pod is running, we will add the `cluster-autoscaler.kubernetes.io/safe-to-evict` annotation to its deployment with the following command

```
kubectl -n kube-system \
    annotate deployment.apps/cluster-autoscaler \
    cluster-autoscaler.kubernetes.io/safe-to-evict="false"
```

Finally let’s update the autoscaler image

```
# we need to retrieve the latest docker image available for our EKS version
export K8S_VERSION=$(kubectl version --short | grep 'Server Version:' | sed 's/[^0-9.]*\([0-9.]*\).*/\1/' | cut -d. -f1,2)
export AUTOSCALER_VERSION=$(curl -s "https://api.github.com/repos/kubernetes/autoscaler/releases" | grep '"tag_name":' | sed -s 's/.*-\([0-9][0-9\.]*\).*/\1/' | grep -m1 ${K8S_VERSION})

kubectl -n kube-system \
    set image deployment.apps/cluster-autoscaler \
    cluster-autoscaler=us.gcr.io/k8s-artifacts-prod/autoscaling/cluster-autoscaler:v${AUTOSCALER_VERSION}
```

Watch the logs
```
kubectl -n kube-system logs -f deployment/cluster-autoscaler
```

We are now ready to scale our cluster

Related files:
- [cluster-autoscaler-autodiscover.yaml](/yamls/cluster-autoscaler-autodiscover.yaml)

## Scale a cluster with CA
### Deploy a Sample App
We will deploy an sample `nginx` application as a `ReplicaSet` of 1 Pod

```
cat <<EoF> ~/environment/cluster-autoscaler/nginx.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-to-scaleout
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        service: nginx
        app: nginx
    spec:
      containers:
      - image: nginx
        name: nginx-to-scaleout
        resources:
          limits:
            cpu: 500m
            memory: 512Mi
          requests:
            cpu: 500m
            memory: 512Mi
EoF

kubectl apply -f ~/environment/cluster-autoscaler/nginx.yaml

kubectl get deployment/nginx-to-scaleout
```

### Scale our ReplicaSet

Let’s scale out the replicaset to 10

```
kubectl scale --replicas=10 deployment/nginx-to-scaleout
```

Some pods will be in the Pending state, which triggers the cluster-autoscaler to scale out the EC2 fleet.
```
kubectl get pods -l app=nginx -o wide --watch
```

```
NAME                                 READY     STATUS    RESTARTS   AGE

nginx-to-scaleout-7cb554c7d5-2d4gp   0/1       Pending   0          11s
nginx-to-scaleout-7cb554c7d5-2nh69   0/1       Pending   0          12s
nginx-to-scaleout-7cb554c7d5-45mqz   0/1       Pending   0          12s
nginx-to-scaleout-7cb554c7d5-4qvzl   0/1       Pending   0          12s
nginx-to-scaleout-7cb554c7d5-5jddd   1/1       Running   0          34s
nginx-to-scaleout-7cb554c7d5-5sx4h   0/1       Pending   0          12s
nginx-to-scaleout-7cb554c7d5-5xbjp   0/1       Pending   0          11s
nginx-to-scaleout-7cb554c7d5-6l84p   0/1       Pending   0          11s
nginx-to-scaleout-7cb554c7d5-7vp7l   0/1       Pending   0          12s
nginx-to-scaleout-7cb554c7d5-86pr6   0/1       Pending   0          12s
nginx-to-scaleout-7cb554c7d5-88ttw   0/1       Pending   0          12s
```

View the cluster-autoscaler logs
```
kubectl -n kube-system logs -f deployment/cluster-autoscaler
```

You will notice Cluster Autoscaler events similar to below

![Scaling ASG](imgs/scaling-asg-up2.png "Scaling ASG")

Check the [EC2 AWS Management Console](https://console.aws.amazon.com/ec2/home?#Instances:sort=instanceId) to confirm that the Auto Scaling groups are scaling up to meet demand. This may take a few minutes. You can also follow along with the pod deployment from the command line. You should see the pods transition from pending to running as nodes are scaled up.

![Scaling ASG](imgs/scaling-asg-up.png "Scaling ASG")


or by using the kubectl

```
kubectl get nodes
```

Output

```
ip-192-168-12-114.us-east-2.compute.internal   Ready    <none>   3d6h   v1.17.7-eks-bffbac
ip-192-168-29-155.us-east-2.compute.internal   Ready    <none>   63s    v1.17.7-eks-bffbac
ip-192-168-55-187.us-east-2.compute.internal   Ready    <none>   3d6h   v1.17.7-eks-bffbac
ip-192-168-82-113.us-east-2.compute.internal   Ready    <none>   8h     v1.17.7-eks-bffbac
```

## Clean Up
```
kubectl delete -f ~/environment/cluster-autoscaler/nginx.yaml

kubectl delete -f https://www.eksworkshop.com/beginner/080_scaling/deploy_ca.files/cluster-autoscaler-autodiscover.yaml

eksctl delete iamserviceaccount \
  --name cluster-autoscaler \
  --namespace kube-system \
  --cluster eksworkshop-eksctl \
  --wait

aws iam delete-policy \
  --policy-arn arn:aws:iam::${ACCOUNT_ID}:policy/k8s-asg-policy

export ASG_NAME=$(aws autoscaling describe-auto-scaling-groups --query "AutoScalingGroups[? Tags[? (Key=='eks:cluster-name') && Value=='eksworkshop-eksctl']].AutoScalingGroupName" --output text)

aws autoscaling \
  update-auto-scaling-group \
  --auto-scaling-group-name ${ASG_NAME} \
  --min-size 3 \
  --desired-capacity 3 \
  --max-size 3

kubectl delete hpa,svc php-apache

kubectl delete deployment php-apache

kubectl delete pod load-generator

cd ~/environment

rm -rf ~/environment/cluster-autoscaler

kubectl delete -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.4.1/components.yaml

kubectl delete ns metrics

helm uninstall kube-ops-view

unset ASG_NAME
unset AUTOSCALER_VERSION
unset K8S_VERSION
```

# Introduction to RBAC
In this module, we’ll learn about how role based access control (RBAC) works in kubernetes.

## What is RBAC?
According to [the official kubernetes docs](https://kubernetes.io/docs/reference/access-authn-authz/rbac/):

_Role-based access control (RBAC) is a method of regulating access to computer or network resources based on the roles of individual users within an enterprise._

The core logical components of RBAC are:

- Entity
A group, user, or service account (an identity representing an application that wants to execute certain operations (actions) and requires permissions to do so).

- Resource
A pod, service, or secret that the entity wants to access using the certain operations.

- Role
Used to define rules for the actions the entity can take on various resources.

- Role binding
This attaches (binds) a role to an entity, stating that the set of rules define the actions permitted by the attached entity on the specified resources.

There are two types of Roles (Role, ClusterRole) and the respective bindings (RoleBinding, ClusterRoleBinding). These differentiate between authorization in a namespace or cluster-wide.

- Namespace

Namespaces are an excellent way of creating security boundaries, they also provide a unique scope for object names as the ‘namespace’ name implies. They are intended to be used in multi-tenant environments to create virtual kubernetes clusters on the same physical cluster.

### Objectives for this module
In this module, we’re going to explore k8s RBAC by creating an IAM user called rbac-user who is authenticated to access the EKS cluster but is only authorized (via RBAC) to list, get, and watch pods and deployments in the ‘rbac-test’ namespace.

To achieve this, we’ll create an IAM user, map that user to a kubernetes role, then perform kubernetes actions under that user’s context.


## Install the test Pods
In this tutorial, we’re going to demonstrate how to provide limited access to pods running in the rbac-test namespace for a user named rbac-user.

To do that, let’s first create the rbac-test namespace, and then install nginx into it:
```
kubectl create namespace rbac-test
kubectl create deploy nginx --image=nginx -n rbac-test
```

To verify the test pods were properly installed, run:
```
kubectl get all -n rbac-test
```

Output should be similar to:

```
NAME                       READY   STATUS    RESTARTS   AGE
pod/nginx-5c7588df-8mvxx   1/1     Running   0          48s

NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx   1/1     1            1           48s

NAME                             DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-5c7588df   1         1         1       48s

```

## Create a user
__For the sake of simplicity, in this module, we will save credentials to a file to make it easy to toggle back and forth between users. Never do this in production or with credentials that have privileged access; It is not a security best practice to store credentials on the filesystem.__

From within the Cloud9 terminal, create a new user called rbac-user, and generate/save credentials for it:
```
aws iam create-user --user-name rbac-user
aws iam create-access-key --user-name rbac-user | tee /tmp/create_output.json
```

By running the previous step, you should get a response similar to:

```
{
    "AccessKey": {
        "UserName": "rbac-user",
        "Status": "Active",
        "CreateDate": "2019-07-17T15:37:27Z",
        "SecretAccessKey": < AWS Secret Access Key > ,
        "AccessKeyId": < AWS Access Key >
    }
}
```

To make it easy to switch back and forth between the admin user you created the cluster with, and this new rbac-user, run the following command to create a script that when sourced, sets the active user to be rbac-user:
```
cat << EoF > rbacuser_creds.sh
export AWS_SECRET_ACCESS_KEY=$(jq -r .AccessKey.SecretAccessKey /tmp/create_output.json)
export AWS_ACCESS_KEY_ID=$(jq -r .AccessKey.AccessKeyId /tmp/create_output.json)
EoF
```

## Map an IAM User to K8s
Next, we’ll define a k8s user called rbac-user, and map to its IAM user counterpart. Run the following to get the existing ConfigMap and save into a file called aws-auth.yaml:
```
kubectl get configmap -n kube-system aws-auth -o yaml | grep -v "creationTimestamp\|resourceVersion\|selfLink\|uid" | sed '/^  annotations:/,+2 d' > aws-auth.yaml
```

Next append the rbac-user mapping to the existing configMap

```
cat << EoF >> aws-auth.yaml
data:
  mapUsers: |
    - userarn: arn:aws:iam::${ACCOUNT_ID}:user/rbac-user
      username: rbac-user
EoF
```

Some of the values may be dynamically populated when the file is created. To verify everything populated and was created correctly, run the following:
```
cat aws-auth.yaml
```

And the output should reflect that rolearn and userarn populated, similar to:

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: aws-auth
  namespace: kube-system
data:
  mapUsers: |
    - userarn: arn:aws:iam::123456789:user/rbac-user
      username: rbac-user
```

Next, apply the ConfigMap to apply this mapping to the system:

```
kubectl apply -f aws-auth.yaml
```

## Test the new user
Up until now, as the cluster operator, you’ve been accessing the cluster as the admin user. Let’s now see what happens when we access the cluster as the newly created rbac-user.

Issue the following command to source the rbac-user’s AWS IAM user environmental variables:

```
. rbacuser_creds.sh
```

By running the above command, you’ve now set AWS environmental variables which should override the default admin user or role. To verify we’ve overrode the default user settings, run the following command:
```
aws sts get-caller-identity
```
You should see something similar to below, where we’re now making API calls as rbac-user:

```

{
    "Account": <AWS Account ID>,
    "UserId": <AWS User ID>,
    "Arn": "arn:aws:iam::<AWS Account ID>:user/rbac-user"
}
```

Now that we’re making calls in the context of the rbac-user, lets quickly make a request to get all pods:
```
kubectl get pods -n rbac-test
```
You should get a response back similar to:


```
No resources found.  Error from server (Forbidden): pods is forbidden: User "rbac-user" cannot list resource "pods" in API group "" in the namespace "rbac-test"
```

We already created the `rbac-user`, so why did we get that error?

Just creating the user doesn’t give that user access to any resources in the cluster. In order to achieve that, we’ll need to define a role, and then bind the user to that role. We’ll do that next.

## Create the role and the role binding
As mentioned earlier, we have our new user rbac-user, but its not yet bound to any roles. In order to do that, we’ll need to switch back to our default admin user.

Run the following to unset the environmental variables that define us as rbac-user:
```
unset AWS_SECRET_ACCESS_KEY
unset AWS_ACCESS_KEY_ID
```

To verify we’re the admin user again, and no longer rbac-user, issue the following command:

```
aws sts get-caller-identity
```

The output should show the user is no longer rbac-user:

```
{
    "Account": <AWS Account ID>,
    "UserId": <AWS User ID>,
    "Arn": "arn:aws:iam::<your AWS account ID>:assumed-role/eksworkshop-admin/i-123456789"
}
```

Now that we’re the admin user again, we’ll create a role called pod-reader that provides list, get, and watch access for pods and deployments, but only for the rbac-test namespace. Run the following to create this role:
```
cat << EoF > rbacuser-role.yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: rbac-test
  name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["pods"]
  verbs: ["list","get","watch"]
- apiGroups: ["extensions","apps"]
  resources: ["deployments"]
  verbs: ["get", "list", "watch"]
EoF
We have the user, we have the role, and now we’re bind them together with a RoleBinding resource. Run the following to create this RoleBinding:

cat << EoF > rbacuser-role-binding.yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: read-pods
  namespace: rbac-test
subjects:
- kind: User
  name: rbac-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
EoF
```

Next, we apply the Role, and RoleBindings we created:
```
kubectl apply -f rbacuser-role.yaml
kubectl apply -f rbacuser-role-binding.yaml
```

## Verify the role and binding
Now that the user, Role, and RoleBinding are defined, lets switch back to rbac-user, and test.

To switch back to rbac-user, issue the following command that sources the rbac-user env vars, and verifies they’ve taken:
```
. rbacuser_creds.sh; aws sts get-caller-identity
```

You should see output reflecting that you are logged in as rbac-user.

As rbac-user, issue the following to get pods in the rbac namespace:
```
kubectl get pods -n rbac-test
```
The output should be similar to:

```
NAME                    READY     STATUS    RESTARTS   AGE
nginx-55bd7c9fd-kmbkf   1/1       Running   0          23h
```

Try running the same command again, but outside of the rbac-test namespace:
```
kubectl get pods -n kube-system
```

You should get an error similar to:

```
No resources found.
Error from server (Forbidden): pods is forbidden: User "rbac-user" cannot list resource "pods" in API group "" in the namespace "kube-system"
```

Because the role you are bound to does not give you access to any namespace other than rbac-test.

## Clean up
Once you have completed this module, you can cleanup the files and resources you created by issuing the following commands:
```
unset AWS_SECRET_ACCESS_KEY
unset AWS_ACCESS_KEY_ID
kubectl delete namespace rbac-test
rm rbacuser_creds.sh
rm rbacuser-role.yaml
rm rbacuser-role-binding.yaml
aws iam delete-access-key --user-name=rbac-user --access-key-id=$(jq -r .AccessKey.AccessKeyId /tmp/create_output.json)
aws iam delete-user --user-name rbac-user
rm /tmp/create_output.json
```

Next remove the rbac-user mapping from the existing configMap by editing the existing aws-auth.yaml file:
```
data:
  mapUsers: |
    []
```

And apply the ConfigMap and delete the aws-auth.yaml file

```
kubectl apply -f aws-auth.yaml
rm aws-auth.yaml
```

# Using IAM groups to manage Kubernetes cluster access
In this module, we’ll learn about how to simplify access to different parts of the kubernetes clusters depending on IAM Groups

## Kubernetes authenticationIn the intro to RBAC module, we have seen how we can give access to individual users to Kubernetes.

If you have different teams which needs different kind of cluster access, it would be difficult to manually add or remove access for each EKS clusters you want them to give or remove access from.

We can leverage on AWS IAM Groups to easily add or remove users and give them permission to whole cluster, or just part of it depending on which groups they belong to.

In this lesson, we will create 3 IAM roles that we will map to 3 IAM groups.

## Create IAM Roles
We are going to create 3 roles:

- a k8sAdmin role which will have admin rights in our EKS cluster
- a k8sDev role which will give access to the developers namespace in our EKS cluster
- a k8sInteg role which will give access to the integration namespace in our EKS cluster

Create the roles:
```
POLICY=$(echo -n '{"Version":"2012-10-17","Statement":[{"Effect":"Allow","Principal":{"AWS":"arn:aws:iam::'; echo -n "$ACCOUNT_ID"; echo -n ':root"},"Action":"sts:AssumeRole","Condition":{}}]}')

echo ACCOUNT_ID=$ACCOUNT_ID
echo POLICY=$POLICY

aws iam create-role \
  --role-name k8sAdmin \
  --description "Kubernetes administrator role (for AWS IAM Authenticator for Kubernetes)." \
  --assume-role-policy-document "$POLICY" \
  --output text \
  --query 'Role.Arn'

aws iam create-role \
  --role-name k8sDev \
  --description "Kubernetes developer role (for AWS IAM Authenticator for Kubernetes)." \
  --assume-role-policy-document "$POLICY" \
  --output text \
  --query 'Role.Arn'

aws iam create-role \
  --role-name k8sInteg \
  --description "Kubernetes role for integration namespace in quick cluster." \
  --assume-role-policy-document "$POLICY" \
  --output text \
  --query 'Role.Arn'

```

__In this example, the `assume-role-policy` allows the root account to assume the role. We are going to allow specific groups to also be able to assume those roles. Check [the official documentation](https://docs.aws.amazon.com/eks/latest/userguide/iam-roles-for-service-accounts-technical-overview.html) for more information.__

Because the above roles are only used to authenticate within our EKS cluster, they don’t need to have AWS permissions. We will only use them to allow some IAM groups to assume this role in order to have access to our EKS cluster.


## Create IAM Groups
We want to have different IAM users which will be added to specific IAM groups in order to have different rights in the kubernetes cluster.

We will define 3 groups:

- k8sAdmin - users from this group will have admin rights on the kubernetes cluster
- k8sDev - users from this group will have full access only in the development namespace of the cluster
- k8sInteg - users from this group will have access to integration namespace.

In fact, users from `k8sDev` and `k8sInteg` groups will only have access to namespaces where we will define kubernetes RBAC access for their associated kubernetes role. We’ll see this but first, let’s creates the groups.

### Create k8sAdmin IAM Group
The k8sAdmin Group will be allowed to assume the k8sAdmin IAM Role.
```
aws iam create-group --group-name k8sAdmin
```

Let’s add a Policy on our group which will allow users from this group to assume our k8sAdmin Role:
```
ADMIN_GROUP_POLICY=$(echo -n '{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowAssumeOrganizationAccountRole",
      "Effect": "Allow",
      "Action": "sts:AssumeRole",
      "Resource": "arn:aws:iam::'; echo -n "$ACCOUNT_ID"; echo -n ':role/k8sAdmin"
    }
  ]
}')
echo ADMIN_GROUP_POLICY=$ADMIN_GROUP_POLICY

aws iam put-group-policy \
--group-name k8sAdmin \
--policy-name k8sAdmin-policy \
--policy-document "$ADMIN_GROUP_POLICY"

```

### Create k8sDev IAM Group
The k8sDev Group will be allowed to assume the k8sDev IAM Role.

```
aws iam create-group --group-name k8sDev
```

Let’s add a Policy on our group which will allow users from this group to assume our k8sDev Role:
```
DEV_GROUP_POLICY=$(echo -n '{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowAssumeOrganizationAccountRole",
      "Effect": "Allow",
      "Action": "sts:AssumeRole",
      "Resource": "arn:aws:iam::'; echo -n "$ACCOUNT_ID"; echo -n ':role/k8sDev"
    }
  ]
}')
echo DEV_GROUP_POLICY=$DEV_GROUP_POLICY

aws iam put-group-policy \
--group-name k8sDev \
--policy-name k8sDev-policy \
--policy-document "$DEV_GROUP_POLICY"
```

### Create k8sInteg IAM Group
```
aws iam create-group --group-name k8sInteg
```

Let’s add a Policy on our group which will allow users from this group to assume our k8sInteg Role:

```
INTEG_GROUP_POLICY=$(echo -n '{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowAssumeOrganizationAccountRole",
      "Effect": "Allow",
      "Action": "sts:AssumeRole",
      "Resource": "arn:aws:iam::'; echo -n "$ACCOUNT_ID"; echo -n ':role/k8sInteg"
    }
  ]
}')
echo INTEG_GROUP_POLICY=$INTEG_GROUP_POLICY

aws iam put-group-policy \
--group-name k8sInteg \
--policy-name k8sInteg-policy \
--policy-document "$INTEG_GROUP_POLICY"
```

You now should have your 3 groups
```
aws iam list-groups
```

```
{
    "Groups": [
        {
            "Path": "/",
            "GroupName": "k8sAdmin",
            "GroupId": "AGPAZRV3OHPJZGT2JKVDV",
            "Arn": "arn:aws:iam::xxxxxxxxxx:group/k8sAdmin",
            "CreateDate": "2020-04-07T13:32:52Z"
        },
        {
            "Path": "/",
            "GroupName": "k8sDev",
            "GroupId": "AGPAZRV3OHPJUOBR375KI",
            "Arn": "arn:aws:iam::xxxxxxxxxx:group/k8sDev",
            "CreateDate": "2020-04-07T13:33:15Z"
        },
        {
            "Path": "/",
            "GroupName": "k8sInteg",
            "GroupId": "AGPAZRV3OHPJR6GM6PFDG",
            "Arn": "arn:aws:iam::xxxxxxxxxx:group/k8sInteg",
            "CreateDate": "2020-04-07T13:33:25Z"
        }
    ]
}
```

## Create IAM Users
In order to test our scenarios, we will create 3 users, one for each groups we created :
```
aws iam create-user --user-name PaulAdmin
aws iam create-user --user-name JeanDev
aws iam create-user --user-name PierreInteg
```

Add users to associated groups:

```
aws iam add-user-to-group --group-name k8sAdmin --user-name PaulAdmin
aws iam add-user-to-group --group-name k8sDev --user-name JeanDev
aws iam add-user-to-group --group-name k8sInteg --user-name PierreInteg
```

Check users are correctly added in their groups:

```
aws iam get-group --group-name k8sAdmin
aws iam get-group --group-name k8sDev
aws iam get-group --group-name k8sInteg
```

__For the sake of simplicity, in this module, we will save credentials to a file to make it easy to toggle back and forth between users. Never do this in production or with credentials that have priviledged access; It is not a security best practice to store credentials on the filesystem.__

Retrieve Access Keys for our fake users:
```
aws iam create-access-key --user-name PaulAdmin | tee /tmp/PaulAdmin.json
aws iam create-access-key --user-name JeanDev | tee /tmp/JeanDev.json
aws iam create-access-key --user-name PierreInteg | tee /tmp/PierreInteg.json
```

Recap:

- PaulAdmin is in the k8sAdmin group and will be able to assume the k8sAdmin role.
- JeanDev is in k8sDev Group and will be able to assume IAM role k8sDev
- PierreInteg is in k8sInteg group and will be able to assume IAM role k8sInteg

## Configure Kubernetes RBAC
You can refer to intro to RBAC module to understand the basic of Kubernetes RBAC.

### Create kubernetes namespaces
- development namespace will be accessible for IAM users from k8sDev group
- integration namespace will be accessible for IAM users from k8sInteg group
```
kubectl create namespace integration
kubectl create namespace development
```

### Configuring access to development namespace
We create a kubernetes role and rolebinding in the development namespace giving full access to the kubernetes user dev-user
```
cat << EOF | kubectl apply -f - -n development
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-role
rules:
  - apiGroups:
      - ""
      - "apps"
      - "batch"
      - "extensions"
    resources:
      - "configmaps"
      - "cronjobs"
      - "deployments"
      - "events"
      - "ingresses"
      - "jobs"
      - "pods"
      - "pods/attach"
      - "pods/exec"
      - "pods/log"
      - "pods/portforward"
      - "secrets"
      - "services"
    verbs:
      - "create"
      - "delete"
      - "describe"
      - "get"
      - "list"
      - "patch"
      - "update"
---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-role-binding
subjects:
- kind: User
  name: dev-user
roleRef:
  kind: Role
  name: dev-role
  apiGroup: rbac.authorization.k8s.io
EOF
```

The role we define will give full access to everything in that namespace. It is a Role, and not a ClusterRole, so it is going to be applied only in the development namespace.

_feel free to adapt or duplicate to any namespace you prefer._

### Configuring access to integration namespace

We create a kubernetes role and rolebinding in the integration namespace for full access with the kubernetes user integ-user
```
cat << EOF | kubectl apply -f - -n integration
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: integ-role
rules:
  - apiGroups:
      - ""
      - "apps"
      - "batch"
      - "extensions"
    resources:
      - "configmaps"
      - "cronjobs"
      - "deployments"
      - "events"
      - "ingresses"
      - "jobs"
      - "pods"
      - "pods/attach"
      - "pods/exec"
      - "pods/log"
      - "pods/portforward"
      - "secrets"
      - "services"
    verbs:
      - "create"
      - "delete"
      - "describe"
      - "get"
      - "list"
      - "patch"
      - "update"
---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: integ-role-binding
subjects:
- kind: User
  name: integ-user
roleRef:
  kind: Role
  name: integ-role
  apiGroup: rbac.authorization.k8s.io
EOF
```

The role we define will give full access to everything in that namespace. It is a Role, and not a ClusterRole, so it is going to be applied only in the integration namespace.

## Configure Kubernetes Role Access
### Gives Access to our IAM Roles to EKS Cluster
In order to give access to the IAM Roles we defined previously to our EKS cluster, we need to add specific mapRoles to the aws-auth ConfigMap

The Advantage of using Role to access the cluster instead of specifying directly IAM users is that it will be easier to manage: we won’t have to update the ConfigMap each time we want to add or remove users, we will just need to add or remove users from the IAM Group and we just configure the ConfigMap to allow the IAM Role associated to the IAM Group.

Update the aws-auth ConfigMap to allow our IAM roles
The aws-auth ConfigMap from the kube-system namespace must be edited in order to allow or delete arn Groups.

This file makes the mapping between IAM role and k8S RBAC rights. We can edit it manually:

We can edit it using eksctl :
```
eksctl create iamidentitymapping \
  --cluster eksworkshop-eksctl \
  --arn arn:aws:iam::${ACCOUNT_ID}:role/k8sDev \
  --username dev-user

eksctl create iamidentitymapping \
  --cluster eksworkshop-eksctl \
  --arn arn:aws:iam::${ACCOUNT_ID}:role/k8sInteg \
  --username integ-user

eksctl create iamidentitymapping \
  --cluster eksworkshop-eksctl \
  --arn arn:aws:iam::${ACCOUNT_ID}:role/k8sAdmin \
  --username admin \
  --group system:masters
```

It can also be used to delete entries
```
eksctl delete iamidentitymapping --cluster eksworkshop-eksctlv --arn arn:aws:iam::xxxxxxxxxx:role/k8sDev --username dev-user
```

you should have the config map looking something like:
```
kubectl get cm -n kube-system aws-auth -o yaml
```
```
apiVersion: v1
data:
  mapRoles: |
    - groups:
      - system:bootstrappers
      - system:nodes
      rolearn: arn:aws:iam::xxxxxxxxxx:role/eksctl-eksworkshop-eksctl-nodegro-NodeInstanceRole-14TKBWBD7KWFH
      username: system:node:{{EC2PrivateDNSName}}
    - rolearn: arn:aws:iam::xxxxxxxxxx:role/k8sDev
      username: dev-user
    - rolearn: arn:aws:iam::xxxxxxxxxx:role/k8sInteg
      username: integ-user
    - groups:
      - system:masters
      rolearn: arn:aws:iam::xxxxxxxxxx:role/k8sAdmin
      username: admin
  mapUsers: |
    []
kind: ConfigMap
```

We can leverage eksctl to get a list of all identities managed in our cluster. Example:
```
eksctl get iamidentitymapping --cluster eksworkshop-eksctl
```


```
arn:aws:iam::xxxxxxxxxx:role/eksctl-quick-nodegroup-ng-fe1bbb6-NodeInstanceRole-1KRYARWGGHPTT	system:node:{{EC2PrivateDNSName}}	system:bootstrappers,system:nodes
arn:aws:iam::xxxxxxxxxx:role/k8sAdmin           admin					system:masters
arn:aws:iam::xxxxxxxxxx:role/k8sDev             dev-user
arn:aws:iam::xxxxxxxxxx:role/k8sInteg           integ-user
```

Here we have created:

- a RBAC role for K8sAdmin, that we map to admin user and give access to system:masters kubernetes Groups (so that it has Full Admin rights)
- a RBAC role for k8sDev that we map on dev-user in development namespace
- a RBAC role for k8sInteg that we map on integ-user in integration namespace

We will see on next section how we can test it.

## Test EKS Access
### Automate assumerole with aws cli
It is possible to automate the retrieval of temporary credentials for the assumed role by configuring the AWS CLI in the files `~/.aws/config` and `~/.aws/credentials`. As an example, we will define three profiles.

Add in ~/.aws/config:
```
mkdir -p ~/.aws

cat << EoF >> ~/.aws/config
[profile admin]
role_arn=arn:aws:iam::${ACCOUNT_ID}:role/k8sAdmin
source_profile=eksAdmin

[profile dev]
role_arn=arn:aws:iam::${ACCOUNT_ID}:role/k8sDev
source_profile=eksDev

[profile integ]
role_arn=arn:aws:iam::${ACCOUNT_ID}:role/k8sInteg
source_profile=eksInteg

EoF
```

Add in ~/.aws/credentials:
```
cat << EoF >> ~/.aws/credentials

[eksAdmin]
aws_access_key_id=$(jq -r .AccessKey.AccessKeyId /tmp/PaulAdmin.json)
aws_secret_access_key=$(jq -r .AccessKey.SecretAccessKey /tmp/PaulAdmin.json)

[eksDev]
aws_access_key_id=$(jq -r .AccessKey.AccessKeyId /tmp/JeanDev.json)
aws_secret_access_key=$(jq -r .AccessKey.SecretAccessKey /tmp/JeanDev.json)

[eksInteg]
aws_access_key_id=$(jq -r .AccessKey.AccessKeyId /tmp/PierreInteg.json)
aws_secret_access_key=$(jq -r .AccessKey.SecretAccessKey /tmp/PierreInteg.json)

EoF
```

Test this with the dev profile:

```
aws sts get-caller-identity --profile dev

{
    "UserId": "AROAUD5VMKW75WJEHFU4X:botocore-session-1581687024",
    "Account": "xxxxxxxxxx",
    "Arn": "arn:aws:sts::xxxxxxxxxx:assumed-role/k8sDev/botocore-session-1581687024"
}
```

The assumed-role is k8sDev, so we achieved our goal.

When specifying the __–profile dev__ parameter we automatically ask for temporary credentials for the role k8sDev. You can test this with `integ` and `admin` also.
```
aws sts get-caller-identity --profile admin

{
    "UserId": "AROAUD5VMKW77KXQAL7ZX:botocore-session-1582022121",
    "Account": "xxxxxxxxxx",
    "Arn": "arn:aws:sts::xxxxxxxxxx:assumed-role/k8sAdmin/botocore-session-1582022121"
}
```

__When specifying the __–profile admin__ parameter we automatically ask for temporary credentials for the role `k8sAdmin`__

### Using AWS profiles with the Kubectl config file

It is also possible to specify the AWS_PROFILE to use with the aws-iam-authenticator in the `~/.kube/config` file, so that it will use the appropriate profile.

#### With dev profile
Create a new KUBECONFIG file to test this:
```
export KUBECONFIG=/tmp/kubeconfig-dev && eksctl utils write-kubeconfig eksworkshop-eksctl
cat $KUBECONFIG | yq e '.users.[].user.exec.args += ["--profile", "dev"]' - -- | sed 's/eksworkshop-eksctl./eksworkshop-eksctl-dev./g' | sponge $KUBECONFIG
```

__Note: this assume you uses yq >= version 4. you can reference to this page to adapt this command for another version.__


We added the `--profile dev` parameter to our kubectl config file, so that this will ask `kubectl` to use our IAM role associated to our `dev` profile, and we rename the context using suffix `-dev`.

With this configuration we should be able to interact with the development namespace, because it has our RBAC role defined.

Let’s create a pod:

```
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx -n development
```

We can list the pods:
```

kubectl get pods -n development
```
```

NAME                     READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          28h
```

… but not in other namespaces:

```
kubectl get pods -n integration

```

```
Error from server (Forbidden): pods is forbidden: User "dev-user" cannot list resource "pods" in API group "" in the namespace "integration"
```

#### Test with integ profile
```
export KUBECONFIG=/tmp/kubeconfig-integ && eksctl utils write-kubeconfig eksworkshop-eksctl
cat $KUBECONFIG | yq e '.users.[].user.exec.args += ["--profile", "integ"]' - -- | sed 's/eksworkshop-eksctl./eksworkshop-eksctl-integ./g' | sponge $KUBECONFIG
```
__Note: this assume you uses yq >= version 4. you can reference to this page to adapt this command for another version.__

Let’s create a pod:
```
kubectl run --generator=run-pod/v1 nginx-integ --image=nginx -n integration
```

We can list the pods:

```
kubectl get pods -n integration
```

```
NAME          READY   STATUS    RESTARTS   AGE
nginx-integ   1/1     Running   0          43s

```

… but not in other namespaces:
```
kubectl get pods -n development
```

```
Error from server (Forbidden): pods is forbidden: User "integ-user" cannot list resource "pods" in API group "" in the namespace "development"
```

#### Test with admin profile
```
export KUBECONFIG=/tmp/kubeconfig-admin && eksctl utils write-kubeconfig eksworkshop-eksctl
cat $KUBECONFIG | yq e '.users.[].user.exec.args += ["--profile", "admin"]' - -- | sed 's/eksworkshop-eksctl./eksworkshop-eksctl-admin./g' | sponge $KUBECONFIG
```

__Note: this assume you uses yq >= version 4. you can reference to this page to adapt this command for another version.__


Let’s create a pod in the default namespace:

```
kubectl run --generator=run-pod/v1 nginx-admin --image=nginx
```

We can list the pods:

```
kubectl get pods
```

```
NAME          READY   STATUS    RESTARTS   AGE
nginx-integ   1/1     Running   0          43s
```

We can list ALL pods in all namespaces:

```
kubectl get pods -A
```

```
NAMESPACE     NAME                       READY   STATUS    RESTARTS   AGE
default       nginx-admin                1/1     Running   0          15s
development   nginx-dev                  1/1     Running   0          11m
integration   nginx-integ                1/1     Running   0          4m29s
kube-system   aws-node-mzbh4             1/1     Running   0          100m
kube-system   aws-node-p7nj7             1/1     Running   0          100m
kube-system   aws-node-v2kg9             1/1     Running   0          100m
kube-system   coredns-85bb8bb6bc-2qbx6   1/1     Running   0          105m
kube-system   coredns-85bb8bb6bc-87ndr   1/1     Running   0          105m
kube-system   kube-proxy-4n5lc           1/1     Running   0          100m
kube-system   kube-proxy-b65xm           1/1     Running   0          100m
kube-system   kube-proxy-pr7k7           1/1     Running   0          100m
```


###Switching between different contexts

It is possible to configure several Kubernetes API access keys in the same KUBECONFIG file, or just tell Kubectl to lookup several files:

```
export KUBECONFIG=/tmp/kubeconfig-dev:/tmp/kubeconfig-integ:/tmp/kubeconfig-admin
```

There is a tool [kubectx / kubens](https://github.com/ahmetb/kubectx) that will help manage KUBECONFIG files with several contexts:

```
curl -sSLO https://raw.githubusercontent.com/ahmetb/kubectx/master/kubectx && chmod 755 kubectx && sudo mv kubectx /usr/local/bin
```

I can use kubectx to quickly list or switch Kubernetes contexts:

```
kubectx

i-0397aa1339e238a99@eksworkshop-eksctl-admin.eu-west-2.eksctl.io
i-0397aa1339e238a99@eksworkshop-eksctl-dev.eu-west-2.eksctl.io
i-0397aa1339e238a99@eksworkshop-eksctl-integ.eu-west-2.eksctl.io
```

## Conclusion
In this module, we have seen how to configure EKS to provide finer access to users combining IAM Groups and Kubernetes RBAC. You can create different groups depending on your needs, configure their associated RBAC access in your cluster, and simply add or remove users from the group to grant or revoke access to your cluster.

Users will only have to configure their AWS CLI in order to automatically retrieve their associated rights in your cluster.

## Clean Up
Once you have completed this module, you can cleanup the files and resources you created by issuing the following commands:

```
unset KUBECONFIG

kubectl delete namespace development integration
kubectl delete pod nginx-admin

eksctl delete iamidentitymapping --cluster eksworkshop-eksctl --arn arn:aws:iam::${ACCOUNT_ID}:role/k8sAdmin
eksctl delete iamidentitymapping --cluster eksworkshop-eksctl --arn arn:aws:iam::${ACCOUNT_ID}:role/k8sDev
eksctl delete iamidentitymapping --cluster eksworkshop-eksctl --arn arn:aws:iam::${ACCOUNT_ID}:role/k8sInteg

aws iam remove-user-from-group --group-name k8sAdmin --user-name PaulAdmin
aws iam remove-user-from-group --group-name k8sDev --user-name JeanDev
aws iam remove-user-from-group --group-name k8sInteg --user-name PierreInteg

aws iam delete-group-policy --group-name k8sAdmin --policy-name k8sAdmin-policy
aws iam delete-group-policy --group-name k8sDev --policy-name k8sDev-policy
aws iam delete-group-policy --group-name k8sInteg --policy-name k8sInteg-policy

aws iam delete-group --group-name k8sAdmin
aws iam delete-group --group-name k8sDev
aws iam delete-group --group-name k8sInteg

aws iam delete-access-key --user-name PaulAdmin --access-key-id=$(jq -r .AccessKey.AccessKeyId /tmp/PaulAdmin.json)
aws iam delete-access-key --user-name JeanDev --access-key-id=$(jq -r .AccessKey.AccessKeyId /tmp/JeanDev.json)
aws iam delete-access-key --user-name PierreInteg --access-key-id=$(jq -r .AccessKey.AccessKeyId /tmp/PierreInteg.json)

aws iam delete-user --user-name PaulAdmin
aws iam delete-user --user-name JeanDev
aws iam delete-user --user-name PierreInteg

aws iam delete-role --role-name k8sAdmin
aws iam delete-role --role-name k8sDev
aws iam delete-role --role-name k8sInteg

rm /tmp/*.json
rm /tmp/kubeconfig*

# reset aws credentials and config files
rm  ~/.aws/{config,credentials}
aws configure set default.region ${AWS_REGION}
```

# How to Setup AWS App Mesh with EKS?
AWS App Mesh is a service mesh that provides application-level networking to make it easy for your services to communicate with each other across multiple types of compute infrastructure.

### Components of App Mesh

Service mesh

Virtual services 

Virtual nodes 

Envoy proxy 

Virtual routers 

Routes

### Prerequisites

AWS account

EKS cluster

AWS CLI

Eksctl

Kubectl

ECR Registry

Helm version 3.0 or later installed.

### 1. To connect with AWS account

'''
aws configure   (Provide Access Key, Secret Access Key and Region)

'''
### 2. Firstly, create an EKS Cluster if you don’t have any existing one.


'''
eksctl create cluster --name mesh-test-cluster --version 1.21 --region ap-

south-1 --zones ap-south-1a,ap-south-1b --nodegroup-name cls-nodes --node-

type t2.micro --nodes 2

'''

### 3. Further to connect with EKS cluster update the Kubeconfig.

'''
aws eks --region ap-south-1 update-kubeconfig --name mesh-test-cluster

'''

### 4. Add the eks-charts repository to Helm.

'''
helm repo add eks https://aws.github.io/eks-charts

'''

### 5. Install the App Mesh Kubernetes custom resource definitions (CRD).

'''
kubectl apply -k "https://github.com/aws/eks-charts/stable/appmesh-controller

/crds?ref=master"

'''

### 6. Create appmesh-system Namespace.

'''
kubectl create ns appmesh-system

'''

### 7. Create an OpenID Connect (OIDC) identity provider for your cluster.

'''
 eksctl utils associate-iam-oidc-provider --region=ap-south-1 --cluster mesh-

test-cluster --approve

'''
### 8. Create an IAM role, attach the AWSAppMeshFullAccess and AWSCloudMapFullAccess policies to it, and bind it to the appmesh-controller Kubernetes service account.


'''
eksctl create iamserviceaccount \
    --cluster mesh-test-cluster \
    --namespace appmesh-system \
    --name appmesh-controller \
    --attach-policy-arn  arn:aws:iam::aws:policy/AWSCloudMapFullAccess,arn:aws:iam::aws:policy/AWSAppMeshFullAccess \
    --override-existing-serviceaccounts \
    --approve

'''

### 9. Deploy the App Mesh controller.

'''
helm upgrade -i appmesh-controller eks/appmesh-controller \
    --namespace appmesh-system \
    --set region=ap-south-1 \
    --set serviceAccount.create=false \
    --set serviceAccount.name=appmesh-controller

'''

### Create Mesh

We can create mesh from Aws-cli, AWS Console and Kubernetes native yaml. However, for now i will create using yaml.

### 11. Create the mesh and verify.

### 12. As mesh has been created now, we can enable/disable sidecar injection by adding a label as below to the namespace.

Create a file namespace.yaml adding the contents below.

### 13. Furthermore, remaining mesh resources can also be created from Aws-cli, AWS Console and Kubernetes native yaml. For now we will use yaml to create the same.

### Virtual-Node
Create virtual-node.yaml with below contents and apply.

### Virtual Router and Route
Create virtual-route.yaml with below contents and apply.

### Virtual Service
Create virtual-service.yaml with below contents and apply.

### 14. Once all the resources have been created we can create the Service and deployment.

### Create a Service account.
Create serviceaccount.yaml with below contents and apply.

### Deploy Workload
Create deployment.yaml file with below contents and apply.

Post successful implementation of above steps we could see pod has come up with one sidecar container which will be responsible for establishing communication between the services.








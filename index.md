## Getting Started with AWS EKS

Amazon AWS EKS service provides cost effective and fully managed kubernetes control plane.The service automatically scales the control plane on load, detects and replaces unhealthy control plane instances, and it provides automated version updates and patching for them. For detailed information regarding service offering please refer to [AWS EKS Userguide](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html?nc2=type_a)

I will present a getting started guide with AWS EKS, We will be creating clusters using [**_eksctl_**](https://eksctl.io/) and build, publish and deploy application workloads using _Jenkins_,_Helm_,_aws ecs_. 

### Create EKS Cluster

Install AWS CLI, kubectl, eksctl as per the user guide directions. Create an IAM User with in AWS with permissions to work with Amazon EKS IAM roles and service linked roles, AWS CloudFormation, and a VPC and related resources.

```markdown
eksctl create cluster --help
# Creating Cluster in us-west2 region
## First, create cluster.yaml file:

  ---
  apiVersion: eksctl.io/v1alpha5
  kind: ClusterConfig

  metadata:
    name:  hello-eks-cluster
    region: us-west-2
    version: "1.21"
    tags:

  managedNodeGroups:
  - name: mng1-ec2
    labels:
     role: "ec2workers"
    desiredCapacity: 3
    instanceType: m6g.xlarge
    volumeSize: 100
    volumeType: gp3
    ssh:
      enableSsm: true
      publicKeyName: myKeyPair

  - name: mng1-ec2-spot
    labels:
      role: ec2SpotWorkers
    desiredCapacity: 3
    #instanceTypes: ["t4g.medium","t4g.small","t4g.large"]
    instanceTypes: ["t3.medium", "t3.large"]
    spot: true
    ssh:
      enableSsm: true
      publicKeyName: myKeyPair
  cloudWatch:
    clusterLogging:
      enableTypes: ["*"]
  secretsEncryption:
    keyARN: arn:aws:kms:us-west-2:012345678912:key/key-e1234566b492e91a45e7e0747f655
  ---
## Next, run create command:
  `eksctl create cluster -f cluster.yaml`
This command will spin up a cloudformation stack with all the resources need to spin up control plan cluster and node groups for data plane. 
Usually it will take around 30 minutes for the cluster to be fully created. Please refer to the user guide for details.
Using the above config file we have created two managed node groups (with on-demand and spot instances) to deploy workloads.
The Node selector will determine where the workload will be deployed to.
The eksctl can be used to manage the cluster from now on,please refer eksctl user guide.

## Configure Kubectl

- The create command also creates a kubeconfig and appends to the .kube\config
- you should be able to view the nodes using kubectl command, for example `kubectl get nodes -l "role=ec2Workers"`
```

## Deploying application workload
We should now be able to use this kubernetes cluster using familiar kubectl command, and any 
CI/CD tooling such as Jenkins,Helm Github actions etc.. to manage your work loads. 

- Rest of the tutorial we will review how to deploy a simple flask service to the cluster.
- The Source code repository and the CI Pipeline is available here: [**_flaskapis_**](https://github.com/ddabberu/flaskapis.git)
- Helm chart to manage the deployment configuration here: [**_helm-charts_**](https://github.com/ddabberu/helm-repos/tree/main/charts)
- Helm repo is setup here: [**_helm-repo_**](https://ddabberu.github.io/helm-repos/) 
### Flask

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)


For more details see [Basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/ddabberu/getting-started-with-eks/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.

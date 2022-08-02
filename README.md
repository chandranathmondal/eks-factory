# eks-factory
EKS Factory automates provisioning and configuration of EKS resources in a self-service manner

### Introduction
EKS Factory is a solution for provisioning and managing EKS infrastructure (Cluster, Node Group etc.). It automates the resource creation and configuration of these resources in a self-service manner. At the core, we use CloudFormation to provision resources. We also leverage Lambda and CodeBuild to configure the provisioned resources.

### Solution Architecture

### Installation

Installation is a 2-step process:

#### 1. Copy the solution source files in a S3 repository
EKS Factory uses nested CloudFormation stacks where parent stack references it's child stack templates stored in S3 bucket. So, we need to upload the templates along with other scripts in an S3 bucket.

```
aws s3 sync . s3://<S3 repository bucket/folder path>/ \
        --exclude ".git/*" --exclude ".gitignore" --exclude ".DS_Store" \
        --exclude "eks-factory.yaml" --exclude "install.sh" --exclude "README.md"
```

> Note: CodeBuild requires source script files to be stored in S3 bucket within the same region, hence please make sure that the bucket is belonging to the same region where you are installing the solution.

##### Parameters
S3 repository: S3 repository bucket/folder path in the format: bucket/folder/sub-folder (without the slash at the begining/end).

#### 2. Creating Service Catalog portfolio and underlying products
EKS Factory offers catalog products under a portfolio. We create these using CloudFormation.

```
aws cloudformation create-stack \
        --stack-name EKS-Factory \
        --template-body file://eks-factory.yaml \
        --parameters ParameterKey=S3RepositoryPath,ParameterValue=<S3 repository bucket/folder path> \
                     ParameterKey=Owner,ParameterValue=<Owner details> \
                     ParameterKey=Users,ParameterValue=<End users> \
        --capabilities CAPABILITY_NAMED_IAM \
        --disable-rollback
```

##### Parameters
S3 repository: S3 repository bucket/folder path in the format: bucket/folder/sub-folder (without the slash at the begining/end). Bucket must belong to the same region where you are installing the solution.
Owner details: Owner name and/or email address.
End users: ARN of the IAM group/role.

### One-Click Installation
The entire installation steps are automated using Shell scripts.

```
sh install.sh <S3 repository bucket/folder path> <Owner details> <End users>
```

> Note: This is the recommended step to install the solution unless you are insterested in any specific steps as mentioned above for troubleshooting or so. Please make sure that the bucket is belonging to the same region where you are installing the solution.

##### Parameters
S3 repository: S3 repository bucket/folder path in the format: bucket/folder/sub-folder (without the slash at the begining/end). Bucket must belong to the same region where you are installing the solution.
Owner details: Owner name and/or email address.
End users: ARN of the IAM group/role.
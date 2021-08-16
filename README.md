# Deploy a high-availability web app using CloudFormation

## Clone the repository
```
git clone https://github.com/bdhakad/Deploy-a-high-availability-web-app-using-CloudFormation.git
```

## update the parameters : 
 1. reate-bucket-iam-role-policy-params.json
   - EnvironmentName
   - BucketName
 2. network-stack-params.json
   - EnvironmentName
   - VpcCIDR
   - PublicSubnet1CIDR
   - PublicSubnet2CIDR
   - PrivateSubnet1CIDR
   - PrivateSubnet2CIDR
 3. server-stack-params.json
   - EnvironmentName
   - ImageId (AMI id according to your region)
   - InstanceType (vCPC and RAM size)
   - EC2KeyPair ( include in for any server you want to access for troubleshooting)
 4. update your region into shell scripts.
   I have used us-east-1  

## Series of steps to run the scripts
   1. change directory to scripts folder
   ```
   cd scripts
   ```
   2. Create a S3 bucket, bucket policy, IAM policy and IAM role
   ```
   ./create.sh s3-and-iam-stack ../bucket-iam-setup/create-bucket-iam-role-policy.yml ../bucket-iam-setup/
   create-bucket-iam-role-policy-params.json
   ```
   3. List buckets
   ```
   aws s3 ls  
   ```
   4. copy the zipped source code in created s3 bucket
   ```
   aws s3 cp ../web-app-src-code/udacity/udacity.zip s3://488113704612-udacity-project-2-b1
   ```
   5. Create the network infrastructure
   ```
   ./create.sh network-stack ../networking/network-stack.yml ../networking/network-stack-params.json
   ```
   6. Create SecurityGroups, LaunchConfiguration, AutoScalingGroup, LoadBalancer, Listener and TargetGroup.

   For trouble shooting private servers uncomment the Bastion host section in server-stack.yml and key name section in LaunchConfiguration
   ```
   ./create.sh server-stack ../servers/server-stack.yml ../servers/server-stack-params.json
   ```

## Scripts to deploy the cloud resources: 
  1. create.sh for creating the stack
   - creating s3-and-iam-stack
   ```
   ./create.sh s3-and-iam-stack ../bucket-iam-setup/create-bucket-iam-role-policy ../bucket-iam-setup/create-bucket-iam-role-policy-params.json
   ```
   - creating network-stack
   ```
   ./create.sh network-stack ../networking/network-stack.yml ../networking/network-stack-params.json
   ```
   - creating server-stack
   ```
   ./create.sh server-stack ../servers/server-stack.yml ../servers/server-stack-params.json
   ```

  2. updating the stack
   - updating s3-and-iam-stack
   ```
   ./update.sh s3-and-iam-stack ../bucket-iam-setup/create-bucket-iam-role-policy ../bucket-iam-setup/create-bucket-iam-role-policy-params.json
   ```
   - updating network-stack
   ```
   ./update.sh network-stack ../networking/network-stack.yml ../networking/network-stack-params.json
   ```
   - updating server-stack
   ```
   ./update.sh server-stack ../servers/server-stack.yml ../servers/server-stack-params.json
   ```

  3. deleteing the stacks
   - deleting network-stack
   ```
   ./delete.sh network-stack
   ```
   - deleting server-stack
   ```
   ./delete.sh server-stack
   ```
   - deleting s3-and-iam-stack
   ```
   ./delete.sh s3-and-iam-stack
   ```

## Using Bastion Host for troubleshoting issues 

 - copy the private key for private servers in Bastion host
 ```
 scp -i "<jumpbox private key>" ./<private key for private servers> <public dns or ip of jumpbox>:/home/ubuntu/<private key for private servers>
 ```  
 - login into jumpbox server using
 ```
 ssh -i "<jumpbox private key>" <public dns or ip of jumpbox>
 ```
 - from inside the jumpbox login into the target private server
 ```
 ssh -i "<private key for private servers>" <private dns or ip of jumpbox>
 ```

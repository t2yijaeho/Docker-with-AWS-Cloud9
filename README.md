# Docker Basics with AWS Cloud9


## 1. Launch AWS CloudShell

Refer to [AWS CloudShell](https://github.com/t2yijaeho/AWS-CloudShell)


## 2. Create an AWS Cloud9 Environment

1. Get an AWS CloudFormation stack template body

    ```console
    wget https://github.com/t2yijaeho/Docker-with-AWS-Cloud9/raw/matia/Template/EC2-Cloud9.yaml
    ```

2. Create an AWS CloudFormation stack

    ```console
    aws cloudformation create-stack \
      --stack-name Cloud9IDE \
      --template-body file://./EC2-Cloud9.yaml
    ```

3. Verify the instance security group creation completed by the CloudFormation stack's events in AWS management console

    <img src="https://github.com/t2yijaeho/Docker-with-AWS-Cloud9/blob/matia/images/SecurityGroup%20Complete.png?raw=true">
    
4. Add inboud rule to AWS Cloud9 EC2 Security Group

    ***It may need some time to get proper Security Group ID (such as sg-01a234b567cd890ef)***

    Find AWS Cloud9 EC2 Security Group ID
    
    ```console
    CLOUD9_SECURITY_GROUP_ID=$(aws ec2 describe-security-groups \
      --filters Name=group-name,Values=*Docker-Basics* \
      --query "SecurityGroups[*].[GroupId]" \
      --output text)
    echo $CLOUD9_SECURITY_GROUP_ID
    ```
    
5. Add Local Machine IP address to Security Group inboud rule
    
   Get your local machine public IP address in the browser
   [Your public IP address](http://checkip.amazonaws.com/)
    
   ***Change `<My IP>` to your local machine IP address (ParameterValue must be in CIDR notation)***
    
    ```console
    aws ec2 authorize-security-group-ingress \
      --group-id $CLOUD9_SECURITY_GROUP_ID \
      --protocol all \
      --cidr "<My IP>/32"
    ```

6. Monitor the progress by the CloudFormation stack's events in AWS management console

    <img src="https://github.com/t2yijaeho/Docker-with-AWS-Cloud9/blob/matia/images/CloudFormation%20Stack%20Creation%20Events.png?raw=true">
    
    
## 3. Verify Docker from the AWS Cloud9 IDE

1. Launch an AWS Cloud9 IDE from the CloudFormation stack's output

    <img src="https://github.com/t2yijaeho/Docker-with-AWS-Cloud9/blob/matia/images/Cloud9%20IDE%20URL.png?raw=true">

2. To expand Terminal window choose the resize icon at the upper-right edge of the console

    <img src="https://github.com/t2yijaeho/Docker-with-AWS-Cloud9/blob/matia/images/Cloud9%20IDE%20Welcome%20Screen.png?raw=true">

3. Get the Docker version

    ```console
    docker --version
    ```
    
    ```console
    mspuser:~/environment $ docker --version
    Docker version 20.10.16, build aa7e414
    ```


## 4. Run Docker sample application

1. Get the Docker sample source code

    ```console
    git clone https://github.com/JungSangup/todo_list_manager.git app
    ```
2. Change to application directory and list files

    ```console
    cd app/
    ls -al
    ```
    
    ```console
    mspuser:~/environment $ cd app/
    mspuser:~/environment/app (master) $ ls -al
    total 204
    drwxrwxr-x 5 ubuntu ubuntu   4096 Jun  3 04:49 .
    drwxr-xr-x 5 ubuntu ubuntu   4096 Jun  3 04:49 ..
    drwxrwxr-x 8 ubuntu ubuntu   4096 Jun  3 04:49 .git
    -rw-rw-r-- 1 ubuntu ubuntu    105 Jun  3 04:49 Dockerfile
    -rw-rw-r-- 1 ubuntu ubuntu    626 Jun  3 04:49 package.json
    drwxrwxr-x 4 ubuntu ubuntu   4096 Jun  3 04:49 spec
    drwxrwxr-x 5 ubuntu ubuntu   4096 Jun  3 04:49 src
    -rw-rw-r-- 1 ubuntu ubuntu 179361 Jun  3 04:49 yarn.lock
    ```

3. Build the app's container image named "docker-101"

    ```console
    docker build --tag docker-101 .
    ```
    
4. Start an app container in detached(in the background) mode and mapping the port 3000 between the host and container

    ```console
    docker run --detach --publish 3000:3000 docker-101
    ```
    
5. Get the Cloud9 instance public DNS name

    ```console
    aws ec2 describe-instances \
      --filters Name=tag-key,Values=*cloud9* \
      --query "Reservations[*].Instances[*].{Instance:PublicDnsName}" \
      --output text
    ```
    
6. Connect to `Todo` application via port 3000 in your local machine web browser

    ***Change `<DNS Name>` to your Cloud9 instance public DNS name***
    
    ```console
    <DNS Name>:3000
    ```
    
7. Add items and see that it works as you expect. You can mark items as complete and remove items

    <img src="https://github.com/t2yijaeho/Docker-with-AWS-Cloud9/blob/matia/images/Todo%20App.png?raw=true">


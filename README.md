# Docker Basics with AWS Cloud9


## 1. Launch AWS CloudShell

1. Sign in to AWS Management Console <img src="https://github.com/t2yijaeho/Docker-with-AWS-Cloud9/blob/matia/images/AWS%20Management%20Console.png?raw=true" width="16">

2. Choose the AWS CloudShell icon <img src="https://github.com/t2yijaeho/Docker-with-AWS-Cloud9/blob/matia/images/AWS%20CloudShell.png?raw=true" width="16"> on the navigation bar

3. Check AWS Command Line Interface (AWS CLI) version

    ```bash
    aws --version
    ```

    <img src="https://github.com/t2yijaeho/Docker-with-AWS-Cloud9/blob/matia/images/AWS%20CloudShell%20version.png?raw=true">


## 2. Create an AWS Cloud9 EC2 Environment

1. Get an AWS CloudFormation stack template body

    ```bash
    wget https://github.com/t2yijaeho/Docker-with-AWS-Cloud9/raw/matia/Template/EC2-Cloud9.yaml
    ```

2. Create an AWS CloudFormation stack

    ```bash
    aws cloudformation create-stack \
      --stack-name Cloud9IDE \
      --template-body file://./EC2-Cloud9.yaml
    ```

3. Add inboud rule to AWS Cloud9 EC2 Security Group

    Get your local machine public IP address in the browser
    [Your public IP address](http://checkip.amazonaws.com/)
    
    Find AWS Cloud9 EC2 Security Group ID
    ```bash
    CLOUD9_SECURITY_GROUP_ID=$(aws ec2 describe-security-groups \
      --filters Name=group-name,Values=*Docker-Basics* \
      --query "SecurityGroups[*].[GroupId]" \
      --output text)
    echo $CLOUD9_SECURITY_GROUP_ID
    ```
    
    Add Local Machine IP address to Security Group inboud rule
    
    ***Change `<My IP>` to your local machine IP address (ParameterValue must be in CIDR notation)***
    
    ```bash
    aws ec2 authorize-security-group-ingress \
      --group-id $CLOUD9_SECURITY_GROUP_ID \
      --protocol all \
      --cidr "<My IP>/32"
    ```

4. Monitor the progress by the CloudFormation stack's events in AWS management console

    <img src="https://github.com/t2yijaeho/Docker-with-AWS-Cloud9/blob/matia/images/CloudFormation%20Stack%20Creation%20Events.png?raw=true">
    
    
## 3. Verify Docker from the AWS Cloud9 IDE

1. Launch an AWS Cloud9 IDE from the CloudFormation stack's output

    <img src="https://github.com/t2yijaeho/Docker-with-AWS-Cloud9/blob/matia/images/CloudFormation%20Stack%20Creation%20Events.png?raw=true">

2. To expand Terminal window choose the resize icon at the upper-right edge of the console

    <img src="https://github.com/t2yijaeho/Docker-with-AWS-Cloud9/blob/matia/images/Cloud9%20IDE%20Welcome%20Screen.png?raw=true">

3. Get the Docker version

    ```bash
    docker --version
    ```
    ```bash
    mspuser:~/environment $ docker --version
    Docker version 20.10.16, build aa7e414
    ```

## 4. Run Docker sample application

1. Get the Docker sample source code
    ```bash
    git clone https://github.com/docker/getting-started.git
    ```
2. Change to application directory and list files
    ```bash
    cd getting-started/
    ls
    ```
    ```bash
    mspuser:~/environment $ cd getting-started/
    mspuser:~/environment/getting-started (master) $ ls
    Dockerfile  Jenkinsfile  LICENSE  README.md  app  build.sh  docker-compose.yml  docs  mkdocs.yml  requirements.txt  yarn.lock
    ```    
3. Build the app's container image named "getting-started"
    ```bash
    docker build --tag getting-started .
    ```
4. Start an app container in detached(in the background) mode and mapping the port 3000 between the host and container
    ```bash
    docker run --detach --publish 3000:3000 getting-started
    ```
5. Get the application Url
    ```bash
    docker run --detach --publish 3000:3000 getting-started
    ```
6. 
7. 
    

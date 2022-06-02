# Docker Basics with AWS Cloud9


## 1. Launch AWS CloudShell

1. Sign in to AWS Management Console <img src="https://github.com/t2yijaeho/Docker-with-AWS-Cloud9/blob/matia/images/AWS%20Management%20Console.png?raw=true" width="16">

2. Choose the AWS CloudShell icon <img src="https://github.com/t2yijaeho/Docker-with-AWS-Cloud9/blob/matia/images/AWS%20CloudShell.png?raw=true" width="16"> on the navigation bar

3. Check AWS Command Line Interface (AWS CLI) version

    ```bash
    aws --version
    ```

    <img src="https://github.com/t2yijaeho/Docker-with-AWS-Cloud9/blob/matia/images/AWS%20CloudShell%20version.png?raw=true">


## 2. Create an Amazon EC2

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

3. AWS CloudFormation returns following output

    ```json
    {
    "StackId": "arn:aws:cloudformation:us-abcd-x:123456789012:stack/Cloud9IDE/b4d0f5e0-d4c2-11ec-9529-06edcc65f112"
    }
    ```

4. Monitor the progress by the stack's events in AWS management console

    <img src="https://github.com/t2yijaeho/Docker-with-AWS-Cloud9/blob/matia/images/CloudFormation%20Stack%20Creation%20Events.png?raw=true">


2. Get your local machine public IP address in the browser

    [Your public IP address](http://checkip.amazonaws.com/)

    ***Change `<My IP>` to your local machine IP address (ParameterValue must be in CIDR notation)***

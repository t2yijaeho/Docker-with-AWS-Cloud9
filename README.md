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
    
    ```console
    [cloudshell-user@ip-10-0-123-234 ~]$ aws cloudformation create-stack \
    >   --stack-name Cloud9IDE \
    >   --template-body file://./EC2-Cloud9.yaml
    {
    "StackId": "arn:aws:cloudformation:kr-cntr-x:123456789012:stack/Cloud9IDE/a1b2c3d4-e5f6-78gh-9012-34ijkl56m789"
    }
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
    
    ```console
    [cloudshell-user@ip-10-0-123-234 ~]$ CLOUD9_SECURITY_GROUP_ID=$(aws ec2 describe-security-groups \
    >   --filters Name=group-name,Values=*Docker-Basics* \
    >   --query "SecurityGroups[*].[GroupId]" \
    >   --output text)
    [cloudshell-user@ip-10-0-123-234 ~]$ echo $CLOUD9_SECURITY_GROUP_ID
    sg-01a234b567cd890ef
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
    
    ```console
    [cloudshell-user@ip-10-0-123-234 ~]$ aws ec2 authorize-security-group-ingress \
    >   --group-id $CLOUD9_SECURITY_GROUP_ID \
    >   --protocol all \
    >   --cidr "123.234.210.33/32"
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


## 4. (Optional) Resize Cloud9 Environment storage volume

1. Check the Cloud9 instance's free disk space

    ```console
    df -h
    ```
    
    ```console
    mspuser:~/environment $ df -h
    Filesystem      Size  Used Avail Use% Mounted on
    udev            473M     0  473M   0% /dev
    tmpfs            98M  812K   97M   1% /run
    /dev/xvda1      9.7G  7.9G  1.8G  82% /
    tmpfs           488M     0  488M   0% /dev/shm
    tmpfs           5.0M     0  5.0M   0% /run/lock
    tmpfs           488M     0  488M   0% /sys/fs/cgroup
    /dev/loop0       26M   26M     0 100% /snap/amazon-ssm-agent/5656
    /dev/loop1       45M   45M     0 100% /snap/snapd/15904
    /dev/loop2       56M   56M     0 100% /snap/core18/2409
    tmpfs            98M     0   98M   0% /run/user/1000
    /dev/loop3       47M   47M     0 100% /snap/snapd/16010
    mspuser:~/environment $
    ```

2. Get a resizing script

    ```console
    wget https://github.com/t2yijaeho/Docker-with-AWS-Cloud9/raw/matia/Scripts/resize.sh
    ```

3. Run script replacing `<XX>` with the volume size in GiB
    
    ```console
    bash resize.sh <XX>
    ```
    
    ```console
    mspuser:~/environment $ bash resize.sh 33
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100    19  100    19    0     0   1266      0 --:--:-- --:--:-- --:--:--  1266
    {
        "VolumeModification": {
            "VolumeId": "vol-073d127ee0739e7e0",
            "ModificationState": "modifying",
            "TargetSize": 33,
            "TargetIops": 100,
            "TargetVolumeType": "gp2",
            "TargetMultiAttachEnabled": false,
            "OriginalSize": 10,
            "OriginalIops": 100,
            "OriginalVolumeType": "gp2",
            "OriginalMultiAttachEnabled": false,
            "Progress": 0,
            "StartTime": "2022-06-13T04:48:23.000Z"
        }
    }
    CHANGED: partition=1 start=2048 old: size=20969439 end=20971487 new: size=69203935,end=69205983
    resize2fs 1.44.1 (24-Mar-2018)
    Filesystem at /dev/xvda1 is mounted on /; on-line resizing required
    old_desc_blocks = 2, new_desc_blocks = 5
    The filesystem on /dev/xvda1 is now 8650491 (4k) blocks long.

    mspuser:~/environment $ 
    ```


## 5. Run Docker sample application

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
    
    ```console
    mspuser::~/environment/app (master) $ docker build --tag docker-101 .
    Sending build context to Docker daemon  6.781MB
    Step 1/5 : FROM node:10-alpine
    10-alpine: Pulling from library/node
    ddad3d7c1e96: Pull complete 
    de915e575d22: Pull complete 
    7150aa69525b: Pull complete 
    d7aa47be044e: Pull complete 
    Digest: sha256:dc98dac24efd4254f75976c40bce46944697a110d06ce7fa47e7268470cf2e28
    Status: Downloaded newer image for node:10-alpine
     ---> aa67ba258e18
    Step 2/5 : WORKDIR /app
     ---> Running in e583a12b7de9
    Removing intermediate container e583a12b7de9
     ---> 89a83df9b607
    Step 3/5 : COPY . .
     ---> 9af4f680db47
    Step 4/5 : RUN yarn install --production
     ---> Running in eb87740ffb64
    yarn install v1.22.5
    [1/4] Resolving packages...
    [2/4] Fetching packages...
    info fsevents@1.2.9: The platform "linux" is incompatible with this module.
    info "fsevents@1.2.9" is an optional dependency and failed compatibility check. Excluding it from installation.
    [3/4] Linking dependencies...
    [4/4] Building fresh packages...
    Done in 12.36s.
    Removing intermediate container eb87740ffb64
     ---> c0383e9e1e37
    Step 5/5 : CMD ["node", "/app/src/index.js"]
     ---> Running in ac730dabf580
    Removing intermediate container ac730dabf580
     ---> 10d9e5c9662a
    Successfully built 10d9e5c9662a
    Successfully tagged docker-101:latest
    mspuser::~/environment/app (master) $
    ```
    
4. Start an app container in detached(in the background) mode and mapping the port 3000 between the host and container

    ```console
    docker run --detach --publish 3000:3000 docker-101
    ```
    
    ```console
    mspuser::~/environment/app (master) $ docker run --detach --publish 3000:3000 docker-101
    c740b3d675b99f55c75888c3b6fa4e9b7e59487ba1471d44e59f06f1dcd9ea6c
    mspuser::~/environment/app (master) $ 
    ```
    
5. Get the Cloud9 instance public DNS name

    ```console
    aws ec2 describe-instances \
      --filters Name=tag-key,Values=*cloud9* \
      --query "Reservations[*].Instances[*].{Instance:PublicDnsName}" \
      --output text
    ```
    
    ```console
    mspuser::~/environment $ aws ec2 describe-instances \
    >   --filters Name=tag-key,Values=*cloud9* \
    >   --query "Reservations[*].Instances[*].{Instance:PublicDnsName}" \
    >   --output text
    ec2-56-123-234-78.us-west-2.compute.amazonaws.com
    mspuser::~/environment $
    ```
    
6. Connect to `Todo` application via port 3000 in your local machine web browser

    ***Change `<DNS Name>` to your Cloud9 instance public DNS name***
    
    ```console
    <DNS Name>:3000
    ```
    
7. Add items and see that it works as you expect. You can mark items as complete and remove items

    <img src="https://github.com/t2yijaeho/Docker-with-AWS-Cloud9/blob/matia/images/Todo%20App.png?raw=true">


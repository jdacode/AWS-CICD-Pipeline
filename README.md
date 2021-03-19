
# AWS-CICD-Pipeline-Node.js

## Architecture 

![arch](/img/arch.png)

<br><br>
## 1. AWS Cloud9

![cloud9](/img/cloud9.png)

### AWS Cloud9 CLI

- ```aws cloud9 create-environment-ec2 --name my-demo-environment --description "This environment is for the AWS Cloud9 tutorial." --instance-type t2.micro --region MY-REGION --connection-type CONNECT_SSM --subnet-id subnet-12a3456b --automatic-stop-time-minutes 30 --owner-arn arn:aws:iam::123456789012:role/aws-service-role/cloud9.amazonaws.com/AWSServiceRoleForAWSCloud9```

- ```git clone https://git-codecommit.us-east-2.amazonaws.com/v1/repos/MyDemoRepo my-demo-repo```

> AWS Cloud9 uses AWS Identity and Access Management (IAM) service-linked roles. A service-linked role is a unique type of IAM role that's linked directly to AWS Cloud9. Service-linked roles are predefined by AWS Cloud9 and include all the permissions that the service requires to call other AWS services on your behalf.

> A service-linked role makes setting up AWS Cloud9 easier because you don’t have to add the necessary permissions. AWS Cloud9 defines the permissions of its service-linked roles, and only AWS Cloud9 can assume its roles. The defined permissions include the trust policy and the permissions policy, and that permissions policy cannot be attached to any other IAM entity.

> You can delete the roles only after first deleting their related resources. This protects your AWS Cloud9 resources because you can't inadvertently remove permission to access the resources. 

<br><br>
## 2. AWS CodeCommit

![arc-workflow](/img/arc-workflow.png)



    1. Use the AWS CLI or the CodeCommit console to create a CodeCommit repository.

    2. From your development machine, use Git to run git clone, specifying the name of the CodeCommit repository. This creates a local repo that connects to the CodeCommit repository.

    3. Use the local repo on your development machine to modify (add, edit, and delete) files, and then run git add to stage the modified files locally. Run git commit to commit the files locally, and then run git push to send the files to the CodeCommit repository.

    4. Download changes from other users. Run git pull to synchronize the files in the CodeCommit repository with your local repo. This ensures you're working with the latest version of the files.

You can use the AWS CLI or the CodeCommit console to track and manage your repositories.

> How is CodeCommit different from file versioning in Amazon S3?
> CodeCommit is optimized for team software development. It manages batches of changes across multiple files, which can occur in parallel with changes made by other developers. Amazon S3 versioning supports the recovery of past versions of files, but it's not focused on collaborative file tracking features that software development teams need. 

## CodeCommit CLI

1. List repositories
    - ```aws codecommit list-repositories```

2. Create repository

    - ```aws codecommit create-repository --repository-name MyTestRepo --repository-description "My Test Repo" --tags Team=Dev```

3. Delete reposotory

    - ```aws codecommit delete-repository --repository-name MyTestRepo```

4. GIT

    - ```git clone https://git-codecommit.us-east-2.amazonaws.com/v1/repos/MyDemoRepo my-demo-repo```
    - ```git remote add origin https://git-codecommit.us-east-2.amazonaws.com/v1/repos/MyDemoRepo```
    - ```git add bees.txt```
    - ```git commit -m "Added bees.txt"```
    - ```git push -u origin main```


<br><br>
## 3. CodeBuild

![codebuild](/img/codebuild.png)

### CodeBuild

1. **Step 1**: Create two S3 buckets

    - ```aws s3 ls```
    - ```aws s3api create-bucket --bucket codebuild-artifact-bucket-45546423 --region us-east-1```

2. **Step 2**: Create the source code

    - ```aws codecommit list-repositories```
    - ```aws codecommit get-folder --repository-name MyRepo --folder-path ""```

3. **Step 3**: Create the buildspec file

    The buildspec file must be named buildspec.yml and placed in the root of your source directory.

    - Use a different buildspec file for different builds in the same repository, such as buildspec_debug.yml and buildspec_release.yml.
    - Store a buildspec file somewhere other than the root of your source directory, such as config/buildspec.yml or in an S3 bucket. The S3 bucket must be in the same AWS Region as your build project. Specify the buildspec file using its ARN (for example, arn:aws:s3:::my-codebuild-sample2/buildspec.yml).

    If a command contains a character, or a string of characters, that is not supported by YAML, you must enclose the command in quotation marks (""). The following command is enclosed in quotation marks because a colon (:) followed by a space is not allowed in YAML. The quotation mark in the command is escaped (\"). 

    > eg: "export PACKAGE_NAME=$(cat package.json | grep name | head -1 | awk -F: '{ print $2 }' | sed 's/[\",]//g')"

```
        version: 0.2

        run-as: Linux-user-name

        env:
            shell: shell-tag
            variables:
                key: "value"
                key: "value"
            parameter-store:
                key: "value"
                key: "value"
            exported-variables:
                - variable
                - variable
            secrets-manager:
                key: secret-id:json-key:version-stage:version-id
            git-credential-helper: no | yes

        proxy:
            upload-artifacts: no | yes
            logs: no | yes

        batch:
            fast-fail: false | true
            # build-list:
            # build-matrix:
            # build-graph:
                
        phases:
            install:
                run-as: Linux-user-name
                on-failure: ABORT | CONTINUE
                runtime-versions:
                    runtime: version
                    runtime: version
                commands:
                    - command
                    - command
                finally:
                    - command
                    - command
            pre_build:
                run-as: Linux-user-name
                on-failure: ABORT | CONTINUE
                commands:
                    - command
                    - command
                finally:
                    - command
                    - command
            build:
                run-as: Linux-user-name
                on-failure: ABORT | CONTINUE
                commands:
                    - command
                    - command
                finally:
                    - command
                    - command
            post_build:
                run-as: Linux-user-name
                on-failure: ABORT | CONTINUE
                commands:
                    - command
                    - command
                finally:
                    - command
                    - command

        reports:
            report-group-name-or-arn:
                files:
                    - location
                    - location
                base-directory: location
                discard-paths: no | yes
                file-format: report-format

        artifacts:
            files:
                - location
                - location
            name: artifact-name
            discard-paths: no | yes
            base-directory: location
            exclude-paths: excluded paths
            enable-symlinks: no | yes
            s3-prefix: prefix
            secondary-artifacts:
                artifactIdentifier:
                files:
                    - location
                    - location
                name: secondary-artifact-name
                discard-paths: no | yes
                base-directory: location
                artifactIdentifier:
                files:
                    - location
                    - location
                discard-paths: no | yes
                base-directory: location
                
        cache:
            paths:
                - path
                - path
```


4. **Step 4**: Create the build project 

    - ```aws codebuild create-project --generate-cli-skeleton```
    - cat > create-project.json 
        ```
        {
        "name": "codebuild-demo-project",
        "source": {
            "type": "S3",
            "location": "codebuild-region-ID-account-ID-input-bucket/MessageUtil.zip"
        },
        "artifacts": {
            "type": "S3",
            "location": "codebuild-region-ID-account-ID-output-bucket"
        },
        "environment": {
            "type": "LINUX_CONTAINER",
            "image": "aws/codebuild/standard:4.0",
            "computeType": "BUILD_GENERAL1_SMALL"
        },
        "serviceRole": "serviceIAMRole"
        }
        ```
    - ```aws codebuild create-project --cli-input-json file://create-project.json```

5. **Step 5**: Run the build

    - ```aws codebuild start-build --project-name project-name```

6. **Step 6**: View summarized build information

    - ```aws codebuild batch-get-builds --ids id```

<br><br>
## CodeDeploy

![codedeploy3](/img/codedeploy3.png)
![codedeploy1](/img/codedeploy1.png)

## CodeDeploy CLI


1. appsec.yml 

```
        version: 0.0
        os: [linux|windows]
        files:  
            - source: /directory/root/to/install/from    
            destination: /directory/to/install/to
        hooks:  
            BeforeInstall:    
                - location: scripts/before-intall.sh      
                timeout: 300      
                runas: root  
            AfterInstall:    
                - location: scripts/after-install.sh      
                timeout: 60      
                runas: root  
            ApplicationStart:    
                - location: scripts/start-service.sh      
                timeout: 60      
                runas: root  
            ApplicationStop:    
                - location: scripts/stop-service.sh      
                timeout: 60      
                runas: root
```    


### IAM

1. Create a Role [EC2]

    - ```aws iam create-role --role-name CodeDeploy-EC2-Instance-Profile --assume-role-policy-document file://CodeDeploy-EC2-Trust.json```

2. Attach Policy to Role [EC2]

    - ```aws iam put-role-policy --role-name CodeDeploy-EC2-Instance-Profile --policy-name CodeDeploy-EC2-Permissions --policy-document file://CodeDeploy-EC2-Permissions.json```

3. Create Instance Profile [EC2] [Use an instance profile to pass an IAM role to an EC2 instance.]

    - ```aws iam create-instance-profile --instance-profile-name CodeDeploy-EC2-Instance-Profile```

4. Add Role to Instance Profile [EC2]

    - ```aws iam add-role-to-instance-profile --instance-profile-name CodeDeploy-EC2-Instance-Profile --role-name CodeDeploy-EC2-Instance-Profile```

5. (Option-1) Create a Role [CodeDeploy]

    - ```aws iam create-role --role-name CodeDeployRole --assume-role-policy-document file://AWSCodeDeployRole.json```

5. (Option-2) Attach Policy to Role [CodeDeploy]

    - ```aws iam create-role --role-name CodeDeployServiceRole```

    - ```aws iam attach-role-policy --policy-arn arn:aws:iam::aws:policy/service-role/AWSCodeDeployRole --role-name CodeDeployServiceRole```

### EC2

7. Create a EC2 Instance [Console]

        Attach Role: CodeDeploy-EC2-Instance-Profile
        Add tag: Key= Name, Value= ec2deploy
        SecurityGroup: Allow HTTP and SSH

9. Install CodeDeploy Agent in EC2 Instance for Amazon Linux [EC2-SSH]

        https://docs.aws.amazon.com/codedeploy/latest/userguide/codedeploy-agent-operations-install.html

        - sudo yum update
        - sudo yum install ruby
        - sudo yum install wget
        - pwd
        - cd /home/ec2-user
        - wget https://aws-codedeploy-us-east-1.s3.us-east-1.amazonaws.com/latest/install
        - chmod +x ./install
        - sudo ./install auto
        - sudo service codedeploy-agent status
        - sudo service codedeploy-agent start

### CodeDeploy

![codedeploy2](/img/codedeploy2.png)

10. Create CodeDeploy

    CONSOLE:

        Compute Plataaform: EC2/On-prenises
        Deployment type: In-place deployment
        Environment configuration: Amazone EC2 instances [Add tag: Key= Name, Value= ec2name]
        Service Role: CodeDeploy-Role


    CREATE APPLICATION:

        aws deploy create-application --application-name CodeDeployNodejsApp --compute-platform Server

    SPECIFY DEPLOYMENT GROUP:

        aws deploy create-deployment-group --application-name CodeDeployNodejsApp --deployment-group-name CodeDeployNodejsGroup --service-role-arn arn:aws:iam::12345678:role/CodeDeployRole --ec2-tag-filters Key=Name,Value=ec2deploy,Type=KEY_AND_VALUE --deployment-config-name CodeDeployDefault.OneAtATime

    CREATE DEPLOYMENT:

        aws deploy create-deployment --application-name CodeDeployNodejsApp --deployment-group-name CodeDeployNodejsGroup --deployment-config-name CodeDeployDefault.OneAtATime --revision S3 --s3-location bucket=CodeDeployDemoBucket,bundleType=zip,eTag=dd56cfdEXAMPLE8e768f9d77fEXAMPLE,key=WordPressApp.zip --description "My demo deployment" 

11. Actions

    - Deploy new version
    - Revision location: s3://bucket-name/folder/object.zip
    - aws deploy list-deployments

12. Logs in EC2 Instance Server [SSH]

    - find / | grep codedeploy-agent.log
    - tail -f /var/log/aws/codedeploy-agent/codedeploy-agent.log

13. Browse into the ip EC2 Instance to connect to the webpage


<br><br>
## AWS

https://docs.aws.amazon.com/codedeploy/latest/userguide/deployments-view-logs.html



To view deployment log files on Amazon Linux, RHEL, and Ubuntu Server instances

On Amazon Linux, RHEL, and Ubuntu Server instances, deployment logs are stored in the following location:

/opt/codedeploy-agent/deployment-root/deployment-logs/codedeploy-agent-deployments.log

To view or analyze deployment logs on Amazon Linux, RHEL, and Ubuntu Server instances, sign in to the instance, and then type the following command to open the CodeDeploy agent log file:

less /var/log/aws/codedeploy-agent/codedeploy-agent.log
tail -f /var/log/aws/codedeploy-agent/codedeploy-agent.log

<br><br>
## License

> Licensed under the [MIT](license) license.
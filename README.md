
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

**Step 1**: Create two S3 buckets

    - ```aws s3 ls```
    - ```aws s3api create-bucket --bucket codebuild-artifact-bucket-45546423 --region us-east-1```

**Step 2**: Create the source code

    - ```aws codecommit list-repositories```
    - ```aws codecommit get-folder --repository-name MyRepo --folder-path ""```

**Step 3**: Create the buildspec file

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


**Step 4**: Upload the source code and the buildspec file

    - ``````
    - ``````
    - ``````
    - ``````
    - ``````
    - ``````



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
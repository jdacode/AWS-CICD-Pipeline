
# AWS-CICD-Pipeline-Node.js

## Architecture 

![arch](/img/arch.png)

<br><br>
## AWS CodeCommit

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

    - ```aws codecommit delete-repository --repository-name MytestRepo```

4. Connect to the CodeCommit repository by cloning the repository

    - ```git clone https://git-codecommit.us-east-2.amazonaws.com/v1/repos/MyDemoRepo my-demo-repo```
    - ```git remote add origin https://git-codecommit.us-east-2.amazonaws.com/v1/repos/MyDemoRepo```
    - ```git add bees.txt```
    - ```git commit -m "Added bees.txt"```
    - ```git push -u origin main```

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
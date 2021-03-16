
# AWS-CICD-Pipeline-1stEdtn

## Architecture 

![arch](/img/arch.png)

<br><br>
## AWS CodeCommit

![arc-workflow](/img/arc-workflow.png)

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
# capstone

GitHub page: https://github.com/Ema-jar/capstone

This is my Capstone project implemented for the Udacity course Cloud DevOps Engineer.

The project is really simple and I've decided to implement a red/blue deployment using a simple index.html file backed by nginix.

Those are the steps I've followed to create and deploy this project.

1. Create a user with minimum privileges
2. Create a EC2 instance called JenkinsMachine
3. SSH login into JenkinsMachine and install Jenkins with all the plugins described in the course
4. SSH login into JenkinsMachine and install Docker, kubectl, aws-iam-authenticator, eksctl and tidy
5. Connect Jenkins with my GitHub repository
6. Define my blue-green pipeline in my Jenkinsfile
7. Push the code on my GitHub repository
8. Wait for the pipeline to start
9. Confirm that we want switch traffic on the new instance

The Jenkins pipeline is pretty standard I just added two steps to cleanup the instance and to debug potential issues.

The first Jenkins step is executed only if the stack has not been created. I implemented this check to avoid an error.

### Useful links and hints

Install AWS CLI: https://docs.aws.amazon.com/eks/latest/userguide/getting-started-console.html
Install eksctl: https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html
Install IAM-AWS-AUTHENTICATOR: https://docs.aws.amazon.com/eks/latest/userguide/install-aws-iam-authenticator.html
Install docker: 

I had to run the following command in order to give Jenkins the right permission:
```
sudo usermod -a -G docker jenkins
sudo service jenkins restart
```
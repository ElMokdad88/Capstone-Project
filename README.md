# capstone

Those are the steps I've followed to create and deploy this project.

1. Create a user with minimum privileges
2. Create a EC2 instance called Jenkins
3. SSH login into Jenkins and install Jenkins with all the plugins described in the course
4. SSH login into Jenkins and install Docker, kubectl, aws-iam-authenticator, eksctl and tidy
5. Connect Jenkins with my GitHub repository
6. Define my blue-green pipeline in my Jenkinsfile
7. Push the code on my GitHub repository
8. Wait for the pipeline to start
9. Confirm that we want switch traffic on the new instance


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

I have copied the kubectl file to /var/lib/jenkins/ folder
Then the jenkins user is not able to access the kubectl binary, it seems that you installed it in a location that is not global.

Make sure the jenkins can access the cluster through kubectl:mv .kube/config to /var/lib/jenkins

Give kubectl root permission

`sudo chown root:root kubectl`

Make the kubectl binary executable

`chmod +x ./kubectl`

Move kubectl to /user/local/bin

`mv ./kubectl/user/local/bin`

Try running the aws eks update-kubeconfig command in a Jenkins step.

Example below.

aws eks --region ap-southeast-2 update-kubeconfig --name capstonecluster



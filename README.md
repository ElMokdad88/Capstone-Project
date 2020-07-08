Before making this project I tested the Blue-Green Deeployment locally using Minikube 

https://medium.com/@andresaaap/simple-blue-green-deployment-in-kubernetes-using-minikube-b88907b2e267


# capstone

Those are the steps I've followed to create and deploy this project.

1. Create a user with minimum privileges
2. Create a EC2 instance called Jenkins Master
3. SSH login into Jenkins and install Jenkins with all the necessary plugins
4. SSH login into Jenkins and install Docker, kubectl, eksctl and tidy
5. Create a Cluster using eksctl  
6. Connect Jenkins with my GitHub repository
7. Define my blue-green pipeline in my Jenkinsfile
8. Push the code on my GitHub repository
9. Wait for the pipeline to start
10. Confirm that we want switch traffic on the new instance



### Useful links and hints

Install AWS CLI: https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-linux.html (This is version 2)
Install eksctl: https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html 
Install docker: https://phoenixnap.com/kb/how-to-install-docker-on-ubuntu-18-04



I had to run the following command in order to give Jenkins the right permission:
```
sudo usermod -a -G docker jenkins
sudo service jenkins restart
```

I have copied the kubectl file to /var/lib/jenkins/ folder
`sudo chown root:root kubectl`
`chmod +x ./kubectl`
`mv ./kubectl/user/local/bin`


After submitting the projeect I deleted my Instances/Cluster to avoid Bills :P

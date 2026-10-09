Deploying to kubernetes cluster:
1. Make sure the nginx-survey-deployment.yaml and nginx-survey-service.yaml files are available in your working directory.
2. Run the command "kubectl create -f nginx-survey-deployment.yaml"
3. Run the command "kubectl create -f nginx-survey-service.yaml"
4. Run the command "kubectl get svc" and take note of the 5-digit port number associated with port 8080 for the "nginx-survey" NodePort service.
5. In the EC2 AWS console, find your instance's security group and make sure to allow inbound traffic on the port number from step 4. Set the type to Custom TCP, the protocol to TCP, and the source port to 0.0.0.0/0.
6. Now the webpage should be available at http://ec2-[PUBLIC IP].compute-1.amazonaws.com:XXXXX


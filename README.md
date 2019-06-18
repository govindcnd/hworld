## Bringing up hello world 
The hello world app can be deployed in two different fashion here. The first one is AWS cloudformation 
and second one is with AWS Cloudformation + EKS. Details steps on bringing up the stack is as follows.

## Method 1 : Using AWS Cloudformation
Simply clone this repo and inside the Cformation folder you will find the yaml file `stack-iaas.yaml` 
to bring up the hello world app on aws with ec2 instnace and load balancer. The highlights of this method 
is as follows
   1. Stack will be created with high availability
   2. Traffic is limited to certain services from the ip you refer
   3. Is reproducible with the same script
   4. Enabled with Configuration management tool `Ansible ` which enables to manage the iaas as code even 
      inside the operating system.
   5. The out put section of cloudformation console with have the end point url to access the service. 
      eg:
      <a href="" target="_blank"><img src="https://images-helloworld.s3-ap-southeast-1.amazonaws.com/cf-output.png" alt="" width="2110" height="560" /></a>
       
      
**Note**    
   The current template is being tested on AWS Ireland and Singapore region only. 
   Incase if you try to run the same on other region do update the ami details accordingly.
   Upon successfull stack creation the output section of cloudformation template will be shared with ELB
   end point to access the service we deployed on the nodes. 


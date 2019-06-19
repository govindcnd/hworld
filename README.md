## Bringing up hello world 
The hello world app can be deployed in two different fashion here. The first one is AWS cloudformation 
and second one is with AWS Cloudformation + EKS. Details steps on bringing up the stack is as follows.

## Method 1 : Using AWS Cloudformation on EC2 
Simply clone this repo and inside the Cformation folder you will find the yaml file `stack-iaas.yaml` 
to bring up the hello world app on aws with ec2 instnace and load balancer and autoscaling load the yaml 
file on aws cloudformation with AWS console and pass on the parameters required. Please remember that 
this yaml is designed to work with existing VPC its associated subnet. By default AWS will have default
vpc in all regions, either you can choose the default VPC or your custom vpc along with the relative Subnets. 

The highlights of this method  is as follows
   1. Stack will be created with high availability
   2. Traffic is limited to certain services from the ip you refer
   3. Is reproducible with the same script
   4. Enabled with Configuration management tool `Ansible ` which enables to manage the iaas as code even 
      inside the operating system.
   5. The out put section of cloudformation console with have the end point url to access the service. 
      eg:
      <a href="" target="_blank"><img src="https://images-helloworld.s3-ap-southeast-1.amazonaws.com/cf-output.png" alt="" width="2110" height="560" /></a>
       
      
**Note**    
   The current template is being tested on AWS `Ireland` and `Singapore` regions only. 
   Incase if you try to run the same on other region do update the ami details accordingly.
   Upon successfull stack creation the output section of cloudformation template will be shared with ELB
   end point to access the service we deployed on the nodes. 

## Method 1 : Using AWS Cloudformation on EKS
This is a bit extended method of setting up an EKS stack based on kubernetes and deploying the apps on
the underlying ec2 stack. Similar to the method 1 we will be setting up the EKS as well as ec2 high available
infrastructure with the help of cloudformation template and rest of the app deployment will be with `kubectl`.
Simply clone this repo and inside EKS folder you will find the yaml file `eks-stack-up.yaml` , to bring up the 
hello world app on aws with ec2 instnace and load balancer and autoscaling load the yaml file on aws cloudformation
with AWS console and pass on the parameters required. Please remember that this yaml is designed to work with 
existing VPC its associated subnet. By default AWS will have default vpc in all regions, either you can choose 
the default VPC or your custom vpc along with the relative Subnets. 
EG: 
    <a href="" target="_blank"><img src="https://images-helloworld.s3-ap-southeast-1.amazonaws.com/stack-up-sample.png" alt="" width="500" height="500" /></a>

**Note on EKS**    
   The current template is being tested on AWS `Ireland` and `Frankfurt` regions only. 
   Incase if you try to run the same on other region do update the ami details accordingly.
   Tested with C5.large and t2.micro instance types. 
   The local machine is expected to have installed with `kubectl`.
   
         
The Steps to deploy the app on EKS is as follows, 
1. Make sure the Stack creation state is `complete` within cloudformation, it might take 10-15 minutes along with 
   EKS and EC2 HA set up. 
2. The out put section of cloudformation console with have the role arn and subnet details required in the futhter steps. 
      eg:

<a href="" target="_blank"><img src="https://images-helloworld.s3-ap-southeast-1.amazonaws.com/eks-cf-outputs.png" alt="" width="2110" height="560" /></a>
      
3. Configuring client kubectl with the cluster details
   
   3.1. IAM user credentials with access to EKS and EC2 must be configured on the client machine. 
   
   3.2. update the kubeconfig file  with the eks stack we just created 

```
aws eks --region region update-kubeconfig --name cluster_name
```

Refer : 
cloudformation output section for the clustername, region will the region upon which you bring this stack up
 eg: `eu-west-1` for Ireland. 


EG : 
    <a href="" target="_blank"><img src="https://images-helloworld.s3-ap-southeast-1.amazonaws.com/eks-cf-outputs.png" alt="" width="1500" height="500" /></a>

4. deploy the webapp and service ( deployment & service for our app) 
```
kubectl apply -f webapp.yaml
```
EG : 

<a href="" target="_blank"><img src="https://images-helloworld.s3-ap-southeast-1.amazonaws.com/deploy-app.png" alt="" width="300" height="100" /></a>
    
5. deploy the config-map ( deployment & service for our app) , After editing the required values as follows .
```
kubectl apply -f aws-auth-configmap.yaml	
```

EG : 

<a href="" target="_blank"><img src="https://images-helloworld.s3-ap-southeast-1.amazonaws.com/config-map.png" alt="" width="400" height="100" /></a>

Please make sure you udpate the configmap  yaml with the role arn which created on the step 1. The role arn can be found in the cloudformation output. 
EG:
 
<a href="" target="_blank"><img src="https://images-helloworld.s3-ap-southeast-1.amazonaws.com/config-map-file.png" alt="" width="500" height="200" /></a>

6. deploy the role , ingress and alb ingress controller for our web app ( deployment & service for our app). After editing the required values as follows .
```
kubectl apply -f rbac-role.yaml
kubectl apply -f alb-ingress-controller-webapp.yaml 
kubectl apply -f web-ingress.yaml 
```
EG : 

<a href="" target="_blank"><img src="https://images-helloworld.s3-ap-southeast-1.amazonaws.com/ingress.png" alt="" width="500" height="200" /></a>

Please make sure you udpate the web-ingress  yaml with the subnets of your stack and alb-ingress-controller yaml with your
eks stack name which can be found in the cloudformation output. 
EG:
    <a href="" target="_blank"><img src="https://images-helloworld.s3-ap-southeast-1.amazonaws.com/ingress-with-subnet.png" alt="" width="1500" height="500" /></a>

   <a href="" target="_blank"><img src="https://images-helloworld.s3-ap-southeast-1.amazonaws.com/alb-ingress-update.png" alt="" width="1500" height="500" /></a>
    
7. verify pods, service , deployments and ingress  
   You have to run the kubectl get  to fetch details from our eks stack. 
```
kubectl get pods
kubectl get nodes
kubectl get ing
```  
EG: 
   <a href="" target="_blank"><img src="https://images-helloworld.s3-ap-southeast-1.amazonaws.com/output.png" alt="" width="500" height="200" /></a>


### Service Verification##

*For Method 1*
To verify the service simply copy past the web-ingress address from the output value againist the load balancer on the Cloudformation Stack. Open up the browser and access the url for the  helloworld and check for headers which would reflect the hostname of container. 
 
*For Method 2*
To verify the service simply copy past the web-ingress address from the output of  `kubectl get ing`  and open up the browser and access the url of helloworld and check for headers which would reflect the hostname of container. 

EG: 
   <a href="" target="_blank"><img src="https://images-helloworld.s3-ap-southeast-1.amazonaws.com/sample-op.png" alt="" width="2000" height="500" /></a>



### References ##
AWS Api documentation : https://docs.aws.amazon.com/

AWS Cloudformation documentation : https://docs.aws.amazon.com/cloudformation/index.html

AWS EKS documentation : https://docs.aws.amazon.com/eks/latest/userguide/getting-started.html

Nginx official documentation :  https://nginx.org/en/docs/

K8s documentation : https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands



### Production Level Scope ##
Upon deploying the same on production level infra the following can be considered
   1. Run Production stack with minimum 3 or 5 number of nodes . 
   2. Run infra on Reserved instance to bring down the cost 
   3. Run infra on Reserved + Spot ( 60% + 40%). 
   4. Optimize the container with required resources so as to achieve max utilization of resources
   5. Upon optimizing find the right instnace type.
   6. Considering Azure AKS / Google container GKE for possible options to bringh down the cost. 
   7. Serving static contents from CDN  (optional) 
   8. Enable Cluster autoscaling  on EKS 
   9. Enable HPA ( Horizontal Pod Autoscaling) .
  10. Use CodeDeploy for app deployment. ( Specific to Method 1, replacing ansible invoking on userdata) 
   

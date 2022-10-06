## EKS-aws-ALB-ingress
  
   Resource for ingressclass, deployment, service and ingress

## Steps after creating IAM roles for eks-cluster, workernode, vpc, public-private subnets, NAT gateway, EKS cluster and worker node setup, install kubectl, eksctl, helm :

## Add the eks-charts to helm repository and Update your local repo to make sure that you have the most recent charts

    #helm repo add eks https://aws.github.io/eks-charts
    
    #helm repo update

## Tags on subnets
   
    #First enable auto-assign public IPv4 address for all subnets
   
   # for each subnet

     Key: kubernetes.io/cluster/yourEKSClusterName
     Value: shared

   # for public subnet

     Key: kubernetes.io/role/elb
     Value: 1

   # for private subnet

     Key: kubernetes.io/role/internal-elb
     Value: 1
   
 ## Registry for ap-northeast-1
   
      602401143452.dkr.ecr.ap-northeast-1.amazonaws.com
      
 ## Associate OIDC provider with cluster
    
    eksctl utils associate-iam-oidc-provider \
    --region us-east-1 \
    --cluster sudip-14sep-eks-cluster \
    --approve
 
 ## Create IAM policy
    
    #curl -o iam_policy_latest.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/main/docs/install/iam_policy.json
    
    #aws iam create-policy \
    --policy-name sudip-14sep-AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy_latest.json
    
 ## Create IAM service account
 
    #eksctl create iamserviceaccount \
      --cluster=sudip-14sep-eks-cluster \
      --namespace=kube-system \
      --name=sudip-14sep-aws-load-balancer-service-account \
      --attach-policy-arn=arn:aws:iam::180789647333:policy/AWSLoadBalancerControllerIAMPolicy \
      --override-existing-serviceaccounts \
      --approve
    
    #eksctl  get iamserviceaccount --cluster sudip-14sep-eks-cluster   
    #kubectl get sa sudip-14sep-aws-load-balancer-service-account -n kube-system
     
     Go to cloudFormation in aws and check there from stacks
     
  ## Install the AWS Load Balancer Controller using Helm V3
  
     #helm install sudip-14sep-aws-load-balancer-controller eks/aws-load-balancer-controller \
        -n kube-system \
        --set clusterName=sudip-14sep-eks-cluster \
        --set serviceAccount.create=false \
        --set serviceAccount.name=sudip-14sep-aws-load-balancer-service-account \
        --set region=ap-northeast-1 \
        --set vpcId=vpc-0165a396e41e292a3 \
        --set image.repository=602401143452.dkr.ecr.ap-northeast-1.amazonaws.com/amazon/aws-load-balancer-controller
      
      #helm list -n kube-system
      #kubectl -n kube-system get deployment 
      #kubectl -n kube-system get deployment sudip-14sep-aws-load-balancer-controller
      #kubectl get pods -n kube-system
      
      Delete/uninstall aws load balancer controller ::
      
       #helm delete sudip-6oct-aws-load-balancer-controller -n kube-system
   
   ##  Create default ingressclass, nginx app1 deployment and service, ingress
   
       #kubectl create -f 01-ingressclass.yml
       #kubectl create -f 02-Nginx-App1-Deployment-and-NodePortService.yml
       #kubectl create -f 03-ALB-ingress.yml
       
       #kubectl get ingress -n sudip-deploy
       #kubectl get po -n sudip-deploy
       #kubectl get svc -n sudip-deploy
   
   ## Delete all deployments,service,ingress and alb ingress controller
     
      #kubectl delete -f 01-ingressclass.yml
      #kubectl delete -f 02-Nginx-App1-Deployment-and-NodePortService.yml
      #kubectl delete -f 03-ALB-ingress.yml
      #helm uninstall sudip-14sep-aws-load-balancer-controller -n kube-system
 

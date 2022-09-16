## Setup your domain name with route53 and ACM(AWS Certificate Manager) for SSL redirection:
      
     #1. Go to AWS and search for Route53. There create hosted zone. Give your domain name(hellodevops.xyz), type is public hosted zone. Then create. Go to the create hosted zone and there you will see that two records has been created. Type is NS and SOA.
     
     #2. Copy the values for NS type record and go to there from where you have bought your domain name. In my case it is porkbun.com .There go to domain management and then details. There you will see a list of AUTHORITATIVE NAMESERVERS. Edit it and replace the values for your created hosted zone's NS type values. Then check it in your terminal - #nslookup -type=ns hellodevops.xyz or #nslookup -type=ns hellodevops.xyz 1.1.1.1. You will see the nameservers in the result which you have added.
     
     #3. Come to the hosted zone you have created and create record. Give any subdomain name like www,sudip etc. Select alias, in 'route traffic to' select 'Alias to Application and Classic Load Balancer', select you region and then choose your load balancer. Create record.
     
     #4. You will see that a record has been created which's type is 'A'. Whenever your LB is changed or you want to change your subdomain name, you have to edit this record.
     
     #5. Later go to aws service named ACM. There request a public certificate. Give '*.hellodevops.xyz' for Fully qualified domain name and then request. Go into that certificate and check what is the status and is CNAME name and CNAME value is being created or not. Then Select on 'Create record in Route53'. Select the auto generated domain and select create record.
     
     #6. Now got to route53's hosted zone and there you will see that a new record has been created which type is CNAME. Check ACM's certificate status is showing 'Issued' or not. If so then check the domain name with your chosen subdomain name. It may be http or https, it should redirect to https only.

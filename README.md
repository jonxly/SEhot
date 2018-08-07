# AWS SE Hands-on Training

Aug2018
<br>
## Brief:
This lab deploys the latest F5 LTM and backend docker demo web servers to showcase how F5 can auto-discover pool members as the pool is scaled horizontally.  
The complete lab takes only about 40mins.  

If you are using this to demo to a customer, practice first, then use the second CFT in step3 which pre-configures the iApp to allow you to focus on showing the auto-discovery in 15mins.  

## Pre-requisities:
1. Create free AWS account. This requires valid personal email, mobile number, and credit card.
   https://aws.amazon.com/resources/create-account/  
   This lab is not designed to run on the shared corporate account.
2. Once your account is active (should take a few minutes)  
   Do the following in the Singapore region.    
   - Create your keypair and store the PEM file locally and securely  
     http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html
   - Launch any T2.micro linux EC2 instance, see that it launches successfully, and then shut it down  
     https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#ec2-launch-instance

---
<br>

## Lab steps:
1. Go to AWS Marketplace and "Click to subscribe" to the AMI we are using, then accept the terms. *Do not* launch F5 from the marketplace  
   [```F5 BIG-IP Virtual Edition - Good - PAYG, 25M```](https://aws.amazon.com/marketplace/pp/B079C44MFH?qid=1532871168475)  
   F5 offers a 30D tree trial which means you do not need to pay for the F5 license, only for the underlying EC2  
   ![](images/F5mktplace1.png)

2. Download a local copy of the lab CFT (CloudFormation Template)  
   https://raw.githubusercontent.com/jonxly/SEhot/master/JT-fullstack-payg-1nic-bigip_jul18.template  
   Optional: use CFT below with Service Discovery iApp config baked in (allows you to skip step 13 when demoing to customer)
   https://raw.githubusercontent.com/jonxly/SEhot/master/JT-fullstackbaked_aug18.template

3. Go to AWS CloudFormation and "create new stack"  
   Select "Upload a template to Amazon S3", and select the CFT you just downloaded
   ![](images/cft1.png)

4. Fill in the parameters for the CFT, then create the stack  
   Only the Stack name, AZ1, and SSH key (keypair created under pre-reqs) are required inputs  
   ![](images/cft3_input.png)

5. Chill or chitchat for _**8 minutes**_ while the CFT does some heavy lifting  
   ![](images/chill.jpg)

6. Check the CFT outputs  
   Specifically those highlighted below:  
   ![](images/cft4_output.png)

7. Access the backend website via BIG-IP (Hint: Bip1subnet1Az1SelfEipAddress)  
   Check your CFT events for errors or shout for help if it does not load  
   ![](images/wsvr.png) 

8. Check Access to F5 UI (hint: Bigip1Url)  
   Check your CFT events for errors or shout for help if it does not load

9. Set the BIG-IP admin password (hint: Bip1subnet1Az1SelfEipAddress), and then login  
   [https://clouddocs.f5.com/cloud/public/v1/aws/AWS_singleNIC.html#awssingleadmin](https://clouddocs.f5.com/cloud/public/v1/aws/AWS_singleNIC.html#awssingleadmin)

10. Poke around TMUI like-a-pro, view Network Map, Pool network stats etc  
    ![](images/networkmap.png)

11. Now its time to simulate a catastophic outage.  
    Simple suggestion is to go into AWS EC2 dashboard and stop your Webserver instance  
    Confirm that the website is no longer available  

12. Next, Lets fix things.  
    Go back to EC2 dashboard, select your webserver instance > "Launch More Like This"  
    Repeat to launch as many as you like (or can afford).  
    What do you now see in Network Map?
    
13. Create an Application Service based on the Service Discovery iApp with the following config  
    ![](images/sdiapp_settings.png)

14. Refresh Network Map to see the app self-heal!  
    Simple yet awesome :)

---
<br>

## **Teardown**
- Terminate all webserver instances from the EC2 dashboard  
  If you do not do this before the next step, deleting your CFT stack will likely fail
- Go to CloudFormation and select "Delete Stack"
- Check EC2 and billing dashboards before logging out
- Submit any **lab related** charges via Concur


## 
# Linux_Server_Configuration
Practicing a Linux Server Configuration using Amazon Lightsail

### Step 1: Accessing Ubuntu AWS Server on Local Machine:
1. I downloaded the Private Key pair from the AWS module.
2. I moved that private key pair into the .ssh folder on my local machine
3. I used the SSH command to remote log into my server
```
ssh -i ~/.ssh/AWSLightsailPrivateKey.pem ubuntu@54.186.68.156
```

# terraform_example

## Instructions
1. __Make sure you have an AWS account__ 
To be able to deploy instances in AWS you need to have an AWS account. For this, you are going to need to provide your credit card details. However, in this exmaple we are going to be using AWS features that are within the free tier, so you won't be charged. Just make sure you leave instances running after you are done playing with them.
If you don't have an AWS here is [how you create one](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)

2. __Get your AWS credentials (access key and secret key)__
You can get those from the AWS console. It is best practice to create an IAM user for your Terraform deploys, assign Administrator rights and save those credentials in a safe place in your computer. Here is [how you can create an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html) 

3. __Keep your creadentials in a safe place__ 
Once you have got your credentials, create a file `terraform.tfvars` in the root folder and place them inside this file as follows:

terraform.tfvars
```JSON

AWS_ACCESS_KEY = "<YOUR ACCESS KEY HERE>"
AWS_SECRET_KEY = "<YOUR SECRET KEY HERE>"

```
>Warning: This file contains sensible data. Make sure you don't share this file on github.

4. __Generate ssh keys__ 
AWS use key pair authentication "to authorize SSH client connections to cluster instances". In order to connect to AWS and be able to deploy instances using Terraform we are going to need to generate ssh keys. We can do this by running `$ ssh keygen -f mykey` from the root of the project's folder. This command will prompt you for a passphrase. You don't need to add a passphrase, you can skip this by hitting enter twice. Once this is done, you will see two files appear in your root folder. 
`mykey` -> private key
`mykey.pub` -> public key

>Warning:  Make sure you don't share this your private key on github.

5. __Run terraform__
In this step, we are going to take advantage of Docker. That means that we don't need to install Terraform in our host machine, instead, we are going to download a Docker image that has Terraform in it and from which we can run Terraform commands.
First, run the 'init' command. This will download the necessary plugins for connecting to aws. 
`$ docker run --rm -it -v $(pwd):/app/ -w /app/ hashicorp/terraform:light init`
Then, run the 'plan' command. This command will show what it is intended to do in this deploy. the -out flag if for instructing terraform to save this info inside `changes.terraform`
`$ docker run --rm -it -v $(pwd):/app/ -w /app/ hashicorp/terraform:light plan -out changes.terraform`
Next, run 'apply':
`$ docker run --rm -it -v $(pwd):/app/ -w /app/ hashicorp/terraform:light apply changes.terraform`

> You will need to install Docker in your host machine if you don't have it already.

If you follow these steps correctly, you should be able to see the nginx server (that we've just deployed) running by grabbing its public ip address, which you can find in the terminal output or in the AWS EC2 console, and placing it in your web browser.

Finally, make sure you destroy this EC2 instance after you are done with this example, otherwise you could be charged.
run: `$ docker run --rm -it -v $(pwd):/app/ -w /app/ hashicorp/terraform:light destroy`
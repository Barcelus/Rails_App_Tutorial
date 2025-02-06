# Setup your AWS account
1. Create your AWS VPC 
Create your own VPC (search VPC in AWS Home searchbox), 
(uzupełnić)

2. Setup your security group
Go to EC2 -> Security Groups, then select **Create Security Group**  
Input your Secuity Group name and description (e.g. "Security Group 1", "Development Demo")  
Under **Inbound Rules** add 3 new rules:  
+ Type: **SSH**; Source: **Anywhere IPv4** (leave all other fields as default)  
+ Type: **HTTP**; Source: **Anywhere IPv4** (leave all other fields as default)  
+ Type: **HTTPS**; Source: **Anywhere IPv4** (leave all other fields as default)  
Notice that protocol and port range is selected automatically based on the type of the inboudn rule.  
Press **Create security group**, your Security Group is now created

3. Create and configure your AWS account
(uzupełnić jeżeli nie ma usera)
(uzupełnić - stwórz access key)

4. Setup AWS CLI (Command Line Interface)
(uzupełnić)
#  Launch an EC2 instance fo your app
In AWS Home, go to EC2 -> Instances. In the upper right corner select a region (e.g. "eu-north-1")
Press **Launch instances**:
+ Type a name for your instance (e..g "Development Demo")
+ Under **Amazon Machine Image** select **Ubuntu**, then **Ubuntu 24.04** (or newer)
+ Under **Instance type** select the free tier eligible instance - usually it will be **t3.micro**
+ Under **Key Pair**, press **Create new key pair**
	(uzupełnić tworzenie key pair)
+ Under **Network settings**:
	+ Select the VPC created in step 1.1
	
# Head1
## Head
### Head
#### Head

> nested
>> nested
1. list
2. still list
    1. nested list
    1. still list

text
*****
text
______
text

==highlighted text==

`code line`

```code block
still block
```



### 1. Setup your AWS account
1. Create your AWS VPC  
Create your own VPC (search VPC in AWS Home searchbox),
> uzupełnić

2. Setup your security group  
Go to EC2 -> Security Groups, then select **Create Security Group**  
Input your Secuity Group name and description (e.g. "Security Group 1", "Development Demo")  
Under **Inbound Rules** add 3 new rules:
+ Type: **SSH**; Source: **Anywhere IPv4** (leave all other fields as default)
+ Type: **HTTP**; Source: **Anywhere IPv4** (leave all other fields as default)
+ Type: **HTTPS**; Source: **Anywhere IPv4** (leave all other fields as default)
Notice that protocol and port range is selected automatically based on the type of the inbound rule.
Press **Create security group**, your Security Group is now created

3. Create and configure your AWS account
> uzupełnić jeżeli nie ma usera
> uzupełnić - stwórz access key

4. Setup AWS CLI (Command Line Interface)
> uzupełnić

### 2. Launch an EC2 instance fo your app
In AWS Home, go to **EC2 -> Instances**. In the upper right corner select a region (e.g. "eu-north-1")  
Press **Launch instances**:
+ Type a name for your instance (e..g "Development Demo")
+ Under **Amazon Machine Image**, select **Ubuntu**, then **Ubuntu 24.04** (or newer)
+ Under **Instance type**, select the free tier eligible instance - usually it will be **t3.micro**
+ Under **Key Pair**, press **Create new key pair**
>	(uzupełnić tworzenie key pair)
+ Under **Network settings**, (press the **Edit** button):
	+ Select the VPC created in step 1.1
	+ (optional) Select a subnet (e.g. "eu-north-1**a**")
	+ Select the security group created in step 1.2
+ Under **Configure storage**, select at least 15GB of memory for your instance

Leave all other options as default.  
Press the **Launch instance** button. After that, go to **EC2 -> Instances -> Instances** and verify that your instance is launched and running.  
Instance is operational when it's **Instance state** is ***Running*** and under **Status check** there is ***3/3 checks passed***.

In the next chapter, you will configure your instance to run your Rails app.

### 3. Setup your EC2 instance
1. Connect to your instance using terminal
1. Install rbenv
2. Install Ruby (using rbenv)
3. Install database - SQLite3
4. Install Rails
5. Install and configure git
6. Install and configure NGINX

### 4. Launch your app
1. Download your app repository from github
2. Launch Puma application server
3. Connect to your application
4. (optional) Setup Public Elastic IP and assing it to your EC2 instance

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
        1. even more nested?
            1. nestingception?
                1. now it's just stupid
                    1. surely it wont work that far
text
*****
text
______
text

<mark>highlighted text</mark>

`code line`

```
code block
still block
```



### 1. Setup your AWS account
1. Create your AWS VPC

> najprawdopodobniej niepotrzebne

2. Setup your security group

Go to EC2 -> Security Groups, then select **Create Security Group**<br>
Input your Secuity Group name and description (e.g. "*Security Group 1*", "*RailsAppDemo*")<br>
Select VPC created in previous step.<br>
Under **Inbound Rules** add 3 new rules:
+ Type: **SSH**; Source: **Anywhere IPv4** (leave all other fields as default)
+ Type: **HTTP**; Source: **Anywhere IPv4** (leave all other fields as default)
+ Type: **HTTPS**; Source: **Anywhere IPv4** (leave all other fields as default)<br>
 
Notice that protocol and port range is selected automatically based on the type of the inbound rule.<br>

Press **Create security group**, your Security Group is now created

3. Create and configure your IAM user for AWS Command Line Interface (CLI)

Go to IAM -> Users. You should see "*Users (0)*", you will need to create a new user.<br>
Press **Create user**, then enter a username (e.g. "*user*"), then click **Next**.<br>
Select **Attach policies directly**, then select a policy called **AdministratorAccess**. Click **Next** and then **Create user**.<br>

In the **Users** view, select your created user and go to **Security credential** tab, scroll down and press **Create access key**.<br>
Select the **Command Line Interface (CLI)** use case, the **Next**<br>
You may enter a description for that key (e.g. "*AWS_CLI_Key*"), then press **Create access key**<br>
Your key has been created - press the **Download .csv file**. Do not lose the secret access key, otherwise you will need to create a new one.<br>
Open the downloaded .csv file - you will find both Public and Secret access key values inside - these will be needed for the next step.<br>

4. Setup AWS CLI (Command Line Interface)

Verify if AWS CLI is not installed on your local machine by entering `aws` command - you should get:<br>
`Command 'aws' not found` (or something similar).<br>
Install the AWS CLI:<br>
`snap install aws-cli --classic`, then verify if the installation was succesful by entering `aws --v`.<br>

Next enter `aws configure`:<br>
Enter the Access Key ID (from the .csv file - to copy and paste from outside into terminal use **Ctrl+Shift+Insert** and **Shift + Insert** respectively)<br>
Enter the Secret access key (from the .csv file)<br>
Enter the default region name - ideally the same region you will launch your EC2 instance in (e.g. "*eu-north-1*")<br>
Leave the default output format empty (just press Enter)<br>

Verify configuration by entering `aws iam list-users` - you should see your created user, with the username you selected and it's access key ID.

### 2. Launch an EC2 instance fo your app<br>
In AWS Home, go to **EC2 -> Instances**. In the upper right corner select a region (e.g. "*eu-north-1*")<br>
Press **Launch instances**:
+ Type a name for your instance (e.g."*RailsAppDemo*")
+ Under **Amazon Machine Image**, select **Ubuntu**, then **Ubuntu 24.04** (or newer)
+ Under **Instance type**, select the free tier eligible instance - usually it will be **t3.micro**
+ Under **Key Pair**, press **Create new key pair**
	+ Enter key pair name (e.g. "*RailsAppDemoKey1*")
	+ Select the **RSA** key pair type
	+ Select the **.pem** file format
	+ Create key pair - notice a file has been downloaded on your local machine.
	+ (optional) Move that file into `~/.ssh` folder
	+ Modify the key file permission - enter the `.ssh` folder in the terminal and enter command: `chmod 400 RailsAppDemoKey1.pem`
+ Under **Network settings**, (press the **Edit** button):
	+ (optional) Select a subnet (e.g. "*eu-north-1****a***")
	+ Select the security group created in step 1.2
+ Under **Configure storage**, select at least 15GB of memory for your instance

Leave all other options as default.<br>
Press the **Launch instance** button. After that, go to **EC2 -> Instances -> Instances** and verify that your instance is launched and running.<br>
Instance is operational when it's **Instance state** is ***Running*** and under **Status check** there is ***3/3 checks passed***.

### 3. Setup your EC2 instance<br>
1. Connect to your instance using terminal

Input the following command:<br>
`ssh -i .ssh/RailsAppDemoKey.pem ubuntu@EC2_INSTANCE_IP`<br>
Substitute the `EC2_INSTANCE_IP` with the public IP adress (or Public DNS).<br>
Enter `yes` when asked to connect to your instance. If the connection was succesful, you will notice your username will be different - `ubuntu@EC2_INSTANCE_PRIVATE_IP`<br>

If you want to exit from instance and return to your local machine, enter `exit`<br>
You can have multiple terminals opened, e.g. one for local machine and one to connect to EC2 instance.

(to be continued)

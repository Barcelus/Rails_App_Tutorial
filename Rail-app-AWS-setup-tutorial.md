### To-do list

Stworzyć VPC - (to chyba nie jest potrzebne)<br>

Verify all this works to get Rails splash

Stylistyka:<br>
+ pozmieniać levele headerów
+ zmienić listy na headery (na pewno?)
+ ogólne poprawki

---

### 1. Setup your AWS account
1. Create your AWS VPC

Create your own VPC (search VPC in AWS Home searchbox),
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

3. Create and configure your AWS account for AWS Command Line Interface (CLI)

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

### 2. Create default Rails app

#### 2.1 Create Ruby on Rails environment on your machine<br>

1. Install and configure git<br>

Enter following commands:<br>
```
sudo apt update
sudo apt install git
```
Verify that git has been installed correctly by entering `git -v`<br>

Set your username and user email:
```
git config --global user.name "YOUR_GITHUB-USERNAME"
git config --global user.email "YOUR_GITHUB_USER_EMAIL"
```
Verify by: `git config --list`<br>

Generate a SSH key for GitHub:<br>
`ssh-keygen -t rsa -b 4096 -C "YOUR_GITHUB_USER_EMAIL"`, then type enter to save the key in default location (`~/.ssh/id_rsa`)<br>
Start the SSH agent and add the key:
```
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```
Enter `cat ~/.ssh/id_rsa.pub` and copy the key.<br>

Go to **GitHub**, then to **GitHub settings -> SSH and GPG keys**<br>
Press **New SSH key**, enter a key title and paste the key.

Test the GitHub SSH connection: `ssh -T git@github.com` (enter `yes` where necessary)<br>
You should see: `Hi <your-username>! You've successfully authenticated, but GitHub does not provide shell access.`

2. Install rbenv<br>

Clone rbenv repo onto your instance:
```
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
```
Setup your shell to load rbenv:
```
~/.rbenv/bin/rbenv init
```
Restart shell (enter `exit` and connect to instance again)<br>
Verify rbenv is installed correctly by entering: `rbenv -v`


`sudo apt-get install autoconf patch build-essential rustc libssl-dev libyaml-dev libreadline6-dev zlib1g-dev libgmp-dev libncurses5-dev libffi-dev libgdbm6 libgdbm-dev libdb-dev uuid-dev`<br>
> ^ w którym kroku to potrzebne?


3. Install Ruby (using rbenv)<br>

Install Ruby version that is used by your app (for this tutorial we'll be using Ruby 3.3.4)
```
sudo apt-get install -y build-essential libssl-dev libyaml-dev zlib1g-dev libffi-dev
rbenv install 3.3.4
```
(this step may take few minutes, if it seems stuck, enter `rbenv install 3.3.4 --verbose` and troubleshoot)<br>
If the `rbenv install` command is not found, install the `ruby-build` plugin:
```
git clone https://github.com/rbenv/ruby-build.git "$(rbenv root)"/plugins/ruby-build
```
After Ruby has been installed, set the Ruby version on your instance:
```
rbenv global 3.3.4
rbenv local 3.3.4
```
Verify Ruby is installed correclty by entering: `ruby -v`<br>

Install ruby gems:
```
gem update --system
sudo gem install bundler
```
Verify installation by entering: `gem env home`<br>

4. Install database - SQLite3

Enter following:
```
sudo apt-get update
sudo apt install sqlite3
```
Verify by entering: `sqlite --version`

sqlite3 sharks.db
> ^na pewno SQLlite3?

5. Install Rails

For this tutorial we'll using Rails version 8.0.0.<br>
Enter:<br>
`sudo gem install rails -v 8.0.0`<br>
Verfiy by: `rails --version`

#### 2.2 Create the Rails app

1. Create Rails app on local machine

2. Verfiy the app is working<br>
(run Puma and see the app)

3. Create a repository of that app

### 3. Launch an EC2 instance fo your app<br>
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
	+ Select the VPC created in step 1.1
> ^wyjebać
	+ (optional) Select a subnet (e.g. "*eu-north-1****a***")
	+ Select the security group created in step 1.2
+ Under **Configure storage**, select at least 15GB of memory for your instance

Leave all other options as default.<br>
Press the **Launch instance** button. After that, go to **EC2 -> Instances -> Instances** and verify that your instance is launched and running.<br>
Instance is operational when it's **Instance state** is ***Running*** and under **Status check** there is ***3/3 checks passed***.

### 4. Setup your EC2 instance<br>
1. Connect to your instance using terminal

Input the following command:<br>
`ssh -i .ssh/RailsAppDemoKey.pem ubuntu@EC2_INSTANCE_IP`<br>
Substitute the `EC2_INSTANCE_IP` with the public IP adress (or Public DNS).<br>
Enter `yes` when asked to connect to your instance. If the connection was succesful, you will notice your username will be different - `ubuntu@EC2_INSTANCE_PRIVATE_IP`<br>

If you want to exit from instance and return to your local machine, enter `exit`<br>
You can have multiple terminals opened, e.g. one for local machine and one to connect to EC2 instance.

2. Setup your EC2 environment for Ruby on Rails

Repeats steps from section 2.1

3. Download your app repository from GitHub

4. Install and configure NGINX

NGINX should be already installed on your host, however, verify by running:<br>
`sudo apt install nginx`<br>
After that, go to `/etc/nginx` folder and create a `nginx.conf` file:<br>
`sudo touch nginx.conf`<br>
Open that file with a text editor (e.g. vim) and paste the following:
```
user www-data;
worker_processes auto;
pid /run/nginx.pid;
error_log /var/log/nginx/error.log;
include /etc/nginx/modules-enabled/*.conf;

events {
	worker_connections 768;
	# multi_accept on;
}

http {
	server {
		listen 80; # Listen on port 80 for HTTP
		server_name YOUR_EC2_PUBLIC_IP; # Replace with your domain or IP

		root /var/www/html; # Path to your website files
		index index.html index.htm; # Default index files

	location / {
		proxy_pass http://127.0.0.1:3000; # Forward requests to Puma on port 3000
		proxy_http_version 1.1;
		proxy_set_header Upgrade $http_upgrade;
		proxy_set_header Connection 'upgrade';
		proxy_set_header Host $host;
		proxy_cache_bypass $http_upgrade;
		}
	}
	##
	# Basic Settings
	##

	sendfile on;
	tcp_nopush on;
	types_hash_max_size 2048;
	# server_tokens off;

	# server_names_hash_bucket_size 64;
	# server_name_in_redirect off;

	include /etc/nginx/mime.types;
	default_type application/octet-stream;

	##
	# SSL Settings
	##

	ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
	ssl_prefer_server_ciphers on;

	##
	# Logging Settings
	##

	access_log /var/log/nginx/access.log;

	##
	# Gzip Settings
	##

	gzip on;

	# gzip_vary on;
	# gzip_proxied any;
	# gzip_comp_level 6;
	# gzip_buffers 16 8k;
	# gzip_http_version 1.1;
	# gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

	##
	# Virtual Host Configs
	##

	include /etc/nginx/conf.d/*.conf;
	include /etc/nginx/sites-enabled/*;
}

```
Remember to input your EC2 public IP adress in the designated place

Remember to modify the nginx.conf file permissions (chmod 777)

### 5. Launch your app
1. Launch Puma application server

`bundle exec rails server -b 0.0.0.0`<br>
> alternatywnie (działa?)<br>
In `store` folder, enter `bin/rails server`
> zapisz jak zatrzymać (sigkill) - htop > kill -9 PROCESS_ID_NO<br>

3. See your app running

Go to your EC2 Public IP
See the Rails splash screen.

4. (optional) Setup Public Elastic IP and assing it to your EC2 instance

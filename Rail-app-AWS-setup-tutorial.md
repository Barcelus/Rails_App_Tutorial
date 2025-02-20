## 1. Setup your AWS account
#### 1.1 Setup your security group

Go to EC2 -> Security Groups, then select **Create Security Group**<br>
Input your Secuity Group name and description (e.g. "*Security Group 1*", "*RailsAppDemo*")<br>
Select VPC created in previous step.<br>
Under **Inbound Rules** add 3 new rules:
+ Type: **SSH**; Source: **Anywhere IPv4** (leave all other fields as default)
+ Type: **HTTP**; Source: **Anywhere IPv4** (leave all other fields as default)
+ Type: **HTTPS**; Source: **Anywhere IPv4** (leave all other fields as default)<br>
 
Notice that protocol and port range is selected automatically based on the type of the inbound rule.<br>

Press **Create security group**, your Security Group is now created.

#### 1.2 Create and configure your IAM user for AWS Command Line Interface (CLI)

Go to IAM -> Users. You should see "*Users (0)*", you will need to create a new user.<br>
Press **Create user**, then enter a username (e.g. "*firstname-lastnameinitial*"), then click **Next**.<br>
Select **Attach policies directly**, then select a policy called **AdministratorAccess**. Click **Next** and then **Create user**.<br>

In the **Users** view, select your created user and go to **Security credential** tab, scroll down and press **Create access key**.<br>
Select the **Command Line Interface (CLI)** use case, the **Next**<br>
You may enter a description for that key (e.g. "*AWS_CLI_Key*"), then press **Create access key**<br>
Your key has been created - press the **Download .csv file**. Do not lose the secret access key, otherwise you will need to create a new one.<br>
Open the downloaded .csv file - you will find both Public and Secret access key values inside - these will be needed for the next step.<br>

#### 1.3 Setup AWS CLI (Command Line Interface)

Verify if AWS CLI is not installed on your local machine by entering:
```
aws
```
You should get:`Command 'aws' not found`

Install the AWS CLI:<br>
```
snap install aws-cli --classic
```
Verify if the installation was succesful by entering:
```
aws --v
```
Next, enter:
```
aws configure
```
Enter the Access Key ID (from the .csv file - to copy and paste from outside into terminal use **Ctrl+Shift+Insert** and **Shift + Insert** respectively)<br>
Enter the Secret access key (from the .csv file)<br>
Enter the default region name - ideally the same region you will launch your EC2 instance in (e.g. "*eu-north-1*")<br>
Leave the default output format empty (just press Enter)<br>

Verify configuration by entering:
```
aws iam list-users
```
You should see your created user, with the username you selected and it's access key ID.

## 2. Create default Rails app

#### 2.1 Create Ruby on Rails environment on your machine<br>

##### 2.1.1 Install and configure git<br>

Enter following commands:<br>
```
sudo apt update
sudo apt install git
```
Verify that git has been installed correctly by entering:
```
git -v
```
Set your username and user email:
```
git config --global user.name "YOUR_GITHUB_USERNAME"
git config --global user.email "YOUR_GITHUB_USER_EMAIL"
```
Verify by:
```
git config --list
```
Generate a SSH key for GitHub:
```
ssh-keygen -t rsa -b 4096 -C "YOUR_GITHUB_USER_EMAIL"
```
Then type enter to save the key in default location (`~/.ssh/id_rsa`)<br>
Start the SSH agent and add the key:
```
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```
Enter the following command and copy the output:
```
cat ~/.ssh/id_rsa.pub
```
Go to **GitHub**, then to **GitHub settings -> SSH and GPG keys**<br>
Press **New SSH key**, enter a key title and paste the copied output.

Test the GitHub SSH connection:
```
ssh -T git@github.com
```
Enter `yes` where necessary.<br>
You should see:<br>
`Hi <your-username>! You've successfully authenticated, but GitHub does not provide shell access.`

##### 2.1.2 Install rbenv<br>

Clone rbenv repo onto your instance:
```
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
```
Setup your shell to load rbenv:
```
~/.rbenv/bin/rbenv init
```
Restart the terminal:
+ On local: close and open the terminal window
+ On remote (EC2 instance): enter `exit` and connect to instance again

Verify rbenv is installed correctly by entering:
```
rbenv -v
```
Additionally, enter:
```
sudo apt-get install autoconf patch build-essential rustc libssl-dev libyaml-dev libreadline6-dev zlib1g-dev libgmp-dev libncurses5-dev libffi-dev libgdbm6 libgdbm-dev libdb-dev uuid-dev
```

##### 2.1.3 Install Ruby (using rbenv)<br>

Install Ruby version that is used by your app (for this tutorial we'll be using Ruby 3.3.4)
```
sudo apt-get install -y build-essential libssl-dev libyaml-dev zlib1g-dev libffi-dev
```
After that:
```
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
Verify Ruby is installed correclty by entering:
```
ruby -v
```
Install ruby gems:
```
gem update --system
sudo gem install bundler
```
Verify installation by entering:
```
gem env home
```

##### 2.1.4 Install database - SQLite3

Enter following:
```
sudo apt-get update
sudo apt install sqlite3
```
Verify by entering:
```
sqlite --version
```

##### 2.1.5 Install Rails

For this tutorial we'll using Rails version 8.0.0.<br>
Enter:
```
sudo gem install rails -v 8.0.0
```
Verfiy by:
```
rails --version
```

#### 2.2 Create the Rails app

##### 2.2.1 Create Rails app on local machine<br>

Enter:
```
rails new RailsAppDemo
```
After all files have been created, go to `/RailsAppDemo` and create databse for the app:
```
rails db:create
```

##### 2.2.2 Launch the app and verfiy it is working<br>

Enter (while in RailsAppDemo directory):
```
rails server
```
You should see a Puma server booting. When the server has launched, go to:<br>
[http://localhost:3000](http://localhost:3000)<br>

You should the Rails splash screen.

##### 2.2.3 Create a repository of that app

Go to GitHub and create a new repository (example name: *RailsAppDemo*)<br>

Inside the app directory enter:
```
git init
git add .
git commit -m "initial commit"
```
Then enter:
```
git remote add origin https://github.com/YOUR_USERNAME/RailsAppDemo
git remote set-url origin git@github.com:YOUR_USERNAME/RailsAppDemo
```
This will allow to push using SSH.<br>
Finally, enter:
```
git branch -M main
git push origin main
```
Go to your GitHub repository website and check if the repository was pushed correctly.

## 3. Launch an EC2 instance fo your app

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
	+ Move that file into `~/.ssh` folder
	+ Modify the key file permission - enter the `.ssh` folder in the terminal and enter command:
```
chmod 400 RailsAppDemoKey1.pem
```
+ Under **Network settings**, (press the **Edit** button):
	+ (optional) Select a subnet (e.g. "*eu-north-1****a***")
	+ Select the security group created in step 1.1
+ Under **Configure storage**, select at least 15GB of memory for your instance

Leave all other options as default.<br>
Press the **Launch instance** button. After that, go to **EC2 -> Instances -> Instances** and verify that your instance is launched and running.<br>
Instance is operational when it's **Instance state** is ***Running*** and under **Status check** there is ***3/3 checks passed***.

## 4. Setup your EC2 instance

#### 4.1 Connect to your instance using terminal

Input the following command:
```
ssh -i .ssh/RailsAppDemoKey1.pem ubuntu@EC2_INSTANCE_PUBLIC_IP
```
Substitute the `EC2_INSTANCE_PUBLIC_IP` with the public IP adress (or Public DNS).<br>
Enter `yes` when asked to connect to your instance. If the connection was succesful, you will notice your username will be different - `ubuntu@EC2_INSTANCE_PRIVATE_IP`<br>

If you want to exit from instance and return to your local machine, enter `exit`<br>
You can have multiple terminals opened, e.g. one for local machine and one to connect to EC2 instance.

#### 4.2 Setup your EC2 environment for Ruby on Rails

Repeats steps from section 2.1

#### 4.3 Download your app repository from GitHub

In the instance home directory enter:
```
git clone git@github.com:YOUR_GITHUB_USERNAME/RailsAppDemo
```
The repository will be cloned into your EC2 instance.

#### 4.4 Install and configure NGINX

NGINX should be already installed on your host, however, verify by running:
```
sudo apt install nginx
```
If NGINX had to be installed, restart your terminal.

After that you will need to create a `nginx.conf` file. You download this file from this repository and upload it to your EC2 instance using (enter on local - make sure the file is in the `RailsAppTutorial` directory):
```
scp -i .ssh/RailsAppDemoKey1.pem ~/RailsAppTutorial/nginx.conf ubuntu@YOUR_EC2_PUBLIC_IP:~/
```
Then move the file into `/etc/nginx` folder:
```
sudo mv ~/nginx.conf /etc/nginx
```

Alternatively, you can create the file from scratch:<br>
Go to `/etc/nginx` folder and create a `nginx.conf` file:
```
sudo touch nginx.conf
```
Open that file with a text editor (e.g. vim) and paste the following:<br>
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
**Remember to input your EC2 public IP adress in the designated place**<br>
In addition, each time you restar your EC2 instance, it will be given a new Public IP adress, which you will need to update in `nginx.conf` file.<br>
To circumvent this, you can assing a Public Elastic IP to your instance (this is a paid feature on AWS).

This file's permissions need to be modified:
```
chmod 777 nginx.conf
```

Each time you make any changes in the `nginx.conf` file (or any other NGINX file), you will need to restart NGINX:
```
sudo systemctl restart nginx
```
Verify that NGINX is running correctly:
```
service nginx status
```

## 5. Launch your app

#### 5.1 Launch Puma application server

To launch Puma server on EC2, enter (in RailsAppDemo directory):<br>
```
bin/rails server
```
Or, if above command results in an error:
```
bundle exec rails server -b 0.0.0.0
```
If both result in an error, you might need to install gems again using:
```
bundle install --gemfile /home/ubuntu/RailsAppDemo/Gemfile
```

If Puma server is already running, you will see a message similar to this:<br>
`A server is already running (pid: 65679, file: /home/ubuntu/RailsAppDemo/tmp/pids/server.pid).`

To stop the Puma server, enter:
```
kill -9 PUMA_PID
```
Replace `PUMA_PID` with the 5-digit PID from above message.

#### 5.2 See your app running

Go to your EC2 Public IP.<br>
See the Rails splash screen.<br>
Remember your `nginx.conf` file is setup for http connections only.<br>

### Congratulations, you've finished the tutorial on how to set up a defaul Rails app on an EC2 instance!
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
2. Install rbenv
3. Install Ruby (using rbenv)
4. Install database - SQLite3
5. Install Rails
6. Install and configure git
7. Install and configure NGINX  
NGINX should be already installed on your host, however, verfity by running:  
`sudo apt install nginx`  
After that, go to `/etc/nginx` folder and create a `nginx.conf` file:
```
sudo touch nginx.conf
```
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

### 4. Launch your app
1. Download your app repository from github
2. Launch Puma application server
3. See your app running
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



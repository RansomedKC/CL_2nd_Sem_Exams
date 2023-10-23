# CL_2nd_Sem_Exams
This is a repo with docs &amp; scripts for LAMP &amp; Laravel deployment via Vagrant &amp; Bash. Customize, contribute &amp; replicate the setup. Requires Vagrant &amp; basic scripting knowledge.
# Cloud Engineering Second Semester Examination Project

# (Deploy LAMP Stack)

This Markdown file provides detailed step by step explanations for the following exam questions:

## Table of Content
1. [Objective](#objective)
2. [Requirements](#requirements)
3. [Solution](#solution)
   - [Vagrantfile for Automated Virtual Machine Configuration](#vagrantfile-for-automated-virtual-machine-configuration)
   - [Bash Script to Deploy LAMP Stack and Laravel Application](#bash-script-to-deploy-lamp-stack-and-laravel-application)
   - [Screenshots for Application Accessibility](#screenshots-for-application-accessibility)


## Objective
Automate the provisioning of two Ubuntu-based servers, named "Master" and "Slave", using Vagrant.
On the Master node, create a bash script to automate the deployment of a LAMP (Linux, Apache, MySQL, PHP) stack.
This script should clone a PHP application from GitHub, install all necessary packages, and configure Apache web server and MySQL. 
Ensure the bash script is reusable and readable.
Using an Ansible playbook:
Execute the bash script on the Slave node and verify that the PHP application is accessible through the VM's IP address (take screenshot of this as evidence)
Create a cron job to check the server's uptime every 12 am.

Requirements

Submit the bash script and Ansible playbook to (publicly accessible) GitHub repository.
Document the steps with screenshots in md files, including proof of the application's accessibility (screenshots taken where necessary)
Use either the VM's IP address or a domain name as the URL.

PHP Laravel GitHub Repository:

https://github.com/laravel/laravel


Submission:

Submit your work via the AltSchool of Cloud Engineering v2 2nd Semester Exam Submission Link

Push your code to GitHub.
Share your GitHub link using the submission form.

# Solution:
## The below steps provides a detailed breakdown of how to automate the provisioning of two Ubuntu-based servers, named "Master" and "Slave_1", using Vagrant.

Below is the Vagrantfile with explanations for each line:

## Vagrantfile for Automated Virtual Machine Configuration

This Vagrantfile is used to configure and provision virtual machines for a specific project. It uses Vagrant, a tool for creating and managing virtual machine environments.

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :
```

- These lines specify that the file is written in Ruby and set the text editor mode for Vim.

```ruby
Vagrant.configure("2") do |config|
```

- This line initiates the Vagrant configuration block using version "2."

```ruby
  config.vm.define "slave_1" do |slave_1|
```

- This section defines the configuration for a virtual machine named "slave_1."

```ruby
    slave_1.vm.hostname = "slave-1"
```

- This line sets the hostname for the "slave_1" virtual machine to "slave-1."

```ruby
    slave_1.vm.box = "ubuntu/jammy64"
```

- This line specifies the base box for the virtual machine, in this case, "ubuntu/jammy64." This is the base operating system image for the VM.

```ruby
    slave_1.vm.network "private_network", ip: "192.168.33.10"
```

- This line configures a private network for the "slave_1" VM with the IP address "192.168.33.10." Private networks are internal to the virtual environment.

```ruby
    slave_1.vm.boot_timeout = 600
```

- This line sets the boot timeout for the "slave_1" VM to 600 seconds. It allows the VM extra time to boot.

```ruby
  end
```

- This section ends the configuration for the "slave_1" virtual machine.

```ruby
  config.vm.define "master" do |master|
```

- This section defines the configuration for a virtual machine named "master."

```ruby
    master.vm.hostname = "master"
```

- This line sets the hostname for the "master" virtual machine to "master."

```ruby
    master.vm.box = "ubuntu/jammy64"
```

- This line specifies the base box for the "master" VM, which is also "ubuntu/jammy64."

```ruby
    master.vm.network "private_network", ip: "192.168.33.11"
```

- This line configures a private network for the "master" VM with the IP address "192.168.33.11."

```ruby
    master.vm.boot_timeout = 600
```

- This line sets the boot timeout for the "master" VM to 600 seconds, allowing extra time for the VM to boot.

```ruby
    master.vm.provision "shell", path: "lamp.sh"
    # master.vm.provision "shell", path: "config_webserver.sh"
```

- All the above code lines define provisioning scripts for the "master" VM. The first script, "lamp.sh," is specified to be executed during VM setup. The second line is commented out but suggests the possibility of running another script for configuring the web server.

```ruby
  end
```

- This section ends the configuration for the "master" virtual machine.

```ruby
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "1024"
    vb.cpus = "2"
  end
end
```

- This section configures the provider-specific settings for the virtual machine. It sets the memory to 1024 MB and the number of CPUs to 2 using the VirtualBox provider.

This Vagrantfile is used to define the configuration of two virtual machines, "slave_1" and "master," with specific networking settings, provisioning scripts, and provider-specific configurations for VirtualBox.
 
**Question**: On the Master node, create a bash script to automate the deployment of a LAMP (Linux, Apache, MySQL, PHP) stack.
**Question**: This script should clone a PHP application from GitHub, install all necessary packages, and configure Apache web server and MySQL. 
**Question**: Ensure the bash script is reusable and readable.

**Solution**: Bash Script to Deploy LAMP Stack and Laravel Application

This Bash script automates the deployment of a LAMP (Linux, Apache, MySQL, PHP) stack and a Laravel PHP application. Below are the details of each step:

### Update and Upgrade the Server

```bash
sudo apt update && sudo apt upgrade -y < /dev/null
```

- `sudo` is used to execute the following command as a superuser.
- `apt update` updates the package list.
- `apt upgrade -y` upgrades installed packages without asking for confirmation.
- `< /dev/null` is used to prevent any input from the script and is typically used when running commands in a non-interactive mode.

### Installation of LAMP Stack

```bash
sudo apt-get install apache2 -y < /dev/null
```

- This command installs the Apache web server.
- `-y` is used to automatically confirm any prompts during installation.

```bash
sudo apt-get install mysql-server -y < /dev/null
```

- This command installs the MySQL server.

```bash
sudo add-apt-repository -y ppa:ondrej/php < /dev/null
```

- This line adds a Personal Package Archive (PPA) for PHP. This PPA is used to obtain newer versions of PHP.

```bash
sudo apt-get update < /dev/null
```

- This command updates the package list to include the packages from the newly added PHP PPA.

```bash
sudo apt-get install libapache2-mod-php php php-common php-xml php-mysql php-gd php-mbstring php-tokenizer php-json php-bcmath php-curl php-zip unzip -y < /dev/null
```

- This command installs various PHP modules and extensions required for a LAMP stack. These include extensions for XML, MySQL, GD (Graphics Draw), string manipulation, JSON, and more.

```bash
sudo sed -i 's/cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/' /etc/php/8.2/apache2/php.ini
```

- This command modifies the PHP configuration file to set `cgi.fix_pathinfo` to 0. This improves PHP security by reducing the risk of PHP code execution through malicious user input.

```bash
sudo systemctl restart apache2 < /dev/null
```

- This line restarts the Apache web server to apply the changes made to the PHP configuration.

### Install Composer

```bash
sudo apt install curl -y 
```

- This command installs the `curl` utility, which is commonly used to transfer data with URLs.

```bash
sudo curl -sS https://getcomposer.org/installer | php 
```

- This command downloads the Composer installer from the official website and pipes it to PHP to execute.

```bash
sudo mv composer.phar /usr/local/bin/composer 
```

- This line moves the downloaded `composer.phar` executable to a location in the system's `PATH`, making Composer globally accessible.

```bash
composer --version < /dev/null
```

- This command verifies the installation of Composer by displaying its version.

### Configuration of Apache2

```bash
cat << EOF > /etc/apache2/sites-available/laravel.conf
```

- This line uses a here document to create a new Apache VirtualHost configuration file for the Laravel application.

```bash
<VirtualHost *:80>
    ServerAdmin ramseyy92@gmail.com
    ServerName 192.168.33.10
    DocumentRoot /var/www/html/laravel/public

    <Directory /var/www/html/laravel>
    Options Indexes MultiViews FollowSymLinks
    AllowOverride All
    Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
EOF
```

- Within the here document, this code defines the VirtualHost configuration for Apache, specifying the server admin email, server name, document root, directory options, and log locations.

```bash
sudo a2enmod rewrite 
```

- This command enables the Apache rewrite module, which is essential for URL rewriting used in many web applications.

```bash
sudo a2ensite laravel.conf
```

- This command enables the newly created VirtualHost configuration.

```bash
sudo systemctl restart apache2
```

- This command restarts the Apache web server to apply the changes in the configuration.

### Clone Laravel Application and Dependencies

```bash
mkdir /var/www/html/laravel && cd /var/www/html/laravel
```

- This command creates a directory for the Laravel application and navigates to it.

```bash
cd /var/www/html && sudo git clone https://github.com/laravel/laravel.git
```

- This line clones the Laravel application from its GitHub repository into the specified directory.

```bash
cd /var/www/html/laravel && composer install --no-dev < /dev/null
```

- This command navigates to the Laravel directory and installs PHP dependencies using Composer. The `--no-dev` flag excludes development dependencies.

```bash
sudo chown -R www-data:www-data /var/www/html/laravel
```

- This line sets ownership of the Laravel files and directories to the `www-data` user and group. This is typically the user Apache runs as.

```bash
sudo chmod -R 775 /var/www/html/laravel
sudo chmod -R 775 /var/www/html/laravel/storage
sudo chmod -R 775 /var/www/html/laravel/bootstrap/cache
```

- These commands modify file and directory permissions to allow the web server to read and write files where necessary.

```bash
cd /var/www/html/laravel && sudo cp .env.example .env
```

- This line creates a copy of the Laravel environment configuration file by copying `.env.example` to `.env`.

```bash
php artisan key:generate
```

- This command generates a unique application key used for encryption and security within Laravel.

### Configuring MySQL: Creating User and Password

```bash
echo "Creating MySQL user and database"
```

- This line is a simple informational message.

```bash
PASS=$2
if [ -z "$2" ]; then
  PASS=`openssl rand -base64 8`
fi
```

- These lines assign a password from the second script argument or generate a random password if not provided.

```bash
mysql -u root <<MYSQL_SCRIPT
...
MYSQL_SCRIPT
```

- This command creates a MySQL user and database. It uses a here document to provide MySQL commands to the `mysql` command-line tool.

```bash
echo "MySQL user and database created."
echo "Username:   $1"
echo "Database:   $1"
echo "Password:   $PASS"
```

- These lines provide information about the created MySQL user, database, and password.

### Execute Key Generation and Migration for PHP

```bash
sudo sed -i 's/DB_DATABASE=laravel/DB_DATABASE=ransomed/' /var/www/html/laravel/.env
```

- This command modifies the `.env` file to set the database name to "ransomed."

```bash
sudo sed -i 's/DB_USERNAME=root/DB_USERNAME=ransomed/' /var/www/html/laravel/.env
```


- This line sets the database username in the `.env` file to "ransomed."

```bash
sudo sed -i 's/DB_PASSWORD=/DB_PASSWORD=kidnap007/' /var/www/html/laravel/.env
```

- This command sets the database password in the `.env` file to "kidnap007."

```bash
php artisan config:cache
```

- This command caches the Laravel configuration to improve performance.

```bash
cd /var/www/html/laravel && php artisan migrate
```

- This command runs the Laravel migration, which sets up the database tables for the application.

**Question**: Document the steps with screenshots in md files, including proof of the application's accessibility (screenshots taken where necessary)
**Question**: Use either the VM's IP address or a domain name as the URL.

**Solution**: Kindly find below screenshots of the two IP addresses for "slave_1" and "master" VM with a cloned laravel PHP application from GitHub. "192.68.33.10" is the IP address for "slave_1" VM, while "192.68.33.11" is the IP address for the "master" VM as seen in the screenshots below.

This is the specific screenshot for "Master" VM.
![Master_ScreenShot1](https://github.com/RansomedKC/CL_2nd_Sem_Exams/assets/137722760/3803e879-42ce-41ad-8abd-bcec19707660)

![Master_Screenshot2](https://github.com/RansomedKC/CL_2nd_Sem_Exams/assets/137722760/2e0ae06d-24b5-478c-8e63-580dfa42f21f)

This is the specific screenshot for "slave_1" VM.
![Slave_Screenshot1](https://github.com/RansomedKC/CL_2nd_Sem_Exams/assets/137722760/075d5d14-cfeb-4aae-8b06-9f1658877f7d)

![Slave_Screenshot2](https://github.com/RansomedKC/CL_2nd_Sem_Exams/assets/137722760/c422e3b7-6063-4d99-a553-6ed65577f79c)

This is the specific screenshot for the Ansible 
![Ansible_Screenshot1](https://github.com/RansomedKC/CL_2nd_Sem_Exams/assets/137722760/cb6a2a64-bc30-4f9a-9c5c-de9646a049d1)

![Ansible_SS2](https://github.com/RansomedKC/CL_2nd_Sem_Exams/assets/137722760/3e179540-d9df-426a-b29b-cea8ae158746)

The above is a detailed explanation of the provided Bash script (script.sh) file used to answer the question.

In this section, the script configures the Laravel application by updating the `.env` file with the database details, then runs the `php artisan config:cache` and `php artisan migrate` commands to set up the Laravel application.

After setting up all of this, I executed the script with the necessary arguments for database configuration, and ensured that I followed the steps and verified that the Laravel application is accessible as per the project requirements.

## Conclusion
In conclusion, this documentation and accompanying scripts facilitate the automation of LAMP stack and Laravel application deployments, ensuring a smoother and more efficient development process. Users are encouraged to explore, customize, and contribute to this open-source project for the benefit of the broader community.
```

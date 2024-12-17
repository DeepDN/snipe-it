# Snipe-IT Installation Guide using NGINX

This guide provides step-by-step instructions to install Snipe-IT on an Ubuntu system using Nginx.

---

## Prerequisites

### Update your system:
```bash
sudo apt update && sudo apt upgrade -y
```

![Update System](https://via.placeholder.com/800x400?text=Update+System+Command)

### Install Required Packages:
```bash
sudo apt install -y nginx mysql-server git unzip curl php php-cli php-fpm php-curl php-mysql php-gd php-ldap php-zip php-mbstring php-xml php-bcmath php-tokenizer
```

![Install Required Packages](https://via.placeholder.com/800x400?text=Install+Required+Packages)

---

## 1. Configure MySQL

### Secure MySQL Installation:
```bash
sudo mysql_secure_installation
```
Follow the prompts to secure MySQL (set root password, remove test databases, etc.).

![MySQL Secure Installation](https://via.placeholder.com/800x400?text=MySQL+Secure+Installation)

### Create Database and User for Snipe-IT:
```bash
sudo mysql -u root -p
```
Inside the MySQL shell:
```sql
CREATE DATABASE snipeit_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'snipeit_user'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON snipeit_db.* TO 'snipeit_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

![MySQL Create Database](https://via.placeholder.com/800x400?text=Create+Database+and+User)

---

## 2. Install Composer

### Download and install Composer globally:
```bash
curl -sS https://getcomposer.org/installer | php
sudo mv composer.phar /usr/local/bin/composer
```

![Install Composer](https://via.placeholder.com/800x400?text=Install+Composer)

### Verify installation:
```bash
composer --version
```

![Verify Composer Installation](https://via.placeholder.com/800x400?text=Verify+Composer+Installation)

---

## 3. Download Snipe-IT

### Navigate to your web root:
```bash
cd /var/www
```

![Navigate to Web Root](https://via.placeholder.com/800x400?text=Navigate+to+Web+Root)

### Clone the Snipe-IT repository:
```bash
sudo git clone https://github.com/snipe/snipe-it.git snipe-it
```

![Clone Repository](https://via.placeholder.com/800x400?text=Clone+Repository)

### Set permissions:
```bash
sudo chown -R www-data:www-data /var/www/snipe-it
sudo chmod -R 755 /var/www/snipe-it
```

![Set Permissions](https://via.placeholder.com/800x400?text=Set+Permissions)

---

## 4. Configure Snipe-IT

### Navigate to the Snipe-IT directory:
```bash
cd /var/www/snipe-it
```

![Navigate to Snipe-IT Directory](https://via.placeholder.com/800x400?text=Navigate+to+Snipe-IT+Directory)

### Copy the .env.example file to .env:
```bash
sudo cp .env.example .env
```

![Copy .env File](https://via.placeholder.com/800x400?text=Copy+.env+File)

### Edit the .env file:
```bash
sudo nano .env
```
Update the following lines:

#### Database Settings:
```env
DB_HOST=127.0.0.1
DB_DATABASE=snipeit_db
DB_USERNAME=snipeit_user
DB_PASSWORD=your_password
```

#### App URL:
```env
APP_URL=http://your_domain_or_IP
```

![Edit .env File](https://via.placeholder.com/800x400?text=Edit+.env+File)

### Install dependencies using Composer:
```bash
sudo composer install --no-dev --prefer-source
```

![Install Dependencies](https://via.placeholder.com/800x400?text=Install+Dependencies)

### Generate the application key:
```bash
sudo php artisan key:generate
```

![Generate Application Key](https://via.placeholder.com/800x400?text=Generate+Application+Key)

---

## 5. Configure Nginx

### Create a new Nginx configuration file:
```bash
sudo nano /etc/nginx/sites-available/snipeit
```

![Create Nginx Config](https://via.placeholder.com/800x400?text=Create+Nginx+Config)

### Add the following configuration:
```nginx
server {
    listen 80;
    server_name your_domain_or_IP;

    root /var/www/snipe-it/public;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.3-fpm.sock; # Adjust PHP version if needed
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

![Nginx Configuration](https://via.placeholder.com/800x400?text=Nginx+Configuration)

### Enable the configuration:
```bash
sudo ln -s /etc/nginx/sites-available/snipeit /etc/nginx/sites-enabled/
```

![Enable Nginx Config](https://via.placeholder.com/800x400?text=Enable+Nginx+Config)

### Test and reload Nginx:
```bash
sudo nginx -t
sudo systemctl reload nginx
```

![Reload Nginx](https://via.placeholder.com/800x400?text=Reload+Nginx)

---

## 6. Set Permissions
Ensure proper permissions for Snipe-IT:
```bash
sudo chown -R www-data:www-data /var/www/snipe-it
sudo chmod -R 755 /var/www/snipe-it/storage /var/www/snipe-it/bootstrap/cache
```

![Set Permissions](https://via.placeholder.com/800x400?text=Set+Permissions)

---

## 7. Access Snipe-IT
Open your browser and navigate to:
```
http://your_domain_or_IP
```

Follow the on-screen installation steps to complete the setup.

![Access Snipe-IT](https://via.placeholder.com/800x400?text=Access+Snipe-IT)

---

## 8. Enable HTTPS (Optional)

If you have a domain, secure your installation using Let's Encrypt:
```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d your_domain
```

![Enable HTTPS](https://via.placeholder.com/800x400?text=Enable+HTTPS)

---

You now have Snipe-IT installed and running on Ubuntu with Nginx!


# Multiple-Domain-Django-Deploy-in-Ubuntu-22.04



Settings.py:
=====================================
Need to check settings.py for edit the domains



virtualhostmiddleware.py:
=========================================================
    if settings.DEVELOPMENT_MODE:
    virtual_hosts = {
        "localhost:8000": "Crunchscaled.urls",
        "localhost:9000": "Crunchscaled.ecomerce_urls",
    }
    
    else:
    virtual_hosts = {
         "www.shopatimall.com": "Crunchscaled.ecomerce_urls",
         "test.krunchiq.com": "Crunchscaled.url",
        
        "www.admin.mediascaled.com": "Crunchscaled.urls",
        "www.e-mall247.com": "Crunchscaled.ecomerce_urls",
        "www.admin.krunchiq.com": "Crunchscaled.url",
    }



===================================================


sudo apt-get update

sudo apt-get install -y python3-venv

python3 -m venv env

source env/bin/activate

git clone ...

pip install -r requirements.txt

sudo apt-get install nginx

change the security group

pip install gunicorn


allow the host in settings.py

bind gunicorn with wsgi:
=============================
gunicorn --bind 0.0.0.0:8000 project_name.wsgi:application
gunicorn --bind 0.0.0.0:9000 project_name.wsgi:application



sudo systemctl reload nginx


To make it permanent running state:
=======================================
sudo apt-get install supervisor

cd /etc/supervisor/conf.d/
sudo touch gunicorn.conf

--workers 4 --timeout 600 --limit-request-line 16384 --limit-request-fields 65536 --limit-request-field_size 32768 --bind 

sudo nano gunicorn.conf:
=========================================

      [program:gunicorn8000]
      directory=/home/ubuntu/project
      command=/home/ubuntu/env/bin/gunicorn --workers 3 --bind 0.0.0.0:8000 project_name.wsgi:application
      autostart=true
      autorestart=true
      stderr_logfile=/var/log/gunicorn/gunicorn8000.err.log
      stdout_logfile=/var/log/gunicorn/gunicorn8000.out.log
      
      [program:gunicorn9000]
      directory=/home/ubuntu/project
      command=/home/ubuntu/env/bin/gunicorn --workers 3 --bind 0.0.0.0:9000 project_name.wsgi:application
      autostart=true
      autorestart=true
      stderr_logfile=/var/log/gunicorn/gunicorn9000.err.log
      stdout_logfile=/var/log/gunicorn/gunicorn9000.out.log
      
      [group:guni]
      programs: gunicorn8000, gunicorn9000


==============================================




sudo mkdir /var/log/gunicorn

sudo supervisorctl reread

sudo supervisorctl update

sudo supervisorctl status

sudo systemctl reload nginx



cd /etc/nginx/sites-available/

sudo nano default

=============================================================


# For domain served on port 8000
      server {
          listen 80;
          server_name admin.krunchiq.com;
      
          location / {
              include proxy_params;
              proxy_pass http://127.0.0.1:8000;
          }
      }

# For domain served on port 9000
     server {
         listen 80;
         server_name www.e-mall247.com;
     
         location / {
             include proxy_params;
             proxy_pass http://127.0.0.1:9000;
         }
     }



=============================================================

# Check Nginx Status
    sudo systemctl status nginx
    sudo systemctl start nginx

# Test Nginx Configuration
    sudo nginx -t



## make some line into comments - example : root /var/www/html;, index index.html, uri uri try_files ...

# and add in location /:
    include proxy_params;
    proxy_pass http://unix:/home/ubuntu/project/app.sock;


# add server name ip_address www.domain.com domain.com;

sudo systemctl reload nginx


# there need permission so add user:
    sudo gpasswd -a www-data ubuntu
    
    sudo systemctl reload nginx
    
    
    sudo systemctl stop supervisor
    sudo systemctl start supervisor
    
    sudo systemctl reload nginx
    
    sudo systemctl restart nginx
    sudo supervisorctl restart all



https://www.valentinog.com/blog/django-vhosts/




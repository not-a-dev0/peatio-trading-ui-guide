### **_Open a new Terminal and login to Instance you installed your Peatio on_**

**Clone the repo and setup the Trading UI**
**_Make sure you checkout the same branch you used for Peatio_**

```
cd ~/code
git clone https://github.com/rubykube/peatio-trading-ui.git
cd peatio-trading-ui
git checkout 1-9-stable

sudo mkdir -p /opt/vendor/bundle
sudo chown -R deploy:deploy /opt/vendor
```

**Before running bundle install, make sure you are using ruby -v2.5.0** 

`ruby -v`

**If it does not return 2.5.0p0, then you need to use rvm to change the ruby version**

```
/bin/bash --login
rvm use 2.5.0
``` 

**Then bundle install**

```
bundle install --path /opt/vendor/bundle
bin/init_config
```

**Edit the /config/application.yml**

```
sudo nano config/application.yml

PLATFORM_ROOT_URL: http://peatio.tech
FRONTEND_ROOT_URL: http://peatio.tech:4000
```

**Install and run yarn:**

```
sudo npm install -g yarn
sudo chown -R deploy /home/deploy/.config
bundle exec rake tmp:create yarn:install
```

**Then bundle precompile assets.** 

```
bundle exec rake tmp:clear log:clear
bundle exec rake assets:precompile
```

**Creating and Sourcing Secret_key_base**

```
bundle exec rake secret
echo "export SECRET_KEY_BASE=YOUR-SECRET" >> ~/.bashrc
source ~/.bashrc
```

**Setup the reverse proxy**

```
sudo nano /etc/nginx/sites-available/default

server {
  server_name      peatio.tech;
  listen           80;
  proxy_set_header Host peatio.tech;

  location ^~ /(?:trading|trading-ui-assets)\/ {
    proxy_pass http://127.0.0.1:4000;
    proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header    X-Real-IP $remote_addr;
    proxy_set_header    Host $http_host;
    proxy_next_upstream http_502 http_504 error timeout invalid_header;
  }

  location / {
    proxy_pass http://127.0.0.1:3000;
    proxy_redirect      default;
    proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header    X-Real-IP $remote_addr;
    proxy_set_header    Host $http_host;
    proxy_next_upstream http_502 http_504 error timeout invalid_header;
  }
}
```

**Verify that the syntax of the config file is valid** 

`sudo nginx -t`

**Restart NGINX**

`sudo systemctl restart nginx`

**Start the server**

`bundle exec rails server -p 4000`

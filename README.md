# Setup VPS

## Standart setup

`sudo su -`

`apt-get update -y
apt-get dist-upgrade -y`

`ufw allow OpenSSH
echo 'y' | ufw enable
ufw status`

`apt-get install git build-essential curl openssl libssl-dev -y`

`systemctl restart sshd`

## Nginx

`apt-get install nginx -y
systemctl status nginx`

`ufw allow 'Nginx FULL'
ufw status`

`rm /etc/nginx/sites-available/default
rm /etc/nginx/sites-enabled/default`

## Allow a lot domains

`sed -i 's/# server_names_hash_bucket_size 64;/server_names_hash_bucket_size 64;/g' /etc/nginx/nginx.conf`

## Creating domains

`DOMAINClIENT=`domain-client

`cat <<EOF > /etc/nginx/sites-available/${DOMAINClIENT}
server {
server_name ${DOMAINClIENT} www.${DOMAINClIENT};
root /home/www/${DOMAINClIENT}/current/public;
index index.html;
if (\$host = www.${DOMAINClIENT}) {
return 301 [https://$](https://%24/){DOMAINClIENT}\$request_uri;
}
location / {
proxy_pass [http://localhost:](http://localhost:1337/)3000;
proxy_http_version 1.1;
proxy_set_header Upgrade \$http_upgrade;
proxy_set_header Connection 'upgrade';
proxy_set_header Host \$host;
proxy_cache_bypass \$http_upgrade;

# we need to remove this 404 handling

# because next's \_next folder and own handling

# try_files \$uri \$uri/ =404;

}
}
EOF`

`DOMAINCMS=`domain-cms

`cat <<EOF > /etc/nginx/sites-available/${DOMAINCMS}
server {
server_name ${DOMAINCMS} www.${DOMAINCMS};
root /home/www/${DOMAINCMS}/current/public;
index index.html;
if (\$host = www.${DOMAINCMS}) {
return 301 [https://$](https://%24/){DOMAINCMS}\$request_uri;
}
location / {
proxy_pass [http://localhost:](http://localhost:1337/)1337;
proxy_http_version 1.1;
proxy_set_header Upgrade \$http_upgrade;
proxy_set_header Connection 'upgrade';
proxy_set_header Host \$host;
proxy_cache_bypass \$http_upgrade;

# we need to remove this 404 handling

# because next's \_next folder and own handling

# try_files \$uri \$uri/ =404;

}
}
EOF`

`ln -s /etc/nginx/sites-available/${DOMAINClIENT} /etc/nginx/sites-enabled/
ln -s /etc/nginx/sites-available/${DOMAINCMS} /etc/nginx/sites-enabled/`

`systemctl restart nginx`

## Install node && pm2

`curl -o- [https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh](https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh) | bash
source ~/.bashrc
nvm i 20.5.1
npm install pm2 -g --unsafe-perm
pm2 startup`

## Creating dirs

`cd /home/
mkdir www
cd www
mkdir` **domain-client**
`mkdir` **domain-cms**
`cd` **domain-client**
`mkdir current
cd ..
cd` **domain-cms**
`mkdir current
cd ..`

## gh setup

`type -p curl >/dev/null || sudo apt install curl -y
curl -fsSL [https://cli.github.com/packages/githubcli-archive-keyring.gpg](https://cli.github.com/packages/githubcli-archive-keyring.gpg) | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg \
&& sudo chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg \
&& echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] [https://cli.github.com/packages](https://cli.github.com/packages) stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
&& sudo apt update \
&& sudo apt install gh -y`

### gh login

`gh auth login`
''

## Cerbot setup

`sudo snap install core; sudo snap refresh core
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx`

## PostgreSQL setup

`sudo apt install postgresql postgresql-contrib
sudo -i -u postgres
createdb NAME
psql
\password postgres
pass
pass`

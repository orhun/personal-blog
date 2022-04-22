+++
title = "Spin Up Your Own No-Bullshit File Hosting Service"
date = 2021-02-14

[taxonomies]
categories = ["Guides"]
+++

A guide for spinning up your own instance of 0x0, a no-bullshit file hosting and URL shortening service.

<!-- more -->

[0x0](https://git.0x0.st/mia/0x0) (AKA "The Null Pointer") is a "no-bullshit file hosting and URL shortening service" that I've been using for a couple of months now. It's very easy to use, simple and straightforward. Although you can use the homepage of the project ([0x0.st](https://0x0.st/)) for hosting files and shortening links, I'd like to tell you how did I spin up my own instance of [0x0](https://git.0x0.st/mia/0x0) because (as the author of the project says) _centralization is bad!_

- [Requirements](#requirements)
- [Configuring 0x0](#configuring-0x0)
- [Testing 0x0 with uWSGI](#testing-0x0-with-uwsgi)
- [Configuring uWSGI](#configuring-uwsgi)
- [Creating a systemd unit file for uWSGI](#creating-a-systemd-unit-file-for-uwsgi)
- [Configuring Nginx for proxying requests](#configuring-nginx-for-proxying-requests)
- [Getting an SSL certificate](#getting-an-ssl-certificate)
- [Using 0x0](#using-0x0)
- [Conclusion](#conclusion)

## Requirements

- A server and preferably a domain. (I'll be using Ubuntu 20.04.1 from [AWS Free Tier](https://aws.amazon.com/free/) and a [free domain](https://www.freenom.com/en/freeandpaiddomains.html))
- Linux dependencies: [Nginx](https://ubuntu.com/tutorials/install-and-configure-nginx), [Certbot](https://certbot.eff.org/docs/install.html), [Python3](https://www.digitalocean.com/community/tutorials/how-to-install-python-3-and-set-up-a-programming-environment-on-an-ubuntu-20-04-server)
- Python dependencies: [uWSGI](https://uwsgi-docs.readthedocs.io/en/latest/Install.html), [python-pip](https://pip.pypa.io/en/stable/installation/)
    - pip install -r [requirements.txt](https://git.0x0.st/mia/0x0/src/branch/master/requirements.txt)

## Configuring 0x0

Connect to your server via SSH and clone the repository:

```sh
cd $HOME/
git clone https://git.0x0.st/mia/0x0 && cd 0x0/
```

Then you can tweak some settings like maximum file size and storage path in [fhost.py](https://git.0x0.st/mia/0x0/src/branch/master/fhost.py) or in `instance/config.py`.

Don't forget to create the configuration file and initialize the database before proceeding:

```sh
mkdir instance/
touch instance/config.py
python3 fhost.py db upgrade
```

## Testing 0x0 with uWSGI

After you [install](https://uwsgi-docs.readthedocs.io/en/latest/Install.html) uWSGI, you can simply test if 0x0 runs with the following command[*](https://git.0x0.st/mia/0x0/issues/8):

```sh
uwsgi --socket 0.0.0.0:8080 --protocol=http --wsgi-file fhost.py --callable app
```

**Note:** If `--wsgi-file` option is not recognized, make sure you have python plugin for uWSGI installed and loaded. [*](https://stackoverflow.com/questions/31330905/uwsgi-options-wsgi-file-and-module-not-recognized)

You should be seeing the homepage of 0x0 at `http://your_server_ip:8080`. If not, debug time...

## Configuring uWSGI

Now we can create a configuration file for UWSGI to serve 0x0 more easily for production. Since this [tutorial](https://www.digitalocean.com/community/tutorials/how-to-serve-flask-applications-with-uswgi-and-nginx-on-ubuntu-18-04#step-4-%E2%80%94-configuring-uwsgi) also suggests doing this, I don't see any reason to not do so!

`$HOME/0x0/0x0.ini`

```ini
[uwsgi]
wsgi-file = fhost.py
callable = app

master = true       # Master mode
processes = 5       # Spawn 5 worker processes to serve requests

socket = 0x0.sock   # Unix socket
chmod-socket = 660  # Socket permissions
vacuum = true       # Clean up the socket when process exits

die-on-term = true  # Shutdown if SIGTERM is received
```

Then you can use the `-c` option for using this configuration file:

```sh
uwsgi -c 0x0.ini
```

## Creating a systemd unit file for uWSGI

We can create a systemd unit file to start our service on boot. Something basic like this will do the job:

`/etc/systemd/system/0x0.service`

```ini
[Unit]
Description=uWSGI instance to serve 0x0
After=network.target

[Service]
User=ubuntu
Group=www-data
WorkingDirectory=/home/ubuntu/0x0
ExecStart=/home/ubuntu/.local/bin/uwsgi --ini 0x0.ini

[Install]
WantedBy=multi-user.target
```

Change `ubuntu` to your `$USER` and you can configure the rest as you like.

Start/enable the service:

```sh
sudo systemctl start 0x0
sudo systemctl enable 0x0
```

Check if it's running w/o errors:

```sh
sudo systemctl status 0x0
```

If you see any errors: debug time once again. My condolences.

## Configuring Nginx for proxying requests

Now we can configure Nginx to pass requests to our Unix socket (`0x0.sock`) via `uwsgi` protocol.

First, let's create a server block configuration:

`/etc/nginx/sites-available/0x0`

```nginx
server {
    server_name your_domain www.your_domain;

    location / {
        include uwsgi_params;
        uwsgi_pass unix:/home/ubuntu/0x0/0x0.sock; # Unix socket location
    }

    location /up {               # Upload directory (FHOST_STORAGE_PATH in fhost.py)
        root /home/ubuntu/0x0;   # Root of the upload directory
        internal;
    }

}
```

Enable the server block configuration:

```sh
sudo ln -s /etc/nginx/sites-available/0x0 /etc/nginx/sites-enabled
```

And lastly {re,}start Nginx to read the new configuration:

```sh
sudo systemctl restart nginx
```

If everything went well, you should be seeing your site up at `http://your_domain`. If not, check the following:

* `sudo less /var/log/nginx/{error,access}.log`
* `sudo journalctl -u {nginx,0x0}`

## Getting an SSL certificate

We can simply use [Certbot](https://certbot.eff.org/) to get an SSL certificate and secure our application. In order to do that, install Certbot and `nginx` plugin:

```sh
sudo apt-get install certbot
sudo apt-get install python3-certbot-nginx
```

Then use `nginx` plugin to create a new certificate:

```sh
sudo certbot --nginx -d your_domain -d www.your_domain
```

It's going to ask for some information and eventually reconfigure Nginx to use the newly created SSL certificate. 

Add `uwsgi_param UWSGI_SCHEME https;` to your server block config (for enforcing HTTPS) and your new server block might look like this at the end:

```nginx
server {
    server_name your_domain www.your_domain;

    location / {
        include uwsgi_params;
        uwsgi_pass unix:/home/ubuntu/0x0/0x0.sock;
        uwsgi_param UWSGI_SCHEME https;
    }

    location /up {
        root /home/ubuntu/0x0;
        internal;
    }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/your_domain/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/your_domain/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
}

server {
    if ($host = www.your_domain) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    if ($host = your_domain) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    listen 80;
    server_name your_domain www.your_domain;
    return 404; # managed by Certbot
}
```

And voila! Your very own 0x0 instance is live at `https://your_domain`.

## Using 0x0

* For posting files: `curl -F'file=@yourfile.png' https://your_domain`
* For posting remote URLs: `curl -F'url=http://example.com/image.jpg' https://your_domain`
* For shortening URLs: `curl -F'shorten=http://example.com/some/long/url' https://your_domain`

I recommend setting up aliases for these commands or using a wrapper script like [this](https://github.com/Calinou/0x0).

To make files expire, simply create a cronjob that runs `cleanup.py` every now and then.

## Conclusion

And here we have a "no-bullshit" file hosting and URL shortening service! Of course, we could've automated the process by writing a Dockerfile but it was my first _significant_ attempt on these topics so I wanted to share my story by blogging it.

Feel free to [contact](https://orhun.dev/) me for improvements/fixes!

viel spaß! :)

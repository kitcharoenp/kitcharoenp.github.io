---
layout : post
title : "RASA chatbot with webdriver"
categories : [rasa]
published : false
---

### Install & init

```shell
# Create project directory
$  sudo mkdir /opt/crm_chatbot

# Change directory permission
$  sudo chown -R ubuntu:ubuntu /opt/crm_chatbot/

# Create virtual environment
$  cd /opt/crm_chatbot/
$  python3 -m venv ./venv

# Activate virtual environment
$  source ./venv/bin/activate

# Install RASA
$  pip install rasa
$  rasa --version
$  rasa init
```

### Ngrog

```shell
# Install ngrog
$  curl -s https://ngrok-agent.s3.amazonaws.com/ngrok.asc |   sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null &&   echo "deb https://ngrok-agent.s3.amazonaws.com buster main" |   sudo tee /etc/apt/sources.list.d/ngrok.list &&   sudo apt update && sudo apt install ngrok

# Run
$  ngrok http 5005

# Run RASA
$  rasa run --port 5002 --connector slack --credentials credentials.yml --endpoints endpoints.yml --log-file out.log --cors * --enable-api --debug
```

### Chromium
```shell
$  sudo snap install chromium
$  snap list
$  sudo snap refresh hold
$  sudo snap refresh --hold

```

### Chromedriver
```shell
$  wget https://edgedl.me.gvt1.com/edgedl/chrome/chrome-for-testing/118.0.5993.70/linux64/chromedriver-linux64.zip
$  sudo apt install unzip 
$  unzip chromedriver-linux64.zip 
```

### Selenium

```shell
$  source ./venv/bin/activate
$  pip install seleniumwire
$  pip install selenium-wire
$  pip install fake_useragent
$  pip install ruamel.yaml
$  pip install undetected_chromedriver
```

### RASA

```shell
$ rasa train
$ ngrok http 5002
$ rasa run actions
```
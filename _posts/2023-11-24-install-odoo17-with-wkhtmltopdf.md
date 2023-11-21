---
layout : post
title : "Install Odoo17 on Ubuntu 22.04 with wkhtmltopdf"
categories : [odoo]
published : true
---


### Install Odoo17 

**Use [Odoo Nightly builds](https://nightly.odoo.com/)**
```shell
# Setup of the Debian nightly repository

$ wget -O - https://nightly.odoo.com/odoo.key | sudo gpg --dearmor -o /usr/share/keyrings/odoo-archive-keyring.gpg
$ echo 'deb [signed-by=/usr/share/keyrings/odoo-archive-keyring.gpg] https://nightly.odoo.com/17.0/nightly/deb/ ./' | sudo tee /etc/apt/sources.list.d/odoo.list


# Install Odoo
$ sudo apt-get update && sudo apt-get install odoo
```

### Install wkhtmltopdf

```shell
# Install dependencies package for wkhtmltopdf

$ sudo apt install xfonts-75dpi  xfonts-base
$ sudo apt --fix-broken install

$ wget http://security.ubuntu.com/ubuntu/pool/main/o/openssl/libssl1.1_1.1.0g-2ubuntu4_amd64.deb
$ sudo dpkg -i libssl1.1_1.1.0g-2ubuntu4_amd64.deb 
$ sudo apt --fix-broken install


$ wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox_0.12.5-1.bionic_amd64.deb
$ sudo dpkg -i wkhtmltox_0.12.5-1.bionic_amd64.deb

$ sudo service odoo restart 
```

### Add System Parameters

**Settings > Parameters > System parameters:**

> `report.url`: **IP_address:Port** This should be used if `web.base.url` parameter is not enough to make it work. Usually this url should be: `http://127.0.0.1:8069`


### Reference
* [ How install libssl1.1 on ubuntu 22.04 ](https://gist.github.com/joulgs/c8a85bb462f48ffc2044dd878ecaa786)

* [PDF Printout Doesn’t Show Header](https://codewithkarani.com/2023/08/08/pdf-printout-doesnt-show-header-in-frappe-erpnext/)

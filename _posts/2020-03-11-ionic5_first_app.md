---
layout: post
title: "Ionic5 : Build First App"
published: true
categories: [ionic]
---

### Required Tools
* [**Node.js v13.x:**][1]
```shell
# Using Ubuntu
curl -sL https://deb.nodesource.com/setup_13.x | sudo -E bash -
sudo apt-get install -y nodejs
```

* A code editor **[Visual Studio Code on Linux][2]** , install it by running:
```shell
sudo snap install --classic code
```

[1]: https://github.com/nodesource/distributions/blob/master/README.md#debinstall "nodejs debinstall"
[2]: https://code.visualstudio.com/docs/setup/linux "visualstudio setup linux"

### Remote Development using SSH


### [Install the Ionic CLI][1]
Chang directtory to your project, Install the Ionic CLI with npm locally:
```shell
cd /opt/ionic5_project
npm install  @ionic/cli
```

Install lab mode as a dev-dependency for testing purpose.
```shell
npm i @ionic/lab --save-dev
```

### Start an App
Start an `meeting` App with `blank` template and type is `angular`
```shell
ionic start meetingApp blank --type=angular
```

### Start server
Start a local dev server for app, [`ionic serve`][2] with `-l` option : Test your apps on multiple platform types in the browser

```shell
ionic serve -l
```

Options:
```
    --no-livereload .......... Do not spin up dev server--just serve files
    --project ................ The name of the project
    --no-open ................ Do not open a browser window
    --local .................. Disable external network usage
    --lab, -l ................ Test your apps on multiple platform types in the browser
    --prod ................... [ng] Flag to use the production configuration
```    




[1]: https://ionicframework.com/docs/intro/cli#install-the-ionic-cli "Install the Ionic"

[2]: https://ionicframework.com/docs/cli/commands/serve "ionic serve"

---
layout : post
title : "Ionic6 React Starting an App"
categories : [ionic]
published : true
---

### [Install Node.js v18.x:](https://github.com/nodesource/distributions/blob/master/README.md#installation-instructions)

```shell
# Using Ubuntu
$ curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -

## Run `sudo apt-get install -y nodejs` to install Node.js 18.x and npm
## You may also need development tools to build native addons:
     sudo apt-get install gcc g++ make
## To install the Yarn package manager, run:
     curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | gpg --dearmor | sudo tee /usr/share/keyrings/yarnkey.gpg >/dev/null
     echo "deb [signed-by=/usr/share/keyrings/yarnkey.gpg] https://dl.yarnpkg.com/debian stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
     sudo apt-get update && sudo apt-get install yarn

$ sudo apt-get install -y nodejs
```

To verify the installation, open a new terminal window and run:
```shell
$ node --version
v18.7.0

$ npm --version
8.15.0
```

### [Install the Ionic CLI](https://ionicframework.com/docs/intro/cli#install-the-ionic-cli) 

```shell
$ sudo npm install -g @ionic/cli
$ ionic --version
6.20.1
```

### [Creation of a React project with Ionic](https://ionicframework.com/docs/react/quickstart#creating-a-project-with-the-ionic-cli)
```shell
$ ionic start
? Use the app creation wizard? No

Pick a framework! 😁

Please select the JavaScript framework to use for your new app. To bypass this prompt next time, supply a value for the
--type option.

? Framework: React

Every great app needs a name! 😍

Please enter the full name of your app. You can change this at any time. To bypass this prompt next time, supply name,
the first argument to ionic start.

? Project name: kfinviz_app

Let's pick the perfect starter template! 💪

Starter templates are ready-to-go Ionic apps that come packed with everything you need to build your app. To bypass this
prompt next time, supply template, the second argument to ionic start.

? Starter template: tabs
? ./kfinviz_app exists. Overwrite? Yes
✔ Preparing directory ./kfinviz_app in 8.21ms
✔ Downloading and extracting tabs starter in 178.18ms
> ionic integrations enable capacitor --quiet -- kfinviz_app io.ionic.starter
> npm i --save -E @capacitor/core@latest
...
```

**waiting for completed install:**
```
...
 create mode 100644 tsconfig.json

Your Ionic app is ready! Follow these next steps:

- Go to your new project: cd ./kfinviz_app
- Run ionic serve within the app directory to see your app in the browser
- Run ionic capacitor add to add a native iOS or Android project using Capacitor
- Generate your app icon and splash screens using cordova-res --skip-config --copy
- Explore the Ionic docs for components, tutorials, and more: https://ion.link/docs
- Building an enterprise app? Ionic has Enterprise Support and Features: https://ion.link/enterprise-edition
$
``` 

run `ionic serve` and have our project running in the browser.

```shell
$ cd kfinviz_app
$ ionic serve
> react-scripts start
[react-scripts] (node:43754) [DEP_WEBPACK_DEV_SERVER_ON_AFTER_SETUP_MIDDLEWARE] DeprecationWarning: 'onAfterSetupMiddleware' option is deprecated. Please use the 'setupMiddlewares' option.
[react-scripts] (Use `node --trace-deprecation ...` to show where the warning was created)
[react-scripts] (node:43754) [DEP_WEBPACK_DEV_SERVER_ON_BEFORE_SETUP_MIDDLEWARE] DeprecationWarning: 'onBeforeSetupMiddleware' option is deprecated. Please use the 'setupMiddlewares' option.
[react-scripts] Starting the development server...
[react-scripts] 
[react-scripts] You can now view kfinviz_app in the browser.
[react-scripts]   Local:            http://localhost:8100
[react-scripts]   On Your Network:  http://192.168.196.90:8100
[react-scripts] Note that the development build is not optimized.
[react-scripts] To create a production build, use npm run build.

[INFO] Development server running!
       
       Local: http://localhost:8100
       
       Use Ctrl+C to quit this process

[react-scripts] webpack compiled successfully
[INFO] Browser window opened to http://localhost:8100!

[react-scripts] No issues found.
```


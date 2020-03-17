---
layout: post
title: "Ionic : Configuring And Command"
published: false
categories: [ionic]
---
### ionic generate
[Synopsis][1]
```
$ ionic generate [<type>] [<name>]
```

| Option        | Description   |
| ------------- |:-------------:|
| --no-module  |  Do not generate an NgModule for the component |


### Configuring `app.module.ts` for a new generate page

```
import {
...
NewGeneratePage,
} from '../pages/';


@NgModule({
  declarations: [
  ...
  NewGeneratePage,
  ],

  entryComponents: [
  ...
  NewGeneratePage,
  ],
```

### Install a package
[Synopsis][2]
```
$ npm install [<@scope>/]<name> --save
```
**-P, --save-prod**: Package will appear in your **dependencies**. This is the default unless **-D or -O** are present.

### Fix vulnerabilities
```
$ npm audit fix

# to install breaking changes
$ npm audit fix --force
```

[1]: https://ionicframework.com/docs/v3/cli/generate/ "ionic generate"
[2]: https://docs.npmjs.com/cli/install#synopsis "npm-install"

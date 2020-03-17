---
layout: post
title: "Code Editor : Visual Studio"
published: false
categories: [editor]
---

### [Installation][2]
install it by running:
```shell
sudo snap install --classic code
```

### Remote Development
* Remote Development Extension Pack
```
# Launch VS Code Quick Open (Ctrl+P), paste the following command, and press enter.
ext install ms-vscode-remote.vscode-remote-extensionpack
```

* Quick start: [Using SSH keys][3]
```shell
export USER_AT_HOST="your-user-name-on-host@hostname"
export PUBKEYPATH="$HOME/.ssh/id_rsa.pub"

ssh-copy-id -i "$PUBKEYPATH" "$USER_AT_HOST"
```

### Integrated Terminal
Use the Ctrl+` keyboard shortcut with the backtick character.

### Markdown
* [Markdown Preview Enhanced][4]


[2]: https://code.visualstudio.com/docs/setup/linux "visualstudio setup linux"
[3]: https://code.visualstudio.com/docs/remote/troubleshooting "remote troubleshooting"
[4]: https://marketplace.visualstudio.com/items?itemName=shd101wyy.markdown-preview-enhanced "markdown-preview-enhanced"

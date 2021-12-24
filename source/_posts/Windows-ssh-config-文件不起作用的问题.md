title: Windows ssh config 文件不起作用的问题
tags:
  - windows
categories:
  - 小技巧
date: 2021-12-24 15:59:46
---
If you use "Git for Windows"

```bash
>cd c:\Program Files\Git\etc\ssh\
```

add to ssh_config following:

```bash
AddKeysToAgent yes
IdentityFile ~/.ssh/id_rsa
IdentityFile ~/.ssh/id_rsa_other
```

ps. you need ssh version >= 7.2 (date of release 2016-02-28)
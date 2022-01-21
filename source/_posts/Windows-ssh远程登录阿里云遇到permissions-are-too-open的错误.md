title: Windows ssh远程登录阿里云遇到permissions are too open的错误
tags:
  - windows
categories:
  - 小技巧
date: 2022-01-21 09:46:50
---
```
set key="E:\tmp\yourkey.pem"
cmd /c icacls %key% /c /t /inheritance:d
cmd /c icacls %key% /c /t /grant %username%:F
cmd /c icacls %key% /c /t /remove Administrator "Authenticated Users" BUILTINAdministrators BUILTIN Everyone System Users
cmd /c icacls %key%
```

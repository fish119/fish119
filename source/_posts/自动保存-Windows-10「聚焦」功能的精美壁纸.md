title: 自动保存 Windows 10「聚焦」功能的精美壁纸
tags:
  - windows
categories:
  - 小技巧
date: 2022-01-06 20:43:11
---
作为 Windows 10 内置的「锁屏壁纸自动换」功能，「Windows 聚焦」不仅具有高质量壁纸，还有着随使用过程而不断提高的壁纸筛选能力。如何将「Windows 聚焦」图片变成Windows10的壁纸，并自动切换呢？思路如下：
- 找到「Windows 聚焦」图片保存目录
- 编写脚本复制其中图片到固定文件夹
- 利用“任务计划程序”每日自动执行脚本
- 将windows壁纸设为幻灯片放映，目录设为保存图片目录
<!--more-->

# 「Windows 聚焦」图片保存目录

```bash
%localappdata%\Packages\Microsoft.Windows.ContentDeliveryManager_cw5n1h2txyewy\LocalState\Assets
```
打开后可发现文件无后缀，改为.jpg文件后即可作为图片打开

# 编写脚本
编写脚本将其复制到E:\wallpaper目录下，并加入.jpg后缀名。代码如下：
```bash
add-type -AssemblyName System.Drawing
New-Item "E:\wallpaper" -ItemType directory -Force;
New-Item "E:\wallpaper\CopyAssets" -ItemType directory -Force;

foreach($file in (Get-Item "$($env:LOCALAPPDATA)\Packages\Microsoft.Windows.ContentDeliveryManager_cw5n1h2txyewy\LocalState\Assets\*"))
{
    if ((Get-Item $file).length -lt 100kb) { continue }
    Copy-Item $file.FullName "E:\wallpaper\CopyAssets\$($file.Name).jpg";
}

foreach($newfile in (Get-Item "E:\wallpaper\CopyAssets\*"))
{
    $image = New-Object -comObject WIA.ImageFile;
    $image.LoadFile($newfile.FullName);
    if($image.Width.ToString() -eq "1920"){ Move-Item $newfile.FullName "E:\wallpaper" -Force; }
}
Remove-Item "E:\wallpaper\CopyAssets\*";
Remove-Item "E:\wallpaper\CopyAssets";
```
重命名为save-spotlight.ps1

# 设置任务计划程序
- 打开“任务计划程序”，新建任务名为“updatewallpaper”
- “触发器”按喜好设置，如我设置的是“每天15:36执行”
- “操作”
    - 选择“启动程序”
    - “程序和脚本”选择powershell目录，如"C:\Program Files\PowerShell\7\pwsh.exe"
    - “添加参数”输入上文所编写脚本文件的路径，如：E:\tmp\save-spotlight.ps1
- 保存后，右键/运行 进行测试，可以看到图片被复制到E:\wallpaper目录下

最后将windows壁纸设为幻灯片放映，目录设为保存图片目录即可。

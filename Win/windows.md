# Windows 配置

## oh-my-posh

[oh-my-posh](https://ohmyposh.dev/docs/)是 Windows PowerShell 的一款插件，提供了终端美化、提示等功能。

安装 oh-my-posh

```sh
winget install JanDeDobbeleer.OhMyPosh -s winget
```

查看配置文件路径：

```sh
$PROFILE
> C:\Users\13199\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1
```

在上述路径创建对应的配置文件，输入自定义配置：

```txt
C:\\Users\\13199\\AppData\\Local\\Programs\\oh-my-posh\\bin\\oh-my-posh.exe init pwsh --config $env:POSH_THEMES_PATH\M365Princess.omp.json | Invoke-Expression
```

其中，主题文件位置在`C:\Users\13199\AppData\Local\Programs\oh-my-posh\themes`中，可以在官方文档中预览。

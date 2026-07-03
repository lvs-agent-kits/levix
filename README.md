# 构建流程
## nodejs 
  + https://nodejs.org/zh-cn version; 24.18.0  
  + 环境变量：`Path` `{install-path}\node-v24.18.0-win-x64`
## visual studio build tools 
  + https://visualstudio.microsoft.com/zh-hans/downloads/; 2026
  + 组件选择：https://github.com/microsoft/vscode/wiki/How-to-Contribute
    + MSVC v143 - VS 2022 C++ x64/x86 Spectre-mitigated libs (Latest)
    + C++ ATL for latest build tools with Spectre Mitigations
    + C++ MFC for latest build tools with Spectre Mitigations
    + Windows on ARM only: Windows 10 SDK (10.0.20348.0)
  + 环境变量
    + VCINSTALLDIR
    + VSCMD_VER
    + WindowsSDKVersion
    + 在 visual studio cmd 窗口中，运行以下代码以查看
```bash
echo %VCINSTALLDIR%
echo %VSCMD_VER%
echo %WindowsSDKVersion%
```
## python 
## git
## 安装好后
```bash
npm install 
```
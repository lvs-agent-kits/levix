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

## 开发环境启动流程

### 1. 启动 Watch 模式 (自动编译)

在第一个终端窗口运行:

```bash
npm run watch
```

等待初次完整编译完成 (约 1-2 分钟),看到 `Finished transpilation with 0 errors` 后表示编译完成。

### 2. 启动 VS Code 桌面版

在第二个终端窗口运行:

```bash
scripts\code.bat
```

**重要**: 必须使用此脚本启动,不要直接用 `node_modules\electron\dist\electron.exe .`,否则可能缺少必要的编译文件导致黑框/白框问题。

### 3. 验证窗口

- 启动后应该能看到 `Code - OSS` 窗口
- 如果窗口在第二显示器或看不到,运行以下 PowerShell 命令移动窗口:

```powershell
Add-Type -Name User32 -Namespace '' -Member '[DllImport("user32.dll")] public static extern bool MoveWindow(IntPtr hWnd, int X, int Y, int nWidth, int nHeight, bool bRepaint); [DllImport("user32.dll")] public static extern bool ShowWindow(IntPtr hWnd, int nCmdShow);'; $w = Get-Process | Where-Object {$_.MainWindowTitle -eq 'Code - OSS'}; if ($w) { [User32]::ShowWindow($w.MainWindowHandle, 3); [User32]::MoveWindow($w.MainWindowHandle, 100, 100, 1200, 800, $true) }
```

### 开发工作流

1. 修改 `src` 目录下的代码
2. Watch 模式会自动编译 (通常几秒)
3. 在 VS Code 开发实例中按 `Ctrl+Shift+P` → `Reload Window` 查看效果

### 常见问题

**黑框/白框问题**:
- 确保先运行 `npm run watch` 等待编译完成
- 使用 `scripts\code.bat` 启动,不要直接用 electron.exe
- 如果还缺失文件,运行 `node build/lib/preLaunch.ts` 重新准备 Electron

**Electron 下载 404 错误**:
- 不要使用 `npm run electron .`,该命令有 bug
- 使用 `scripts\code.bat` 启动即可

**类型检查错误**:
- `copilotSystemNotification.ts:69` 的类型错误不影响运行,可忽略

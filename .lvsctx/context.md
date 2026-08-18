# 现状
## 启动开发窗口，获得即时反馈
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

**重要**: 必须使用此脚本启动

# TODO
1. 归档所需要的所有库文件，理清相关的更新关系。（是否已经 LTS，诸如此类）
2. 理清楚我可以通过什么方式增加插件。
3. 删去 Vscode 原生的 Agent 相关文件。

# 原则
1. 遇到不需要的功能，应删尽删。

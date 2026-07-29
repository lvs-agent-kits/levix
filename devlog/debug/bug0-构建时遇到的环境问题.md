# Bug 0: npm install 构建时遇到的环境问题

## 时间
2026-07-29

## 背景
在 Windows 上运行 `npm install` 安装 VS Code 源码的依赖时，`postinstall.ts` 脚本报错退出。

## 报错现象

```
spawn C:\WINDOWS\system32\cmd.exe ENOENT
npm error code 1
```

`ENOENT` = Error NO ENTry = 找不到文件。但 `cmd.exe` 明明存在于 `C:\WINDOWS\system32\cmd.exe`。

## 根因分析

经过多轮排查，定位到 **3 个独立的 bug**：

---

### Bug 1: Node.js v24 在 Windows 上 `spawn(cmd.exe, shell: true)` 失败

**原因**: `postinstall.ts` 使用 `child_process.spawn(command, args, { shell: true })` 来跑 npm install。在 Node.js v24 + Windows 组合下，`spawn` + `shell: true` 会无法找到 `cmd.exe`。

这是一个已知的 Node.js bug。即使 `cmd.exe` 文件确实存在，`spawn` 仍然报 `ENOENT`。

**修复**: 在 Windows 上改用 `child_process.execSync` 直接执行命令，绕过 `spawn`。

```ts
// 修复前（Linux/macOS 仍用此方式）
child_process.spawn('npm.cmd', ['install'], { shell: true, ... })

// 修复后（Windows 专用）
child_process.execSync(`npm.cmd install`, { cwd: targetDir, env, stdio: 'inherit' })
```

**文件**: `build/npm/postinstall.ts` → `npmInstallAsync()` 函数

---

### Bug 2: postinstall 不检查目录是否存在就直接执行

**原因**: `dirs.ts` 里硬编码了所有要跑 `npm install` 的目录列表，包括 `extensions/copilot` 和 `remote/web`。但我之前删掉了这两个文件夹，脚本没有检查目录是否存在就尝试执行，导致失败。

**修复**: 在 `npmInstallAsync()` 开头加 `fs.existsSync()` 检查，跳过不存在的目录。

```ts
if (!fs.existsSync(targetDir)) {
    log(dir, 'Directory does not exist, skipping.');
    return;
}
```

---

### Bug 3: 创建 .claude 符号链接时源文件不存在

**原因**: `postinstall.ts` 末尾会创建 `.claude/CLAUDE.md` → `.github/copilot-instructions.md` 和 `.claude/skills` → `.agents/skills` 的符号链接（用于 Claude Code 测试环境）。但我删掉了 `.github` 和 `.agents` 目录，`ensureAgentHarnessLink()` 调用 `fs.statSync()` 时直接抛异常。

**修复**: 创建链接前先检查源文件是否存在。

```ts
const copilotInstructionsAbs = path.join(root, '.github', 'copilot-instructions.md');
if (fs.existsSync(copilotInstructionsAbs)) {
    // 创建链接
}
```

---

## 修改总结

修改了 `build/npm/postinstall.ts`：

| 修改位置 | 做了什么 |
|---------|---------|
| `npmInstallAsync()` Windows 路径 | 用 `execSync` 替代 `spawn` |
| `npmInstallAsync()` 开头 | 加目录存在性检查 |
| `main()` 末尾 .claude 链接部分 | 加源文件存在性检查 |
| 并发控制 | Windows 上并发数降为 1 |

## 经验教训

1. **Node.js v24 在 Windows 上有 spawn bug** — 如果遇到 `spawn ENOENT` 但文件确实存在，换 `execSync` 或 `execFile`
2. **构建脚本不该假设目录一定存在** — 尤其是用户可以随意删文件夹的项目
3. **ENOENT 不一定真的是文件不存在** — 可能是 spawn 的底层实现问题

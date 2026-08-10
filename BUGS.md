# 待修复 BUGS

## 类型错误：`copilotSystemNotification.ts` 第 69 行

- **文件**: `src/vs/platform/agentHost/node/copilot/copilotSystemNotification.ts`
- **错误**: `error TS2345: Argument of type 'any' is not assignable to parameter of type 'never'.`
- **位置**: 第 69 行 `softAssertNever(kind)`
- **原因**: `switch (kind.type)` 的 `default` 分支中，TypeScript 未能将 `kind` 收窄为 `never` 类型，仍保留为 `any`
- **发现时间**: 2026-08-09，启动开发环境 watch 模式时
- **影响**: 仅编译期类型报错，不影响运行时功能

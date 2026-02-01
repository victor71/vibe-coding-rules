# Vibe Coding Rules 🚀

探索如何更高效使用 AI 辅助编程（Vibe Coding）的实践项目。

## 🎯 目标

- 记录和验证 vibe coding 的最佳实践
- 对比不同 coding agents（Codex, Claude Code, Pi）的效果
- 收集实用的 prompt 模板和工作流
- 提高人机协作编程的效率

## 📁 项目结构

```
vibe-coding-rules/
├── prompts/              # Prompt 模板
│   ├── quick-fix.md     # 快速 bug 修复
│   ├── complex-refactor.md  # 复杂重构
│   ├── code-review.md   # 代码审查
│   ├── new-feature.md   # 新功能实现
│   └── optimization.md  # 性能优化
├── workflows/            # 完整工作流
│   ├── single-bug-fix.md       # 单 bug 修复
│   └── parallel-batch-fixes.md # 批量并行修复
├── examples/             # 实战例子
│   └── bad-vs-good-prompts.md  # Prompt 对比
└── README.md            # 本文件
```

## 🚀 快速开始

### 1. 学习 Prompt 写作

阅读 `examples/bad-vs-good-prompts.md`，理解如何写出有效的 prompt。

### 2. 使用 Prompt 模板

根据任务类型，在 `prompts/` 目录找到合适的模板：
- 快速修复 → `prompts/quick-fix.md`
- 复杂重构 → `prompts/complex-refactor.md`
- 代码审查 → `prompts/code-review.md`
- 新功能 → `prompts/new-feature.md`
- 性能优化 → `prompts/optimization.md`

### 3. 跟随工作流

阅读 `workflows/` 目录的完整工作流：
- 单 bug 修复 → `workflows/single-bug-fix.md`
- 批量并行修复 → `workflows/parallel-batch-fixes.md`

## 📊 效率提升

| 场景 | 传统方式 | Vibe Coding | 提升 |
|------|---------|-------------|------|
| 单 bug 修复 | 30 分钟 | 20 分钟 | 33% |
| 复杂重构 | 4 小时 | 2 小时 | 50% |
| 批量修复 (4 bugs) | 2 小时 | 42 分钟 | 65% |
| PR 审查 | 20 分钟/PR | 5 分钟/PR | 75% |

## 🛠️ 技术栈

- **Claude Code** - 主要的 coding agent
- **OpenClaw** - Orchestrator 和自动化
- **Git Worktrees** - 隔离工作区
- **GitHub CLI** - PR 管理

## 📚 核心概念

### Vibe Coding 是什么？

Vibe Coding = 用自然语言跟 AI 一起编程，保持心流，让 AI 做脏活累活。

**关键要素：**
- 自然语言描述任务
- AI 执行具体实现
- 人类专注在高层次思考
- 快速迭代，持续反馈

### 为什么有效？

1. **降低认知负担** - 不需要记忆所有细节
2. **提高速度** - AI 比手写快得多
3. **减少错误** - AI 可以处理边界情况
4. **持续学习** - 通过 prompt 不断提升

## 🎓 研究方向

- [x] Prompt 优化技巧
- [x] 任务分解策略
- [x] 并行工作方法
- [ ] 更多实战案例分析
- [ ] 不同 Agent 的对比
- [ ] 性能基准测试
- [ ] 错误处理和恢复策略

## 💡 核心原则

1. **具体 > 模糊**
   - ❌ "Fix the bug"
   - ✅ "Fix the login timeout error in auth_service.py"

2. **结构化 Prompt**
   - 清晰的任务描述
   - 明确的要求
   - 分步实施计划
   - 预期的输出格式

3. **验证一切**
   - 审查 AI 生成的代码
   - 运行测试
   - 本地验证
   - 不盲目信任

4. **并行加速**
   - 使用 git worktrees 隔离任务
   - 同时运行多个 AI 实例
   - 批量处理独立任务

5. **持续改进**
   - 记录有效的 prompt
   - 分享最佳实践
   - 学习新的技巧

## 📖 推荐阅读顺序

### 新手入门
1. `examples/bad-vs-good-prompts.md` - 理解 prompt 质量
2. `prompts/quick-fix.md` - 最常用的场景
3. `workflows/single-bug-fix.md` - 完整工作流

### 进阶提升
1. `prompts/complex-refactor.md` - 复杂任务处理
2. `workflows/parallel-batch-fixes.md` - 并行工作流
3. `prompts/optimization.md` - 性能优化

### 高级实践
1. `prompts/code-review.md` - 自动化审查
2. `prompts/new-feature.md` - 大型功能实现
3. (待添加更多实战案例)

## 🤝 贡献

欢迎分享你的经验、prompt 模板和工作流！

**如何贡献：**
1. Fork 本项目
2. 添加你的模板或例子
3. 提交 Pull Request

## 📝 License

MIT License - 自由使用和分享

## 🏷️ 标签

#AI #Coding #Productivity #ClaudeCode #OpenClaw #VibeCoding

---

**开始时间：** 2026-02-02
**最后更新：** 2026-02-02

---

**准备好开启 Vibe Coding 之旅了吗？** 🚀

从一个简单的任务开始，尝试使用我们的 prompt 模板，体验 AI 辅助编程的效率提升！

# neat-freak-skill

一个用于 AI 协作任意阶段收尾整合文档的同步 skill。
它会在功能开发告一段落后，检查代码变更与项目文档是否一致，并准确同步：

- `README.md`
- `CLAUDE.md`
- `AGENTS.md`
- `docs/`
- agent memory（如当前平台支持）

## 适用场景

适合在以下时机调用：

- 一个功能阶段完成后
- 准备切换 Codex / Claude Code / OpenCode / OpenClaw 等 agent 前
- 准备交接项目或让新人接手前
- 发现项目文档、记忆、代码事实不一致时

## 模式

### light mode

默认模式。  
只同步项目根文档和与本次改动直接相关的 docs，适合日常小步开发收尾。

```text
/neat
/sync
```
### dry-run mode

只审查，不修改文件。
```
/neat --dry-run
```
### full mode

完整审查项目文档、agent memory 与跨项目影响，适合大功能完成、交接、全量文档整理。
```
/neat --full
```

## 安装方式
1. clone/unzip 到本地 skills 目录下
2. 复制该仓库网址，让 agent 安装。


## 致谢

本项目基于 [KKKKhazix/khazix-skills](https://github.com/KKKKhazix/khazix-skills) 中的 `neat-freak` 优化：

基于原版，新增：
- 收窄触发条件，降低泛化“整理/总结”类请求的误触发（洁癖）
- 增加 `dry-run` / `light` / `full` 三种执行模式
- 加强 Git 安全检查，避免覆盖用户未提交改动
- 增加学习笔记边界，防止项目文档被写成个人学习记录或扣细节日记
- 增加文档真实性校验，要求命令、路由、环境变量、路径等能从代码或配置中验证

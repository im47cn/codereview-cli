# CodeRocket CLI 更新日志

## [v1.0.6] - 2025-01-31

### 🔧 Bug修复和改进

#### 🔄 向后兼容性增强
- **Git Hooks向后兼容**：添加对旧配置路径的支持
  - `post-commit` 和 `pre-commit` hooks 现在支持从 `~/.codereview-cli/env` 加载配置
  - 自动检测并加载旧版本的 `ai-service-manager.sh` 脚本
  - 确保现有用户无需立即重新安装即可正常使用

#### 🛠️ 代码质量改进
- **修复重复函数定义**：移除 `install.sh` 中重复的 `setup_user_commands()` 函数定义
- **路径一致性修复**：
  - 修复 README.md 中克隆后的目录名称（`cd coderocket-cli`）
  - 修复 CONTRIBUTING.md 中的仓库URL一致性问题
  - 更新性能优化指南中的所有路径引用（`codereview` → `coderocket`）

#### 📚 文档完善
- **统一品牌命名**：确保所有文档中的路径、命令和引用都使用新的 CodeRocket 品牌
- **修复安装说明**：确保用户能够正确克隆和安装项目
- **改进用户体验**：提供清晰的迁移路径和向后兼容性说明

## [v1.0.5] - 2025-01-31

### 🚀 重大新功能：智能AI服务故障转移

#### ✨ 核心特性
- **智能错误识别**：自动识别429限流、认证错误、网络超时等不同类型的AI服务错误
- **自动服务切换**：当主要AI服务失败时，自动切换到其他可用的AI服务
- **透明用户体验**：提供清晰的切换提示和错误说明，用户无需手动干预
- **灵活配置选项**：支持启用/禁用自动切换，自定义服务优先级和重试策略

#### 🔧 技术实现
- **新增错误分类器** (`lib/ai-error-classifier.sh`)：
  - 支持Gemini、OpenCode、ClaudeCode等多种AI服务的错误模式识别
  - 提供结构化的错误分类和处理策略
  - 支持用户友好的错误描述生成

- **增强AI服务管理器** (`lib/ai-service-manager.sh`)：
  - 新增 `intelligent_ai_call()` 函数：智能AI调用，支持多服务自动切换
  - 新增 `intelligent_ai_review()` 函数：智能代码审查，支持故障转移
  - 新增 `call_ai_with_error_handling()` 函数：带详细错误处理的AI调用
  - 新增服务可用性检测和优先级管理功能

- **更新Git Hooks**：
  - `post-commit` 和 `pre-commit` hooks 使用新的智能调用机制
  - 保持向后兼容性，现有配置无需修改

#### ⚙️ 配置选项
- `AI_AUTO_SWITCH=true`: 启用/禁用自动切换 (默认启用)
- `AI_MAX_RETRIES=3`: 最大重试次数
- `AI_RETRY_DELAY=1`: 重试延迟时间(秒)
- `AI_SERVICE_PRIORITY="gemini opencode claudecode"`: 服务优先级

#### 📚 文档和测试
- 新增 `docs/AI_FAILOVER_CONFIG.md`：详细的故障转移配置指南
- 新增 `test-ai-failover.sh`：全面的故障转移功能测试脚本
- 更新故障排除指南，添加智能故障转移相关说明

#### 🎯 错误处理策略
| 错误类型 | 处理策略 | 用户体验 |
|---------|---------|---------|
| 429限流 | 立即切换 | 🚫 服务达到使用限制，切换到备用服务 |
| 认证错误 | 跳过服务 | 🔐 认证失败，请检查API密钥配置 |
| 网络错误 | 重试后切换 | 🌐 网络连接问题，重试后切换服务 |
| CLI未安装 | 跳过服务 | 📦 CLI工具未安装，使用其他可用服务 |

## [v1.0.4] - 2025-01-31

### 🚀 重大改进：自动配置用户命令和 PATH

#### ✨ 新功能
- **自动创建用户命令**：安装时自动在 `~/.local/bin/` 创建用户级别的命令
- **智能 PATH 配置**：自动检测用户 shell 并配置 PATH 环境变量
- **多 Shell 支持**：支持 bash、zsh、fish 等主流 shell
- **备选方案保障**：即使全局命令失败，用户命令也能正常工作

#### 🛠️ 技术改进
- 新增 `setup_user_commands()` 函数：创建用户级别的命令别名
- 新增 `configure_user_path()` 函数：智能配置 PATH 环境变量
- 增强安装流程：无论选择哪种安装模式，都会设置用户命令作为备选
- 改进安装完成提示：根据实际可用命令显示使用说明

#### 💡 用户体验提升
- **零配置使用**：安装完成后用户可以直接使用 `cr`、`coderocket`、`codereview-cli` 命令
- **自动备份**：修改配置文件前自动备份原文件
- **智能检测**：自动检测当前 PATH 状态并给出相应提示
- **跨平台兼容**：支持 macOS 和 Linux 的不同 shell 配置文件

#### 🔧 解决的问题
- 解决了用户安装后需要手动配置 PATH 的问题
- 解决了全局命令失败时用户无法使用的问题
- 解决了不同 shell 环境下配置不一致的问题

---

## [v1.0.3] - 2025-01-31

### 🔧 重要修复：全局命令语法错误自动检测和修复

#### 🚨 问题修复
- **修复全局命令语法错误**：解决了 `/usr/local/bin/cr` 等命令中的转义字符问题
- **智能错误检测**：安装脚本现在能自动检测有语法错误的全局命令
- **自动修复机制**：重新安装时会自动覆盖有问题的全局命令文件
- **语法验证**：创建命令后会自动验证语法正确性

#### 🛠️ 技术改进
- 增强了 `install.sh` 中的 `create_command` 函数
- 添加了 `check_and_fix_commands` 函数来检测问题命令
- 修复了命令模板中的转义字符错误（`OLD_VERSION=\$(cat` 而不是 `OLD_VERSION=\\\$(cat`）
- 提供了专门的修复脚本 `fix-global-commands-only.sh`

#### 📦 新增工具
- **fix-global-commands-only.sh**：专门修复全局命令的独立脚本
- **quick-fix.md**：详细的问题解决方案文档
- 多种修复方案：自动修复、手动修复、临时解决方案

#### 💡 用户指南
- 运行 `bash fix-global-commands-only.sh` 可快速修复全局命令
- 重新运行安装脚本会自动检测并修复问题命令
- 本地命令 `bash bin/coderocket` 始终可用作备选方案
- **新增**：`create-user-commands.sh` 在用户目录创建无需管理员权限的命令

#### ✅ 验证修复结果
- 成功在用户目录 `~/.local/bin/` 创建了可执行的命令
- 所有三个命令别名 (`cr`, `codereview-cli`, `coderocket`) 都正常工作
- 显示正确的版本信息 (1.0.3) 和精美的渐变 banner
- 无需管理员权限，完全在用户空间运行

---

## [v1.0.2] - 2025-01-31

### 🎨 界面优化：Gemini CLI 风格的精美 Banner

#### ✨ 新功能
- **渐变 Banner 设计**：参考 Gemini CLI 的精美设计风格，使用 256 色渐变效果
- **响应式界面**：根据终端宽度自动选择合适的 ASCII art 显示
- **专业级视觉效果**：蓝绿渐变色彩，媲美 Gemini CLI 的高质量界面
- **多场景 Banner**：支持启动、安装、帮助等不同场景的 banner 显示

#### 🛠️ 技术改进
- 优化 banner 显示逻辑，支持多种终端环境
- 改进脚本加载机制，确保 banner 在所有命令中正确显示
- 统一所有命令的视觉风格和用户体验
- 增强终端兼容性，支持不同宽度的终端窗口

#### 💫 用户体验
- 启动时显示精美的渐变 banner，提升专业感
- 帮助和版本信息界面更加美观易读
- 安装脚本界面全面升级，视觉效果更佳
- 所有交互界面保持一致的设计风格

---

## [v1.0.1] - 2025-01-31

### 🎉 重大更新：项目重命名为 CodeRocket CLI

#### ✨ 新功能
- **项目重命名**：CodeRocket CLI 正式更名为 **CodeRocket CLI**
- **多命令兼容性**：支持 `coderocket`、`codereview-cli`、`cr` 三个命令别名
- **精美 Banner**：添加了好看的 ASCII art banner，提升用户体验
- **智能命令检测**：根据当前使用的命令名称显示相应的帮助信息

#### 🔄 向后兼容
- 完全兼容原有的 `codereview-cli` 命令
- 支持简短别名 `cr` 命令
- 所有功能保持不变，用户无需修改使用习惯

#### 📝 文档更新
- 更新 README.md 文档，反映新的项目名称
- 更新安装脚本中的所有引用
- 更新 Git hooks 中的配置路径
- 更新所有帮助信息和错误提示

#### 🛠️ 技术改进
- 重构全局命令创建逻辑
- 优化 banner 显示系统
- 改进命令行界面的用户体验
- 统一配置文件路径（从 `~/.codereview-cli` 到 `~/.coderocket`）

#### 📦 安装更新
- 安装目录更改为 `~/.coderocket`
- 仓库地址更新为 `https://github.com/im47cn/coderocket-cli`
- 保持一键安装脚本的便利性

### 🔧 使用说明

#### 新用户
```bash
# 一键安装
curl -fsSL https://raw.githubusercontent.com/im47cn/coderocket-cli/main/install.sh | bash

# 使用任意命令
coderocket help
codereview-cli help  # 完全兼容
cr help              # 简短别名
```

#### 老用户
- 无需任何操作，原有命令继续有效
- 建议重新运行安装脚本获取最新功能
- 可以开始使用新的 `coderocket` 命令

---

## [v1.0.0] - 2025-01-30

### 🎯 初始版本
- AI 驱动的代码审查功能
- 支持 Git hooks 自动触发
- 集成 Google Gemini AI
- 支持 GitLab MR 自动创建
- 全局和项目级配置支持

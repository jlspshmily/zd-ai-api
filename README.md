# 政德后端项目（zd-ai-api）

基于 Laravel 的一体化应用，承担两项职责：

1. **API 服务端**：为后台管理系统提供 RESTful API，统一管理数据与业务逻辑。
2. **门户网站**：面向公众的内容展示站点，采用 Blade 服务端渲染，兼顾 SEO 与访问速度。

## 当前状态

项目处于**初始化骨架阶段**（Laravel 13 全新安装），目前实际包含：

- 门户首页路由 `/`（`routes/web.php` → `resources/views/welcome.blade.php`）
- 健康检查路由 `/up`
- 异常处理已针对 `api/*` 请求返回 JSON（`bootstrap/app.php`），为后续 API 开发做好了准备
- `User` 模型及对应的迁移、工厂、填充器

尚未引入（后续按需添加）：

- API 路由（暂无 `routes/api.php`）与业务控制器
- 认证方案（如 Laravel Sanctum）
- 前端交互库（如 Alpine.js）

> 本文档以仓库实际代码为准，随功能迭代同步更新。

## 技术栈

以后端实际依赖（`composer.json` / `package.json`）为准：

| 类别 | 技术 | 版本 |
| --- | --- | --- |
| 后端框架 | Laravel | ^13.17 |
| PHP | PHP | ^8.3（Sail 运行时为 8.5） |
| 模板引擎 | Blade | 随 Laravel |
| CSS 方案 | Tailwind CSS（`@tailwindcss/vite` 插件） | ^4.0 |
| 构建工具 | Vite（`laravel-vite-plugin`） | ^8.0 |
| 测试框架 | Pest（`pest-plugin-laravel`） | ^5.1 |
| 代码规范 | Laravel Pint | ^1.27 |
| 本地环境 | Laravel Sail（MySQL 8.4 + Redis） | ^1.66 |

## 环境要求

- PHP >= 8.3（需要相应扩展：OpenSSL、PDO、Mbstring、Tokenizer、XML、Ctype、json、fileinfo 等）
- Composer
- Node.js 与 npm
- 默认使用 SQLite（开箱即用）；如需 MySQL / Redis，可使用 Sail（需 Docker）

## 快速开始

### 方式一：一键安装

```bash
composer run setup
```

该脚本依次执行：`composer install` → 复制 `.env.example` 为 `.env` → 生成应用密钥 → 执行迁移 → `npm install` → 前端构建。

### 方式二：手动安装

```bash
composer install
cp .env.example .env
php artisan key:generate

# 数据库（默认 SQLite）
touch database/database.sqlite
php artisan migrate

# 前端依赖与构建
npm install
npm run build

# 启动开发服务器
composer run dev
```

### 方式三：使用 Sail（Docker）

```bash
composer install
cp .env.example .env
./vendor/bin/sail up -d
./vendor/bin/sail artisan migrate
```

Sail 会启动应用容器（PHP 8.5）、MySQL 8.4 与 Redis，并在 MySQL 中自动创建 `testing` 测试库。

## 常用命令

| 命令 | 说明 |
| --- | --- |
| `composer run dev` | 启动本地开发（`php artisan dev`，含 HTTP 服务与 Vite 热更新） |
| `composer run test` | 运行测试（先清理配置缓存再执行 `php artisan test`） |
| `php artisan test` | 直接运行 Pest 测试 |
| `php artisan migrate --seed` | 执行迁移并填充数据库（创建测试用户 `test@example.com`） |
| `npm run dev` | 仅启动 Vite 开发服务器 |
| `npm run build` | 构建生产前端资源 |
| `vendor/bin/pint` | 按 Laravel 风格格式化 PHP 代码 |
| `php artisan route:list` | 查看路由列表 |

## 关键目录结构

```
zd-ai-api/
├── app/
│   ├── Http/Controllers/     # 控制器（当前仅基类）
│   ├── Models/               # Eloquent 模型（当前仅 User）
│   └── Providers/            # 服务提供者
├── bootstrap/                # 应用引导与中间件/异常配置（api/* JSON 响应在此定义）
├── config/                   # 框架与应用配置
├── database/
│   ├── migrations/           # 数据库迁移（users、cache、jobs 等）
│   ├── factories/            # 模型工厂
│   └── seeders/              # 数据填充器
├── resources/
│   ├── css/app.css           # Tailwind 入口
│   ├── js/app.js             # 前端 JS 入口
│   └── views/                # Blade 模板
├── routes/
│   ├── web.php               # 门户站点路由
│   └── console.php           # 计划任务与 Artisan 命令
├── tests/                    # Pest 测试（Unit / Feature）
├── compose.yaml              # Sail 的 Docker Compose 定义
└── vite.config.js            # Vite + Tailwind 构建配置
```

## 配置说明

复制 `.env.example` 生成 `.env` 后按需调整，关键默认值：

- **数据库**：`DB_CONNECTION=sqlite`（MySQL 连接项已注释备用）
- **会话 / 缓存 / 队列**：默认均使用 `database` 驱动，无需额外服务即可运行
- **应用地址**：`APP_URL=http://localhost:8000`

生产环境部署时请务必确认：`APP_ENV=production`、`APP_DEBUG=false`、使用强随机 `APP_KEY`，敏感信息一律通过环境变量配置。

## 测试

项目使用 Pest 编写测试，位于 `tests/Unit` 与 `tests/Feature`：

```bash
composer run test          # 完整测试
php artisan test --compact # 简洁输出
```

## 许可证

MIT License。

# 三角洲行动交易行价格查询平台（PRD）

## 一、项目简介

### 项目名称

Delta Force Market Tracker

（三角洲行动交易行价格查询平台）

### 项目目标

开发一个响应式 Web 网站，用于实时查询《三角洲行动》交易行物品价格、历史价格走势以及市场变化情况。

核心价值：

* 实时价格查询
* 历史价格分析
* 涨跌趋势分析
* 收藏关注物品
* 后续支持价格提醒

---

# 二、技术架构

## 前端

### 框架

Next.js 15（App Router）

### 语言

TypeScript

### UI组件

* TailwindCSS
* shadcn/ui

### 图表

Apache ECharts

### 状态管理

TanStack Query

---

## 后端

### 框架

NestJS

### ORM

Prisma

### 数据库

MySQL 8

### 缓存

Redis

### 定时任务

NestJS Schedule

---

## 部署

Docker

Docker Compose

---

# 三、系统架构

数据流：

第三方价格API
↓
Price Provider
↓
定时同步任务
↓
MySQL
↓
Redis缓存
↓
NestJS API
↓
Next.js前端

要求：

* Provider模式
* 支持未来切换不同数据源
* 禁止业务代码直接调用第三方接口

---

# 四、功能需求

## 首页

URL：

/

展示内容：

### 搜索框

支持：

* 中文搜索
* 模糊搜索

### 市场概览

展示：

* 物品总数
* 数据更新时间
* 今日监控数量

### 热门物品

展示：

* 名称
* 当前价格

### 涨幅榜 TOP10

字段：

* 排名
* 名称
* 涨幅

### 跌幅榜 TOP10

字段：

* 排名
* 名称
* 跌幅

---

## 搜索页

URL：

/search

功能：

* 模糊搜索
* 分类筛选
* 分页

分类：

* 武器
* 配件
* 子弹
* 医疗
* 收藏品
* 钥匙卡

接口：

GET /api/items

参数：

keyword
page
pageSize
category

---

## 物品详情页

URL：

/item/[id]

展示：

### 基础信息

* 名称
* 图标
* 分类

### 当前价格

示例：

1,240,000 哈夫币

### 涨跌幅

展示：

* 24小时
* 7天
* 30天

### 价格走势图

时间范围：

* 24小时
* 7天
* 30天

图表：

ECharts折线图

### 历史统计

展示：

* 历史最高价
* 历史最低价
* 平均价格

### 收藏按钮

支持：

* 收藏
* 取消收藏

---

## 收藏页

URL：

/favorites

展示：

* 收藏物品
* 当前价格
* 今日涨跌

支持：

* 取消收藏

---

# 五、用户系统

采用邮箱注册登录。

功能：

* 注册
* 登录
* 登出

认证方式：

JWT + HttpOnly Cookie

数据库字段：

id
email
passwordHash
createdAt

---

# 六、数据库设计

## users

id BIGINT PRIMARY KEY

email VARCHAR(100)

password_hash VARCHAR(255)

created_at DATETIME

updated_at DATETIME

---

## items

id BIGINT PRIMARY KEY

item_id VARCHAR(50)

name VARCHAR(100)

icon VARCHAR(500)

category VARCHAR(50)

created_at DATETIME

updated_at DATETIME

---

## item_prices

id BIGINT PRIMARY KEY

item_id VARCHAR(50)

price BIGINT

created_at DATETIME

---

## favorites

id BIGINT PRIMARY KEY

user_id BIGINT

item_id VARCHAR(50)

created_at DATETIME

---

# 七、数据同步模块

## Provider接口

interface PriceProvider {
getItems(): Promise<Item[]>;
getLatestPrices(): Promise<Price[]>;
getItemHistory(itemId: string): Promise<History[]>;
}

---

## 定时任务

执行频率：

每5分钟

流程：

同步价格
↓
更新Redis
↓
写入价格历史表
↓
计算涨跌排行

---

## 缓存设计

item:list

item:detail:{id}

item:history:{id}

rank:gainers

rank:losers

---

# 八、API设计

## 获取物品列表

GET /api/items

---

## 获取物品详情

GET /api/items/:id

---

## 获取历史价格

GET /api/items/:id/history

---

## 注册

POST /api/auth/register

---

## 登录

POST /api/auth/login

---

## 获取收藏

GET /api/favorites

---

## 添加收藏

POST /api/favorites

---

## 删除收藏

DELETE /api/favorites/:id

---

# 九、统一响应格式

{
"code": 0,
"message": "success",
"data": {}
}

错误：

{
"code": 500,
"message": "error message",
"data": null
}

---

# 十、管理后台（预留）

URL：

/admin

功能：

* 查看同步状态
* 查看接口日志
* 手动同步数据
* 查看缓存状态

---

# 十一、SEO要求

必须支持：

* SSR
* Metadata
* Sitemap
* robots.txt

允许收录：

/item/*

页面。

---

# 十二、项目结构

apps/

web/

app/

components/

services/

hooks/

lib/

api/

src/

modules/

prisma/

common/

---

# 十三、开发规范

要求：

* TypeScript
* ESLint
* Prettier
* Swagger
* DTO参数校验
* Prisma Migration
* Docker部署
* README自动生成
* 全局异常处理
* 日志系统

---

# 十四、MVP阶段

第一阶段必须完成：

✓ 第三方价格接口接入

✓ Provider模式实现

✓ 定时同步任务

✓ 首页

✓ 搜索

✓ 详情页

✓ 价格走势图

第二阶段：

* 用户系统
* 收藏功能

第三阶段：

* 邮件价格提醒
* 价格预警
* 利润分析工具
* 开放API

---

# 十五、Codex执行要求

优先级最高：

1. 实现价格数据Provider
2. 实现定时同步任务
3. 实现数据库模型
4. 实现API接口
5. 实现前端页面

禁止先开发UI后开发数据层。

如果真实数据源不可用：

必须实现MockProvider。

保证项目在无真实接口情况下仍可运行演示。

项目交付标准：

docker compose up -d

执行后能够直接访问网站并展示完整功能。

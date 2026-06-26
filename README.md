# 基于 JavaWeb 的物品修复系统的设计与实现

一个社区驱动的物品维修指南管理平台，用户可以编写、发布和分享各类设备的维修教程。基于 Java Servlet + Layui + MySQL 构建的经典 B/S 架构毕业设计项目。

---

## 项目概述

本系统作为一个**维修指南知识库**，允许用户为常见设备（手机、笔记本电脑、游戏主机、电动工具）贡献维修文章。系统包含完整的文章审核流程、用户互动（点赞与评论）和管理员后台。

### 系统架构

```
┌─────────────────────────────────────────────────────┐
│                  浏览器 (HTML + Layui)               │
├─────────────────────────────────────────────────────┤
│              Java Servlet (控制器层)                  │
├─────────────────────────────────────────────────────┤
│               Service 层 (业务逻辑)                   │
├─────────────────────────────────────────────────────┤
│               DAO 层 (JDBC + SQL)                    │
├─────────────────────────────────────────────────────┤
│                     MySQL 数据库                     │
└─────────────────────────────────────────────────────┘
```

---

## 功能特性

### 用户管理

| 功能 | 说明 |
|---|---|
| **用户注册** | 新用户填写基本信息完成注册 |
| **用户登录** | 凭用户名与密码登录，根据角色进入不同界面 |
| **个人信息修改** | 修改昵称、性别、年龄、邮箱、头像 |
| **密码修改** | 验证原密码后修改当前账号密码 |
| **管理员用户管理** | 查看所有用户、新增用户（分配初始角色）、编辑/删除用户 |

### 文章管理

文章支持**审核工作流**，共四种状态：

`草稿` → `待审核` → `已通过` / `已拒绝`

| 功能 | 说明 |
|---|---|
| **浏览文章** | 按分类（手机 / 笔记本电脑 / 游戏主机 / 电动工具）查看文章 |
| **创建草稿** | 使用 TinyMCE 富文本编辑器撰写文章，保存为私人草稿 |
| **提交审核** | 将草稿提交给管理员审核 |
| **编辑文章** | 编辑自己的草稿；审核失败的文章可修改后重新提交 |
| **删除文章** | 用户删除自己的草稿；管理员可删除任何已发布文章 |
| **文章审核** | 管理员审核通过或拒绝提交，结果实时反馈给用户 |

### 点赞管理

| 功能 | 说明 |
|---|---|
| **点赞 / 取消点赞** | 对已审核通过的文章切换点赞状态 |
| **查看点赞** | 查看自己点赞过的所有文章 |

### 评论管理

| 功能 | 说明 |
|---|---|
| **发表评论** | 对任意已审核通过的文章发布评论 |
| **查看评论** | 查看自己发表过的所有评论 |
| **删除评论** | 管理员可删除任意文章下的评论 |

---

## 技术栈

| 层次 | 技术 |
|---|---|
| **前端** | HTML5, CSS3, JavaScript, [Layui](https://layui.dev/) |
| **富文本编辑器** | TinyMCE（通过 `layui_exts` 集成） |
| **后端** | Java (JDK 8+), Java Servlet 4.0 |
| **数据库** | MySQL 8.0 |
| **服务器** | Apache Tomcat 9/10（端口 8080） |
| **系统架构** | B/S 架构，MVC 开发模式 |
| **开发工具** | Eclipse IDE for Enterprise Java (WTP) |
| **JSON 处理** | Alibaba Fastjson, Google Gson |
| **数据访问** | 原生 JDBC（`PreparedStatement`） |
| **代码简化** | Lombok（`@Data` 注解） |
| **文件上传** | Apache Commons FileUpload, Commons IO |
| **JavaScript** | jQuery 3.6.0 |

---

## 数据库设计

系统使用 MySQL 数据库 `repairdb`。数据库结构文件位于 [`repairdb.sql`](./repairdb.sql)。

### 数据表

| 表名 | 说明 |
|---|---|
| `s_userinfo` | 用户表（主键 `userId`，`userName`，`password`，`role`，个人资料字段） |
| `v_articleinfo` | 文章表（主键 `articleId`，外键 `userId`，`topic`，`typeName`，`release` 状态，`views`，`likes`，`favorites`，`dataHtml`） |
| `v_modeltype` | 文章分类表（`typeId`，`typeName`：手机 / 笔记本电脑 / 游戏主机 / 电动工具） |
| `c_useractions` | 用户互动记录表——每篇文章的点赞与收藏记录 |
| `c_usercomment` | 评论表（主键 `commentId`，外键 `articleId`，`name`，`content`，`uploadTime`） |

### 文章状态（`release` 字段）

| 值 | 含义 | 可见范围 |
|---|---|---|
| `0` | **草稿** — 已保存但未提交 | 仅作者可见 |
| `1` | **待审核** — 等待管理员审核 | 作者和管理员 |
| `3` | **已通过** — 审核通过，公开发布 | 所有人可见 |
| `2` | **已拒绝** — 审核未通过，附有原因 | 作者和管理员 |

### 用户角色（`role` 字段）

| 值 | 角色 | 权限 |
|---|---|---|
| `0` | **管理员** | 文章审核、用户管理、评论管理 |
| `1` | **普通用户** | 撰写文章、点赞、评论 |

---

## 项目结构

```
GraduationProject/
├── README.md                          # 本文件
├── repairdb.sql                       # 数据库备份（表结构 + 初始数据）
├── build/classes/com/zpq/             # 编译后的 Java 类文件
└── src/main/
    ├── java/com/zpq/
    │   ├── controller/                # 25 个 Servlet 控制器类
    │   ├── service/                   # 6 个业务接口 + 实现类
    │   ├── dao/                       # 5 个数据访问接口 + 实现类
    │   ├── pojo/                      # 6 个数据模型 / POJO 类
    │   └── utils/
    │       └── DBUtil.java            # 数据库连接工具类
    └── webapp/
        ├── WEB-INF/
        │   ├── web.xml                # Servlet 部署描述符
        │   └── lib/                   # JAR 依赖包
        ├── META-INF/
        ├── conf/                      # Tomcat 配置文件
        ├── layui/                     # Layui UI 框架
        ├── layui_exts/                # TinyMCE 富文本编辑器
        ├── js/                        # jQuery
        ├── style/                     # CSS 样式表
        ├── img/                       # 图片及系统结构图
        ├── Login.html                 # 登录页（欢迎页）
        ├── Register.html              # 注册页
        └── pages/
            ├── Public/
            │   └── Home.html          # 文章首页
            ├── Article/               # 文章相关页面（增删改查、分类浏览）
            ├── Manage/                # 管理页面（审核、用户管理、评论管理）
            ├── Personal/              # 个人中心（资料、密码、点赞、评论）
            └── Error/
                └── ErrorPage.html     # 404 错误页面
```

---

## 后端架构

采用标准的 **MVC 三层架构**，包路径为 `com.zpq`。

### `pojo` 层 — 数据模型

| 类名 | 说明 |
|---|---|
| `Userinfo` | 用户实体 |
| `Articleinfo` | 文章实体 |
| `Usercomment` | 评论实体 |
| `Useractions` | 用户互动（点赞/收藏）记录 |
| `Modeltype` | 文章分类类型 |
| `Vo` | 通用 JSON 响应封装（`code`，`msg`，`count`，`data`），适配 Layui 表格组件 |

### `dao` 层 — 数据访问

| 接口 | 实现类 | 主要操作 |
|---|---|---|
| `UserinfoDao` | `UserinfoDaoImpl` | 登录、注册、查询、搜索、编辑、删除用户 |
| `ArticleinfoDao` | `ArticleinfoDaoImpl` | 文章增删改查、搜索、保存草稿、发布、审核 |
| `UsercommentDao` | `UsercommentDaoImpl` | 新增、查询、删除评论 |
| `UseractionsDao` | `UseractionsDaoImpl` | 点赞/收藏记录管理 |
| `ModeltypeDao` | `ModeltypeDaoImpl` | 文章分类查询 |

### `service` 层 — 业务逻辑

每个服务遵循 Interface + Impl 模式：

| 服务 | 职责 |
|---|---|
| `LoginService` / `RegisterService` | 用户认证与注册 |
| `ArticleService` | 文章核心业务（增删改查 + 审核 + 搜索） |
| `UserInfoService` | 个人资料管理 |
| `UserCommentService` | 评论业务操作 |
| `UserActionService` | 点赞/收藏业务 |

### `controller` 层 — Servlet 控制器（共 25 个）

| 分类 | Servlet 列表 |
|---|---|
| **认证** | `CheckLoginServlet`，`CheckRegisterServlet`，`LoginOutServlet` |
| **用户管理** | `AddUserServlet`，`DeleteUserServlet`，`SelectAllUserServlet`，`SelectUserActionServlet`，`UpdateUserActionServlet` |
| **个人中心** | `PersonalInfoServlet`，`EditPasswordServlet` |
| **文章操作** | `SaveArticleServlet`，`DeleteArticleServlet`，`SelectAllArticleServlet`，`SelectMyArticleServlet`，`SearchArticleServlet`，`SearchMyArticleServlet`，`ShowArticleServlet`，`ArticleDetailServlet`，`CountModelArticleServlet` |
| **文章审核** | `ReviewArticleServlet`，`UploadArticleReviewServlet` |
| **评论管理** | `CommentServlet`，`SearchCommentServlet`，`DeleteCommentServlet` |
| **文件处理** | `UploadImageServlet` |

---

## 部署与运行

### 环境要求

- **JDK** 8 或更高版本
- **Apache Tomcat** 9 或 10
- **MySQL** 8.0
- **Eclipse IDE for Enterprise Java**（推荐）

### 部署步骤

1. **克隆项目**

   ```bash
   git clone <仓库地址>
   cd GraduationProject
   ```

2. **创建并导入数据库**

   ```sql
   CREATE DATABASE repairdb DEFAULT CHARACTER SET utf8mb4;
   SOURCE repairdb.sql;
   ```

3. **配置数据库连接**

   修改 `DBUtil.java` 中的数据库连接参数（主机、端口、用户名、密码）。

4. **导入 Eclipse**

   - `File → Import → Existing Projects into Workspace`
   - 选择项目根目录

5. **配置 Tomcat 服务器**

   - `Window → Preferences → Server → Runtime Environments`
   - 添加 Tomcat 安装路径
   - 右键项目 → `Run As → Run on Server`

6. **访问应用**

   打开浏览器，访问 `http://localhost:8080/RepairSystem/Login.html`

---

## 系统功能结构图

> 项目原始功能结构图：

> ![](src/main/webapp/img/修复论坛组织结构图.png)

---

## 依赖说明（`WEB-INF/lib/`）

| JAR 包 | 用途 |
|---|---|
| `mysql-connector-j` | MySQL JDBC 驱动（v9.2.0） |
| `servlet-api` | Java Servlet API |
| `fastjson` / `gson` | JSON 序列化与反序列化 |
| `lombok` | `@Data` 注解，简化 POJO 代码 |
| `commons-fileupload` / `commons-io` | 文件上传处理 |
| `jstl` / `standard` | JSTL 标签库（已引入但未使用——项目无 JSP 页面） |

---

## 补充说明

- 系统使用 **Cookie** 保存用户身份信息（`userName` 和 `role`），未采用基于 Session 的安全框架。
- 所有前端页面均为 **静态 HTML**（非 JSP），通过 AJAX 与后端进行 JSON 数据交互。
- `Vo.java` 类的统一 JSON 响应格式（`code`，`msg`，`count`，`data`）与 Layui 表格组件完美适配。

---
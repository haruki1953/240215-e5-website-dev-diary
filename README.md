e5分享网站开发记录

[提问记录](assets/提问记录.md)

### 240215
**决定了开发流程**
- **编写项目文档**：项目概述、界面与功能、技术方案
- **编写接口文档**
- **开发后端**
- **开发前端**
- **测试与部署**

[01_项目文档](01_项目文档.md)

规划设计了页面
1. 登录注册页
2. 主页面布局
3. 首页：列出正在分享的账号
4. 用户列表
5. 用户主页
6. 动态页：类似群聊，显示正在帮助自己的用户所发的消息
7. e5账号分享管理页


### 240216

可以使用`uuid`库生成UUID，用于：动态消息id、私信消息id、通知id


### 240217
整理并描述了每个页面的功能


### 240218
在Node.js中，有一些工具可以像Django的admin那样快速可视化操作SQLite数据库。  
Express Admin 是一个基于Express.js的中间件，用于为MySQL, MariaDB, PostgreSQL, SQLite等数据库创建管理界面


### 240219
基本完成了数据库的设计，对后端大部分接口进行了梳理


### 240220
后端开发，mvc三层架构：表现层、业务逻辑层、数据访问层  
[02_后端开发记录](02_后端开发记录.md)  

后端命名 e5share_node  
前端命名 e5share_vue3  

初始化项目
1. 安装配置ESLint [node项目中使用ESLint](笔记/node项目中使用ESLint.md)
2. 新建 app.js 初始化一个基本的 express 服务器实例
3. 配置 cors 跨域
4. 提交至 development 分支


### 240221
1. 安装sqlite3，好像因为缺少编译工具会报错，需要设置代理，以不在本地编译而从远程服务器下载预编译的二进制包。
    [【02_后端开发记录】二、数据库创建](02_后端开发记录.md#二、数据库创建)  
2. orm框架Sequelize的基本使用以及在项目中的使用方式
    [【node项目中使用sqlite数据库】](笔记/node项目中使用sqlite数据库.md#)


### 240222
完成了sequelize在项目中的配置，做了写接口前的准备  
试着弄了adminjs，但感觉太大了就放弃了


### 240223
[03_接口文档](03_接口文档.md)


### 240224
完成了注册登录接口，正在写用户信息修改相关接口  
整理好了接口编写流程，写起来顺很多了：
**准备**
- 接口文档先写个大概
- 整理接口要干的事  
**代码**
- 先完成路由和路由处理函数，确保畅通
- 配置表单数据验证
- 编写业务逻辑  
**完善**
- 测试接口
- 完善接口文档


### 240225
完成了用户信息修改相关接口
- multer 解析表单数据
- sharp 压缩处理图片
- moment 解析日期并设置时区

### 240226
完成了 公共接口 与 e5动态相关接口  
明天再测试测试 确认访问动态权限 之类的吧

### 240227
正在写e5分享相关接口，头有点疼，没写多少就撂了

### 240228
只完成了注销分享接口，还很史  
还是看看指尖相触恋恋不舍吧，好累

### 240229
后端基本的功能终于马马虎虎完成了  
明天大概可以开始写前端了

### 240301
开始前端开发
[04_前端开发记录](04_前端开发记录.md)

### 240302
完成登录页面

### 240303
只弄了导航栏，还挺好看

### 240304
- 个人信息获取与渲染
- 渲染通知徽章提示
- 通知抽屉

### 240305
今天什么也没干😭  
倒是想了想交错式瀑布流应该怎样做，直接分成三列对应三个数组，然后遍历获取的数据并判断元素高度添加入最低的列  
希望有现成的组件吧

### 240306
思考了一下需要解决的问题
1. 登录（等需调用api的）按键防抖
2. 对用户可以自定义的信息，转义特殊字符
    （也可以不用，Vue 的 `{{}}` 插值表达式和 `v-bind` 指令都会自动转义特殊字符）  
    至于头像，后端已经处理并压缩了，不会有xss攻击

优化了项目结构，完成了用户列表的获取

### 240307
完成了动态获取与其未读提示

### 240308
- 登录访问拦截
- logo通过配置文件控制
- 完成通知卡片的静态样式

### 240309
- 完成通知渲染
- 首页卡片静态样式
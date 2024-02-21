## Node 项目中使用 ESLint 笔记

### 步骤一：安装 ESLint
在你的 Node 项目中执行以下命令，使用 pnpm 将 ESLint 安装为开发依赖项：
```bash
pnpm add eslint --save-dev
```

### 步骤二：初始化 ESLint 配置文件
在项目根目录下执行以下命令，初始化 ESLint 配置文件：
```bash
pnpm create @eslint/config
```
根据向导提示，选择适合的配置选项
```
? How would you like to use ESLint? ...
  To check syntax only
  To check syntax and find problems
> To check syntax, find problems, and enforce code style
如果你想要搭配 VSCode 插件来自动修正代码，则建议选择最后一个选项：检查语法、找到问题，并强制执行代码风格规则。

? What type of modules does your project use? ...
  JavaScript modules (import/export)
> CommonJS (require/exports)
  None of these
如果你是在开发一个 Node.js 后端项目，通常你会选择 CommonJS (require/exports) 这个选项。

? Which framework does your project use? ...
  React
  Vue.js
> None of these
对于一个 Node.js 后端项目，通常不会使用 React 或 Vue.js 这类前端框架。因此，你可以选择 "None of these" 选项。

? Does your project use TypeScript? » No / Yes
未使用 TypeScript 选择 No

? Where does your code run? ...  (Press <space> to select, <a> to toggle all, <i> to invert selection)
√ Browser
√ Node
选择 Node

? How would you like to define a style for your project? ...
> Use a popular style guide
  Answer questions about your style
如果你想使用一个流行的代码风格指南，你可以选择 "Use a popular style guide"。

? Which style guide do you want to follow? ...
> Airbnb: https://github.com/airbnb/javascript
  Standard: https://github.com/standard/standard
  Google: https://github.com/google/eslint-config-google
  XO: https://github.com/xojs/eslint-config-xo
你可以选择一个你喜欢的代码风格指南
选择 Airbnb

? What format do you want your config file to be in? ...
> JavaScript
  YAML
  JSON
通常情况下，JavaScript 格式是最常见的选择

Checking peerDependencies of eslint-config-airbnb-base@latest The config that you've selected requires the following dependencies:
正在检查最新版本的 eslint-config-airbnb-base 的 peerDependencies。 
你选择的配置需要以下依赖项：

eslint-config-airbnb-base@latest eslint@^7.32.0 || ^8.2.0 eslint-plugin-import@^2.25.2
? Would you like to install them now? » No / Yes
是的，你需要安装这些依赖项才能使用 Airbnb 的 ESLint 配置。请选择"Yes"，然后按 Enter 键确认安装。

? Which package manager do you want to use? ...
  npm
  yarn
> pnpm
```



### 步骤三：安装并配置 VSCode ESLint 插件
打开 VSCode 设置（`Ctrl + ，`或者菜单栏中选择“文件” > “首选项” > “设置”），在设置中添加以下配置以启用在保存时自动修复错误的功能：
```json
"editor.codeActionsOnSave": {
	"source.fixAll": true
}
```

如果行尾报错，将行尾序列改为LF
`Ctrl + ，` 搜索eol，将默认行尾字符修改为 `\n`

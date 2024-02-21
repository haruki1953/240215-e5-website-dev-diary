# 原生sqlite
#### 1. 安装 SQLite3 包
首先，安装 `sqlite3` 包，它是 Node.js 中与 SQLite 数据库交互的主要工具。
```bash
npm install sqlite3
```
注意：好像因为缺少编译工具会报错，需要设置代理，以不在本地编译
```
pnpm config set proxy http://localhost:10809
pnpm config set https-proxy http://localhost:10809
```
当你运行`npm install sqlite3`命令时，npm会首先尝试从远程服务器下载预编译的二进制包。如果这个过程成功，那么就不需要在本地进行编译。然而，如果由于网络问题或者其他原因导致下载失败，npm会尝试在本地编译这些插件。

#### 2. 创建 SQLite 数据库文件
在项目根目录下创建一个 SQLite 数据库文件，例如 `mydatabase.db`。

#### 3. 连接数据库并执行查询
编写 Node.js 代码连接到 SQLite 数据库并执行查询。
```javascript
// 引入 SQLite3 模块
const sqlite3 = require('sqlite3').verbose();

// 指定数据库文件路径
const dbPath = './mydatabase.db';

// 创建或打开数据库连接
const db = new sqlite3.Database(dbPath, sqlite3.OPEN_READWRITE | sqlite3.OPEN_CREATE, (err) => {
  if (err) {
    console.error('Failed to open database', err.message);
    return;
  }
  console.log('Connected to the database.');
});

// 创建表格
db.serialize(() => {
  db.run(`CREATE TABLE IF NOT EXISTS Users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    username TEXT NOT NULL,
    email TEXT NOT NULL
  )`);

  // 插入数据示例
  const stmt = db.prepare('INSERT INTO Users (username, email) VALUES (?, ?)');
  stmt.run('user1', 'user1@example.com');
  stmt.run('user2', 'user2@example.com');
  stmt.finalize();

  // 查询数据示例
  db.each('SELECT id, username, email FROM Users', (err, row) => {
    if (err) {
      console.error(err.message);
      return;
    }
    console.log(row.id, row.username, row.email);
  });
});

// 关闭数据库连接
db.close((err) => {
  if (err) {
    console.error('Failed to close database', err.message);
    return;
  }
  console.log('Database connection closed.');
});
```

#### 4. 注意事项

- 确保在使用数据库之前先创建或打开数据库连接。
- 使用 `serialize()` 方法确保在数据库上执行的操作按顺序进行。
- 使用 `db.run()` 执行 SQL 语句，可以执行创建表格、插入数据等操作。
- 使用 `db.each()` 查询数据，并处理每一行的结果。
- 最后，使用 `db.close()` 关闭数据库连接以释放资源。


#### .verbose()
在 SQLite 中，`.verbose()` 是一个方法，用于开启调试模式，使得 SQLite 模块输出更多的调试信息到控制台。当调试模式开启时，SQLite 模块会输出更多有用的信息，例如数据库操作的错误、警告以及执行的 SQL 语句等。

在 Node.js 中，当你使用 `require('sqlite3').verbose()` 时，你获得了一个启用了详细输出的 SQLite 模块实例，这意味着当你使用这个实例进行数据库操作时，你可以在控制台上看到更多的调试信息，有助于你调试和理解代码的执行情况。


#### serialize
`db.serialize()` 是 SQLite3 模块提供的一个方法，用于在一个序列化的环境中执行多个数据库操作。在这个方法中执行的所有操作都会按照顺序逐一执行，确保操作之间的顺序性和可靠性。

具体来说，`db.serialize()` 方法接受一个回调函数作为参数，在这个回调函数中可以执行多个数据库操作，例如创建表、插入数据、查询数据等。这些操作会在一个序列化的环境中按照顺序执行，保证了操作的一致性。

使用 `db.serialize()` 可以简化数据库操作的管理，并确保操作之间的正确顺序执行，避免了可能的并发问题。


#### sqlite3.OPEN_READWRITE | sqlite3.OPEN_CREATE
在 SQLite3 中，`sqlite3.OPEN_READWRITE | sqlite3.OPEN_CREATE` 是指定打开数据库的模式的一个参数。具体含义如下：

- `sqlite3.OPEN_READWRITE`：表示以读写模式打开数据库，允许进行读取和写入操作。
- `sqlite3.OPEN_CREATE`：表示如果数据库不存在，则创建一个新的数据库文件。

将这两个参数进行按位或运算，可以同时指定打开数据库的模式为读写模式，并且如果数据库文件不存在时创建一个新的数据库文件。


#### db.run
`db.run` 主要用于执行 SQL 语句中的更新、插入、删除等操作，而不是用于查询操作。它通常用于执行不返回结果集的 SQL 命令，比如执行 `INSERT`、`UPDATE`、`DELETE` 等命令。要执行查询操作，一般使用 `db.all`、`db.get` 或 `db.each` 等方法。


#### db.all、db.get、db.each
这三个方法都是用于执行 SQL 查询操作的，但它们在处理结果集的方式有所不同：

1. **`db.all(sql, [params,] callback)`**
   - 这个方法用于执行查询，并将所有匹配的结果作为一个数组传递给回调函数。
   - 如果查询成功，回调函数的第二个参数将是一个包含查询结果的数组。
   - 如果没有匹配的结果，传递给回调函数的数组将为空。

2. **`db.get(sql, [params,] callback)`**
   - 这个方法用于执行查询，并将第一个匹配的结果传递给回调函数。
   - 如果查询成功且有匹配的结果，回调函数的第二个参数将是一个包含查询结果的对象。
   - 如果没有匹配的结果，回调函数的第二个参数将是 `undefined`。

3. **`db.each(sql, [params,] callback, [complete])`**
   - 这个方法用于执行查询，并对每一行匹配的结果都调用一次回调函数。
   - 如果查询成功，回调函数将被调用多次，每次传递一个包含当前行数据的对象。
   - 可选的 `complete` 回调函数在查询结束时被调用，不带任何参数。

# Sequelize
## 简单使用
首先，你需要确保在项目中安装了 Sequelize 和相应的数据库驱动。对于 SQLite，你需要安装 `sqlite3` 包。
```bash
npm install sequelize sqlite3
```

然后，你可以按照以下步骤使用 Sequelize：
1. **配置 Sequelize**：创建一个 Sequelize 实例，并配置连接信息。
```javascript
const { Sequelize } = require('sequelize');

// 创建 Sequelize 实例
const sequelize = new Sequelize({
  dialect: 'sqlite', // 使用的数据库类型
  storage: 'path/to/database.sqlite', // 数据库文件路径
});
```

2. **定义模型**：定义你的数据模型，这些模型将映射到数据库中的表。
```javascript
// 定义 User 模型
const User = sequelize.define('User', {
  username: {
    type: Sequelize.STRING,
    allowNull: false,
    unique: true,
  },
  email: {
    type: Sequelize.STRING,
    allowNull: false,
    unique: true,
    validate: {
      isEmail: true,
    },
  },
});
```

3. **同步数据库**：使用 `sync()` 方法将模型同步到数据库中。
```javascript
// 同步数据库
(async () => {
  try {
    await sequelize.sync({ force: true }); // force: true 将在每次应用启动时重新创建表
    console.log('Database synchronized successfully');
  } catch (error) {
    console.error('Error synchronizing database:', error);
  }
})();
```
`force: true` 选项会在每次同步数据库时都重新创建表，这意味着它会删除现有的表并重新创建它们，从而清除所有数据。这样可以确保每次应用启动时数据库都处于一致的状态。在生产环境中，通常不建议使用 `force: true`，因为它会导致数据丢失。

如果不使用 `force: true` 选项，则 Sequelize 会尝试根据模型定义来同步数据库，但不会删除现有的表或数据。它会检查数据库中是否存在相应的表，如果不存在，则会创建缺失的表。如果已存在表，但结构不匹配模型定义，则 Sequelize 会尝试根据模型定义进行更新。这样可以保留现有数据，并根据需要更新表结构。

4. **执行数据库操作**：通过模型执行数据库操作，如创建、查询、更新或删除记录。
```javascript
// 创建新用户
(async () => {
  try {
    const newUser = await User.create({
      username: 'john_doe',
      email: 'john@example.com',
    });
    console.log('New user created:', newUser.toJSON());
  } catch (error) {
    console.error('Error creating user:', error);
  }
})();

// 查询所有用户
(async () => {
  try {
    const users = await User.findAll();
    console.log('All users:', users.map(user => user.toJSON()));
  } catch (error) {
    console.error('Error fetching users:', error);
  }
})();
```
这就是一个简单的 Sequelize 使用示例。你可以根据自己的需求进一步扩展和定制。


## 在项目中使用
在一般的Node.js后端项目中，通常会将数据库连接、模型定义以及初始化操作封装在一个统一的模块中，例如`models/index.js`。这样的模块负责连接数据库、定义模型，并在需要时执行数据库的初始化操作。

下面是一种常见的做法：
1. **数据库连接：** 在`/db/index.js`中创建 Sequelize 实例并连接数据库。
2. **模型定义：** 在同一个文件中定义所有的模型，或者按照模块划分，将相关的模型定义放在单独的文件中。
3. **数据库初始化：** 在需要的时候执行数据库的初始化操作，例如创建表格、插入初始数据等。
4. **导出模型：** 最后，导出 Sequelize 实例、定义好的模型、初始化函数，以便其他模块使用。


下面是一个示例`models/index.js`文件的结构：
```js
// models/index.js

const { Sequelize } = require('sequelize');

// 创建 Sequelize 实例并连接数据库
const sequelize = new Sequelize({
  dialect: 'sqlite', // 使用的数据库类型
  storage: './db/database.sqlite', // 数据库文件路径
});

// 导入模型定义
const User = require('./User')(sequelize, Sequelize);

// 数据库初始化函数
const initializeDatabase = async () => {
  try {
    // 在这里执行数据库初始化操作，例如创建表格、插入初始数据等
    await sequelize.sync();

    // 检查是否数据库中已经存在数据
    const usersCount = await User.count();
    if (usersCount === 0) {
      // 执行初始化内容，例如创建初始用户
      await User.create({
        username: 'admin',
        email: 'admin@example.com',
      });
      console.log('Initial user created successfully');
    } else {
      console.log('Database already contains data, skipping initialization');
    }

    console.log('Database initialized successfully');
  } catch (error) {
    console.error('Error initializing database:', error);
  }
};

// 导出 Sequelize 实例、模型对象、初始化函数
module.exports = {
  sequelize,
  User,
  initializeDatabase,
};
```

下面是模型定义
```js
// models/User.js

module.exports = (sequelize, Sequelize) => {
  const User = sequelize.define('User', {
    username: {
      type: Sequelize.STRING,
      allowNull: false,
      unique: true,
    },
    email: {
      type: Sequelize.STRING,
      allowNull: false,
      unique: true,
      validate: {
        isEmail: true,
      },
    },
  });

  return User;
};
```

然后，在项目的入口文件（例如`app.js`或`index.js`）中，你可以引入这个`models/index.js`文件，并在启动服务器之前调用`initializeDatabase()`函数来初始化数据库。
```js
// app.js

const { sequelize, initializeDatabase } = require('./models/index');

// 匿名立即执行函数
(async () => {
  try {
    // 初始化数据库
    await initializeDatabase();
    console.log('Database initialized successfully');
    
    // 启动服务器
    app.listen(PORT, () => {
      console.log(`Server is running on port ${PORT}`);
    });
  } catch (error) {
    console.error('Failed to start server:', error);
  }
})();
```


## 另一种形式（写了半天，推荐）
```
/
|-- db/
|   |-- database.sqlite
|   |-- index.js
|   `-- initialize.js
|-- models/
|   |-- index.js
|   `-- user.js
`-- app.js

```

首先是 `/db/index.js` 创建 Sequelize 实例并连接数据库，最后导出：
```javascript
const { Sequelize } = require('sequelize');
const path = require('path');

// 创建 Sequelize 实例并连接数据库
const sequelize = new Sequelize({
  dialect: 'sqlite', // 使用的数据库类型
  storage: path.join(__dirname, 'database.sqlite'), // 数据库文件路径
});

// 导出 Sequelize 实例
module.exports = sequelize;
```

然后是 `/models/user.js` 定义 User 模型并导出：
```javascript
const { DataTypes } = require('sequelize');
const sequelize = require('../db/index');

// 定义 User 模型
const User = sequelize.define('User', {
  username: {
    type: DataTypes.STRING,
    allowNull: false,
    unique: true,
  },
  email: {
    type: DataTypes.STRING,
    allowNull: false,
    unique: true,
    validate: {
      isEmail: true,
    },
  },
});

module.exports = User;
```

接下来是 `/models/index.js` 集中导入所有模型并导出，方便使用：
```javascript
const User = require('./user');
// ...

// 导出 模型对象
module.exports = {
  User,
  // ...
};
```


最后是 `/db/initialize.js` 定义初始化函数并导出：
```javascript
const { sequelize } = require('./index');
const { User } = require('../models/index');

// 数据库初始化函数
const initializeDatabase = async () => {
  try {
    // 在这里执行数据库初始化操作，例如创建表格、插入初始数据等
    await sequelize.sync();

    // 检查是否数据库中已经存在数据
    const usersCount = await User.count();
    if (usersCount === 0) {
      // 执行初始化内容，例如创建初始用户
      await User.create({
        username: 'admin',
        email: 'admin@example.com',
      });
      console.log('Initial user created successfully');
    } else {
      console.log('Database already contains data, skipping initialization');
    }

    console.log('Database initialized successfully');
  } catch (error) {
    console.error('Error initializing database:', error);
  }
};

module.exports = initializeDatabase;
```

在 app.js 中，引入`/db/initialize.js`文件，并在启动服务器之前调用`initializeDatabase()`函数来初始化数据库。
```js
// app.js

const initializeDatabase = require('./db/initialize');

// 匿名立即执行函数
(async () => {
  try {
    // 初始化数据库
    await initializeDatabase();
    console.log('Database initialized successfully');
    
    // 启动服务器
    app.listen(PORT, () => {
      console.log(`Server is running on port ${PORT}`);
    });
  } catch (error) {
    console.error('Failed to start server:', error);
  }
})();
```
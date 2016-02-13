## Installation

Sequelize is available via NPM.

```bash
$ npm install --save sequelize

# And one of the following:
$ npm install --save pg pg-hstore
$ npm install --save mysql // For both mysql and mariadb dialects
$ npm install --save sqlite3
$ npm install --save tedious // MSSQL
```

## Setting up a connection

Sequelize will setup a connection pool on initialization so you should ideally only ever create one instance per database.

## 配置一个连接
Sequelize 将在初始化的时候会设置一个连接池,因此理论上每个实例会创建一次数据库连接

```js
var sequelize = new Sequelize('database', 'username', 'password', {
  host: 'localhost',
  dialect: 'mysql'|'mariadb'|'sqlite'|'postgres'|'mssql',

  pool: {
    max: 5,
    min: 0,
    idle: 10000
  },

  // SQLite only
  storage: 'path/to/database.sqlite'
});

// Or you can simply use a connection uri
// 或者你可以简单的这样使用一个connection uri
var sequelize = new Sequelize('postgres://user:pass@example.com:5432/dbname');
```

The Sequelize constructor takes a whole slew of options that are available via the [API reference](http://sequelize.readthedocs.org/en/latest/api/sequelize/).

Sequelize 整个的构造参数的配置都可以通过访问 [API reference](http://sequelize.readthedocs.org/en/latest/api/sequelize/).

## Your first model

## 你的第一个模型(model)

Models are defined with `sequelize.define('name', {attributes}, {options})`.

模型都是使用这样的格式定义
> `sequelize.define('name', {attributes}, {options})`.

```js
var User = sequelize.define('user', {
  firstName: {
    type: Sequelize.STRING,
    field: 'first_name' // Will result in an attribute that is firstName when user facing but first_name in the database
  },
  lastName: {
    type: Sequelize.STRING
  }
}, {
  freezeTableName: true // Model tableName will be the same as the model name
});

User.sync({force: true}).then(function () {
  // Table created
  return User.create({
    firstName: 'John',
    lastName: 'Hancock'
  });
});
```

Many more options can be found in the [Model API reference](http://sequelize.readthedocs.org/en/latest/api/model/)

更多的参数可以查找 [Model API reference](http://sequelize.readthedocs.org/en/latest/api/model/)

### Application wide model options

### (应用)Application 的通用型模型参数

The Sequelize constructor takes a `define` option which will be used as the default options for all defined models.

Sequelize 的`define`的参数将会作为所有模型的默认参数

```js
var sequelize = new Sequelize('connectionUri', {
  define: {
    timestamps: false // true by default
  }
});

var User = sequelize.define('user', {}); // timestamps is false by default
var Post = sequelize.define('post', {}, {
  timestamps: true // timestamps will now be true
});
```

## Promises
Sequelize uses promises to control async control-flow. If you are unfamilar with how promises work, now might be a good time to brush up on them, [here](https://github.com/wbinnssmith/awesome-promises) and [here](https://github.com/petkaantonov/bluebird#what-are-promises-and-why-should-i-use-them)

## 承诺(Promises)

Basically a promise represents a value which will be present at some point - "I promise you I will give you a result or an error at some point". This means that

基本的承诺都将会表现成一个相同的观点" 我承诺你将会在一个点上返回一个结果或者一个错误"

```js
// DON'T DO THIS
// 不要这样用
user = User.findOne()

console.log(user.name);
```

_will never work!_ This is because `user` is a promise object, not a data row from the DB. The right way to do it is:
这个不会运行,因为`user`是一个promise 对象,不是一个数据库的数据,正确的做法是

```js
User.findOne().then(function (user) {
    console.log(user.name);
});
```

Once you've got the hang of what promises are and how they work, use the [bluebird API reference](https://github.com/petkaantonov/bluebird/blob/master/API.md) as your go to tool. In particular, you'll probably be using [`.all`](https://github.com/petkaantonov/bluebird/blob/master/API.md#all---promise) a lot.

同时,你可以获得大量promises对象怎么运作,使用[bluebird API reference](https://github.com/petkaantonov/bluebird/blob/master/API.md) 作为你的工具.例如,你可能使用[`.all`](https://github.com/petkaantonov/bluebird/blob/master/API.md#all---promise) a lot.



## Definition

To define mappings between a model and a table, use the `define` method. Sequelize will then automatically add the attributes `createdAt` and `updatedAt` to it. So you will be able to know when the database entry went into the db and when it was updated the last time. If you do not want timestamps on your models, only want some timestamps, or you are working with an existing database where the columns are named something else, jump straight on to [configuration ][0]to see how to do that.

## 定义

使用`define`方法，来定义模型和表之间的映射。Sequelize 将会自动添加`createdAt` 和 `updateAt`。所以你会知道当一个数据实体进入到数据库的时候将会自动更新它的最新的插入时间。如果你不想在你的模型上使用时间戳，仅仅想要时间戳，或者你正在使用已经存在的数据库的表列中已经有类似的字段名，请跳转到
[configuration ][0] 查看如何使用。


```js
var Project = sequelize.define('Project', {
  title: Sequelize.STRING,
  description: Sequelize.TEXT
})

var Task = sequelize.define('Task', {
  title: Sequelize.STRING,
  description: Sequelize.TEXT,
  deadline: Sequelize.DATE
})
```

You can also set some options on each column:
你也可以为每列设置一些选项

```js
var Foo = sequelize.define('Foo', {
 // instantiating will automatically set the flag to true if not set
 // 初始化将会自动把这个标示设置为true，如果没有设置
 flag: { type: Sequelize.BOOLEAN, allowNull: false, defaultValue: true},

 // default values for dates => current time
 ／／默认的时间值为当前时间
 myDate: { type: Sequelize.DATE, defaultValue: Sequelize.NOW },

 // setting allowNull to false will add NOT NULL to the column, which means an error will be
 // thrown from the DB when the query is executed if the column is null. If you want to check that a value
 // is not null before querying the DB, look at the validations section below.
 // 设置运行allowNull为false将会添加 NOT NUll 到 column上，这意味着一个执行这个column为null的时候会抛出错误异常。如果你想检查这个值是不是null在数据库查询钱，请看后边的章节
 title: { type: Sequelize.STRING, allowNull: false},

 // Creating two objects with the same value will throw an error. The unique property can be either a
 // boolean, or a string. If you provide the same string for multiple columns, they will form a
 // composite unique key.
 //创建两个相同值的对象将会抛出一个错误。unique属性可以是一个boolean值或者一个string。如果你提供对多个columns为一个stirng，他们将会是一个composite unique key
 someUnique: {type: Sequelize.STRING, unique: true},
 uniqueOne: { type: Sequelize.STRING,  unique: 'compositeIndex'},
 uniqueTwo: { type: Sequelize.INTEGER, unique: 'compositeIndex'}

 // The unique property is simply a shorthand to create a unique index.
 //这个unique属性简写创建一个unique 索引
 someUnique: {type: Sequelize.STRING, unique: true}
 // It's exactly the same as creating the index in the model's options.
 //这样是完全一样的为一个模型创建索引
 {someUnique: {type: Sequelize.STRING}},
 {indexes: [{unique: true, fields: ['someUnique']}]}

 // Go on reading for further information about primary keys
 //现在阅读更多关于primary keys
 identifier: { type: Sequelize.STRING, primaryKey: true},

 // autoIncrement can be used to create auto_incrementing integer columns
 //自动递增可以在类型为integer columns创建auto_incrementing
 incrementMe: { type: Sequelize.INTEGER, autoIncrement: true },

 // Comments can be specified for each field for MySQL and PG
 // Mysql 或者 PG可以指定每个columns的注释
 hasComment: { type: Sequelize.INTEGER, comment: "I'm a comment!" },

 // You can specify a custom field name via the "field" attribute:
 //你可以指定一个自定义的字段名通过“field”属性
 fieldWithUnderscores: { type: Sequelize.STRING, field: "field_with_underscores" },

 // It is possible to create foreign keys:
 //创建外键也是可以的
 bar_id: {
   type: Sequelize.INTEGER,

   references: {
     // This is a reference to another model
     //这个是引用其他model
     model: Bar,

     // This is the column name of the referenced model
     //这个是引用模型的column名字
     key: 'id',

     // This declares when to check the foreign key constraint. PostgreSQL only.
     //在检查的时候宣告外键的约束，只有PostgreSQL支持
     deferrable: Sequelize.Deferrable.INITIALLY_IMMEDIATE
   }
 }
})
```
//可选注释也可以用在table上，详见[model configuration][0]
The comment option can also be used on a table, see [model configuration][0]

## Data types
## 数据类型

Below are some of the datatypes supported by sequelize. For a full and updated list, see [DataTypes](../api/datatypes).

以下是一些sequelize支持的类型。全部的列表见[DataTypes](../api/datatypes).

```js
Sequelize.STRING                      // VARCHAR(255)
Sequelize.STRING(1234)                // VARCHAR(1234)
Sequelize.STRING.BINARY               // VARCHAR BINARY
Sequelize.TEXT                        // TEXT

Sequelize.INTEGER                     // INTEGER
Sequelize.BIGINT                      // BIGINT
Sequelize.BIGINT(11)                  // BIGINT(11)

Sequelize.FLOAT                       // FLOAT
Sequelize.FLOAT(11)                   // FLOAT(11)
Sequelize.FLOAT(11, 12)               // FLOAT(11,12)

Sequelize.REAL                        // REAL        PostgreSQL only.
Sequelize.REAL(11)                    // REAL(11)    PostgreSQL only.
Sequelize.REAL(11, 12)                // REAL(11,12) PostgreSQL only.

Sequelize.DOUBLE                      // DOUBLE
Sequelize.DOUBLE(11)                  // DOUBLE(11)
Sequelize.DOUBLE(11, 12)              // DOUBLE(11,12)

Sequelize.DECIMAL                     // DECIMAL
Sequelize.DECIMAL(10, 2)              // DECIMAL(10,2)

Sequelize.DATE                        // DATETIME for mysql / sqlite, TIMESTAMP WITH TIME ZONE for postgres
Sequelize.BOOLEAN                     // TINYINT(1)

Sequelize.ENUM('value 1', 'value 2')  // An ENUM with allowed values 'value 1' and 'value 2'
Sequelize.ARRAY(Sequelize.TEXT)       // Defines an array. PostgreSQL only.

Sequelize.JSON                        // JSON column. PostgreSQL only.
Sequelize.JSONB                       // JSONB column. PostgreSQL only.

Sequelize.BLOB                        // BLOB (bytea for PostgreSQL)
Sequelize.BLOB('tiny')                // TINYBLOB (bytea for PostgreSQL. Other options are medium and long)

Sequelize.UUID                        // UUID datatype for PostgreSQL and SQLite, CHAR(36) BINARY for MySQL (use defaultValue: Sequelize.UUIDV1 or Sequelize.UUIDV4 to make sequelize generate the ids automatically)

Sequelize.RANGE(Sequelize.INTEGER)    // Defines int4range range. PostgreSQL only.
Sequelize.RANGE(Sequelize.BIGINT)     // Defined int8range range. PostgreSQL only.
Sequelize.RANGE(Sequelize.DATE)       // Defines tstzrange range. PostgreSQL only.
Sequelize.RANGE(Sequelize.DATEONLY)   // Defines daterange range. PostgreSQL only.
Sequelize.RANGE(Sequelize.DECIMAL)    // Defines numrange range. PostgreSQL only.

Sequelize.ARRAY(Sequelize.RANGE(Sequelize.DATE)) // Defines array of tstzrange ranges. PostgreSQL only.
```

The BLOB data type allows you to insert data both as strings and as buffers. When you do a find or findAll on a model which has a BLOB column. that data will always be returned as a buffer.

If you are working with the PostgreSQL TIMESTAMP WITHOUT TIME ZONE and you need to parse it to a different timezone, please use the pg library's own parser:

BLOB数据类型运行你插入的数据作为strings和buffers。当你在模型上的一个BLOB column上使用find or findAll。将会总是作为buffer返回

```js
require('pg').types.setTypeParser(1114, function(stringValue) {
  return new Date(stringValue + "+0000");
  // e.g., UTC offset. Use any offset that you would like.
  // e.g., UTC 偏移值。你可以喜欢用任意的偏移值。
});
```

In addition to the type mentioned above, integer, bigint, float ®®and double also support unsigned and zerofill properties, which can be combined in any order:
Be aware that this does not apply for PostgreSQL!

除了上面的类型，integer, bigint, float ®®and double 也支持unsigned and zerofill 属性，可以以任意顺序结合
要注意的是这些属性不支持PostgreSQL

```js
Sequelize.INTEGER.UNSIGNED              // INTEGER UNSIGNED
Sequelize.INTEGER(11).UNSIGNED          // INTEGER(11) UNSIGNED
Sequelize.INTEGER(11).ZEROFILL          // INTEGER(11) ZEROFILL
Sequelize.INTEGER(11).ZEROFILL.UNSIGNED // INTEGER(11) UNSIGNED ZEROFILL
Sequelize.INTEGER(11).UNSIGNED.ZEROFILL // INTEGER(11) UNSIGNED ZEROFILL
```

_The examples above only show integer&comma; but the same can be done with bigint and float_
上面的例子只显示了integer，对于bigint 和 float 同样可以

Usage in object notation:
使用在对象的标记上：
```js
// for enums:
sequelize.define('model', {
  states: {
    type:   Sequelize.ENUM,
    values: ['active', 'pending', 'deleted']
  }
})
```

## Deferrable
## 可延缓
When you specify a foreign key column it is optionally possible to declare the deferrable
type in PostgreSQL. The following options are available:

在PostgreSQL，你可以指定外键column是可选可以描述成deferrable属性，以下配置是可以的

```js
// Defer all foreign key constraint check to the end of a transaction
// 定义所有的外键约束在事务结束的时候检查
Sequelize.Deferrable.INITIALLY_DEFERRED

// Immediately check the foreign key constraints
// 立即检查外键约束
Sequelize.Deferrable.INITIALLY_IMMEDIATE

// Don't defer the checks at all
// 不延迟检查
Sequelize.Deferrable.NOT
```

The last option is the default in PostgreSQL and won't allow you to dynamically change
the rule in a transaction. See [the transaction section](transactions/#options) for further information.

最好一个选项是PostgreSQL默认的配置，它不允许你在事务的时候动态修改。更多信息见[the transaction section](transactions/#options)

## Getters & setters

It is possible to define 'object-property' getters and setter functions on your models, these can be used both for 'protecting' properties that map to database fields and for defining 'pseudo' properties.

Getters and Setters can be defined in 2 ways (you can mix and match these 2 approaches):

* as part of a single property definition
* as part of a model options

**N.B:** If a getter or setter is defined in both places then the function found in the relevant property definition will always take precedence.


定义'object-property'的getter和setter是可以的，这些可以使用'protecting'属性数据库字段和定义'pseudo'属性。

Getters and Setters可以以两种方式定义（你可以混合使用这2种方式）
* 作为单一属性的定义
* 作为模型的选项

### Defining as part of a property

### 作为一个属性定义
```js
var Employee = sequelize.define('Employee', {
  name:  {
    type     : Sequelize.STRING,
    allowNull: false,
    get      : function()  {
      var title = this.getDataValue('title');
      // 'this' allows you to access attributes of the instance
      // ‘this’ 运行你放访问实例的属性
      return this.getDataValue('name') + ' (' + title + ')';
    },
  },
  title: {
    type     : Sequelize.STRING,
    allowNull: false,
    set      : function(val) {
      this.setDataValue('title', val.toUpperCase());
    }
  }
});

Employee
  .create({ name: 'John Doe', title: 'senior engineer' })
  .then(function(employee) {
    console.log(employee.get('name')); // John Doe (SENIOR ENGINEER)
    console.log(employee.get('title')); // SENIOR ENGINEER
  })
```

### Defining as part of the model options

### 作用模型的选项定义

Below is an example of defining the getters and setters in the model options. The `fullName` getter,  is an example of how you can define pseudo properties on your models - attributes which are not actually part of your database schema. In fact, pseudo properties can be defined in two ways: using model getters, or by using a column with the [`VIRTUAL` datatype](../api/datatypes#virtual). Virtual datatypes can have validations, while getters for virtual attributes cannot.

Note that the `this.firstname` and `this.lastname` references in the `fullName` getter function will trigger a call to the respective getter functions. If you do not want that then use the `getDataValue()` method to access the raw value (see below).

下面的例子是作为模型的选项定义getters and setters。 `fullName` getter 这个例子是让你可以怎样定义一个pseudo属性在你的模型上。这个属性实际上并不在你的数据库定义上。实际上，pseudo属性可以有两种方式定义：使用模型的getters，或者使用column [`VIRTUAL` datatype](../api/datatypes#virtual). Virtual数据类型可以使用验证，在getters作为virtual属性的时候就不能


注意`this.firstname` and `this.lastname`在`fullName` getter引用将会触发各自的getter函数。如果你不想可以使用`getDataValue()`方法访问原始的数据（往下看）。

```js
var Foo = sequelize.define('Foo', {
  firstname: Sequelize.STRING,
  lastname: Sequelize.STRING
}, {
  getterMethods   : {
    fullName       : function()  { return this.firstname + ' ' + this.lastname }
  },

  setterMethods   : {
    fullName       : function(value) {
        var names = value.split(' ');

        this.setDataValue('firstname', names.slice(0, -1).join(' '));
        this.setDataValue('lastname', names.slice(-1).join(' '));
    },
  }
});
```

### Helper functions for use inside getter and setter definitions

* retrieving an underlying property value - always use `this.getDataValue()`

* 取回一个表面的属性值总是使用`this.getDataValue()`

```js
/* a getter for 'title' property */
/*一个获取'title'属性值的getter*/
function() {
    return this.getDataValue('title');
}
```

* setting an underlying property value - always use `this.setDataValue()`
* 设置一个表面的属性值总是使用`this.setDataValue()`

```js
/* a setter for 'title' property */
/*一个设置'title'属性值的setting *／
function(title) {
    return this.setDataValue('title', title.toString().toLowerCase());
}
```

**N.B:** It is important to stick to using the `setDataValue()` and `getDataValue()` functions (as opposed to accessing the underlying "data values" property directly) - doing so protects your custom getters and setters from changes in the underlying model implementations.

**注意:**坚持使用`setDataValue()` and `getDataValue()`(立即的访问底层"data values"属性)是很重要的，这样可以保护来自于自定义的getters and setters对于值变化的实现

## Validations
## 验证

Model validations, allow you to specify format&sol;content&sol;inheritance validations for each attribute of the model&period;

Validations are automatically run on `create`, `update` and `save`. You can also call `validate()` to manually validate an instance.

The validations are implemented by [validator.js][3].

```js
var ValidateMe = sequelize.define('Foo', {
  foo: {
    type: Sequelize.STRING,
    validate: {
      is: ["^[a-z]+$",'i'],     // will only allow letters
      is: /^[a-z]+$/i,          // same as the previous example using real RegExp
      not: ["[a-z]",'i'],       // will not allow letters
      isEmail: true,            // checks for email format (foo@bar.com)
      isUrl: true,              // checks for url format (http://foo.com)
      isIP: true,               // checks for IPv4 (129.89.23.1) or IPv6 format
      isIPv4: true,             // checks for IPv4 (129.89.23.1)
      isIPv6: true,             // checks for IPv6 format
      isAlpha: true,            // will only allow letters
      isAlphanumeric: true      // will only allow alphanumeric characters, so "_abc" will fail
      isNumeric: true           // will only allow numbers
      isInt: true,              // checks for valid integers
      isFloat: true,            // checks for valid floating point numbers
      isDecimal: true,          // checks for any numbers
      isLowercase: true,        // checks for lowercase
      isUppercase: true,        // checks for uppercase
      notNull: true,            // won't allow null
      isNull: true,             // only allows null
      notEmpty: true,           // don't allow empty strings
      equals: 'specific value', // only allow a specific value
      contains: 'foo',          // force specific substrings
      notIn: [['foo', 'bar']],  // check the value is not one of these
      isIn: [['foo', 'bar']],   // check the value is one of these
      notContains: 'bar',       // don't allow specific substrings
      len: [2,10],              // only allow values with length between 2 and 10
      isUUID: 4,                // only allow uuids
      isDate: true,             // only allow date strings
      isAfter: "2011-11-05",    // only allow date strings after a specific date
      isBefore: "2011-11-05",   // only allow date strings before a specific date
      max: 23,                  // only allow values
      min: 23,                  // only allow values >= 23
      isArray: true,            // only allow arrays
      isCreditCard: true,       // check for valid credit card numbers

      // custom validations are also possible:
      isEven: function(value) {
        if(parseInt(value) % 2 != 0) {
          throw new Error('Only even values are allowed!')
        // we also are in the model's context here, so this.otherField
        // would get the value of otherField if it existed
        }
      }
    }
  }
})
```

Note that where multiple arguments need to be passed to the built-in validation functions&comma; the arguments to be passed must be in an array&period; But if a single array argument is to be passed&comma; for instance an array of acceptable strings for `isIn`, this will be interpreted as multiple string arguments instead of one array argument&period; To work around this pass a single-length array of arguments&comma; such as `[['one', 'two']]` as shown above&period;

To use a custom error message instead of that provided by validator.js&comma; use an object instead of the plain value or array of arguments&comma; for example a validator which needs no argument can be given a custom message with

```js
isInt: {
  msg: "Must be an integer number of pennies"
}
```

or if arguments need to also be passed add an`args`property&colon;

```js
isIn: {
  args: [['en', 'zh']],
  msg: "Must be English or Chinese"
}
```

When using custom validator functions the error message will be whatever message the thrown`Error`object holds&period;

See [the validator.js project][3] for more details on the built in validation methods&period;

**Hint&colon; **You can also define a custom function for the logging part&period; Just pass a function&period; The first parameter will be the string that is logged&period;

### Validators and `allowNull`

If a particular field of a model is set to allow null &lpar;with `allowNull: true`&rpar; and that value has been set to `null` &comma; its validators do not run&period; This means you can&comma; for instance&comma; have a string field which validates its length to be at least 5 characters&comma; but which also allows`null`&period;

### Model validations

Validations can also be defined to check the model after the field-specific validators&period; Using this you could&comma; for example&comma; ensure either neither of `latitude` and `longitude` are set or both&comma; and fail if one but not the other is set&period;

Model validator methods are called with the model object's context and are deemed to fail if they throw an error&comma; otherwise pass&period; This is just the same as with custom field-specific validators&period;

Any error messages collected are put in the validation result object alongside the field validation errors&comma; with keys named after the failed validation method's key in the `validate` option object&period; Even though there can only be one error message for each model validation method at any one time&comma; it is presented as a single string error in an array&comma; to maximize consistency with the field errors&period;

An example&colon;

```js
var Pub = Sequelize.define('Pub', {
  name: { type: Sequelize.STRING },
  address: { type: Sequelize.STRING },
  latitude: {
    type: Sequelize.INTEGER,
    allowNull: true,
    defaultValue: null,
    validate: { min: -90, max: 90 }
  },
  longitude: {
    type: Sequelize.INTEGER,
    allowNull: true,
    defaultValue: null,
    validate: { min: -180, max: 180 }
  },
}, {
  validate: {
    bothCoordsOrNone: function() {
      if ((this.latitude === null) !== (this.longitude === null)) {
        throw new Error('Require either both latitude and longitude or neither')
      }
    }
  }
})
```

In this simple case an object fails validation if either latitude or longitude is given&comma; but not both&period; If we try to build one with an out-of-range latitude and nolongitude, `raging_bullock_arms.validate()` might return

```js
{
  'latitude': ['Invalid number: latitude'],
  'bothCoordsOrNone': ['Require either both latitude and longitude or neither']
}
```

## Configuration

You can also influence the way Sequelize handles your column names&colon;

```js
var Bar = sequelize.define('Bar', { /* bla */ }, {
  // don't add the timestamp attributes (updatedAt, createdAt)
  timestamps: false,

  // don't delete database entries but set the newly added attribute deletedAt
  // to the current date (when deletion was done). paranoid will only work if
  // timestamps are enabled
  paranoid: true,

  // don't use camelcase for automatically added attributes but underscore style
  // so updatedAt will be updated_at
  underscored: true,

  // disable the modification of tablenames; By default, sequelize will automatically
  // transform all passed model names (first parameter of define) into plural.
  // if you don't want that, set the following
  freezeTableName: true,

  // define the table's name
  tableName: 'my_very_custom_table_name'
})
```

If you want sequelize to handle timestamps, but only want some of them, or want your timestamps to be called something else, you can override each column individually:

```js
var Foo = sequelize.define('Foo',  { /* bla */ }, {
  // don't forget to enable timestamps!
  timestamps: true,

  // I don't want createdAt
  createdAt: false,

  // I want updatedAt to actually be called updateTimestamp
  updatedAt: 'updateTimestamp'

  // And deletedAt to be called destroyTime (remember to enable paranoid for this to work)
  deletedAt: 'destroyTime',
  paranoid: true
})
```

You can also change the database engine&comma; e&period;g&period; to MyISAM&period; InnoDB is the default.

```js
var Person = sequelize.define('Person', { /* attributes */ }, {
  engine: 'MYISAM'
})

// or globally
var sequelize = new Sequelize(db, user, pw, {
  define: { engine: 'MYISAM' }
})
```

Finaly you can specify a comment for the table in MySQL and PG

```js
var Person = sequelize.define('Person', { /* attributes */ }, {
  comment: "I'm a table comment!"
})
```

## Import

You can also store your model definitions in a single file using the `import` method&period; The returned object is exactly the same as defined in the imported file's function&period; Since `v1:5.0` of Sequelize the import is cached&comma; so you won't run into troubles when calling the import of a file twice or more often&period;

```js
// in your server file - e.g. app.js
var Project = sequelize.import(__dirname + "/path/to/models/project")

// The model definition is done in /path/to/models/project.js
// As you might notice, the DataTypes are the very same as explained above
module.exports = function(sequelize, DataTypes) {
  return sequelize.define("Project", {
    name: DataTypes.STRING,
    description: DataTypes.TEXT
  })
}
```

The `import` method can also accept a callback as an argument&period;

```js
sequelize.import('Project', function(sequelize, DataTypes) {
  return sequelize.define("Project", {
    name: DataTypes.STRING,
    description: DataTypes.TEXT
  })
})
```

## Database synchronization

When starting a new project you won't have a database structure and using Sequelize you won't need to&period; Just specify your model structures and let the library do the rest&period; Currently supported is the creation and deletion of tables&colon;

```js
// Create the tables:
Project.sync()
Task.sync()

// Force the creation!
Project.sync({force: true}) // this will drop the table first and re-create it afterwards

// drop the tables:
Project.drop()
Task.drop()

// event handling:
Project.[sync|drop]().then(function() {
  // ok ... everything is nice!
}).catch(function(error) {
  // oooh, did you enter wrong database credentials?
})
```

Because synchronizing and dropping all of your tables might be a lot of lines to write&comma; you can also let Sequelize do the work for you&colon;

```js
// Sync all models that aren't already in the database
sequelize.sync()

// Force sync all models
sequelize.sync({force: true})

// Drop all tables
sequelize.drop()

// emit handling:
sequelize.[sync|drop]().then(function() {
  // woot woot
}).catch(function(error) {
  // whooops
})
```

Because `.sync({ force: true })` is destructive operation, you can use `match` option as an additional safety check.
`match` option tells sequelize to match a regex against the database name before syncing - a safety check for cases
where `force: true` is used in tests but not live code.

```js
// This will run .sync() only if database name ends with '_test'
sequelize.sync({ force: true, match: /_test$/ });
```

## Expansion of models

Sequelize allows you to pass custom methods to a model and its instances&period; Just do the following&colon;

```js
var Foo = sequelize.define('Foo', { /* attributes */}, {
  classMethods: {
    method1: function(){ return 'smth' }
  },
  instanceMethods: {
    method2: function() { return 'foo' }
  }
})

// Example:
Foo.method1()
Foo.build().method2()
```

Of course you can also access the instance's data and generate virtual getters&colon;

```js
var User = sequelize.define('User', { firstname: Sequelize.STRING, lastname: Sequelize.STRING }, {
  instanceMethods: {
    getFullname: function() {
      return [this.firstname, this.lastname].join(' ')
    }
  }
})

// Example:
User.build({ firstname: 'foo', lastname: 'bar' }).getFullname() // 'foo bar'
```

You can also set custom methods to all of your models during the instantiation&colon;

```js
var sequelize = new Sequelize('database', 'username', 'password', {
  // Other options during the initialization could be here
  define: {
    classMethods: {
      method1: function() {},
      method2: function() {}
    },
    instanceMethods: {
      method3: function() {}
    }
  }
})

// Example:
var Foo = sequelize.define('Foo', { /* attributes */});
Foo.method1()
Foo.method2()
Foo.build().method3()
```

### Indexes
Sequelize supports adding indexes to the model definition which will be created during `Model.sync()` or `sequelize.sync`.

```js
sequelize.define('User', {}, {
  indexes: [
    // Create a unique index on email
    {
      unique: true,
      fields: ['email']
    },

    // Creates a gin index on data with the jsonb_path_ops operator
    {
      fields: ['data'],
      using: 'gin',
      operator: 'jsonb_path_ops'
    },

    // By default index name will be [table]_[fields]
    // Creates a multi column partial index
    {
      name: 'public_by_author',
      fields: ['author', 'status'],
      where: {
        status: 'public'
      }
    },

    // A BTREE index with a ordered field
    {
      name: 'title_index',
      method: 'BTREE',
      fields: ['author', {attribute: 'title', collate: 'en_US', order: 'DESC', length: 5}]
    }
  ]
})
```


[0]: #configuration
[3]: https://github.com/chriso/validator.js
[5]: /docs/latest/misc#asynchronicity
[6]: https://github.com/petkaantonov/bluebird/blob/master/API.md#spreadfunction-fulfilledhandler--function-rejectedhandler----promise



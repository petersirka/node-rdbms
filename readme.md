Simple node.js RDMBS REST client
================================

- designed for [www.partialjs.com](http://partialjs.com)

***

## NPM partial-rdbms

- create on your desktop empty directory with name: website
- open terminal and find this directory: cd /Desktop/website/
- write and run on terminal:

```text
$ npm install partial-rdbms
```

## Methods SQL Server

```js
/*  
    Constructor
    @url {String}
    @connectionString {String}
    @autolock {Boolean} :: optional, default true
*/
SQLServer(url, connectionString, autolock);
 
/*
    Execute
    @sql {String}
    @params {Object}
    @callback {Function}
    return {SQLServer}
*/
SQLServer.execute(sql, params, callback);
 
/*  
    Scalar
    @sql {String}
    @params {Object}
    @callback {Function}
    @cache {Boolean} :: optional, default false
    return {SQLServer}
*/
SQLServer.scalar(sql, params, callback, cache)
 
/*
    Reader
    @sql {String}
    @params {Object}
    @callback {Function}
    @cache {Boolean} :: optional, default false
    return {SQLServer}
*/
SQLServer.reader(sql, params, callback, cache);
 
/*
    Count
    @sql {String}
    @builder {QueryBuilder}
    @callback {Function}
    @cache {Boolean} :: optional, default false
    return {SQLServer}
*/
SQLServer.count(schema, builder, callback, cache);
 
/*
    Find by primary key
    @schema {String}
    @value {Object}
    @callback {Function}
    @without {String array} :: Without columns name
    @cache {Boolean} :: optional, default false
    return {SQLServer}
*/
SQLServer.findPK(schema, value, callback, without, cache);
 
/*
    Find all
    @schema {String}
    @builder {QueryBuilder}
    @order {OrderBuilder}
    @take {Number}
    @skip {Number}
    @callback {Function}
    @without {String array} :: Without columns name
    @cache {Boolean} :: optional, default false
    return {SQLServer}
*/
SQLServer.findAll(schema, builder, order, take, skip, callback, without, cache);
 
/*
    Find TOP
    @top {Number}
    @schema {String}
    @builder {QueryBuilder}
    @order {OrderBuilder}
    @callback {Function}
    @without {String array} :: Without columns name
    @cache {Boolean} :: optional, default false
    return {SQLServer}
*/
SQLServer.findTop(top, schema, builder, order, callback, without, cache);
 
/*
    Insert record
    @schema {String}
    @value {Object}
    @callback {Function}
    @without {String array} :: Without columns name
    return {SQLServer}
*/
SQLServer.insert(schema, value, callback, without);
 
/*
    Update record
    @schema {String}
    @value {Object}
    @callback {Function}
    @without {String array} :: Without columns name
    return {SQLServer}
*/
SQLServer.update(schema, value, callback, without);
 
/*
    Delete record
    @schema {String}
    @value {Object}
    @callback {Function}
    return {SQLServer}
*/
SQLServer.delete(schema, value, callback);
```

***

## Methods MySQL

```js
/*
    Constructor
    @url {String}
    @connectionString {String}
*/
MySQL(url, connectionString);
 
/*
    Execute
    @sql {String}
    @params {Object}
    @callback {Function}
    return {MySQL}
*/
MySQL.execute(sql, params, callback);
 
/*  
    Scalar
    @sql {String}
    @params {Object}
    @callback {Function}
    @cache {Boolean} :: optional, default false
    return {MySQL}
*/
MySQL.scalar(sql, params, callback, cache)
 
/*
    Reader
    @sql {String}
    @params {Object}
    @callback {Function}
    @cache {Boolean} :: optional, default false
    return {MySQL}
*/
MySQL.reader(sql, params, callback, cache);
 
/*
    Count
    @sql {String}
    @builder {QueryBuilder}
    @callback {Function}
    @cache {Boolean} :: optional, default false
    return {MySQL}
*/
MySQL.count(schema, builder, callback, cache);
 
/*
    Find by primary key
    @schema {String}
    @value {Object}
    @callback {Function}
    @without {String array} :: Without columns name
    @cache {Boolean} :: optional, default false
    return {MySQL}
*/
MySQL.findPK(schema, value, callback, without, cache);
 
/*
    Find all
    @schema {String}
    @builder {QueryBuilder}
    @order {OrderBuilder}
    @take {Number}
    @skip {Number}
    @callback {Function}
    @without {String array} :: Without columns name
    @cache {Boolean} :: optional, default false
    return {MySQL}
*/
MySQL.findAll(schema, builder, order, take, skip, callback, without, cache);
 
/*
    Find TOP
    @top {Number}
    @schema {String}
    @builder {QueryBuilder}
    @order {OrderBuilder}
    @callback {Function}
    @without {String array} :: Without columns name
    @cache {Boolean} :: optional, default false
    return {MySQL}
*/
MySQL.findTop(top, schema, builder, order, callback, without, cache);
 
/*
    Insert record
    @schema {String}
    @value {Object}
    @callback {Function}
    @without {String array} :: Without columns name
    return {MySQL}
*/
MySQL.insert(schema, value, callback, without);
 
/*
    Update record
    @schema {String}
    @value {Object}
    @callback {Function}
    @without {String array} :: Without columns name
    return {MySQL}
*/
MySQL.update(schema, value, callback, without);
 
/*
    Delete record
    @schema {String}
    @value {Object}
    @callback {Function}
    return {MySQL}
*/
MySQL.delete(schema, value, callback);
```

***

## Simple ORM via HTTP-RDBMS

- supports parameters (resolve for SQL injection)
- supports schema builder
- supports query builder
- supports order builder
- supports paging

```js
var builders = new requiere('partial.js/builders');
var rdbms = new requiere('partial.js/rdbms');
 
var db = new rdbms.SQLServer('http://myrdbms/sqlserver/', 'Data Source=;Database=;Uid=;Pwd=;');
 
// or
 
var db = new rdbms.MySQL('http://myrdbms/mysql/', 'Server=;Database=;Uid=;Pwd=;');
 
builders.schema('tbl_user', {
    Id: 'int',
    FirstName: 'string(50)',
    LastName: 'string(50)',
    Age: 'int'
}, 'Id');
 
var newUser = {
    FirstName: 'Peter',
    LastName: 'Sirka',
    Age: 28
};
 
db.insert('tbl_user', newUser, function(data) {
 
    console.log(data.Id);
    db.delete('tbl_user', newUser);
 
});
 
var where = new builders.QueryBuilder();
var order = new builders.OrderBuilder();
 
order.asc('Id').desc('FistName');
where.addValue('Id', '>', 10).addOperator('AND').addValue('FistName', '=', 'Peter');
// Query:
// SQL Server: Id > @a AND FirstName = @b
// MySQL: Id > ? AND FirstName = ?
 
db.findTop(10, 'tbl_user', where, order, function(data) {
    console.log(data);
});
 
db.execute('UPDATE tbl_user SET Price=20 WHERE Id BETWEEN {a} AND {b}', { a: 10, b: 20 });
// Query:
// SQL Server: UPDATE tbl_user SET Price=20 WHERE Id BETWEEN @a AND @b
// MySQL: UPDATE tbl_user SET Price=20 WHERE Id BETWEEN ? AND ?
 
db.scalar('SELECT COUNT(*) FROM tbl_user', null, null, function(err, data) {
    console.log(data);
});
 
// multiple recordset
db.reader('SELECT Id, LastName FORM tbl_user; SELECT Id, FirstName FROM tbl_user', function(err, data) {
    // data[0] == []
    // data[1] == []
});
 
db.count('tbl_user', function(err, data) {
    // data.value
});
 
db.count('tbl_user', where, function(err, data) {
    // data.value
});
```

***

## The MIT License

Copyright (c) 2012-2013 Peter Širka <petersirka@gmail.com>

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the 'Software'), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

## Contact

[www.petersirka.sk](http://www.petersirka.sk)

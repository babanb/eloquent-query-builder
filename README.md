# Eloquent Query Builder

eloquent-query-builder is a promise-based Node.js query builder for Postgres, MySQL, MariaDB, SQLite and Microsoft SQL Server. It features solid query building methods like eloquent queries in php Laravel framework

## Install
```
using yarn

$ yarn add eloquent-query-builder

using npm 

$ npm install --save eloquent-query-builder

#install sequelize 
$ npm install --save sequelize

# And one of the following: 
$ npm install --save pg pg-hstore # Postgres 
$ npm install --save mysql2
$ npm install --save mariadb
$ npm install --save sqlite3
$ npm install --save tedious # Microsoft SQL Server

```
## How to use
```
const Sequelize = require('sequelize');
var queryBuilder = require('eloquent-query-builder');
 const options = {
            host: '127.0.0.1',
            logging: false,
            dialect: 'mysql',
        }

const sequelize = new Sequelize(dbName, dbUser, dbPassword, options);
const DBO = new queryBuilder(sequelize);


let result = await DBO.table('users').get();

# which executes query like 
select * from `users`

```
The select method defines the fields to be selected for a given query.

```
 await DBO.table('users').select('firstname','lastname').get();
 //or 
 await DBO.table('users').select('firstname,lastname').get();
 //or 
 await DBO.table('users').select('*').get();

```

## Where Clauses
Query builder offers a bunch of dynamic methods to add where clauses. Also, it supports sub-queries by passing a closure or another query instead of the actual value.

### where

```
const users = await DBO.table('users').where('id', 1).get();

```
Also, you can define the comparison operator to the where clause.

```
const users = await DBO.table('users').where('age','>', 18).get();

// where with like 

const users = await DBO.table('users').where('city','like', `"del%"`).get();

```
### where ( with callback )

You can also add a callback to the where clause. Callback outputs a little different SQL query by grouping all the where clauses.

```
await DBO.table('users').where('id',1).where(funtion(qry){
    qry.orWhere('id',4')
}).get();

// it will execute query like 
select * from `users` where (`id` = 1 or `id` = 4)

```

### orWhere

```
const users = await DBO.table('users').where('age',18).orWhere('age', 20).get();
```

### orWhereNull

```
const users = await DBO.table('users').where('age',18).orWhereNull('age').get();
```


### orWhereNotNull

```
const users = await DBO.table('users').where('age',18)..orWhereNotNull('age').get();
```

### whereNot

```
const users = await DBO.table('users').whereNot('age', 18).get();
```

### orWhereNot

```
const users = await DBO.table('users').whereNot('age', 18).orWhereNot('age', 20).get();
```

### whereIn

```
const users = await DBO.table('users').whereIn('age', [16,18]).get();
```


### whereNotIn

```
const users = await DBO.table('users').whereNotIn('age', [16,18]).get();
```


### whereNull

```
const users = await DBO.table('users').whereNull('age').get();
```


### whereNotNull

```
const users = await DBO.table('users').whereNotNull('age').get();
```


### whereExists

```
const users = await DBO.table('users').whereExists(function(qry){
    qry.from('address').where('users.id', 'address.user_id)
}).get();

```


### whereNotExists

```
const users = await DBO.table('users').whereNotExists(function(qry){
    qry.from('address').where('users.id', 'address.user_id)
}).get();

```

### whereBetween

```
const users = await DBO.table('users').whereBetween('age',[18,20]).get();

// or 

const users = await DBO.table('users').whereBetween('age',18, 20).get();

```
### whereNotBetween

```
const users = await DBO.table('users').whereNotBetween('age',[18,20]).get();

// or 

const users = await DBO.table('users').whereNotBetween('age',18, 20).get();

```

## Joins 

### inner join 

```
await DBO.table('users').join('accounts', 'user.id', 'accounts.user_id').get();

// this will execute

`select * from users inner join accounts on user.id=accounts.user_id`

```
### left join 

```
await DBO.table('users').leftJoin('accounts', 'user.id', 'accounts.user_id').get();

```
### right join 

```
await DBO.table('users').rightJoin('accounts', 'user.id', 'accounts.user_id').get();

```
### left outer join 

```
await DBO.table('users').leftOuterJoin('accounts', 'user.id', 'accounts.user_id').get();

```
### right outer join 

```
await DBO.table('users').rightOuterJoin('accounts', 'user.id', 'accounts.user_id').get();

```
### outer join 

```
await DBO.table('users').outerJoin('accounts', 'user.id', 'accounts.user_id').get();

```
### full outer join 

```
await DBO.table('users').fullOuterJoin('accounts', 'user.id', 'accounts.user_id').get();

```
### corss join 

```
await DBO.table('users').crossJoin('accounts', 'user.id', 'accounts.user_id').get();

```

## Ordering and Limits

### distinct

```
await DBO.table('users').distinct('age').get();

```
### groupBy

```
await DBO.table('users').groupBy('age').get();

// or 

await DBO.table('users').groupBy('age','city').get();

```
### orderBy

```
await DBO.table('users').orderBy('age').get();

//or

await DBO.table('users').orderBy('age','desc').get();

```
### having

```
await DBO.table('users').groupBy('age').having('age', '>', 18).get();

```
### offset/limit

```
await DBO.table('users').offset(11).limit(10).get();

```


## pagination

### paginate

```
await DBO.table('users').paginate(1, 10);

#results
{
  pages: {
    pages: 20,
    currentPage: 1,
    perPage: 10,
    lastPage: 20,
  },
  result: [{...}]
}
```

## Aggregates

### count

```
await DBO.table('users').count();

```
### countDistinct

```
await DBO.table('users').countDistinct('age');

```

### min

```
await DBO.table('users').min('age');

```

### max

```
await DBO.table('users').max('age');

```

### sum

```
await DBO.table('cart').sum('total');

```
### avg

```
await DBO.table('users').avg('age');

```

## Helpers

### list
```
await DBO.table('users').list('email');

```
### first
```
await DBO.table('users').first();

```
### rawQuery
```
await DBO.rawQuery('select * from users');

```

## Sub Queries

```
const subquery = Database
  .table('accounts')
  .where('account_name', 'somename')
  .select('account_name').getRaw();

const users = DBO.table('users').whereIn('id',subquery).get();

```
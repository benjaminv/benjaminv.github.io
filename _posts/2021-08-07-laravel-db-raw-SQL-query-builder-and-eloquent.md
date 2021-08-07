---
  layout: post
  title: Laravel Database Raw SQL, Query Builder and Eloquent ORM
  featured: true
  author: ben
  tags: [laravel, db, sql, query builder, eloquent]
  image: '/images/posts/laravel-db-raw-sql-query-builder-eloquent-orm.png'
---

[Contents]

- [1. Raw SQL via DB Facade](#1-raw-sql-via-db-facade)
- [2. Chaining with the Query Builder](#2-chaining-with-the-query-builder)
  - [2.1. Constraining Methods](#21-constraining-methods)
    - [2.1.1. Examples](#211-examples)
      - [2.1.1.1. `select()`](#2111-select)
      - [2.1.1.2. `where()`](#2112-where)
      - [2.1.1.3. `orWhere()`](#2113-orwhere)
        - [2.1.1.3.1. Caution of using multiple `orWhere()`](#21131-caution-of-using-multiple-orwhere)
      - [2.1.1.4. `whereBetween()`](#2114-wherebetween)
      - [2.1.1.5. `whereIn()`](#2115-wherein)
      - [2.1.1.6. `whereRaw()`](#2116-whereraw)
      - [2.1.1.7. `whereExist()`](#2117-whereexist)
      - [2.1.1.8. `distinct()`](#2118-distinct)
  - [2.2. Modifying Methods](#22-modifying-methods)
    - [2.2.1. Examples](#221-examples)
      - [2.2.1.1. `orderBy(colName, direction)`](#2211-orderbycolname-direction)
      - [2.2.1.2. `groupBy()`](#2212-groupby)
      - [2.2.1.3. `skip()` and `take()`](#2213-skip-and-take)
  - [2.3. Conditional Methods](#23-conditional-methods)
    - [2.3.1. Examples](#231-examples)
      - [2.3.1.1. `when()`](#2311-when)
  - [2.4. Ending/Returning Methods](#24-endingreturning-methods)
    - [2.4.1. Examples](#241-examples)
      - [2.4.1.1. `get()`](#2411-get)
      - [2.4.1.2. `first()` and `firstOrFail()`](#2412-first-and-firstorfail)
      - [2.4.1.3. `find(id)` and `findOrFail(id)`](#2413-findid-and-findorfailid)
      - [2.4.1.4. `value()`](#2414-value)
      - [2.4.1.5. `count()`](#2415-count)
      - [2.4.1.6. `min()` and `max()`](#2416-min-and-max)
      - [2.4.1.7. `sum()` and `avg()`](#2417-sum-and-avg)
  - [2.5. Raw Queries and Raw Query inside Query Builder](#25-raw-queries-and-raw-query-inside-query-builder)
    - [2.5.1. Raw Query](#251-raw-query)
    - [2.5.2. Raw Query inside Query Builder](#252-raw-query-inside-query-builder)
  - [2.6. Join Clauses](#26-join-clauses)
  - [2.7. Unions](#27-unions)
  - [2.8. Insert/Update/Delete/Transaction](#28-insertupdatedeletetransaction)
- [3. Eloquent ORM](#3-eloquent-orm)
  - [3.1. Eloquent Methods](#31-eloquent-methods)
    - [3.1.1. Retrieving Data](#311-retrieving-data)
    - [3.1.2. Inserts and Updates](#312-inserts-and-updates)
    - [3.1.3. Delection](#313-delection)
  - [3.2. Query Scopes](#32-query-scopes)
    - [3.2.1. Global Scope](#321-global-scope)
    - [3.2.2. Local Scope](#322-local-scope)
  - [3.3. Mutators & Casting](#33-mutators--casting)
    - [3.3.1. Accessors, Mutators](#331-accessors-mutators)
    - [3.3.2. Attribute Casting](#332-attribute-casting)
    - [3.3.3. Custom Casts](#333-custom-casts)
  - [3.4. Eloquent Collections](#34-eloquent-collections)
    - [3.4.1. Collection Methos](#341-collection-methos)
  - [3.5. Eloquent Serialization](#35-eloquent-serialization)
    - [3.5.1. To Arrays](#351-to-arrays)
    - [3.5.2. To JSON](#352-to-json)
  - [3.6. Eloquent Relationships](#36-eloquent-relationships)
  - [3.7. Manipulation of Relationships](#37-manipulation-of-relationships)
  - [3.8. Eager Loading](#38-eager-loading)
    - [3.8.1. Constraining Eager Loads](#381-constraining-eager-loads)
    - [3.8.2. Lazy Eager Loading](#382-lazy-eager-loading)
    - [3.8.3. Preventing Lazy Loading](#383-preventing-lazy-loading)
  - [3.9. Eloquent Events](#39-eloquent-events)
  - [3.10. Test](#310-test)
- [4. Reference](#4-reference)
  
  
As we all agree Laravel is created using php and support most (if not all) native php methods. This is also true when comes to database operations in Laravel.  

In this post, I will demostrate the differences between using row SQL, chained Query Builder as well Eloquent ORM when play with database in Laravel.  

## 1. Raw SQL via DB Facade
```php
// UserController
use Illuminate\Support\Facade\DB;
...

// Basic statement
DB::statement('drop table users');

// Raw select, and parameter binding
DB::select('select * from contacts where validated = ?', [true]);

// Select using the fluent builder
$users = DB::table('users')->get();

// Joins and other complex calls
DB::table('users')
    ->join('contacts', function ($join) {
        $join->on('users.id', '=', 'contacts.user_id')
             ->where('contacts.type', 'donor');
    })
    ->get();
```
It is possible to make any raw call to database using the `DB` facade with `statement()` method, `DB::statement('SQL statement here')`

But there are also specific methods for various common actions: `select()`, `insert()`, `update()`, and `delete()`.

| Method | Example |
|---|---|
| Raw `select` | `DB::select('select * from users');`  |
| Raw `insert` | `DB::insert('insert into contacts (name, email) values (?, ?)', ['sally', 'sally@me.com']); `  |
| Raw `update` | `DB::update('update contacts set status = ? where id = ?', ['donor', $id]);`  |
| Raw `delete` | `DB::delete('delete from contacts where archived = ?', [true]);`  |

## 2. Chaining with the Query Builder
```php
// UserController
use Illuminate\Support\Facade\DB;
...
$usersOfType = DB::table('users')
    ->where('type', $type)
    ->get();
```

Let’s take a look at what methods the query builder allows us to chain. The methods can be split up into four categories: **constraining methods, modifying methods, conditional methods, and ending/returning methods**.

| constraining methods | modifying methods | conditional methods | ending/returning methods|
|---|---|---|---|
| `select()` | `orderBy(colName, direction)` | `when()` | `get()` |
| `where()` | `groupBy()` and `having()` or `havingRaw()` | `unless()` | `first()` and `firstOrFail()` |
| `orWhere()` | `skip()` and `take()` |  | `find(id)` and `findOrFail(id)` |
| `whereBetween(colName, [low, high])` | `latest(colName)` and `oldest(colName)` |  | `value()` |
| `whereIn(colName, [1, 2, 3])`<br>`whereInRaw(colName, [1, 2, 3])`<br>`whereIntegerInRaw(colName, [1, 2, 3])` | `inRandomOrder()` |  | `count()` |
| `whereNull(colName) and whereNotNull(colName)` |  |  | `min()` and `max()` |
| `whereRaw()` |  |  | `sum()` and `avg()` |
| `whereExists()` |  |  |  |
| `distinct()` |  |  |  |

Further than the listed, custom methods exist for making raw statement - for example, `select()` has a `selectRaw()` counterpart that allows you to pass in a string for the query builder to place after the `WHERE` statement.  

One can however pass in the result of a DB::raw() call to almost any method in the query builder to achieve the same result.  

Other SQL native methods like [join and union](https://laravel.com/docs/8.x/queries#joins) are also made possible in Query Builder.  

| Raw Methods | Join Methods | Union Methods | Insert/Update/Delete |
|---|---|---|---|
| `selectRaw()` | `join()` | `union()` | `insert()` / `insertOrIgnore()` |
| `whereRaw()` / `orWhereRaw()` | `leftJoin()` / `rightJoin()` | `unionAll()` | `upsert()` |
| `havingRaw()` / `orHavingRaw()` | `crossJoin()` |  | `update()`<br>`increment()`<br>`decrement()`<br>json `update()` |
| `orderBy()` | Advanced Join Clauses |  | `delete()`<br>`truncate()` |
| `groupBy()` | `joinSub()` |  | `transaction()` |


### 2.1. Constraining Methods  

| Method | Funciton |
|---|---|
|`select()`|Allows you to choose which columns you’re selecting |
| `where()` | Allows you to limit the scope of what’s being returned using `WHERE` |
| `orWhere()` | Creates simple `OR WHERE` statements |
| `whereBetween(colName, [low, high])` | Allows you to scope a query to return only rows where a column is between two values (inclusive of the two values) |
| `whereIn(colName, [1, 2, 3])`<br>`whereInRaw(colName, [1, 2, 3])`<br>`whereIntegerInRaw(colName, [1, 2, 3])` | Allows you to scope a query to return only rows where a column value is in an explicitly provided list of options |
| `whereNull(colName) and whereNotNull(colName)` | Allow you to select only rows where a given column `is NULL` or `is NOT NULL`, respectively |
| `whereRaw()` | Allows you to pass in a raw, unescaped string to be added after the `WHERE` statement |
| `whereExists()` | Allows you to select only rows that, when passed into a provided subquery, return at least one row |
| `distinct()` | Selects only rows where the selected data is unique when compared to the other rows in the returned data |

#### 2.1.1. Examples
##### 2.1.1.1. `select()`
```php
// select() method
$emails = DB::table('contacts')
    ->select('email', 'email2 as second_email')
    ->get();
// Or
$emails = DB::table('contacts')
    ->select('email')
    ->addSelect('email2 as second_email')
    ->get();
```

##### 2.1.1.2. `where()`
```php
// where() method
$newVips = DB::table('contacts')
    ->where('vip', true)
    ->where('created_at', '>', now()->subDay());
// Or
$newVips = DB::table('contacts')->where([
    ['vip', true],
    ['created_at', '>', now()->subDay()],
]);
```

##### 2.1.1.3. `orWhere()`
```php
// orWhere() method with a closure
$contacts = DB::table('contacts')
    ->where('vip', true)
    ->orWhere(function ($query) {
        $query->where('created_at', '>', now()->subDay())
            ->where('trial', false);
    })
    ->get();
```

###### 2.1.1.3.1. Caution of using multiple `orWhere()`
```php
$canEdit = DB::table('users')
    ->where('admin', true)
    ->orWhere('plan', 'premium')
    ->where('is_plan_owner', true)
    ->get();

// is equivalent to 

SELECT * FROM users
    WHERE admin = 1
    OR plan = 'premium'
    AND is_plan_owner = 1;
```
and, 
```php
$canEdit = DB::table('users')
    ->where('admin', true)
    ->orWhere(function ($query) {
        $query->where('plan', 'premium')
            ->where('is_plan_owner', true);
    })
    ->get();

// is equivalent to

SELECT * FROM users
    WHERE admin = 1
    OR (plan = 'premium' AND is_plan_owner = 1);
```

##### 2.1.1.4. `whereBetween()`
```php
// whereBetween() method
$mediumDrinks = DB::table('drinks')
    ->whereBetween('size', [6, 12])
    ->get();
```

##### 2.1.1.5. `whereIn()`
```php
// whereIn() method
$closeBy = DB::table('contacts')
    ->whereIn('state', ['FL', 'GA', 'AL'])
    ->get();
```

##### 2.1.1.6. `whereRaw()`
```php
// whereRaw() method
// BEWARE OF SQL INJECTION! ALWAYS AVOID!
$goofs = DB::table('contacts')->whereRaw('id = 12345')->get()
```

##### 2.1.1.7. `whereExist()`
```php
// whereExist() method
$commenters = DB::table('users')
    ->whereExists(function ($query) {
        $query->select('id')
            ->from('comments')
            ->whereRaw('comments.user_id = users.id');
    })
    ->get();
// get those users who have left at least one comment
```

##### 2.1.1.8. `distinct()`
```php
// distinct() method
$lastNames = DB::table('contacts')->select('city')->distinct()->get();
```

### 2.2. Modifying Methods  

| Method | Function |
|---|---|
| `orderBy(colName, direction)` | Orders the results. The second parameter may be either `asc` (the default, ascending order) or `desc` (descending order) |
| `groupBy()` and `having()` or `havingRaw()` | Groups your results by a column. Optionally, `having()` and `havingRaw()` allow you to filter your results based on properties of the groups |
| `skip()` and `take()` | Most often used for pagination, these allow you to define how many rows to return and how many to skip before starting the return |
| `latest(colName)` and `oldest(colName)` | Sort by the passed column (or created_at if no column name is passed) in descending (`latest()`) or ascending (`oldest()`) order |
| `inRandomOrder()` | Sorts the result randomly |

#### 2.2.1. Examples
##### 2.2.1.1. `orderBy(colName, direction)`
```php
// orderBy() method
$contacts = DB::table('contacts')
    ->orderBy('last_name', 'asc')
    ->get();
```

##### 2.2.1.2. `groupBy()`
```php
// groupBy() method
$populousCities = DB::table('contacts')
    ->groupBy('city')
    ->havingRaw('count(contact_id) > 30')
    ->get();
```

##### 2.2.1.3. `skip()` and `take()`
```php
// skip() and take()
// returns rows 31-40
$page4 = DB::table('contacts')->skip(30)->take(10)->get();
```

### 2.3. Conditional Methods  

| Method | Function |
|---|---|
| `when()` | Given a truthy first parameter, applies the query modification contained in the closure; given a falsy first parameter, it does nothing |
| `unless()` | The exact inverse of when(). If the first parameter is falsy, it will run the second closure |
#### 2.3.1. Examples
##### 2.3.1.1. `when()`
```php
$status = request('status'); // Defaults to null if not set

$posts = DB::table('posts')
    ->when($status, function ($query) use ($status) {
        return $query->where('status', $status);
    })
    ->get();

// Or
$posts = DB::table('posts')
    ->when($ignoreDrafts, function ($query) {
        return $query->where('draft', false);
    })
    ->get();
```

You can also pass a third parameter, another closure, which will only be applied if the first parameter is falsy.  

### 2.4. Ending/Returning Methods
These methods stop the query chain and trigger the execution of the SQL query. Without one of these at the end of the query chain, your return will always just be an instance of the **`query builder`**  

| Method | Function |
|---|---|
| `get()` | Gets all results for the built query |
| `first()` and `firstOrFail()` | Get only the first result—like `get()`, but with a `LIMIT 1` added |
| `find(id)` and `findOrFail(id)` | Like `first()`, but you pass in an ID value that corresponds to the primary key to look up |
| `value()` | Plucks just the value from a single field from the *first* row |
| `count()` | Returns an integer count of all of the matching results |
| `min()` and `max()` | Return the minimum or maximum value of a particular column |
| `sum()` and `avg()` | Return the sum or average of all of the values in a particular column |

#### 2.4.1. Examples
##### 2.4.1.1. `get()`

```php
// get() method
$contacts = DB::table('contacts')->get();
$vipContacts = DB::table('contacts')->where('vip', true)->get();
```

##### 2.4.1.2. `first()` and `firstOrFail()`
```php
// first() method
$newestContact = DB::table('contacts')
    ->orderBy('created_at', 'desc')
    ->first();
```

> `first()` fails silently if there are no results, whereas `firstOrFail()` will throw an exception.  

*If you pass an array of column names to either method, it will return the data for just those columns instead of all columns.*

##### 2.4.1.3. `find(id)` and `findOrFail(id)`
```php
// find() method
$contactFive = DB::table('contacts')->find(5);
```

> `find()` fails *silently* if a row with that ID doesn’t exist, while `findOrFail()` will throw an exception.

##### 2.4.1.4. `value()`
```php
// value() method
$newestContactEmail = DB::table('contacts')
    ->orderBy('created_at', 'desc')
    ->value('email');
```

##### 2.4.1.5. `count()`
```php
// count() method
$countVips = DB::table('contacts')
    ->where('vip', true)
    ->count();
```

##### 2.4.1.6. `min()` and `max()`
```php
// max() method
$highestCost = DB::table('orders')->max('amount');
```

##### 2.4.1.7. `sum()` and `avg()`
```php
// avg() method
$averageCost = DB::table('orders')
    ->where('status', 'completed')
    ->avg('amount');
```

### 2.5. Raw Queries and Raw Query inside Query Builder 

#### 2.5.1. Raw Query

#### 2.5.2. Raw Query inside Query Builder

### 2.6. Join Clauses 

### 2.7. Unions

### 2.8. Insert/Update/Delete/Transaction








## 3. Eloquent ORM
```php
// ContactController
namespace App;
...
use Illuminate\Database\Eloquent\Model;
...
class Contact extends Model
...
public function show($contactId)
{
    return view('contacts.show')
        ->with('contact', Contact::findOrFail($contactId));
}
```

### 3.1. Eloquent Methods

#### 3.1.1. Retrieving Data

#### 3.1.2. Inserts and Updates

#### 3.1.3. Delection

### 3.2. Query Scopes

#### 3.2.1. Global Scope

#### 3.2.2. Local Scope

### 3.3. Mutators & Casting

#### 3.3.1. Accessors, Mutators

#### 3.3.2. Attribute Casting

#### 3.3.3. Custom Casts

### 3.4. Eloquent Collections

#### 3.4.1. Collection Methos

### 3.5. Eloquent Serialization

#### 3.5.1. To Arrays

#### 3.5.2. To JSON

### 3.6. Eloquent Relationships

### 3.7. Manipulation of Relationships

### 3.8. Eager Loading

#### 3.8.1. Constraining Eager Loads

#### 3.8.2. Lazy Eager Loading

#### 3.8.3. Preventing Lazy Loading

### 3.9. Eloquent Events

### 3.10. Test


  
## 4. Reference
- Stauffer, M. (2019). Laravel: Up & Running: A Framework for Building Modern PHP Apps. United States: O'Reilly Media.
- https://laravel.com/docs/8.x/queries
- https://laravel.com/docs/8.x/eloquent

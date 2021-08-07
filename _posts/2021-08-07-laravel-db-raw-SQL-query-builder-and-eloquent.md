---
  layout: post
  title: Laravel Database Raw SQL, Query Builder and Eloquent ORM
  featured: true
  author: ben
  tags: [git, ssh, rsa, chmod]
  image: '/images/posts/laravel-db-raw-sql-query-builder-eloquent-orm.png'
---

[Contents]

- [1. Raw SQL via DB Facade](#1-raw-sql-via-db-facade)
- [2. Chaining with the Query Builder](#2-chaining-with-the-query-builder)
- [3. Chaining with the Query Builder](#3-chaining-with-the-query-builder)
- [4. Reference](#4-reference)
  
  
As we all agree Laravel is created using php and support most (if not all) native php methods. This is also true when comes to database operations in Laravel.  

In this post, I will demostrate the differences between using row SQL, chained Query Builder as well Eloquent ORM when play with database in Laravel.  

## 1. Raw SQL via DB Facade
```php
// UserController
use Illuminate\Support\Facade\DB;
...

$users = DB::select('select * from users');
```
  
## 2. Chaining with the Query Builder
```php
// UserController
use Illuminate\Support\Facade\DB;
...
$usersOfType = DB::table('users')
    ->where('type', $type)
    ->get();
```
  
## 3. Chaining with the Query Builder
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
  
## 4. Reference
- Stauffer, M. (2019). Laravel: Up & Running: A Framework for Building Modern PHP Apps. United States: O'Reilly Media.

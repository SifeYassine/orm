# PHP ORM

A simple, lightweight **Object-Relational Mapper** written in plain PHP on top of PDO. It provides a minimal Active-Record-style API for CRUD operations and schema management (creating and altering tables) against a MySQL database, with no framework and no dependencies.

A presentation of the project's design is included in [`ORM.pdf`](ORM.pdf).

## Features

- **CRUD**: `find`, `all`, `create`, `update`, `save` (insert or update), `delete`
- **Schema management**: `createTable` and `alterTable` (`ADD` / `DROP` columns) from a simple column → type map
- **Prepared statements** with named parameters for all data operations
- **Model definitions**: describe a table and its columns in a small PHP class (see `User.php`)
- Contract defined by `ORMInterface`

### Supported column types

| Type      | SQL                              |
| --------- | -------------------------------- |
| `id`      | `INT PRIMARY KEY AUTO_INCREMENT` |
| `string`  | `VARCHAR(255)`                   |
| `text`    | `TEXT`                           |
| `integer` | `INT`                            |
| `boolean` | `BOOLEAN`                        |

## Project Structure

```
orm/
├── ORM.pdf                 # Project presentation
└── ORM/
    ├── config.php          # Database connection settings
    ├── Database.php        # PDO connection wrapper
    ├── ORMInterface.php    # ORM contract
    ├── ORM.php             # ORM implementation
    ├── User.php            # Example model (users table)
    ├── createTable.php     # Example: create the users table
    ├── alterTable.php      # Example: add/drop columns
    ├── testCreate.php      # Example: insert a user
    ├── testRead.php        # Example: find a user by id
    ├── testReadAll.php     # Example: list all users
    ├── testUpdate.php      # Example: update a user
    └── testDelete.php      # Example: delete a user
```

## Getting Started

### Prerequisites

- PHP 7.4+ with the `pdo_mysql` extension
- MySQL / MariaDB (e.g. via XAMPP, Laragon, or Docker)

### Installation

```bash
git clone https://github.com/SifeYassine/orm.git
cd orm/ORM
```

Create the database:

```sql
CREATE DATABASE orm;
```

Then update `config.php` with your credentials:

```php
return [
    'host'     => 'localhost',
    'dbname'   => 'orm',
    'username' => 'root',
    'password' => '',
    // ...
];
```

### Run the examples

```bash
php createTable.php   # create the users table
php testCreate.php    # insert a user
php testReadAll.php   # list users
php testRead.php      # find user #1
php testUpdate.php    # update user #1
php testDelete.php    # delete user #1
php alterTable.php    # add example columns
```

## Usage

### Define a model

```php
class Post {
    protected $table = 'posts';
    protected $attributes = [
        'id'      => 'id',
        'title'   => 'string',
        'body'    => 'text',
        'views'   => 'integer',
        'is_live' => 'boolean',
    ];

    public function getTable()      { return $this->table; }
    public function getAttributes() { return $this->attributes; }
}
```

### Work with data

```php
require 'ORM.php';

$post = new Post();
$orm  = new ORM($post->getTable());

$orm->createTable($post->getAttributes());

$orm->create(['title' => 'Hello', 'body' => 'First post', 'views' => 0, 'is_live' => true]);
$all = $orm->all();
$one = $orm->find(1);
$orm->update(1, ['views' => 42]);
$orm->save(['id' => 1, 'title' => 'Hello, world']);   // updates because "id" is set
$orm->delete(1);

$orm->alterTable('ADD',  ['slug' => 'string']);
$orm->alterTable('DROP', ['slug']);
```

## Limitations

This is an educational project. Table and column names are interpolated directly into SQL (only values are bound), so never pass untrusted input as table or column names. Relationships, query building, and migrations are out of scope.

## Author

**Sifeddine Yassine** · [@SifeYassine](https://github.com/SifeYassine)

---
layout: post
title:  "Tempest: Configuration and Adapters"
comments: true
date:   2016-01-16
tags:
- orm
- tempest
---
One of the first things we'll need to do is set Tempest up to be used within the context of an actual HTTP request. I'm a big fan of the way Rails handles configuration of libraries via [initializers](http://guides.rubyonrails.org/configuring.html#using-initializer-files). We'll be taking a similar approach, providing a simple configuration method that can be called during the early stages of the web request.

### Configuration
We'll use a specific class for configuring things, appropriately named `\Temptest\Config\`, with a simple `init` method. This method will accept various configuration options which we'll flesh out in articles to come. Here are a couple ideas for some properties we might one day support:

```php
\Tempest\Config::init([
    "adapter" => "\\Tempest\\Adapter\\MySQL",    // Different database adapters
    "connection" => "localhost:/tmp/mysql.sock", // Connection details
    "debug" => true                              // Debug Mode
]);
```

The above approach makes it very simple to configure the way the ORM works at the entry point to your application. We can add more configuration options as they make sense, and of course, we'll build in support to automatically pull configuration from [Environment Variables](https://en.wikipedia.org/wiki/Environment_variable).

### Adapters
One of the first configuration options we should work on is the idea of Adapters.  Our ORM should be able to talk to many different types of databases. Since we'll need to talk to at least one during development, let's get it out of the way first.

Each adapter is going to have several components, each specialized for a specific job within the ORM. Off the top of my head, I can think of 2 separate pieces that would be good candidates to be their own classes:

* **Database Communication**: Not all databases work the same, so the method by which our ORM talks to the underlying database will likely need to be different for different adapters. We'll have the database connection and communication live in it's own class, so custom adapters have a simple way to override the functionality.
* **Query Generation**: There are subtle differences in the structure of SQL for the various databases. What might work in MySQL will be different in PostgreSQL, but the ORM must function the same for both. The way that queries are generated will also be abstracted into it's own set of classes (but we'll figure that out later).

With that in mind, let's select a class hierarchy that will make this nice and easy to implement.

```
\Adapter\DAO.php            # Handles all communication to and from the database
\Adapter\QueryBuilder.php   # Handles the translation of data structures to queries
```

So, we've now abstracted some of our desired functionality into an Adapter, which we can subclass as appropriate for custom database implementations. A lot of the detail is still unclear, such as how we'll actually generate queries, but that's not the problem we're trying to solve right now, so we can let it slide.

There are a few more classes we're going to need to get set up, but these are the true backbone of Tempest, things that won't need to be subclassed on an individual adapter level. These are the classes that define the core API of the project.

```
# Core Classes
\Tempest\Model.php
\Tempest\Config.php
\Tempest\Debug.php

# Adapter (And Example)
\Tempest\Adapter\Base\DAO.php
\Tempest\Adapter\Base\QueryBuilder.php
\Tempest\Adapter\MySQL\DAO.php
\Tempest\Adapter\MySQL\QueryBuilder.php
```

We'll have the `Model` class handle the definition of models, as well as querying from them. `Config` handles initializing the ORM at the start of a request, and `Debug` allows developers to get insight into what the ORM is doing under the hood.

For adapters, we'll provide an [Interface](https://secure.php.net/manual/en/language.oop5.interfaces.php) for the `DAO` and `QueryBuilder` classes, with a MySQL implementation in it's own Adapter namespace. This way we can test our ORM with a specific example use case, with the possibility of easily adding more.

### Writing Code
First, let's paint the `Config` class with broad strokes. We don't yet know all of the methods it might need, but we do know two things:

1. It needs to provide an `init` method to configure the ORM at the start of a request
2. It needs to be able to get the `init` parameters from `ENV` vars

```php
<?php

namespace Tempest;

class Config {
	const DEFAULT_NAME = 'default';
	private static $instances = [];
	private static $active_config = null;

	// Instantiate new configs. Inserts them into the config cache by name.
	public static function init($params){
		$params["name"] = $params["name"] ?? static::DEFAULT_NAME;
		$instance = new static($params);
		static::$instances[$instance->name] = $instance;

		// If the config is 'default', it is automatically set to active.
		if($instance->name == static::DEFAULT_NAME){
			static::use(static::DEFAULT_NAME);
		}

		return $instance;
	}

	// Set the active config to a specified one
	public static function use($name){
		static::$active_config = static::$instances[$name] ?? null;
		return static::$instances[$name ?? static::DEFAULT_NAME];
	}

	// Constructor turns the parameters into config properties
	public static function __construct($params){
		foreach($params as $key => $value){
			$this->{$key} = $value;
		}
	}
}
?>
```

I've taken a few creative liberties with our configuration class. First, you'll notice that all of the possible arguments you can inititialize with are set in the static `$OPTIONS` array.  This way we can easily map direct arguments to their appropriate `ENV` variables. Second, rather than simply setting static values, I create a specific instance for each set of configurations, and keep my configurations separated by name. This makes our ORM very powerful indeed, as it can easily be used in projects that must talk to multiple databases, as the different configurations can be used in the same request. We use a static `use` method to use a configuration by name.

```php
<?php
	\Tempest\Config::init([
		"name" => "AuthenticationDB",
		"debug" => false,
		"database_url" => "123"
	]);

	// Use the built-in `getenv` method to populate your database config from ENV vars
	\Tempest\Config::init([
		"name" => getenv("ARTICLEDB_NAME"),
		"debug" => getenv("ARTICLEDB_DEBUG"),
		"database_url" => getenv("ARTICLEDB_DATABASE_URL")
	]);

    \Tempest\Config::use("AuthenticationDB");
    $user = User::where("email", $email);

    \Tempest\Config::use("ArticleDB");
    $articles = Article::where("author_id", $user->id);
?>
```

Next up, we need to create the basic idea of a DAO. DAO stands for [Data Access Object](https://en.wikipedia.org/wiki/Data_access_object), and it will handle the transfer of all information to and from the database. The DAO has a few unique responsibility, and we should think through them before writing any code. Each job should have it's own method, so it's a good thought experiment to think about this ahead of time.

* **Manage Connection**: The DAO will handle getting a connection object that is connected to the active configuration. We can also add methods to force the connection to be closed. Since we want to see all the ins-and-outs of the ORM, we're going to be writing our own underlying connection adapters using [PDO](https://secure.php.net/manual/en/book.pdo.php) (But that's another story).
* **Execute Queries**: The DAO must provide a mechanism to provide a query to the database and return the result set back. This will be handled with a single method, aptly named `execute`

```php
<?php
	namespace Tempest\Adapter\Base;

	interface DAO {
		public function execute($query, $params);
		public function conn();
	}
?>
```

```php
<?php
	namespace Tempest\Adapter\MySQL;
	use \Tempest\Adapter\Base\DAO as BaseDAO;

	class DAO extends BaseDAO {
		public function execute($query, $params){
		}

		public function conn() {
		}
	}
?>
```

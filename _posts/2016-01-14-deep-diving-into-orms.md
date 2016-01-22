---
layout: post
title:  "Deep Diving into ORMs"
date:   2016-01-14
tags:
- orm
- tempest
---
It's rare that I advocate writing something from scratch when an off-the-shelf solution will fit your needs perfectly, as is almost always the case with an ORM.  This time, I got a little too curious, and I _wanted_ to know how the sausage was made.

It all began with a discussion at work about which ORM solution we should poke with a few sticks. Our schema can be a little _wonky_, and we're doing a few _strange_ things, and a good chunk of the off-the-shelf offerings work great when everything is done _perfectly_.  We weren't perfect. It was time to figure out how these things _really_ worked. It took less than 30 minutes for my insanity-riddled brain to decide, "Hey yeah, you could probably just write one from scratch really quick.".  Oh boy.

### What's it gotta do?
Like almost any library or project I work on, I try to tease out the public API first.  It's a lot easier to architect something if you know what you want the finished product to look like. An architect always draws the building before putting down a foundation.

#### Models
At the most basic level, the ORM has to handle mapping objects to data from your database. I'm a big fan of the patterns that [ActiveRecord](http://guides.rubyonrails.org/active_record_basics.html) and [Eloquent](https://laravel.com/docs/5.0/eloquent) have taken, and used them for inspiration.  Users should be able to define a model by defining a simple class.  A few pieces of configuration should also be provided in the form of static variables:

```php
class Model {
    public static $table = null;
    public static $primary_key = null;
}
```

Models should provide a few simple methods that can access, modify, and delete data.  Instances of models should also have methods to persist changes to the database, and to delete the local instance:

```php
class Model {
    public static find($primary_key){...}
    public static destroy($primary_key){...}
    public save(){...}
    public delete(){...}
}
```

#### Query Interface
The methods above are pretty _ok_, but they're nowhere near advanced enough to handle the types of data and relationships that we'll be dealing with in the real world.  The Query Interface is the combination of more advanced methods that allow users to build more complex queries. These methods allow users to add limits, ordering information, and custom query parameters:

```php
Model::where("column > ?", 10).limit(50).order("created_at");
```

Users should be able to chain as many of these methods as they want, eventually calling "execute" to get the data.  Some other methods also "end" the chain and return explicit values:

```php
Model::where("a > ?", 10)->execute(); // Get the data
Model::where("a > ?", 10)->count(); // Count the records
Model::where("a > ?", 10)->sample(5); // Randomly sample n records
```

#### Associations
Now that I knew how the API would work (from an absurdly high level), it's time to figure out how associations should be defined.  Most of your models are going to have related data in another table, and it should be easy to get to it.  I opted for having a single static array to define each of my association types.  It forces a logical grouping for your associations in code, and the Associative Array syntax makes the task easy:

```php
class User extends Model {
    public static $has_many = [
        "tasks" => ["class" => "Task", "foreign_key" => "owner_id"],
    ];

    public static $belongs_to = [
        "team" => ["class" => "Team"]
    ];

    public static $has_one = [
        "profile" => ["class" => "Profile"]
    ];
}
```

#### Validations
Models should provide a simple way to ensure that data is properly formatted before it gets stored in the database; we need to _validate_ it. Much like my approach to Associations, all validations will be defined in one big bucket:

```php
class User extends Model {
    public static $validations = [
        "name" => ["present"],
        "email" => ["present", "unique", "email"]
    ]
}
```

For the time being, I'm not going to concern myself with exactly how a string like "present" will convert into a validation.  Remember, at this point, we're just painting with broad strokes.  We can worry about the details later.

#### Scopes
A lot of times, you'll be accessing data in a similar pattern.  Blogs almost always order posts with the most-recent submissions being first. Perhaps your blog posts will have a "Published" flag, to determine if they're visible or not.  These things should be easy to define on the model, so users don't need to rewrite the same query over-and-over.

```php
class Post extends Model {
    public static $scopes = [
        "published" => [],
        "recent" => []
    ]
}
```

I toyed with the idea of using another class-level object to group these together, but scopes are so highly configurable that that solution felt limiting.  Scopes should be able to define filter criteria, order clauses, limits, and more.  I opted for using a method name convention on the class level instead:

```php
class Post extends Model {
    function scopePublished(){
        return self::where("published = true");
    }

    function scopeRecent(){
        return self::order("created_at");
    }
}
```

#### And More!
After we have all the big picture items sorted out, it's time to take a look at some of the "nice to haves".  I poked around a few existing ORMs and found a few neat features that can add a lot of power to our shiny new toy:

##### Custom Attributes
Sometimes you'll want your model to be able to handle data that isn't going to persist in the database.  The obvious example is creating a new `User` record.  You'll want to set `password` and `password_confirmation` on the model, and have them validate accordingly, but they won't be saved to the database (at least, not in their current form).

```php
class User extends Model{
    public static $custom_attrs = ["password", "password_confirmation"];
}
```

##### Advanced Chaining
We have a lot of really cool ideas defined above, including scopes, advanced query building, and associations.  These should all play nicely together, and a user should be able to use them in conjunction with one another to get to the data the need in record time.

```php
User::find(1)->posts()->published()->where("active = ?", true)->order("created_date")->limit(5);
```

The above would find all published sites belonging to the given User, where the site is marked as `active`, ordered by the `created_date`, and returning a maximum of 5 records.  That's a really easy to read and concise way to get to the data, and I think meets our needs perfectly.

### The End Result
Alright, we've just painted a pretty descriptive blueprint of where we want this ORM to end up.  Based on the above, I could define a pretty thorough class to describe a table and all of it's associated data and requirements.

```php
class User extends Model {
    public static $custom_attrs = ["password", "password_confirmation"];

    public static $has_many = [
        "posts" => ["class" => "Post", "foreign_key" => "author_id"]
    ];

    public static $belongs_to = [
        "organization" => ["class" => "Organization"]
    ];

    public static $validations = [
        "email"    => ["present", "unique"],
        "name"     => ["present"],
        "password" => ["present", "matches" => "password_confirmation"]
    ];

    function scopeActive(){
        return self::where("active = ?", true);
    }
}
```

I think that's a pretty good starting point.  We've got a target we can aim for, and a set of features that every _real_ ORM has.  In the next post, we'll start getting into the finer details of how our code will work, what the internal structure will look like, and how it all gets glued together.
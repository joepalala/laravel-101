README
---

*Disclaimer: Most of this stuff come directly from the Laravel Docs*

Like any tool in the "real world", you feel more confident if you understand how that tool works.
Application development is no different. When you understand how your development tools function, you
feel more comfortable and confident using them.

By getting to know the overall framework better, everything feels less "magical" and you will
be more confident building your applications. If you don't understand all of the terms right away,
don't lose heart! Just try to get a basic grasp of what is going on, and your knowledge will grow as
you explore other sections of the documentation.


First Things
------

The entry point for all requests to a Laravel application is the public/index.php file. All requests
are directed to this file by your web server (Apache / Nginx) configuration. The index.php file
doesn't contain much code. Rather, it is simply a starting point for loading the rest of the
framework.

The index.php file loads the Composer generated autoloader definition, and then retrieves an instance
of the Laravel application from bootstrap/app.php script. The first action taken by Laravel itself is
to create an instance of the application / service container.

HTTP / Console Kernels
Next, the incoming request is sent to either the HTTP kernel or the console kernel, depending on the
type of request that is entering the application. These two kernels serve as the central location that
all requests flow through. For now, let's just focus on the HTTP kernel, which is located in
app/Http/Kernel.php.

The HTTP kernel extends the Illuminate\Foundation\Http\Kernel class, which defines an array of
bootstrappers that will be run before the request is executed. These bootstrappers configure error
handling, configure logging, detect the application environment, and perform other tasks that need to
be done before the request is actually handled.

The HTTP kernel also defines a list of HTTP middleware that all requests must pass through before
being handled by the application. These middleware handle reading and writing the HTTP session,
determining if the application is in maintenance mode, verifying the CSRF token, and more.

The method signature for the HTTP kernel's handle method is quite simple: receive a Request and return
a Response. Think of the Kernel as being a big black box that represents your entire application. Feed
it HTTP requests and it will return HTTP responses.

Service Providers
---

One of the most important Kernel bootstrapping actions is loading the service providers for your
application. All of the service providers for the application are configured in the config/app.php
configuration file's providers array. First, the register method will be called on all providers,
then, once all providers have been registered, the boot method will be called.

Service providers are responsible for bootstrapping all of the framework's various components, such as
the database, queue, validation, and routing components. Since they bootstrap and configure every
feature offered by the framework, service providers are the most important aspect of the entire
Laravel bootstrap process.

Dispatch Request
---

Once the application has been bootstrapped and all service providers have been registered, the
Request will be handed off to the router for dispatching. The router will dispatch the request to a
route or controller, as well as run any route specific middleware.

The most basic Laravel routes simply accept a URI and a Closure, providing a very simple and
expressive method of defining routes:

```
Route::get('foo', function () {
    return 'Hello World';
});
```

routing
---

Laravel upon getting a request will check the router to find out what response corresponds to the url
(Resource Location)

If it doesn't know what to do with it, it will check what it should do by default which is send an
error page saying the route has not been found.


The Default Route Files
---

All Laravel routes are defined in your route files, which are located in the routes directory. These
files are automatically loaded by the framework. The `routes/web.php` file defines routes that are for
your web interface. 

These routes are assigned the web middleware group, which provides features like
session state and CSRF protection. The routes in routes/api.php are stateless and are assigned the api
middleware group.

For most applications, you will begin by defining routes in your routes/web.php file.

For API - define routes in `routes/api.php`



Routes Introduction
---
Middleware provide a convenient mechanism for filtering HTTP requests entering your application. 

For example, Laravel includes a middleware that verifies the user of your application is authenticated. If the user is not authenticated, the middleware will redirect the user to the login screen. 


However, if the user is authenticated, the middleware will allow the request to proceed further into the application.

Of course, additional middleware can be written to perform a variety of tasks besides authentication.
A CORS middleware might be responsible for adding the proper headers to all responses leaving your
application. 

A *logging middleware* might log all incoming requests to your application.

There are several middleware included in the Laravel framework, including middleware for authentication and CSRF protection. 

All of these middleware are located in the  app/Http/Middleware
directory.

So basically by using middleware you just tell the response what condition ayou will check for and
what to do if its not true then what it will  do if false.

Route Resources
---

Create a route resource first 

`Route::resource('bookings', 'BookingsController');`

Check your routes

`php artisan routes`

Generate the controller for the route resource

Creating Migrations
---

To create a migration, you may use the migrate:make command on the Artisan CLI:

php artisan migrate:make create_users_table
The migration will be placed in your app/database/migrations folder, and will contain a timestamp
which allows the framework to determine the order of the migrations.

You may also specify a --path option when creating the migration. The path should be relative to the
root directory of your installation:

php artisan migrate:make foo --path=app/migrations
The --table and --create options may also be used to indicate the name of the table, and whether the
migration will be creating a new table:

php artisan migrate:make add_votes_to_user_table --table=users

php artisan migrate:make create_users_table --create=users

Running Migrations

Running All Outstanding Migrations

php artisan migrate
Running All Outstanding Migrations For A Path

php artisan migrate --path=app/foo/migrations
Running All Outstanding Migrations For A Package

php artisan migrate --package=vendor/package
Note: If you receive a "class not found" error when running migrations, try running the  composer
dump-autoload command.
Forcing Migrations In Production
Some migration operations are destructive, meaning they may cause you to lose data. In order to
protect you from running these commands against your production database, you will be prompted for
confirmation before these commands are executed. To force the commands to run without a prompt, use
the --force flag:

php artisan migrate --force

Rolling Back Migrations

Rollback The Last Migration Operation

php artisan migrate:rollback
Rollback all migrations

php artisan migrate:reset
Rollback all migrations and run them all again

php artisan migrate:refresh

php artisan migrate:refresh --seed

Database Seeding

Laravel also includes a simple way to seed your database with test data using seed classes. All seed
classes are stored in app/database/seeds. Seed classes may have any name you wish, but probably should
follow some sensible convention, such as UserTableSeeder, etc. By default, a  DatabaseSeeder class is
defined for you. From this class, you may use the call method to run other seed classes, allowing you
to control the seeding order.

Example Database Seed Class
---

```
class DatabaseSeeder extends Seeder {

    public function run()
    {
        $this->call('UserTableSeeder');

        $this->command->info('User table seeded!');
    }

}

class UserTableSeeder extends Seeder {

    public function run()
    {
        DB::table('users')->delete();

        User::create(array('email' => 'foo@bar.com'));
    }
}
```

To seed your database, you may use the db:seed command on the Artisan CLI:

`php artisan db:seed`

By default, the db:seed command runs the DatabaseSeeder class, which may be used to call other seed
classes. However, you may use the --class option to specify a specific seeder class to run
individually:

`php artisan db:seed --class=UserTableSeeder`

You may also seed your database using the migrate:refresh command, which will also rollback and re-run
all of your migrations:

`php artisan migrate:refresh --seed`





Controllers
---

Instead of defining all of your request handling logic as Closures in route files, you may wish to
organize this behavior using Controller classes. Controllers can group related request handling logic
into a single class. Controllers are stored in the app/Http/Controllers directory.


aDefining Controllers
---
Below is an example of a basic controller class. Note that the controller extends the base controller
class included with Laravel. The base class provides a few convenience methods such as the  middleware
method, which may be used to attach middleware to controller actions:
```
<?php

namespace App\Http\Controllers;

use App\User;
use App\Http\Controllers\Controller;

class UserController extends Controller
{
    /**
     * Show the profile for the given user.
     *
     * @param  int  $id
     * @return Response
     */
    public function show($id)
    {
        return view('user.profile', ['user' => User::findOrFail($id)]);
    }
}
```


You can define a route to this particular action by doing this:

```
Route::get('user/{id}', 'UserController@show');
```






Assigning Middleware To Routes
----

If you would like to assign middleware to specific routes, you should first assign the middleware a
key in your `app/Http/Kernel.php` file. 

By default, the $routeMiddleware property of this class contains
entries for the middleware included with Laravel. To add your own, simply append it to this list and assign it a key of your choosing. 

For example:

```php
// Within App\Http\Kernel Class...

protected $routeMiddleware = [
    'auth' => \Illuminate\Auth\Middleware\Authenticate::class,
    'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
    'bindings' => \Illuminate\Routing\Middleware\SubstituteBindings::class,
    'can' => \Illuminate\Auth\Middleware\Authorize::class,
    'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
    'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
];
```





Database: Seeding
---

Introduction
---

Laravel includes a simple method of seeding your database with test data using seed classes. All seed
classes are stored in the database/seeds directory. Seed classes may have any name you wish, but
probably should follow some sensible convention, such as UsersTableSeeder, etc. By default, a
DatabaseSeeder class is defined for you. From this class, you may use the call method to run other
seed classes, allowing you to control the seeding order.


Writing Seeders
---

To generate a seeder, execute the make:seeder Artisan command. All seeders generated by the framework
will be placed in the database/seeds directory:

`php artisan make:seeder UsersTableSeeder`

A seeder class only contains one method by default: run. This method is called when the db:seed
Artisan command is executed. Within the run method, you may insert data into your database however you
wish. You may use the query builder to manually insert data or you may use Eloquent model factories.

As an example, let's modify the default DatabaseSeeder class and add a database insert statement to
the run method:

```php

use Illuminate\Database\Seeder;
use Illuminate\Database\Eloquent\Model;

class DatabaseSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        DB::table('users')->insert([
            'name' => str_random(10),
            'email' => str_random(10).'@gmail.com',
            'password' => bcrypt('secret'),
        ]);
    }
}
```

Using Model Factories
---
Of course, manually specifying the attributes for each model seed is cumbersome. Instead, you can use
model factories to conveniently generate large amounts of database records. First, review the model
factory documentation to learn how to define your factories. Once you have defined your factories, you
may use the factory helper function to insert records into your database.

For example, let's create 50 users and attach a relationship to each user:

```php
/**
 * Run the database seeds.
 *
 * @return void
 */
public function run()
{
    factory(App\User::class, 50)->create()->each(function ($u) {
        $u->posts()->save(factory(App\Post::class)->make());
    });
}
```

Calling Additional Seeders
---

Within the DatabaseSeeder class, you may use the call method to execute additional seed classes. Using
the call method allows you to break up your database seeding into multiple files so that no single
seeder class becomes overwhelmingly large. Simply pass the name of the seeder class you wish to run:

/**
 * Run the database seeds.
 *
 * @return void
 */
public function run()
{
    $this->call(UsersTableSeeder::class);
    $this->call(PostsTableSeeder::class);
    $this->call(CommentsTableSeeder::class);
}

Running Seeders
---

Once you have written your seeder classes, you may use the db:seed Artisan command to seed your
database. By default, the db:seed command runs the DatabaseSeeder class, which may be used to call
other seed classes. However, you may use the --class option to specify a specific seeder class to run
individually:

`php artisan db:seed`

`php artisan db:seed --class=UsersTableSeeder`

You may also seed your database using the migrate:refresh command, which will also rollback and re-run
all of your migrations. This command is useful for completely re-building your database:

`php artisan migrate:refresh --seed`




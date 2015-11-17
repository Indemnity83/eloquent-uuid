# eloquent-uuid
An Eloquent UUID Trait to use with Laravel 5.1.

[![MIT licensed](https://img.shields.io/badge/license-MIT-blue.svg)](https://raw.githubusercontent.com/hyperium/hyper/master/LICENSE)

It **should** work with Laravel 5.0 also, but it's untested.

The trait overwrites the static `boot` method and listens to the `creating`
event. It generates a UUID (strips the dashes) and stores it in the primary
key attribute. Thus, you'll need a `CHAR(32)` primary key for your model
(see migrations below).

## Installation

	composer require alsofronie/eloquent-uuid:dev-master

## Use

	<?php

	namespace App;

	use Alsofronie\Uuid\UuidModelTrait;
	use Illuminate\Database\Eloquent\Model;

	class User extends Model {

		use UuidModelTrait;

		// ...
	}
	
	?>

## Migration

In order to use the UUID's as primary key, you must use a `CHAR(32)` type.

	<?php

	use Illuminate\Database\Schema\Blueprint;
	use Illuminate\Database\Migrations\Migration;

	class CreateUsersTable extends Migration
	{
	    /**
	     * Run the migrations.
	     *
	     * @return void
	     */
	    public function up()
	    {
	        Schema::create('users', function (Blueprint $table) {
	            $table->char('id',32);	// that is the primary key holding the UUID
	            $table->string('name');
	            $table->string('email')->unique();
	            $table->string('password', 60);
	            $table->rememberToken();
	            $table->timestamps();
	            $table->primary('id');	// we declare it as primary here
	        });
	    }

	    /**
	     * Reverse the migrations.
	     *
	     * @return void
	     */
	    public function down()
	    {
	        Schema::drop('users');
	    }
	}
	
	?>

## Warnings

There seems to be an issue with the `factory` on phpunit tests: the returned
`$user` object will have an `id` of `1` but the database is correct.
More, if you're using  `make()` method, the `creating` event will never get
called and you'll have no `id` (expected);

	$returnedUser = factory(App\Models\User::class)->create();
    
	$this->assertTrue(strlen($returnedUser->id) < 10);

    $dbUser = \App\Models\User::first();
    $this->assertTrue(strlen($user->id) == 32);

## WIP

 - add option for Uuid version generator
 - drop the Webpatser\Uuid\Uuid dependency
 - make it faster
 - add some mechanism to `Schema` to have nice `$table->uuid('id');` directly
 - write tests :)



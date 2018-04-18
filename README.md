# Laravel5 use UUID to user id

於Laravel5中, 替換user id auto_increment 為 uuid。

# Installation

    laravel new <new_project_name>

##  install laravel-uuid component

    cd /path/to/project
    composer require webpatser/laravel-uuid

## install laravel Auth

    php artisan make:auth

## move User model to Models folder
created models folder app\  and move User.php to Models folder.
change User.php namespace **App** to **App\Models** 
go to config\auth.php, find below

    'providers' => [
	    'users' => [
	    'driver' => 'eloquent',
	    'model' => App\User::class,
    ],
and change 

    App\User::class

 to

     App\Models\User::class

go to app\Http\Controllers\Auth\RegisterController.php, find below

    use App\User;
change to 

    use App\Models\User;
## Remove User model auto increment
add code below to disabled user model auto increment

    /**
    * Indicates if the IDs are auto-incrementing.
    *
    * @var  bool
    */
    public $incrementing = false;
## Creating a traits folder and Uuids trait
On \app create folder **Traits**, and create a **Uuids.php** . Afterward, copy code below to Uuids.php then save

    <?php
    namespace App\Traits;

    use Webpatser\Uuid\Uuid;

    trait Uuids{
	    /**
	    * Boot function from laravel.
	    */
	    protected  static  function boot(){
		    parent::boot();
	    
		    static::creating(function($model){
		    $model->{$model->getKeyName()} = Uuid::generate()->string;
	    });
	    }
    }
## In User model use Uuids trait
go to app\Models\User.php, add code below:

> use App\Traits\Uuids;
> 
> use Notifiable, Uuids;

    use App\Traits\Uuids;
    class User extends Authenticatable
    {
	    use Notifiable, Uuids;
	    ...

## Setup your database config

In your .env file, you can setup your database name, username and password

## Fix Syntax error or access violation: 1071 Specified key was too long

go to app\Providers\AppServiceProvider.php, and inside the `boot` method set a default string length:

>  Schema::defaultStringLength(191);

	    /**
	    * Bootstrap any application services.
	    *
	    * @return  void
	    */
	    public  function boot()
	    {
		    Schema::defaultStringLength(191);
	    }

## Execute migration

    php artisan migrate

# How to use Laravel Sancum for authentication?

###### Laravel Sanctum provides a featherweight authentication system for SPAs (single page applications), mobile applications, and simple, token based APIs. Sanctum allows each user of your application to generate multiple API tokens for their account. These tokens may be granted abilities / scopes which specify which actions the tokens are allowed to perform.

### Installation
You may install Laravel Sanctum via the Composer package manager:

```php
composer require laravel/sanctum
```

Next, you should publish the Sanctum configuration and migration files using the vendor:publish Artisan command. The
sanctum configuration file will be placed in your application's config directory:

```php
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
```

Finally, you should run your database migrations. Sanctum will create one database table in which to store API tokens:

```php
php artisan migrate
```

Next, if you plan to utilize Sanctum to authenticate a SPA, you should add Sanctum's middleware to your api middleware
group within your application's app/Http/Kernel.php file:

```   php
'api' => [Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful::class]
```
For web routes
```php
'web' => [Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful::class]
```
### Issuing API Tokens
To begin issuing tokens for users, your User model should use the Laravel\Sanctum\HasApiTokens trait:
```php
use Laravel\Sanctum\HasApiTokens;
 
class User extends Authenticatable
{
    use HasApiTokens, HasFactory, Notifiable;
}
```

To issue a token, you may use the createToken method. The createToken method returns a Laravel\Sanctum\NewAccessToken instance. API tokens are hashed using SHA-256 hashing before being stored in your database, but you may access the plain-text value of the token using the plainTextToken property of the NewAccessToken instance. You should display this value to the user immediately after the token has been created:
```php
use Illuminate\Http\Request;
// if matching email found, $user will be the object and hold user data.
//if matching email not found,$user will return null.

    $user=  User::where('email',$email)->first();
    $token = $user->createToken('tokenName')->plainTextToken;
 
   return response()->json(['status'=>'success','token'=>$token]);
});

```
### Protecting Routes
To protect routes so that all incoming requests must be authenticated, you should attach the sanctum authentication guard to your protected routes within your routes/web.php and routes/api.php route files
```php
Route::post('/userProfile',[userController::class,'userProfile'])->middleware('auth:sanctum');
```

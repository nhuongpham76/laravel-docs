# Macro for laravel
```text
Macroable là một trait trong Laravel, các class có thể mở rộng sử dụng Macros được gọi là Macroable class.
Macros cung cấp cho lập trình viên một phương thức đơn giản cho phép ta mở rộng một lớp đã tồn tại của Laravel với các hàm do bản thân định nghĩa.
```

# Hướng dẫn
```text
class AppServiceProvider extends ServiceProvider
{
    /**
    * Register any application services.
    *
    * @return void
    */
    public function register()
    {
    //
    }

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
      	Builder::macro('toRawSql', function() {
        	return array_reduce($this->getBindings(), function($sql, $binding){
          	    return preg_replace('/\?/', is_numeric($binding) ? $binding : "'".$binding."'" , $sql, 1);
        	}, $this->toSql());
		});
    }
}
```

- Sử dụng với model User:
```text
User::query()->where('name', 'Huy')->toRawSql()
```
- kết quả: 
```text
select * from users where name = 'huy'
```


- Cách Laravel handle macro mà mình đăng ký qua 2 magic method __call và __callStatic trong PHP
```text

// vendor/laravel/framework/src/Illuminate/Support/Traits/Macroable.php
public static function __callStatic($method, $parameters)
{
if (! static::hasMacro($method)) {
throw new BadMethodCallException("Method {$method} does not exist.");
}

    if (static::$macros[$method] instanceof Closure) {
      return call_user_func_array(Closure::bind(static::$macros[$method], null, static::class), $parameters);
    }

    return call_user_func_array(static::$macros[$method], $parameters);
}


// vendor/laravel/framework/src/Illuminate/Support/Traits/Macroable.php
public function __call($method, $parameters)
{
if (! static::hasMacro($method)) {
throw new BadMethodCallException("Method {$method} does not exist.");
}

    if (static::$macros[$method] instanceof Closure) {
      return call_user_func_array(static::$macros[$method]->bindTo($this, static::class), $parameters);
    }

  	return call_user_func_array(static::$macros[$method], $parameters);
}
```

- Các Macroable Class trong Laravel
```text
Illuminate\Auth\RequestGuard
Illuminate\Auth\SessionGuard
Illuminate\Cache\Repository
Illuminate\Console\Command
Illuminate\Console\Scheduling\Event
Illuminate\Cookie\CookieJar
Illuminate\Database\Eloquent\FactoryBuilder
Illuminate\Database\Eloquent\Relations\Relation
Illuminate\Database\Grammar
Illuminate\Database\Query\Builder
Illuminate\Database\Schema\Blueprint
Illuminate\Filesystem\Filesystem
Illuminate\Foundation\Testing\TestResponse
Illuminate\Http\JsonResponse
Illuminate\Http\RedirectResponse
Illuminate\Http\Request
Illuminate\Http\Response
Illuminate\Http\UploadedFile
Illuminate\Mail\Mailer
Illuminate\Routing\PendingResourceRegistration
Illuminate\Routing\Redirector
Illuminate\Routing\ResponseFactory
Illuminate\Routing\Route
Illuminate\Routing\Router
Illuminate\Routing\UrlGenerator
Illuminate\Support\Arr
Illuminate\Support\Collection
Illuminate\Support\LazyCollection
Illuminate\Support\Str
Illuminate\Support\Testing\Fakes\NotificationFake
Illuminate\Translation\Translator
Illuminate\Validation\Rule
Illuminate\View\Factory
Illuminate\View\View
```
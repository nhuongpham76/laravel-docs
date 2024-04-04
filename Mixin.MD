# Mixin for laravel
```text
Trong trường hợp chúng ta có nhiều hàm muốn thêm, việc viết hàng loạt các khai báo macro trong AppServiceProvider khiến ServiceProvider của chúng ta trở nên quá lớn và việc quản lý chỉnh sửa trở nên khó khăn.
Thay vào đó, Laravel cho phép lập trình viên mix các hàm của một Object vào Macroable Class sử dụng mixin().

Tương tự như, macro() ta thực hiện khai báo mixin() trong hàm boot() của ServiceProvider.
Tuy nhiên thay vì truyền vào tên và hàm callback tương ứng ta thực hiện truyền vào một đối tượng của class ta muốn mix vào Macroable Class.
Laravel sau đó sẽ thực hiện tách các phương thức và đăng ký vào danh sách các Macros.
```

# Hướng dẫn
```text
use ...

class BuilderMixin ()
{

    /**
     * To raw sql
     *
     * @return void
     */
    public function toRawSql()
    {
        return array_reduce($this->getBindings(), function($sql, $binding){
            return preg_replace('/\?/', is_numeric($binding) ? $binding : "'".$binding."'" , $sql, 1);
        }, $this->toSql());
    }

    /**
     * To text sql
     *
     * @return void
     */
    public function toTextSql()
    {
        return array_reduce($this->getBindings(), function($sql, $binding){
            return preg_replace('/\?/', is_numeric($binding) ? $binding : "'".$binding."'" , $sql, 1);
        }, $this->toSql());
    }
}
```
- Register AppServiceProvider
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
      	Builder::mixin(new BuilderMixin());
    }
}
```

- Xử lý hàm trùng lặp
```text
Trong quá trình thêm các hàm mới, ta có khả năng sẽ sinh ra các Macros trùng lặp trong.
Mặc định Laravel sẽ thực hiện ghi đè các hàm trùng lặp với hàm chúng ta định nghĩa.
Trong trường hợp ta không muốn ghi đè các hàm đã tồn tại với hàm trong Mixin ta có thể truyền thêm giá trị boolean vào tham số thứ hai replace.
```
```text
Builder::mixin(new BuilderMixin(), false);
```
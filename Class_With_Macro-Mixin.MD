# Custom
```text
Để dễ dàng thêm các hàm tự định nghĩa vào class có sẵn,
các class nêu trên sử dụng trait Illuminate\Support\Traits\Macroable. 
```
- static void macro(string $name, object|callable $macro) : Đăng ký macro mới.
- static void mixin(object $mixin, bool $replace = true) : Mix một object vào class.
- static bool hasMacro(string $name) : Kiểm tra Macro đăng ký hay chưa.
- static mixed __callStatic(string $method, array $parameters) / static mixed __call(string $method, array $parameters) : Gọi hàm Macro được đăng ký.
# Luồng hoạt động:
1. Ta thực hiện đăng ký một hàm mới thông qua macro() hoặc mixin(), hàm callback được đưa vào một mảng static macros[].
2. Khi lập trình viên thực hiện gọi một hàm không tồn tại, PHP sẽ tự chuyển tới hàm __callStatic()/__call().
3. Laravel thực hiện kiểm tra hàm chúng ta gọi tới phải nằm trong mảng các Macros đã đăng ký.
4. Nếu hàm tồn tại, hệ thống thực hiện gọi động phương thức đó và trả về kết quả. Nếu không hệ thống trả về BadMethodCallException.


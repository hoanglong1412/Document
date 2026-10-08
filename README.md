# C++ 

## Core C++ Fundamentals

<details> <summary>Variable / Scope / Lifetime</summary>

#### 📦 Variable
```cpp
int age = 25;
[type] [name] = [value];
```
---

#### 🌍 Scope
Scope = phạm vi mà biến có thể được truy cập.

* **Local Scope / Block Scope:** Trong lòng cặp dấu `{}`.
* **Global Scope:** Toàn cục file/chương trình.
* **Class Scope:** Thuộc tính trong class.
* **Namespace Scope:** Nằm trong phân vùng không gian tên.
---

#### ⏳ Lifetime

Lifetime = khoảng thời gian biến tồn tại trong memory.

* **Automatic Lifetime (Stack):**
  * Tạo khi vào function.
  * Hủy khi ra khỏi scope.
  * Quản lý bởi Stack memory.
* **Static Lifetime:**
  * Tạo 1 lần duy nhất khi chạy chương trình.
  * Sống tới khi kết thúc chương trình.
  * Nằm ở Static storage (Data/BSS segment).
* **Dynamic Lifetime (Heap):**
  * Sống từ lúc cấp phát tới khi gọi `delete`/`free`.
  * Do lập trình viên tự quản lý.
  * Nằm ở Heap memory.
* **Thread Lifetime (C++11):**
  * Mỗi luồng (thread) có một bản sao độc lập.
  * ```cpp
    thread_local int value = 0;
    ```

#### </details> <!-- end --> 

---

<details> <summary>Memory Layout (Stack vs Heap)</summary>

#### 📊 ASCII art chart 
```text
+--------------------------+  High Address
| Stack                    |
| grows downward ↓         |
+--------------------------+
| Free Space               |
+--------------------------+
| grows upward ↑           |
| Heap                     |
+--------------------------+
| BSS Segment              |  not init static/global var
+--------------------------+
| Data Segment             |  static/global var
+--------------------------+
| Text Segment             |
+--------------------------+  Low Address
```

---

#### ⚡ Stack vs Heap

| Feature           | Stack     | Heap       |
| ------------------| --------- | ---------- |
| **Allocation**    | automatic | manual     |
| **Speed**         | fast      | slower     |
| **Lifetime**      | auto      | manual     |
| **Size**          | small     | large      |
| **Fragmentation** | no        | yes        |
| **Thread safety** | natural   | needs sync |

#### </details> <!-- end --> 

---

<details> <summary><i style="color: grey">[✓] Pointer / Reference</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] const</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] static</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] constexpr</i></summary></details>

---

<details> <summary>Inline</summary>

#### 📘 Explanation 

```cpp
inline int add(int a, int b)
{
    return a + b;
}
```

* **Ý nghĩa truyền thống:** `Gợi ý` compiler copy trực tiếp thân hàm vào chỗ gọi nhằm xóa bỏ `Function Call Overhead` (push params, save registers, jump, return).
* **Modern Compiler:** Compiler tự quyết định inline dựa trên tối ưu hóa, bất kể có keyword hay không.
* **Tác dụng thực tế (One Definition Rule - ODR):** 
  * Tránh lỗi `multiple definition error` khi một hàm định nghĩa (define) trực tiếp trong file `.h` và được `#include` ở nhiều file `.cpp`.
  * **C++17 Inline Variable:** Áp dụng tương tự cho biến toàn cục đặt trong file Header.

#### </details> <!-- end --> 

---

<details> <summary>Enum / Enum class</summary>

#### 📘 Trick 

Có thể loop qua enum bằng cách sau

```cpp
enum class MyEnum {
    NONE = -1,
    ENUM_1,
    ENUM_2,
    ENUM_3,
    MAX
};
inline MyEnum begin(MyEnum) { return MyEnum::NONE; }
inline MyEnum end(MyEnum) { return MyEnum::MAX; }
inline MyEnum operator*(MyEnum colIdx) { return colIdx; }
inline MyEnum& operator++(MyEnum& colIdx) {
 return colIdx = MyEnum(std::underlying_type<MyEnum>::type(colIdx) + 1);
}
```
#### </details> <!-- end --> 

---

<details> <summary>Typedef / using</summary>

#### 📘 Explanation 

Cả hai đều dùng để tạo bí danh (alias) cho kiểu dữ liệu:

```cpp
typedef unsigned long ulong; /* old C++ */

using ulong = unsigned long; /* Prefer trong Modern C++ vì dễ đọc và hỗ trợ Template Alias */
```

#### </details> <!-- end --> 

---

<details> <summary>Macro</summary>

#### 📘 Explanation 

- Replace text ở preprocessing (BEFORE compiler compile C++ code)
- Cách chặn việc hiểu nhầm hàm của macro -> Thêm `()` ví dụ : `(std::min)(1,2)`
- Có thể dùng kiểu `X-Macros` để giảm lặp code như ví dụ dưới

    ```cpp
    #define COLOR_LIST \
        X(RED)   \
        X(GREEN) \
        X(BLUE)

    typedef enum {
    #define X(color) COLOR_##color,
    COLOR_LIST
    #undef X
    } Color;

    const char* color_strings[] = {
    #define X(color) #color,
    COLOR_LIST
    #undef X
    };

    #define STRINGIFY(x) #x
    unordered_map<Color, string> Color_To_String{
    #define X(color) { COLOR_##color, STRINGIFY(COLOR_##color) },
    COLOR_LIST
    #undef X
    };

    unordered_map<string, Color> String_To_Color{
    #define X(color) { STRINGIFY(COLOR_##color), COLOR_##color },
    COLOR_LIST
    #undef X
    };
    ```

#### </details> <!-- end --> 

---

<details> <summary>Memory alignment / padding</summary>

#### 📘 Explanation

* **Memory Alignment (Canh lề bộ nhớ):** CPU đọc dữ liệu theo từng khối (`Chunk`: 4-byte trên x32, 8-byte trên x64). Do đó, địa chỉ của một biến phải chia hết cho kích thước của chính nó để CPU có thể đọc trọn gói trong 1 chu kỳ.
* **Data Padding:** Compiler tự động chèn các byte trống vào cấu trúc `struct/class` để đảm bảo quy tắc canh lề cho các phần tử phía sau.
* **Quy luật:** Kích thước struct sẽ bị ép căn lề theo kiểu dữ liệu có kích thước lớn nhất trong nó.

```cpp
struct A {
    char a;     // 1 byte -> chèn thêm 7 byte padding để 'b' nằm đúng biên 8-byte
    double b;   // 8 byte (Kích thước lớn nhất)
};
// => sizeof(A) = 16 bytes (thay vì 9 bytes)
```

#### </details> <!-- end --> 

---

<details> <summary><i style="color: grey">[✓] Endianness (sắp xếp byte trái <-> phải)
</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Lvalue (Có định danh - lấy được địa chỉ)</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Rvalue (temporary value - ex: 5, x+1)</i></summary></details>

---

<details> <summary>Move vs Copy semantics</summary>

#### 📘 Comparison Table 

| Feature              | Copy                                | Move                                                            |
| :--------------------| :---------------------------------- | :-------------------------------------------------------------- |
| **What it does**     | Creates a full duplicate            | Transfers ownership                                             |
| **Speed**            | Slower (copies memory/resources)    | Faster (just moves pointers)                                    |
| **Old object**       | Still holds a valid copy            | Becomes empty or "moved-from"                                   |
| **When used**        | When original is still needed       | When original is temporary/disposable                           |
| **Code Example**     | `std::string b = a;`                | `std::string b = std::move(a);`                                 |
| **Related Function** | Copy constructor<br>Copy assignment | Move constructor<br>Move assignment                             |
| **Note**             | _None_                              | • STL containers use move to improve performance<br>• `std::move` does not move, it just casts to rvalue |

#### </details> <!-- end --> 

---

<details> <summary>Rule of</summary>

#### 📘 Explanation
* **Rule of 3** : old - destructor, copy constructor/assignment
* **Rule of 5** : C++11 - rule of 3 + move constructor/assignment
* **Rule of 0** : modern - Use RAII types → avoid manual special member functions.

#### </details> <!-- end --> 

---

<details> <summary>Initialization types</summary>

#### 📘 Explanation

* direct T obj(arg) -> call constructor
* copy T obj = arg -> call constructor (nếu khác explicit)
* direct-list initialization T obj{arg} -> should use
* copy-list initialization T obj = {arg} -> (nếu khác explicit)

Note: 
* Tất cả đều gọi constructor chứ không gọi operator
* Nếu có dấu bằng thì không được gọi nếu khác explicit
* Nên dùng direct-list initialization vì sẽ tránh được narrowing conversion (Tự convert làm sai giá trị, `ví dụ: int a = 3.14 => a = 3`)

#### </details> <!-- end -->

---

<details> <summary><i style="color: grey">[✓] Function overloading (hàm cùng tên, khác param)
</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Default arguments (từ phải -> trái; chạy lúc compile -> nếu virtual thì gọi default của Base chứ ko phải Derived)</i></summary></details>

---

<details> <summary>Lambda expression</summary>

#### 📘 Code

```cpp
[capture](parameters)
{
    body
}

// capture:
//  [var_name] (value)
//  [&var_name] (ref)
//  [=] (all value)
//  [&] (all ref)
```

#### </details> <!-- end --> 

---

<details> <summary>Function pointer</summary>

#### 📘 Code

```cpp
return_type (*pointer_name)(parameters)
int (*fp)(int, int);
```

#### </details> <!-- end --> 

---

<details> <summary><i style="color: grey">[✓] std::function</i></summary></details>

---

<details> <summary>Exception</summary>

#### 📘 Explanation

- Exception xảy ra thì sẽ tìm catch gần nhất theo callstack, nếu tìm không thấy thì terminate
- **Stack Unwinding** : khi đang tìm chỗ catch lúc runtime thì scope được rời khỏi lần lượt và destructor của các object local được gọi => RAII + exception safety
- Khi đã tìm thấy catch thì sẽ xử lý ở catch đó và dừng lại => không tìm catch khác nữa
- **Rethrow** : trong TH vẫn muốn tìm catch khác theo callstack thì gọi `throw` để tiếp tục throw ra ngoài
- **noexcept** : Các hàm được đánh dấu `noexcept` có nghĩa là đảm bảo không xảy ra exception, TH xảy ra sẽ termiante
- Destructor mặc định chính là noexcept => không được throw ở Destructor
- Catch sẽ bắt theo thứ tự từ trên xuống, và sẽ dừng khi có 1 catch nhận được (trừ TH Rethrow) => cần sắp xếp catch theo thứ thự ưu tiên
- Flow
    ```
    Exception
      ↓
    throw
      ↓
    find matching catch
      ↓
    stack unwinding
      ↓
    RAII destructors cleanup
      ↓
    catch
    ```

#### </details> <!-- end --> 

---

## Object-Oriented Programming (OOP)

<details> <summary><i style="color: grey">[✓] Class (blueprint) / Object (instance)</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Constructor (default, param, copy, move,...) / Destructor</i></summary></details>

---

<details> <summary>Copy constructor</summary>

#### 📘 Explanation
Hàm khởi tạo sao chép dùng để tạo một đối tượng mới từ một đối tượng đã tồn tại. Nếu lập trình viên không tự định nghĩa, compiler sẽ tự sinh ra một bản mặc định thực hiện sao chép nông (**Shallow Copy**).

```cpp
ClassName(const ClassName& other)
{
    // ex1: deep copy (Cấp phát vùng nhớ mới và chép dữ liệu qua)
    data = new Data(*other.data); 
    
    // ex2: shallow copy (Chỉ chép địa chỉ con trỏ, trỏ chung một vùng nhớ)
    data = other.data;            
}

// Cách gọi thực tế:
ClassName* instance = new ClassName(other_instance);
```

#### </details> <!-- end --> 

---

<details> <summary>Move constructor</summary>

#### 📘 Explanation
Hàm khởi tạo di chuyển (C++11) giúp chuyển giao quyền sở hữu tài nguyên từ một đối tượng tạm thời sang đối tượng mới mà không cần sao chép dữ liệu, giúp tối ưu hiệu năng.

```cpp
ClassName(ClassName&& other) noexcept
{
    data = other.data;     // Lấy quyền sở hữu tài nguyên
    other.data = nullptr;  // Đưa đối tượng cũ về trạng thái an toàn
}

// Cách gọi thực tế bằng cách cast sang rvalue:
ClassName* instance = new ClassName(std::move(other_instance));
```

#### </details> <!-- end --> 

---

<details> <summary>Assignment operator</summary>

#### 📘 Explanation
Toán tử gán được dùng khi đối tượng đã được khởi tạo trước đó và muốn gán giá trị mới. Bao gồm hai loại: toán tử gán sao chép (Copy Assignment) và toán tử gán di chuyển (Move Assignment).

```cpp
// 1. Copy Assignment Operator
ClassName& operator=(const ClassName& other)
{
    if (this == &other) return *this; // Kiểm tra tự gán (Self-assignment)

    delete data; // Giải phóng tài nguyên hiện tại

    data = new Data(*other.data); // Deep copy
    // data = other.data;         // Shallow copy

    return *this;
}

// 2. Move Assignment Operator
ClassName& operator=(ClassName&& other) noexcept
{
    if (this == &other) return *this; // Kiểm tra tự gán

    delete data;           // Giải phóng tài nguyên hiện tại
    data = other.data;     // Chuyển giao tài nguyên
    other.data = nullptr;  // Đưa đối tượng cũ về trạng thái an toàn

    return *this;
}
```

#### </details> <!-- end --> 

---

<details> <summary>Operator cheatsheet</summary>

#### 📘 Cheatsheet
```
Operator Cheatsheet 
│ 
├── Operator Overloading 
│ ├── T operator+(const T& rhs) const { return /* T */; } 
│ ├── bool operator==(const T& rhs) const { return /* bool */; } 
│ ├── T* operator->() const { return ptr; } 
│ ├── T& operator*() const { return *ptr; } 
│ └── auto operator<=>(const T& rhs) const = default; 
│ 
├── Functor (Gọi object như hàm) 
│ └── R operator()(/* params */) const { return /* R */; } 
│ 
├── Conversion Operator (Cho phép chuyển object thành type khác) 
│ ├── operator bool() const { return /* bool */; } 
│ ├── operator int() const { return /* int */; } 
│ └── explicit operator T() const { return /* T */; } 
│ 
├── User-Defined Literal 
│ └── constexpr T operator"" _km(long double value) { return /* T */; } 
│ 
├── Custom Memory Management 
│ ├── static void* operator new(size_t size) { return ::operator new(size); } 
│ └── static void operator delete(void* ptr) noexcept { ::operator delete(ptr); } 
│ 
├── Coroutine Customization 
│ └── Awaiter operator co_await() { return /* Awaiter */; } 
│ 
└── Copy / Move Semantics 
 ├── T& operator=(const T& rhs) { /* copy */ return *this; } 
 └── T& operator=(T&& rhs) noexcept { /* move */ return *this; } 
```

#### </details> <!-- end --> 

---

<details> <summary><i style="color: grey">[✓] Encapsulation (tính đóng gói - giấu data)</i></summary></details>

---

<details> <summary>Inheritance (kế thừa)</summary>

#### 📘 Explanation
Cho phép một lớp (Derived Class) kế thừa lại các thuộc tính và phương thức từ lớp khác (Base Class). Phạm vi truy cập (Access Modifier) của các thành phần bị thay đổi tùy thuộc vào kiểu kế thừa:

```cpp
class Derived : public Base     // -> Giữ nguyên access modifier của Base khi sang Derived
class Derived : protected Base  // -> Chuyển tất cả thành phần public của Base thành protected ở Derived
class Derived : private Base    // -> Chuyển tất cả thành phần public/protected của Base thành private ở Derived
```

#### </details> <!-- end --> 

---

<details> <summary><i style="color: grey">[✓] Polymorphism (đa hình - overload/override)</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Abstraction (trừu tượng - ẩn xử lý phức tạp, chỉ thấy cái cần dùng)</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Virtual function (Nếu gọi hàm virtual ở construtor thì hàm thực thi là hàm base, do lúc đó vptr đang trỏ tới base; nhớ luôn thêm virtual cho destructor của Base Class)</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Pure virtual function</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Abstract class</i></summary></details>

---

<details> <summary>Vtable / Vptr</summary>

#### 📘 Explanation
Cơ chế giúp C++ thực hiện đa hình động (**Dynamic Binding**) tại thời điểm Runtime:
* **Vtable (Virtual Table):** Một bảng tĩnh được tạo ra cho mỗi Class có chứa hàm `virtual`, lưu trữ danh sách các con trỏ hàm trỏ tới các hàm virtual tương ứng. Được lưu ở readonly segment
* **Vptr (Virtual Table Pointer):** Một con trỏ ẩn được tự động thêm vào bên trong mỗi **đối tượng** (instance). Nó trỏ thẳng tới bảng `Vtable` của Class đó để tra cứu hàm cần gọi khi Runtime.

```text
Animal object
+------------------+
| vptr ------------|----+
| data members     |    |
+------------------+    |
                        ▼
                 +------------------+
                 | Animal_vtable    |
                 | speak()          |
                 | eat()            |
                 +------------------+
```

#### </details> <!-- end --> 

---

<details> <summary><i style="color: grey">[✓] Override (ghi đè hàm lớp cha) / Final (ngăn không cho lớp con ghi đè hoặc kế thừa)</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Friend class / function (cho phép hàm hoặc class bên ngoài truy cập vào các thành viên private/protected)</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Multiple inheritance (thêm 1 kế thừa là thêm 1 vptr; thứ tự constutor từ trái -> phải; Destructor là ngược lại)</i></summary></details>

---

<details> <summary>Diamond problem</summary>

#### 📘 Explanation
Lỗi "hình thoi" xảy ra trong đa kế thừa khi một lớp con kế thừa từ hai lớp cha, mà hai lớp cha đó lại cùng kế thừa từ một lớp ông tổ. Điều này khiến lớp con có 2 bản sao của lớp ông tổ, gây ra sự mơ hồ (**Ambiguous Error**) khi gọi hàm.

```cpp
class Grand;
class Parent_1 : public Grand;
class Parent_2 : public Grand;
class Son : public Parent_1, public Parent_2; 
// -> Lỗi biên dịch: Ambiguous error

// --- Cách khắc phục ---
// Cách 1: Sử dụng kế thừa ảo (Virtual Inheritance) khi khai báo lớp cha
class Parent_1 : virtual public Grand;
class Parent_2 : virtual public Grand;

// Cách 2: Chỉ định rõ hàm của lớp cha muốn gọi bằng toán tử phạm vi ::
son.Parent_1::fun();
```

#### </details> <!-- end --> 

---

<details> <summary>Object slicing</summary>

#### 📘 Explanation
Hiện tượng "lát cắt đối tượng" xảy ra khi bạn gán hoặc truyền một đối tượng lớp con (Derived) vào một đối tượng lớp cha (Base) theo kiểu **truyền tham trị (Pass-by-value)**. Hệ thống sẽ sao chép phần thuộc tính của lớp cha và cắt bỏ hoàn toàn phần thuộc tính mở rộng của lớp con.

```cpp
Dog d;
Animal a = d; // Toàn bộ phần dữ liệu của Dog bị cắt mất, 'a' chỉ còn là Animal

void foo(Animal a) { /* Dữ liệu Dog bị slice tại đây */ }
foo(d);

// --- Cách khắc phục ---
// Luôn sử dụng Tham chiếu (Reference) hoặc Con trỏ (Pointer) khi truyền đối tượng đa hình
void foo(const Animal& a) { /* Không bị object slicing */ }
```

#### </details> <!-- end --> 

---

<details> <summary>RTTI (Run-Time Type Information)</summary>

#### 📘 Explanation
Cơ chế của C++ cho phép xác định chính xác kiểu dữ liệu thực tế của một đối tượng ngay trong quá trình chương trình đang chạy (Runtime). RTTI chỉ hoạt động với các Class có tính đa hình (có ít nhất một hàm `virtual`).

* **`dynamic_cast`:** Ép kiểu an toàn giữa các lớp trong cây kế thừa tại thời điểm runtime. Nếu ép kiểu con trỏ thất bại, nó trả về `nullptr`. Nếu ép kiểu tham chiếu thất bại, nó ném ra ngoại lệ `std::bad_cast`.
* **`typeid`:** Trả về đối tượng `std::type_info` chứa thông tin về kiểu dữ liệu của biến hoặc đối tượng tại runtime.

#### </details> <!-- end --> 

</details>

---

## Modern C++ (C++11 → C++23)

<details> <summary><i style="color: grey">[✓] auto</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Range-based for `for (auto item : item_list)`</i></summary></details>

---

<details> <summary>Smart pointers</summary>

#### 📘 Explanation
Là các lớp bao bọc (RAII wrapper) giúp quản lý bộ nhớ tự động, ngăn ngừa rò rỉ bộ nhớ (memory leak) bằng cách tự giải phóng tài nguyên khi con trỏ ra khỏi phạm vi hoạt động (scope).

* **`unique_ptr`:** Sở hữu độc quyền tài nguyên (Exclusive ownership). Không thể sao chép (copy), chỉ có thể chuyển giao quyền sở hữu qua cơ chế di chuyển (move semantics). Siêu nhẹ và có hiệu năng tương đương con trỏ thô.
* **`shared_ptr`:** Đồng sở hữu tài nguyên (Shared ownership). Nhiều con trỏ có thể trỏ vào cùng một vùng nhớ. Hệ thống sử dụng một khối điều khiển chứa biến đếm tham chiếu (Reference Counting). Khi số lượng tham chiếu về `0`, vùng nhớ sẽ tự giải phóng.
* **`weak_ptr`:** Trình quan sát không sở hữu (Non-owning observer). Được dùng để liên kết với một `shared_ptr` nhưng không làm tăng biến đếm tham chiếu. Mục đích cốt lõi là giải quyết lỗi tham chiếu vòng (**Circular Reference**) gây treo bộ nhớ.

#### </details> <!-- end --> 

---

<details> <summary><i style="color: grey">[✓] nullptr</i></summary></details>

---

<details> <summary>decltype</summary>

#### 📘 Explanation
Từ khóa dùng để kiểm tra kiểu dữ liệu của một biểu thức hoặc một biến tại thời điểm biên dịch (Compile-time). Quy tắc xử lý dựa trên cấu trúc bao bọc của biểu thức:

```cpp
int x = 10;

// Trường hợp truyền tên biến thông thường
decltype(x) a_1 = x;    // typeof(a_1) => int

// Trường hợp truyền biểu thức (có dấu ngoặc đơn lồng nhau)
decltype((x)) a_2 = x;  // typeof(a_2) => int& (Tham chiếu)
```

#### </details> <!-- end --> 

---

<details> <summary><i style="color: grey">[✓] Structured binding (unpack giá trị cấu trúc hoặc mảng, ví dụ: `auto [x, y] = p;`)</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] std::optional (Hộp chứa giá trị có thể có hoặc trống, ví dụ: `std::optional<int>` -> nhận `std::nullopt` hoặc gọi hàm `.has_value()`)</i></summary></details>

---

<details> <summary>std::variant</summary>

#### 📘 Explanation
Hộp chứa an toàn về kiểu dữ liệu (Type-safe union) của C++17. Tại một thời điểm, nó chỉ lưu trữ duy nhất một giá trị thuộc một trong các kiểu được định nghĩa trước. Nếu truy cập sai kiểu dữ liệu, hệ thống sẽ ném ra ngoại lệ `std::bad_variant_access`.

```cpp
std::variant<int, double, std::string> v;

v = 3.14;                  // v giữ kiểu double
v = "Hello C++";           // v chuyển sang giữ kiểu std::string

// Lấy giá trị ra bằng std::get
std::string s = std::get<std::string>(v);
```

#### </details> <!-- end --> 

---

<details> <summary>std::tuple</summary>

#### 📘 Explanation
Cấu trúc dữ liệu chứa một nhóm các phần tử có kiểu dữ liệu khác nhau với số lượng cố định. Được dùng làm giá trị trả về của hàm khi muốn trả về nhiều biến mà không cần phải khai báo một `struct` thủ công.

```cpp
// Trước C++17 (Phải khai báo tường minh hoặc dùng std::make_tuple)
std::tuple<int, bool, std::string> info = std::make_tuple(101, true, "A");

// Từ C++17 (Hỗ trợ CTAD - Class Template Argument Deduction tự suy luận kiểu)
std::tuple info { 101, true, 'A' };
```

#### </details> <!-- end --> 

---

<details> <summary>std::any</summary>

#### 📘 Explanation
Hộp chứa đa kiểu dữ liệu an toàn (Type-safe container) từ C++17, cho phép một biến có thể thay đổi và lưu trữ bất kỳ kiểu dữ liệu nào tại thời điểm Runtime.

```cpp
std::any value;

value = 10;                   // Chứa int
value = std::string("Hello"); // Đổi sang chứa std::string

// Ép kiểu an toàn để lấy giá trị ra
int x = std::any_cast<int>(value); // Nếu ép sai kiểu sẽ ném ra ngoại lệ std::bad_any_cast
```

#### </details> <!-- end --> 

---

<details> <summary><i style="color: grey">[✓] std::span (Vùng xem liên tục mảng dữ liệu, dùng thay thế an toàn cho cấu trúc con trỏ thô kết hợp biến kích thước `T* + size`)</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Concepts (C++20: Cơ chế ràng buộc điều kiện cho Template tại thời điểm Compile-time, dùng thay thế sạch sẽ cho `std::enable_if` của SFINAE)</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Coroutines (C++20: Các hàm bất đồng bộ có khả năng tạm dừng và tiếp tục thực thi, sử dụng thông qua các từ khóa `co_await`, `co_yield`, `co_return`)</i></summary></details>

---

<details> <summary>Modules</summary>

#### 📘 Explanation
Hệ thống quản lý mã nguồn hiện đại từ C++20 nhằm thay thế cơ chế `#include` tiền xử lý lỗi thời. Modules giúp tối ưu hóa tốc độ biên dịch (chỉ biên dịch một lần) và cô lập phạm vi mã nguồn tốt hơn.

```cpp
// File: MathModule.cppm (Khai báo module)
export module MathModule;
export int add(int a, int b) { return a + b; }

// File: main.cpp (Sử dụng module)
import MathModule;
import std::iostream;

int main() {
    std::cout << add(5, 10);
}
```

#### </details> <!-- end --> 

---

## MultiProcess / Multithreading / Concurrency

<details> <summary>Process</summary>

#### 📘 Explanation
Tiến trình (Process) là một chương trình đang thực thi và được hệ điều hành cấp phát một không gian địa chỉ bộ nhớ cùng tài nguyên hệ thống hoàn toàn độc lập.

* Tất cả những ứng dụng hiển thị độc lập trong Task Manager đều là các Process.
* Một ứng dụng lớn có thể chạy đồng thời nhiều Process (ví dụ: Google Chrome chia mỗi tab thành một tiến trình riêng).
* Các Process không dùng chung bộ nhớ; muốn giao tiếp với nhau phải sử dụng các cơ chế IPC (Inter-Process Communication) như Shared Memory, Pipes, Sockets.

#### 📘 IPC (Inter-Process Communication)

| Cách IPC | Mô hình truyền dữ liệu | Phạm vi | Ưu điểm | Điểm cần lưu ý |
| :--- | :--- | :--- | :--- | :--- |
| **Pipe (ống dẫn vô danh)** | Luồng byte một chiều | Các tiến trình có quan hệ, thường là cha–con | Đơn giản, phù hợp truyền dữ liệu tuần tự | Không giữ ranh giới giữa các thông điệp |
| **Named Pipe / FIFO** | Luồng byte một chiều | Các tiến trình trên cùng máy | Các tiến trình không cần có quan hệ cha–con | Cần thống nhất cách đóng gói dữ liệu |
| **Message Queue** | Hàng đợi các thông điệp riêng biệt | Thường trên cùng máy | Giữ ranh giới thông điệp, gửi và nhận không cần diễn ra cùng lúc | Bị giới hạn kích thước hàng đợi và thông điệp |
| **Shared Memory** | Nhiều tiến trình cùng đọc/ghi một vùng nhớ | Cùng máy | Tránh sao chép dữ liệu qua lại sau khi thiết lập | Phải tự đồng bộ để tránh *data race* |
| **Semaphore** | Bộ đếm/tín hiệu đồng bộ | Thường trên cùng máy | Điều phối quyền truy cập tài nguyên | Không dùng để truyền nội dung dữ liệu |
| **Signal** | Thông báo sự kiện ngắn | Cùng máy | Gửi thông báo nhanh như dừng hoặc đánh thức | Chỉ truyền rất ít thông tin; xử lý signal có nhiều giới hạn |
| **Socket** | Luồng byte (TCP) hoặc datagram (UDP) | Cùng máy hoặc qua mạng | Linh hoạt, hỗ trợ ứng dụng phân tán | Cần xử lý kết nối, giao thức và lỗi mạng |
| **RPC** | Gọi hàm/dịch vụ qua một giao thức | Cùng máy hoặc qua mạng | Giao diện gần giống lời gọi hàm thông thường | Vẫn có độ trễ và lỗi truyền thông; cần tuần tự hóa dữ liệu |


---

<details>
<summary>IPC Detail</summary>

### 🔹 Pipe

> Hệ điều hành tạo một vùng đệm. Tiến trình ghi byte vào một đầu; tiến trình khác đọc byte từ đầu còn lại.

```text
Process A ── write() ──► [ Kernel Buffer ] ── read() ──► Process B
```

- **Pipe vô danh:** Thường được tạo trước `fork()`, để tiến trình cha và con cùng sử dụng.
- **Named Pipe / FIFO:** Có tên trong hệ thống tệp, nên các tiến trình không có quan hệ cha–con cũng có thể mở nó.
- Một pipe thông thường truyền theo **một chiều**; muốn trao đổi hai chiều có thể dùng hai pipe.
- Pipe là **luồng byte**: một lần `read()` không nhất thiết tương ứng với một lần `write()`. Ứng dụng cần tự quy định độ dài hoặc ký tự phân cách thông điệp.

---

### 🔹 Message Queue

> Bên gửi đặt từng thông điệp vào hàng đợi; bên nhận lấy chúng ra sau đó.

```text
Process A ── send(M1) ──► ┌────┬────┬────┐ ── receive() ──► Process B
                          │ M1 │ M2 │ M3 │
                          └────┴────┴────┘
```

- **Giữ ranh giới thông điệp:** Bên nhận lấy một thông điệp, không phải một đoạn byte tùy ý như pipe.
- Hai tiến trình không nhất thiết phải chạy hoặc gọi gửi/nhận tại cùng một thời điểm, miễn hàng đợi còn tồn tại và còn chỗ.
- Có giới hạn về dung lượng và kích thước thông điệp; hành vi khi hàng đợi đầy phụ thuộc API và chế độ chặn/không chặn.

---

### 🔹 Shared Memory

> Hai tiến trình ánh xạ cùng một vùng nhớ vào không gian địa chỉ của mình. Sau khi thiết lập, chúng truy cập dữ liệu trực tiếp.

```text
Process A ── read/write ──► [ Shared Memory ] ◄── read/write ── Process B
```

Phù hợp khi trao đổi **lượng dữ liệu lớn** hoặc cập nhật thường xuyên. Tuy nhiên, nếu hai bên cùng sửa dữ liệu, kết quả có thể sai do *data race*.

### 🔹 Semaphore

> Semaphore điều phối thời điểm tiến trình được truy cập vùng nhớ hoặc tài nguyên chung.

```text
Process A: wait() → ghi Shared Memory → post()
Process B: wait() → đọc Shared Memory → post()
```

Trong thực tế, có thể dùng **mutex**, **semaphore** hoặc cơ chế đồng bộ phù hợp khác. Semaphore không tự mang nội dung cần trao đổi; dữ liệu vẫn nằm trong Shared Memory hoặc một kênh IPC khác.

---

### 🔹 Signal

> Một tiến trình hoặc hệ điều hành gửi tín hiệu để báo cho tiến trình khác rằng một sự kiện đã xảy ra.

```text
Process A ── signal ──► Process B
                         └─ xử lý sự kiện / dừng / tiếp tục
```

Ví dụ trên hệ Unix là `SIGTERM` để yêu cầu kết thúc tiến trình. Signal thích hợp cho **thông báo ngắn**, không thích hợp để truyền một khối dữ liệu. Khi cần gửi dữ liệu, hãy kết hợp nó với kênh IPC khác.

---

### 🔹 Socket

> Mỗi tiến trình dùng một socket làm điểm gửi/nhận dữ liệu.

```text
Process A ── Socket A ◄──── mạng / hệ điều hành ────► Socket B ── Process B
```

- **TCP:** Truyền luồng byte tin cậy, có thứ tự. Ứng dụng vẫn phải tự xác định ranh giới thông điệp.
- **UDP:** Truyền từng datagram; ứng dụng cần xử lý khả năng mất gói hoặc sai thứ tự khi cần.
- **Unix domain socket:** Dùng giữa các tiến trình trên cùng máy, không cần đi qua mạng IP.

### 🔹 RPC (Remote Procedure Call)

> RPC xây trên một kênh truyền như socket, đóng gói yêu cầu và phản hồi thành lời gọi dịch vụ.

```text
Client: getUser(42)
          │
          ▼
    [ Mã hóa → Truyền → Giải mã ]
          │
          ▼
Server: xử lý → trả kết quả
```

RPC giúp tổ chức giao tiếp theo các hàm/dịch vụ, nhưng lời gọi vẫn có thể chậm, hết thời gian chờ hoặc thất bại do kết nối.


 </details>
</details> <!-- end --> 

---

<details> <summary>Thread</summary>

#### 📘 Explanation
Luồng (Thread) là đơn vị thực thi mã nguồn nhỏ nhất được hệ điều hành định thời (Schedule). Một Process có thể chứa nhiều Thread chạy song song.

* Các Thread trong cùng một Process chia sẻ chung vùng nhớ (Heap, Global, Static) và tài nguyên của Process đó.
* **Đặc điểm quan trọng:** Mỗi Thread sở hữu một vùng nhớ Stack riêng biệt (để lưu biến cục bộ, gọi hàm của riêng luồng đó) chứ không dùng chung Stack với Thread khác. *(stack của thread chứ ko phải stack của object)*

#### 📊 C++ Multithreading Cheat Sheet
| Nhóm Công Cụ | Tên Công Cụ / Khóa Từ | Đặc Tính Cốt Lõi | Mục Đích Sử Dụng Chính | Khuyên Dùng / Lưu Ý |
|---|---|---|---|---|
| Bảo Vệ Dữ Liệu Shared (Data Protection) | std::mutex (C++11) | Khóa độc quyền (Mutual Exclusion). Chỉ 1 thread được giữ khóa tại một thời điểm. | Tránh Race Condition khi nhiều thread cùng ghi vào một vùng nhớ (vùng tranh chấp). | Thường dùng kèm std::lock_guard hoặc std::unique_lock để tự động unlock (RAII). |
| | std::recursive_mutex (C++11) | Cho phép 1 thread khóa lại nhiều lần trên cùng 1 mutex mà không bị Deadlock. | Dùng khi các hàm thành viên gọi lẫn nhau và đều yêu cầu khóa chung một mutex. | Tránh lạm dụng vì làm tăng chi phí quản lý tài nguyên (overhead). |
| | std::shared_mutex (C++17) | Khóa phân quyền: Nhiều thread cùng đọc (Shared), nhưng chỉ 1 thread được ghi (Exclusive). | Áp dụng cho mô hình Reader-Writer: Dữ liệu đọc nhiều nhưng ít khi ghi/cập nhật. | Tăng hiệu năng rõ rệt so với mutex thường khi có số lượng thread đọc áp đảo. |
| Thao Tác Nguyên Tử (Lock-Free) | std::atomic<T> (C++11) | Thao tác không thể bị chia cắt ở cấp độ CPU (Read-Modify-Write). Không dùng Lock. | Bảo vệ các biến đơn lẻ (đếm counter, cờ hiệu bool, con trỏ) mà không bị overhead do lock. | Nhanh hơn mutex. Chỉ áp dụng được cho các kiểu dữ liệu cơ bản (primitive types). |
| Báo Hiệu / Đồng Bộ (Signaling) | std::condition_variable (C++11) | Cho phép thread ngủ đông để chờ một điều kiện cụ thể được kích hoạt bởi thread khác. | Giải quyết bài toán Producer-Consumer, đồng bộ thứ tự thực thi giữa các thread. | Bắt buộc phải dùng kèm với std::unique_lock và biến std::mutex. |
| | std::latch (C++20) | Bộ đếm ngược một chiều (Count-down). Khi đếm về 0, tất cả các thread đang đợi sẽ được giải phóng. | Đồng bộ điểm xuất phát: Chờ một nhóm tác vụ hoàn thành xong mới cho luồng chính chạy tiếp. | Chỉ sử dụng được một lần duy nhất (không thể tái thiết lập lại bộ đếm). |
| | std::barrier (C++20) | Bộ đếm ngược nhiều chiều, có tính chu kỳ. Tự động reset lại bộ đếm sau khi mở cổng. | Đồng bộ các thread chạy theo từng giai đoạn (Phases) lặp đi lặp lại. | Có thể truyền thêm một hàm Callback để thực thi mỗi khi hoàn thành một Phase. |
| | std::counting_semaphore (C++20) | Bộ đếm giới hạn số lượng thread được phép truy cập đồng thời vào một tài nguyên (Slot). | Giới hạn tài nguyên phần cứng (Ví dụ: Giới hạn tối đa 5 kết nối DB được mở cùng lúc). | std::binary_semaphore (giới hạn = 1) có thể dùng thay thế cho Mutex nhưng không có khái niệm Ownership. |
| Truyền Dữ Liệu Bất Đồng Bộ (Task-Based) | std::promise / std::future (C++11) | Kênh truyền dữ liệu/exception một chiều và duy nhất một lần (One-shot) giữa 2 thread. | Trả kết quả từ luồng phụ (Worker) về luồng chính (Main). | Sử dụng std::shared_future nếu có nhiều thread Consumer cùng muốn lấy kết quả (Multiple Readers). |
| | std::packaged_task (C++11) | Đóng gói một hàm (Callable) để tự động nạp kết quả trả về (return) vào một std::future. | Tạo cấu trúc Thread Pool, tách biệt giữa việc định nghĩa tác vụ và việc thực thi nó trên thread. | Không thể copy, bắt buộc phải dùng std::move khi chuyển giao giữa các thread. |
| | std::async (C++11) | Hàm cấp cao tự động kích hoạt một tác vụ chạy bất đồng bộ (có thể tạo thread mới hoặc hoãn lại). | Thực thi nhanh một hàm ngầm mà không muốn tự quản lý vòng đời của std::thread. | Bẫy destructor: Cần lưu kết quả vào biến auto f, nếu không nó sẽ block main thread chạy tuần tự. |
| Quản Lý Vòng Đời Luồng (Thread Management) | std::jthread (C++20) | Bản nâng cấp của std::thread. Tự động gọi .join() khi bị hủy (RAII) và hỗ trợ dừng luồng từ xa. | Thay thế hoàn toàn cho std::thread truyền thống để tránh lỗi crash chương trình do quên .join(). | Hỗ trợ std::stop_token giúp luồng chủ động kiểm tra xem có bị yêu cầu dừng (stop_requested) hay không. |
| Lập Trình Bất Đồng Bộ Cao Cấp | Coroutines (co_await, co_return) (C++20) | Hàm có khả năng tạm dừng (suspend) và tiếp tục (resume) mà không làm block thread hệ điều hành. | Lập trình bất đồng bộ quy mô lớn (xử lý hàng vạn kết nối mạng I/O cùng lúc) với cú pháp như code tuần tự. | C++20 chỉ cung cấp hạ tầng cấp thấp (plumbing). Cần dùng thư viện bên thứ 3 (cppcoro, asio) để triển khai thực tế. |


#### </details> <!-- end --> 

---

<details> <summary>Race condition</summary>

#### 📘 Explanation
* Short: thứ tự thực thi sai
* Vấn đề sai sót về mặt thời gian hoặc thứ tự thực thi của các thread trong chương trình khiến cho kết quả cuối cùng không đúng như mong muốn

#### </details> <!-- end --> 

---

<details> <summary>Data race</summary>

#### 📘 Explanation
* short: Data sai, rác
* Từ 2 thread/process trở lên cùng truy cập vào vùng nhớ chung (shared resource).
* Ít nhất 1 thread/process write

#### </details> <!-- end --> 

---

<details> <summary>Mutex</summary>

#### 📘 Explanation
Cơ chế đồng bộ hóa luồng, đảm bảo tại một thời điểm chỉ có duy nhất một luồng được quyền truy cập vào vùng mã giới hạn (Critical Section).

* **`std::lock_guard`:** Trình quản lý khóa theo cơ chế RAII. Nó tự động khóa Mutex khi khởi tạo và tự động giải phóng (Unlock) khi ra khỏi phạm vi Scope, ngăn ngừa quên unlock gây treo luồng.
* **`std::unique_lock`:** Cơ chế bọc khóa nâng cao dựa trên RAII. Nó linh hoạt hơn `std::lock_guard` vì hỗ trợ hoãn khóa (Deferred locking), khóa/mở thủ công nhiều lần trong Scope, cấu hình thời gian chờ (Timeout) và bắt buộc phải dùng kèm với `std::condition_variable`.

#### </details> <!-- end --> 

---

<details> <summary><i style="color: grey">[✓] Recursive mutex (Cùng một thread lock nhiều lần mà không bị deadlock - dùng lock-count để biết có log không)
</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Spinlock (Cơ chế lặp vòng liên tục để kiểm tra trạng thái khóa thay vì đưa luồng vào trạng thái ngủ như Mutex thông thường)</i></summary></details>

---

<details> <summary>Semaphore</summary>

#### 📘 Explanation
Cơ chế đồng bộ hóa dựa trên biến đếm (Signaling), dùng để giới hạn số lượng luồng cùng truy cập vào một tài nguyên hoặc phát tín hiệu thông báo giữa các luồng.

```cpp
#include <semaphore>

// std::counting_semaphore<MAX> sem(INITIAL);
// Tạo semaphore có max là 3 slot (tránh lỗi cữ release làm tăng lên vô hạn slot - counter thì cần = max)
// hiện tại đang available 3 slot (counter = 3)
std::counting_semaphore<3> sem(3);

void worker()
{
    sem.acquire(); 
    // - Xin cấp phát 1 slot (Biến đếm giảm 1)
    // - Nếu biến đếm == 0, luồng hiện tại bị chặn và phải đợi

    // critical work

    sem.release(); 
    // - Trả lại 1 slot (Biến đếm tăng 1)
    // - Nếu có luồng khác đang đợi, hệ thống sẽ đánh thức luồng đó dậy
}
```

#### </details> <!-- end --> 

---

<details> <summary>Atomic</summary>

#### 📘 Explanation
Cơ chế đảm bảo một tác vụ được thực hiện trọn vẹn, không thể bị chia cắt hay gián đoạn bởi luồng khác (Thread-safe mà không cần dùng khóa mutex).

* **Thao tác không Atomic:** Lệnh tăng biến `x++` của CPU thực chất bị chia làm 3 bước rời rạc: Đọc giá trị (Load) -> Tăng giá trị (Add) -> Lưu lại (Store). Nhiều luồng xen vào giữa các bước này sẽ gây sai lệch dữ liệu.
* **Thao tác Atomic (`std::atomic`):** Gộp các bước trên thành một tác vụ duy nhất không thể phân tách. Các luồng khác bắt buộc phải đợi tác vụ này hoàn thành xong mới được truy cập. Các hàm thành viên phổ biến: `load`, `store`, `exchange`.
* **Lưu ý về `static`:** local `static` trong hàm được đảm bảo Thread-safe từ C++11 (Magic Statics) nhưng **chỉ an toàn trong quá trình khởi tạo giá trị lần đầu tiên** (bằng cơ chế mutex ngầm). Sau khi khởi tạo xong, các thao tác đọc/ghi tiếp theo trên biến đó không hề Thread-safe.

#### </details> <!-- end --> 

---

<details> <summary>Memory ordering</summary>

#### 📘 Explanation
CPU/Compiler tự động re-order (đảo lệnh) để tối ưu hiệu năng => gây bug trong đa luồng.
Giải quyết: Dùng `std::atomic` kết hợp các Memory Ordering để đặt "hàng rào" chặn đảo lệnh.

* **memory_order_relaxed:** Chỉ bảo vệ tính nguyên tử (ko rách dữ liệu), tự do đảo lệnh. Dùng cho bộ đếm (counter).
* **memory_order_release:** (Dùng cho Store) Các lệnh PHÍA TRƯỚC ko được nhảy xuống PHÍA SAU lệnh này. Dùng để "gửi" dữ liệu.
* **memory_order_acquire:** (Dùng cho Load) Các lệnh PHÍA SAU ko được nhảy lên PHÍA TRƯỚC lệnh này. Dùng để "nhận" dữ liệu.
* **memory_order_acq_rel:** Kết hợp cả 2 cho thao tác Read-Modify-Write (như `fetch_add`).
* **memory_order_seq_cst:** Default. Nghiêm ngặt nhất. Ép tất cả các luồng phải thấy chung một thứ tự thực thi toàn cục duy nhất. Chi phí phần cứng cao nhất.


#### </details> <!-- end --> 

---

<details> <summary><i style="color: grey">[✓] Deadlock (các thread chờ lẫn nhau)</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Livelock (các thread vẫn chạy nhưng không có tiến triển - chạy lòng vòng)</i></summary></details>


---

<details> <summary><i style="color: grey">[✓] Starvation (thread bị bỏ đói, không bao giờ được chạy)</i></summary></details>

---

<details> <summary>Condition variable</summary>

#### 📘 Cơ chế cốt lõi
Giải pháp cho luồng "đi ngủ" chờ điều kiện mà không tốn CPU (thay vì dùng vòng lặp vô hạn `while`).
* **Bắt buộc đi kèm:** `std::mutex` và `std::unique_lock<std::mutex>`.
* **Không dùng:** `std::lock_guard` (vì CV cần nhả/gài lại khóa liên tục trong lúc chờ).

#### </details> <!-- end --> 

---

<details> <summary>Future / Promise</summary>

#### 📘 Explanation
Cơ chế đồng bộ hóa truyền dữ liệu một chiều bất đồng bộ từ C++11:
* **`std::promise` (Producer):** Tôi sẽ cung cấp kết quả sau
* **`std::future` (Consumer):** Tôi sẽ đợi kết quả đó
* **Shared State:** Kênh trung gian lưu trữ giá trị/exception kết nối giữa promise và future.
* **IMPORTANT:** 
    * Chỉ `set` và `get` được duy nhất **1 lần**. 
    * Hàm `f.get()` sẽ **move** dữ liệu ra ngoài, gọi lần 2 sẽ ném lỗi `std::future_error`.
    * Muốn đọc dữ liệu từ nhiều thread (`Multiple Readers`), phải chuyển đổi sang **`std::shared_future`** thông qua `auto sf = f.share();`.

#### 📘 Sample code
```cpp
// Initialize the producer
std::promise<int> p;
// Link the consumer to the producer
std::future<int> f = p.get_future();

std::thread worker([&]
{
    try {
        // set_value(T)
        // set_exception()
        p.set_value(42); 
    } catch (...) {
        // throw exception for future
        p.set_exception(std::current_exception()); 
    }
});

/* block util ...*/
//  f.get()-> get value
//  f.wait()-> just wait f execute done
//  f.wait_for()-> wait with timeout
try {
    std::cout << f.get(); 
} catch (const std::exception& e) {
    std::cout << "Worker lỗi: " << e.what();
}

worker.join();
```

```
Thread A
(Promise)

set_value(42)
      │
      ▼
 ┌─────────┐
 │ Shared  │
 │ State   │
 └─────────┘
      ▲
      │
get()
      │

Thread B
(Future)
```

#### </details> <!-- end --> 

---

<details> <summary>Async</summary>

#### 📘 Explanation
* Hàm `std::async` dùng để kích hoạt một tác vụ chạy bất đồng bộ (Asynchronous task).
* return 1 `std::future`  
* Launch Policy:
    * `std::launch::async` : tạo thread mới và chạy liền
    * `std::launch::deferred` : không tạo thread mới, cũng không chạy liền. Chạy khi future get data (f.get(), f.wait())
    * `std::launch::async | std::launch::deferred` (Mặc định): Compiler tự chọn dựa trên tài nguyên hệ thống (Cẩn thận: Hệ thống quá tải có thể bị ép thành `deferred`).

⚠️ **IMPORTANT:** 
* Phải luôn lưu kết quả trả về của `std::async` vào một biến (như `auto f`). Nếu bỏ qua không gán, hàm hủy của `std::future` tạm thời sẽ **block hoàn toàn main thread** cho đến khi task chạy xong (mất tác dụng bất đồng bộ).
* Hàm `f.get()` chỉ được gọi **1 lần duy nhất** trong suốt vòng đời của `std::future`.

```cpp
// default std::launch::async | std::launch::deferred
// => compiler choose one
auto f = std::async(std::launch::deferred, [] {
    std::this_thread::sleep_for(
        std::chrono::seconds(3));

    return 100;
});

// f only run when this is called, it call in current thread
std::cout << f.get();
```

#### </details> <!-- end --> 

---

<details> <summary>Thread pool</summary>

#### 📘 Explanation
Thread Pool là một tập hợp các worker thread được tạo sẵn và tái sử dụng để xử lý nhiều tác vụ. Các task được đưa vào queue, worker thread lấy task ra thực thi. Cách này giảm chi phí tạo/hủy thread, tăng khả năng mở rộng và thường kết hợp với std::condition_variable, std::future và std::packaged_task để xây dựng hệ thống xử lý bất đồng bộ hiệu quả. 
* Tạo sẵn một nhóm thread 
* Tái sử dụng nhiều lần
* Tránh việc tạo xóa thread liên tục của phần cứng

#### </details> <!-- end --> 

---

<details> <summary>Lock-free programming</summary>

#### 📘 Explanation
Kỹ thuật thiết kế ứng dụng đa luồng hiệu năng siêu cao mà hoàn toàn không sử dụng các cơ chế khóa chặn luồng (như Mutex hay Semaphore). Lock-free programming dựa hoàn toàn vào các chỉ thị nguyên tử cấp phần cứng (Atomic operations) và các hàm so sánh tráo đổi CAS (Compare-And-Swap) để đảm bảo tối thiểu có một luồng luôn tiến triển công việc mà không bị block.

#### </details> <!-- end --> 

---

<details> <summary>Producer Consumer</summary>

#### 📘 Explanation
Mô hình bài toán đồng bộ hóa kinhdefini gồm hai nhóm luồng: Nhóm sản xuất (**Producer**) liên tục tạo ra dữ liệu và đẩy vào một hàng đợi chung (Buffer), nhóm tiêu thụ (**Consumer**) lấy dữ liệu từ hàng đợi đó ra để xử lý. Bài toán yêu cầu đồng bộ để Producer không đẩy dữ liệu vào khi hàng đợi đầy, và Consumer không lấy dữ liệu ra khi hàng đợi rỗng (thường giải quyết bằng `condition_variable`).

```
Producer
    │
    ▼

+---------+
| Queue   |
+---------+

    │
    ▼

Consumer
```

#### </details> <!-- end --> 

---

<details> <summary>Reader Writer lock</summary>

#### 📘 Explanation
Cơ chế khóa chia sẻ loại trừ từ C++14 (`std::shared_mutex`). Nó giải quyết bài toán tối ưu hiệu năng khi dữ liệu có tần suất đọc rất cao nhưng tần suất ghi rất thấp:
* Cho phép **nhiều luồng cùng giữ khóa đọc** (`std::shared_lock`) chạy đồng thời để xem dữ liệu.
* Chỉ cho phép **duy nhất một luồng giữ khóa ghi độc quyền** (`std::unique_lock`) tại một thời điểm; khi luồng ghi làm việc, tất cả các luồng đọc khác đều bị chặn lại.

```cpp
std::shared_mutex m;
int data = 0;

void reader()
{
    std::shared_lock lock(m); // multi thread read => OK
    std::cout << data;
}

void writer()
{
    std::unique_lock lock(m); // one thread write at a time
    ++data;
}
```

#### </details> <!-- end --> 

---

## Memory Management

<details> <summary>new/delete - malloc/free</summary>

#### 📘 Explanation
* `new` : 
    * operator new cấp phát bộ nhớ void* 
    * Chuyển void* thành this và gọi constructor để khởi tạo data
    * Trả về con trỏ kiểu đối tượng. throw bad_alloc nếu lỗi
* `delete`: Destructor -> operator delete
* `malloc`: là hàm cấp phát bộ nhờ và trả về void*; trả về NULL nếu lỗi
* `calloc`: giống malloc nhưng dùng cho array (init all bit =  0)
* `realloc`: cấp phát lại cho 1 con trỏ, data cũ giữ, data cấp phát thêm rác
* `free`: Giải phóng vùng nhớ được cấp phát bởi *alloc. Không gọi Destructor. An toàn với NULL, lỗi nặng nếu free hai lần.


#### </details> <!-- end --> 

---

<details> <summary>RAII (Resource Acquisition Is Initialization)</summary>

#### 📘 Explanation
* **Khái niệm**: Là idiom cốt lõi của C++, gắn liền vòng đời của tài nguyên (Resource) với vòng đời của một đối tượng (object) cục bộ trên Stack.
* **Cơ chế**: Tài nguyên được cấp phát khi khởi tạo đối tượng (Constructor) và tự động giải phóng khi đối tượng ra khỏi phạm vi - scope (Destructor).
* **Bản chất "Resource"**: Không chỉ có memory (Smart pointer, vector) mà bao gồm mọi tài nguyên hệ thống như File handle (`fstream`), Socket, Mutex Lock (`lock_guard`).
* **Lợi ích tối cao**: Tránh rò rỉ tài nguyên, đặc biệt giúp an toàn ngoại lệ (Exception Safety) nhờ cơ chế Stack Unwinding tự động gọi Destructor khi chương trình crash/throw lỗi giữa chừng.
* **Short**: resource lifetime = object lifetime

#### </details> <!-- end --> 

---

<details> <summary><i style="color: grey">[✓] Memory leak (cấp phát nhưng không release)</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Resource leak (FILE*, Socket, Mutex, Handle, Database Connection)</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Dangling pointer (memory còn nhưng object mất - delete nhưng ko set về nullptr)</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Double free (delete 2 lần -> có thể gây crash hoặc unexpected behavior)</i></summary></details>

---

<details> <summary>Placement new</summary>

#### 📘 Explanation
Cấp phát bộ nhớ ở vùng nhớ tạo sẵn mà ko cần cấp phát thêm
```cpp
char buffer[sizeof(MyClass)];
MyClass* p = new(buffer) MyClass();

p->~MyClass(); // ko gọi delete do bộ nhớ ko phải do object quản lý
```

#### </details> <!-- end --> 

---

<details> <summary><i style="color: grey">[✓] Custom allocator (Tạo memory pool rồi dùng dần)</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Fragmentation (memory bị chia nhỏ và phân tán -> truy xuất chậm)</i></summary></details> 

---

<details> <summary>Cache locality</summary>

#### 📘 Explanation
Lưu data hay được dùng (*) vào bộ nhớ đệm CPU để truy xuất nhanh hơn. Bình thường trùy xuất ở RAM sẽ chậm hơn
- Lưu ở L1-L2-L3 (L1 thì nhanh nhất nhưng size nhỏ)
- Data hay được dùng (*): 
    - **Temporal Locality (Tính cục bộ thời gian)** : biến được truy xuất nhiều lần trong thời gian ngắn (i trong for)
    - **Spatial Locality (Tính cục bộ không gian)** : thường đọc 1 data sẽ không chỉ lấy size của data đó, mà lấy 1 block gần nhau (cache line = 64 bytes) -> loop qua vector sẽ nhanh hơn loop qua list, mặc dù cùng là O(n)

#### </details> <!-- end --> 

---

<details> <summary><i style="color: grey">[✓] Virtual memory (Mỗi process một vùng nhớ ảo riêng, vùng ảo này map với vùng thật ở RAM)</i></summary></details>

---


<details> <summary><i style="color: grey">[✓] Paging (Kỹ thuật ánh xạ virtual memory vào Physical memory, page map với Frame, ko cần liên tục trong bộ nhớ)</i></summary></details>

---

## STL

### Container

<details> <summary>Overview</summary>

| Container | Cấu trúc bộ nhớ (Memory Layout) | Cache Locality | Truy cập ngẫu nhiên `[i]` | Chèn / Xóa ở Đầu | Chèn / Xóa ở Cuối | Chèn / Xóa ở Giữa |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **`array`** | Liên tục (Stack) | 🌟 Xuất sắc (Spatial) | \(O(1)\) | Không hỗ trợ | Không hỗ trợ | Không hỗ trợ |
| **`vector`** | Liên tục (Heap) | 🌟 Xuất sắc (Spatial) | \(O(1)\) | \(O(n)\) | \(O(1)\)* | \(O(n)\) |
| **`deque`** | Các block liên tục (Heap) |  Khá tốt | \(O(1)\) | \(O(1)\) | \(O(1)\) | \(O(n)\) |
| **`list`** | Rải rác, liên kết đôi (Heap) | ❌ Rất tệ (Cache Miss) | Không hỗ trợ | \(O(1)\) | \(O(1)\) | \(O(1)\)** |
| **`forward_list`**| Rải rác, liên kết đơn (Heap) | ❌ Rất tệ (Cache Miss) | Không hỗ trợ | \(O(1)\)*** | Không hỗ trợ | \(O(1)\)*** |
| **`set / map`** | Cây BST / Đỏ Đen (Heap) | ❌ Tệ (Duyệt con trỏ) | Không hỗ trợ | \(O(\log n)\) | \(O(\log n)\) | \(O(\log n)\) |
| **`unordered_...`**| Bảng băm + Linked List (Heap)| ⚠️ Trung bình - Tệ | Không hỗ trợ | \(O(1)\) | \(O(1)\) | \(O(1)\) |
| **`priority_queue`**| Mảng phẳng biểu diễn Heap (Heap)| 🌟 Xuất sắc (Spatial) | Không hỗ trợ | Không hỗ trợ | `push`/`pop`: \(O(\log n)\) | Không hỗ trợ |
| **`bitset`** | Nén bit trong mảng nguyên (Stack)| 🌟 Xuất sắc (Nén chặt) | \(O(1)\) | Không hỗ trợ | Không hỗ trợ | Không hỗ trợ |

*📌 Ghi chú:*
* `*`: \(O(1)\) amortized (trung bình), có thể tốn \(O(n)\) nếu vector phải cấp phát lại bộ nhớ khi bị đầy.
* `**`: Với điều kiện đã tìm thấy và có con trỏ/iterator tại vị trí cần chèn/xóa.
* `***`: `forward_list` thao tác thông qua hàm `insert_after()` và `erase_after()`.

#### </details> <!-- end --> 

---


<details> <summary>Sequence Container (Bộ nhớ liên tục & Kế cận)</summary>

### 🔹 std::array & std::vector
> Toàn bộ data nằm xếp hàng sát nhau trong một khối nhớ duy nhất. Tận dụng tối đa 100% Cache Line khi duyệt qua.

```text
[ Cache Line = 64 bytes ] ───► Nạp nguyên một block này vào L1/L2 Cache cùng lúc
┌───────────┬───────────┬───────────┬───────────┬───────────┐
│  Data[0]  │  Data[1]  │  Data[2]  │  Data[3]  │  Data[4]  │ ...  (Continuous Memory)
└───────────┴───────────┴───────────┴───────────┴───────────┘
```

### 🔹 std::deque
> Quản lý bằng một mảng con trỏ (Map), mỗi con trỏ trỏ tới một Block nhớ liên tục chứa Data thực tế.

```text
      [ Mảng Con Trỏ / Map ]
         ┌───┬───┬───┐
         │ • │ • │ • │
         └─│─┴─│─┴─│─┘
           │   │   └────────────────────────┐
           ▼   ▼                            ▼
     ┌───┬───┬───┐                    ┌───┬───┬───┐
     │ 1 │ 2 │ 3 │ (Block liên tục 1) │ 7 │ 8 │ 9 │ (Block liên tục 3)
     └───┴───┴───┘                    └───┴───┴───┘
```

#### </details> <!-- end --> 

---

<details> <summary>Node-based Containers (Bộ nhớ phân mảnh, nhảy con trỏ)</summary>

### 🔹 std::list (Doubly Linked List)
> Các node nằm tự do, rải rác trên Heap. Phải đi qua con trỏ để tìm phần tử tiếp theo \(\rightarrow\) Gây Cache Miss cực nặng.

```text
  Heap Address: 0x1020                 Heap Address: 0x0040                 Heap Address: 0x0890
┌───────────────────────┐            ┌───────────────────────┐            ┌───────────────────────┐
│  Prev  │ Data │  Next │ ────────►  │  Prev  │ Data │  Next │ ────────►  │  Prev  │ Data │  Next │
│ (NULL) │  10  │ 0x0040│  ◄──────── │ 0x1020 │  20  │ 0x0890│  ◄──────── │ 0x0040 │  30  │ (NULL)│
└───────────────────────┘            └───────────────────────┘            └───────────────────────┘
   [Cache Miss tại đây] ──────────────► [Cache Miss tại đây] ──────────────► [Cache Miss tại đây]
```

### 🔹 std::forward_list (Singly Linked List)
> Tương tự như `list` nhưng bớt được con trỏ `Prev`, chỉ có thể nhảy tiến lên phía trước.

```text
┌──────────────┐      ┌──────────────┐      ┌──────────────┐
│ Data │ Next ─┼─────►│ Data │ Next ─┼─────►│ Data │ Next  │───► NULL
│  10  │0x0040 │      │  20  │0x0890 │      │  30  │(NULL) │
└──────────────┘      └──────────────┘      └──────────────┘
```

#### </details> <!-- end --> 

---

<details> <summary>Associative Containers (Cấu trúc Cây & Bảng băm)</summary>

### 🔹 std::set / std::map (Red-Black Tree)
> Cây nhị phân tìm kiếm cân bằng. Các Node phân mảnh trên Heap, liên kết nhau qua các pointer `Left`, `Right`, `Parent`.

```text
                 Heap: 0x0500
               ┌──────────────┐
               │ Parent: NULL │
               │   Data: 50   │
               │ Left  │ Right│
               └───│───┴───│──┘
                   │       └─────────────────────────────┐
                   ▼ Heap: 0x0120                        ▼ Heap: 0x0980
           ┌──────────────┐                      ┌──────────────┐
           │ Parent:0x0500│                      │ Parent:0x0500│
           │   Data: 25   │                      │   Data: 75   │
           │ Left  │ Right│                      │ Left  │ Right│
           └──────────────┘                      └──────────────┘
```

### 🔹 std::unordered_set / std::unordered_map
> Một mảng liên tục các Buckets (chứa con trỏ). Nếu trùng mã băm (Hash Collision), các phần tử sẽ được nối thêm vào thông qua Linked List (mô hình Chaining).

```text
[Mảng Buckets]
┌───┐
│ 0 │ ───► NULL
├───┤
│ 1 │ ───► ┌─────────────┐      ┌─────────────┐
├───┤      │ Data │ Next ┼────► │ Data │ Next ┼────► NULL (Xử lý xung đột băm)
│ 2 │      └─────────────┘      └─────────────┘
├───┤
│ 3 │ ───► ┌─────────────┐
└───┘      │ Data │ Next ┼────► NULL
           └─────────────┘
```

#### </details> <!-- end --> 

---

<details> <summary>Container Adapters & Bit manipulation</summary>

### 🔹 std::priority_queue (Max-Heap biểu diễn trên Vector)
> Logic là một cây nhị phân hoàn chỉnh (Heap), nhưng cấu trúc vật lý được làm "phẳng" và lưu trữ xếp liền nhau bên trong một `std::vector` \(\rightarrow\) Tận dụng tối đa Cache Locality.

```text
Cấu trúc Logic (Cây Heap):             Cấu trúc Vật lý thực tế trong RAM (Mảng phẳng):
          [100] (i=0)                 ┌───────┬───────┬───────┬───────┬───────┐
          /     \                     │  100  │  19   │  36   │  17   │   3   │
       [19]     [36]                  └───────┴───────┴───────┴───────┴───────┘
      (i=1)     (i=2)                     i=0     i=1     i=2     i=3     i=4
      /    \                          └───────────────────────────────────────┘
    [17]   [3]                                   Vùng nhớ liên tục!
   (i=3)  (i=4)                      Công thức tìm nút con: Left = 2i+1, Right = 2i+2
```

### 🔹 std::bitset
> Dữ liệu được nén chặt ở cấp độ bit bên trong các khối số nguyên hệ thống (ví dụ các block 64-bit `uint64_t`).

```text
 1 Byte trong bool thông thường:  [00000001] -> Lãng phí 7 bit trống
 
 1 Khối bộ nhớ của std::bitset<64>:
 ┌─────────────────────────────────────────────────────────────────┐
 │1│0│1│1│0│0│1│0│1│1│1│0│0│0│... (Đủ 64 bit dữ liệu nén khít nhau) │
 └─────────────────────────────────────────────────────────────────┘
 ◄───────────────────────── 8 bytes duy nhất ──────────────────────►
 (Có thể nạp toàn bộ hàng ngàn bit vào L1 Cache chỉ trong vài chu kỳ CPU)
```
#### </details> <!-- end --> 

---

### Algorithms

<details> <summary>std::sort</summary>

#### 📘 Explanation
Sắp xếp các phần tử trong một khoảng `[first, last)` theo thứ tự tăng dần hoặc theo một hàm so sánh (`compare`) tùy biến.
- **Độ phức tạp**: $O(N \log N)$ cho cả trường hợp trung bình và tệ nhất (thường sử dụng **IntroSort** - kết hợp QuickSort, HeapSort và InsertionSort).
- **Bộ nhớ phụ trợ**: $O(\log N)$ do đệ quy.
- **Lưu ý**: `std::sort` không giữ nguyên thứ tự ban đầu của các phần tử bằng nhau (không ổn định). Nếu cần giữ thứ tự, hãy dùng `std::stable_sort`.

#### 💻 Code Example
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> vec = {5, 2, 9, 1, 5, 6};
    
    // Sắp xếp tăng dần
    std::sort(vec.begin(), vec.end()); // {1, 2, 5, 5, 6, 9}
    
    // Sắp xếp giảm dần sử dụng Lambda
    std::sort(vec.begin(), vec.end(), [](int a, int b) {
        return a > b;
    }); // {9, 6, 5, 5, 2, 1}
    
    return 0;
}
```
</details> <!-- end -->

---

<details> <summary>std::find</summary>

#### 📘 Explanation
Tìm kiếm tuyến tính (Linear Search) phần tử đầu tiên trong khoảng `[first, last)` có giá trị bằng `value`.
- **Độ phức tạp**: $O(N)$ trong trường hợp tệ nhất (phải duyệt qua toàn bộ container).
- **Cơ chế**: Duyệt tuần tự từ đầu đến cuối. Thích hợp cho các container chưa được sắp xếp như `std::vector`, `std::list`.
- **Trả về**: Một iterator trỏ đến phần tử tìm thấy đầu tiên. Nếu không tìm thấy, trả về `last` (end iterator).

#### 💻 Code Example
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> vec = {10, 20, 30, 40, 50};
    
    auto it = std::find(vec.begin(), vec.end(), 30);
    
    if (it != vec.end()) {
        std::cout << "Tim thay tai vi tri: " << std::distance(vec.begin(), it) << "\n";
    } else {
        std::cout << "Khong tim thay\n";
    }
    
    return 0;
}
```
</details> <!-- end -->

---

<details> <summary>std::binary_search</summary>

#### 📘 Explanation
Kiểm tra xem một phần tử có tồn tại trong khoảng **đã được sắp xếp** `[first, last)` hay không bằng thuật toán Tìm kiếm nhị phân (Binary Search).
- **Độ phức tạp**: $O(\log N)$ nếu container hỗ trợ Random Access Iterator (như `std::vector`), $O(N)$ nếu là Forward Iterator (như `std::list`).
- **Lưu ý**: Container **bắt buộc phải được sắp xếp trước** khi gọi hàm này. Nếu chưa sắp xếp, kết quả trả về sẽ không chính xác (Undefined Behavior).
- **Trả về**: Kiểu `bool` (`true` nếu tìm thấy, `false` nếu không tìm thấy). Hàm này không trả về vị trí của phần tử.

#### 💻 Code Example
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> vec = {1, 3, 5, 7, 9, 11}; // Da sap xep
    
    bool found = std::binary_search(vec.begin(), vec.end(), 7);
    
    if (found) {
        std::cout << "Phan tu 7 ton tai trong mang\n";
    } else {
        std::cout << "Khong tim thay 7\n";
    }
    
    return 0;
}
```
</details> <!-- end -->

---

<details> <summary>std::lower_bound</summary>

#### 📘 Explanation
Tìm phần tử đầu tiên trong một khoảng **đã được sắp xếp** `[first, last)` mà có giá trị **lớn hơn hoặc bằng** ( $\ge$ ) `value` truyền vào.
- **Độ phức tạp**: $O(\log N)$ với Random Access Iterator.
- **Cơ chế**: Sử dụng Binary Search để thu hẹp phạm vi tìm kiếm.
- **Trả về**: Iterator trỏ đến phần tử thỏa mãn. Nếu tất cả các phần tử đều nhỏ hơn `value`, hàm trả về `last`.
- **Ứng dụng**: Xác định vị trí chèn một phần tử mới mà vẫn giữ nguyên thứ tự sắp xếp của mảng.

#### 💻 Code Example
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> vec = {10, 20, 30, 30, 40, 50}; // Da sap xep
    
    // Tim phan tu dau tien >= 30
    auto it = std::lower_bound(vec.begin(), vec.end(), 30);
    
    std::cout << "Phan tu >= 30 dau tien tai chi so: " << std::distance(vec.begin(), it) << "\n"; // Chi so: 2
    std::cout << "Gia tri: " << *it << "\n"; // Gia tri: 30
    
    return 0;
}
```
</details> <!-- end -->

---

<details> <summary>std::upper_bound</summary>

#### 📘 Explanation
Tìm phần tử đầu tiên trong một khoảng **đã được sắp xếp** `[first, last)` mà có giá trị **lớn hơn hẳn** ( $>$ ) `value` truyền vào.
- **Độ phức tạp**: $O(\log N)$ với Random Access Iterator.
- **Điểm khác biệt với lower_bound**: `lower_bound` lấy $\ge$, còn `upper_bound` lấy $>$. Do đó, khoảng `[lower_bound, upper_bound)` sẽ chứa toàn bộ các phần tử bằng chính xác `value` (bằng với hàm `std::equal_range`).
- **Trả về**: Iterator trỏ đến phần tử đầu tiên thỏa mãn. Nếu không có phần tử nào lớn hơn `value`, trả về `last`.

#### 💻 Code Example
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> vec = {10, 20, 30, 30, 40, 50}; // Da sap xep
    
    // Tim phan tu dau tien > 30
    auto it = std::upper_bound(vec.begin(), vec.end(), 30);
    
    std::cout << "Phan tu > 30 dau tien tai chi so: " << std::distance(vec.begin(), it) << "\n"; // Chi so: 4
    std::cout << "Gia tri: " << *it << "\n"; // Gia tri: 40
    
    return 0;
}
```
</details> <!-- end -->

---

<details> <summary>std::transform</summary>

#### 📘 Explanation
Áp dụng một hàm toán học hoặc một phép biến đổi (Unary / Binary Operation) lên từng phần tử trong một khoảng và lưu kết quả vào một container đích (được chỉ định bởi output iterator).
- **Độ phức tạp**: $O(N)$ - thực hiện đúng $N$ lần phép biến đổi.
- **Đặc điểm**: Rất mạnh mẽ khi cần map dữ liệu từ dạng này sang dạng khác (ví dụ: biến đổi toàn bộ chuỗi thành chữ hoa, nhân đôi giá trị các phần tử, v.v.).
- **Lưu ý**: Container đích phải có đủ kích thước trước khi ghi đè, hoặc phải sử dụng `std::back_inserter` để chèn tự động.

#### 💻 Code Example
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> src = {1, 2, 3, 4, 5};
    std::vector<int> dest;
    
    // Nhan doi moi phan tu va chèn vao dest
    std::transform(src.begin(), src.end(), std::back_inserter(dest), [](int x) {
        return x * 2;
    }); // dest se la {2, 4, 6, 8, 10}
    
    // Bien doi truc tiep tren mang src (In-place)
    std::transform(src.begin(), src.end(), src.begin(), [](int x) {
        return x + 10;
    }); // src se la {11, 12, 13, 14, 15}
    
    return 0;
}
```
</details> <!-- end -->

---

<details> <summary>std::accumulate</summary>

#### 📘 Explanation
Tính tổng (hoặc tích lũy bằng một phép toán tùy chọn) của tất cả các phần tử trong một khoảng `[first, last)` bắt đầu bằng một giá trị khởi tạo `init`.
- **Độ phức tạp**: $O(N)$ - duyệt qua từng phần tử một lần.
- **Thư viện phụ thuộc**: Nằm trong `<numeric>` (không phải `<algorithm>`).
- **Lưu ý quan trọng**: Kiểu dữ liệu trả về dựa hoàn toàn vào kiểu dữ liệu của `init`. Nếu tính tổng mảng số thực `float` nhưng truyền `init` là số nguyên `0` (kiểu `int`), kết quả sẽ bị làm tròn mất phần thập phân sau mỗi bước. Do đó, hãy truyền `0.0` hoặc `0.0f` nếu làm việc với số thực.

#### 💻 Code Example
```cpp
#include <iostream>
#include <vector>
#include <numeric> // Bat buoc phai co cho accumulate

int main() {
    std::vector<int> vec = {1, 2, 3, 4, 5};
    
    // Tinh tong thong thuong voi gia tri khoi tao la 0
    int sum = std::accumulate(vec.begin(), vec.end(), 0); // 15
    
    // Tinh tich cac phan tu dung Lambda custom
    int product = std::accumulate(vec.begin(), vec.end(), 1, [](int a, int b) {
        return a * b;
    }); // 1 * 1 * 2 * 3 * 4 * 5 = 120
    
    return 0;
}
```
</details> <!-- end -->

---

## Template & Metaprogramming

<details> <summary>Function template </summary>

#### 📘 Explanation
* khuôn mẫu
* sinh code lúc complile
* chỉ sinh những hàm được dùng
* viết ở file `.h` , viết ở `.cpp` vẫn được ở Th đặc biệt

```cpp
template<typename T>    // option 1
template<class T>       // option 2
T add(T a, T b)
{
    return a + b;
}
```

</details> <!-- end -->

---

<details> <summary><i style="color: grey">[✓] Class template (tương tự trên nhưng cho class)</i></summary></details>

---

<details> <summary>Template specialization </summary>

#### 📘 Explanation
Viết phiên bảng riêng cho kiểu dữ liệu nhất định

```cpp
template<>
void print<bool>(bool value)
{
    std::cout << (value ? "true" : "false");
}
```
</details> <!-- end -->

---

<details> <summary><i style="color: grey">[ ] Partial specialization ??? (learn later)</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Variadic template (Nhận N tham số)</i></summary></details>

---

<details> <summary>SFINAE (Substitution Failure Is Not An Error)</summary>

#### 📘 Explanation
Lỗi thay thế không phải là lỗi. Đây là một nguyên tắc cốt lõi trong ngôn ngữ C++ (đặc biệt trong Template Metaprogramming). Nguyên tắc này quy định rằng: khi trình biên dịch cố gắng thay thế một kiểu dữ liệu vào khuôn mẫu (template) và gặp lỗi, thay vì báo lỗi biên dịch (như thường lệ), nó sẽ lẳng lặng loại bỏ khuôn mẫu đó và tiếp tục tìm kiếm các lựa chọn (overload) khác phù hợp hơn

```cpp
#include <iostream>
#include <type_traits>

// Hàm chỉ được chọn nếu T là kiểu số nguyên
template <typename T>
typename std::enable_if<std::is_integral<T>::value, void>::type
print(T t) {
    std::cout << "So nguyen: " << t << "\n";
}
```
</details> <!-- end -->

---

<details> <summary>CRTP (Curiously Recurring Template Pattern)</summary>

#### 📘 Explanation
là một mẫu thiết kế trong C++, nơi một lớp dẫn xuất (Derived Class) kế thừa từ một lớp mẫu (Class Template), và chính lớp dẫn xuất đó lại được truyền vào làm tham số mẫu cho lớp cơ sở (Base Class)
* Đa hình tĩnh
* Đa hình lúc compile

```cpp
template <typename T>
class Base {
public:
    void interface() {
        // Ép kiểu `this` thành con trỏ của lớp dẫn xuất
        static_cast<T*>(this)->implementation();
    }
};

class Derived : public Base<Derived> {
public:
    void implementation() { /* Thực thi cụ thể */ }
};

//---
int main() {
    Derived obj;
    obj.interface();
}
```
</details> <!-- end -->

---

<details> <summary><i style="color: grey">[✓] TMP (Template Meta Programming) - Dùng template để tính lúc compile (giờ đã được thay 1 phần bởi constexpr</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Fold expression (cú pháp C++17 để gộp param pack ...)</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Perfect forwarding (truyền đúng kiểu dữ liệu (Lvalue/Rvalue) bằng std::forward</i></summary></details>

---

## OS / System / Low-Level

<details> <summary><i style="color: grey">[✓] System call (User Mode yêu cầu Kernel làm việc (thao tác trên RAM, đọc file..))</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Context switch (chuyển ngữ cảnh, chuyển thread)</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Kernel vs User mode (1 cái tác động được tới hard, 1 cái không)</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Process memory layout (Memory Layout)</i></summary></details>

---

<details> <summary>Static library (lib) </summary>

#### 📘 Explanation
- Thư viện tĩnh
- File *.lib chứa compiled code
- Exe gọi phải copy source ở file *.lib làm nặng source
#### 📘 Cách dùng
- Config : 
    - Add *.lib vào Additional Dependencies (cần đúng đường dẫn)
    - Add references nếu source lib cùng sln với src exe
- Coding : `#pragma comment(lib, "<path>//MyMath.lib")`
- Usage : Gọi hàm bình thường, có thể f12, f11 để vào hàm
</details> <!-- end -->

---

<details> <summary>Dynamic library (dll) </summary>

#### 📘 Explanation
- Thư viện động
- File *.lib (Import Library) chỉ chứa linker
- File *.dll chứa source compile
- Dll là file, khi được load thì đọc file rồi thực thi, chứ dll không phải một dạng chạy ngầm
- TH load Run-time có thể hack nên cần để ý security
#### 📘 Cách dùng
- Load-time / Implicit Linking (tương tự như dùng static lib)
- Run-time / Explicit Linking
    - Load theo tên dll, tên hàm (LoadLibrary, GetProcAddress)
    - Gọi thông qua function pointer

</details> <!-- end -->

---

<details> <summary><i style="color: grey">[✓] Symbol linking (khi build thì ra các symbol như ?Draw@@YAXXZ, linker tìm Declaration/Definition để nối)</i></summary></details>

---

<details> <summary><i style="color: grey">[ ] ABI ??? (learn later)</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Name mangling (tự gen tên hàm khi compile, xử lý cho overloading biết gọi hàm nào)</i></summary></details>

---

<details> <summary><i style="color: grey">[✓] extern "C" (linking theo chuẩn C, hàm tên sao giữ vậy chứ không thêm symbol kiểu Draw@@YAXXZ như C++)</i></summary></details>

---
<details> <summary><i style="color: grey">[✓] Compiler stages - thứ tự compile (source -> preprocessor -> compiler -> assembler -> linker -> exe/dll)</i></summary></details>

---

<details> <summary><i style="color: grey">[ ] Assembly basics ??? (learn later) </i></summary></details>

---

<details> <summary><i style="color: grey">[✓] CPU cache (CPU core -> L1 -> L2 -> L3 -> RAM; cache line = 64byte) </i></summary></details>

---

<details> <summary><i style="color: grey">[ ] SIMD ??? (learn later) </i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Undefined behavior (crash, chạy đúng, chạy sai, optimize) </i></summary></details>

---

## Debugging / Performance

---

<details> <summary><i style="color: grey">[✓] Benchmark/Profiling (đo hiệu năng) </i></summary></details>

---

<details> <summary><i style="color: grey">[✓] Big-O (O1, O(n),...) </i></summary></details>

---

<details> <summary>Experience</summary>

#### 📘 Explanation
Các tool cũng như các cách đã từng dùng để debug

- multi-thread : ghi log + dùng DebugView để check realtime
- crash app: phân tích file crash dump (*)
- mem leak: Task manager, Diagnostic Tools (VisualStudio), _CrtSetDbgFlag (Add code và build mode debug)
- Remote debug : Dùng msvsmon.exe (Remote Debugger)
- check window app : Dùng spyxx để coi property và message đến app
- test crash : Dùng Application Verifier để CT dễ crash hơn
- Add action debug bằng visual thì có thể dùng template sau:
    - `C++` : [$PID][$TID][{Environment.TickCount}] [$FUNCTION] - Text
    - `C# :` [$PID][$TID][{System.DateTime.Now}] [$FUNCTION] - Text
    - `Endline` : {"\n",s8b}
---
#### 📘 Cách set đường dẫn sinh file crash dump (*):

1. Win + R → regedit
2. Go to path below
    ```
    HKEY_LOCAL_MACHINE
    └─ SOFTWARE
        └─ Microsoft
            └─ Windows
                └─ Windows Error Reporting
                    └─ LocalDumps
    ```

3. Setting as below

    | Registry Key | Type | Value | Description |
    |-------------|---------------|-----------------|-------------|
    | (Default) | REG_SZ | (empty) | Giá trị mặc định |
    | DumpCount | REG_DWORD | 10 | Số lượng file dump tối đa |
    | DumpFolder | REG_EXPAND_SZ | E:\CrashDump | Thư mục lưu crash dump |
    | DumpType | REG_DWORD | 2 | Loại dump được tạo |

</details> <!-- end -->

---

## QA & Trick
- custom deleter c++ là gì ---> `unique_ptr<T,custom_delete>` thay vì gọi delete T khi giải phóng thì sẽ gọi custom_delete
- Size của class không có biến member => 1 byte

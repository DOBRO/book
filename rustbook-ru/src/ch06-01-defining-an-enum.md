## Определение перечисления

Давайте посмотрим на ситуацию, которую мы могли бы выразить в коде и рассмотрим, почему перечисления полезны и более уместны чем структуры в данном случае. Представим, что нам нужно работать с IP-адресами. В настоящее время используются два основных стандарта IP-адресов: версия четыре и версия шесть. Это единственные варианты IP адресов, с которым столкнётся наша программа: мы  можем *перечислить* все возможные варианты, где перечисления получают свои имена.

Любой IP-адрес может быть адресом версии четыре или версии шесть, но не оба одновременно. Это свойство IP-адресов делает перечисление подходящей структурой данных, потому что значения перечислений могут быть только одним из его вариантов. Адреса версии четыре, так и версии шесть по-прежнему являются IP-адресами, поэтому они должны рассматриваться как один и тот же тип, когда код обрабатывает ситуации применимые к любому виду IP-адреса.

Можно выразить эту концепцию в коде, определив перечисление `IpAddrKind` и составив список возможных видов IP-адресов, `V4` и `V6`. Вот варианты перечислений:

```rust
enum IpAddrKind {
    V4,
    V6,
}
```

`IpAddrKind` теперь является пользовательским типом данных, который мы можем использовать в другом месте нашего кода.

### Значения перечислений

Экземпляры каждого варианта перечисления `IpAddrKind` можно создать следующим образом:

```rust
enum IpAddrKind {
    V4,
    V6,
}
fn main() {
    let four = IpAddrKind::V4;
    let six = IpAddrKind::V6;
}
```

Обратите внимание, что варианты перечисления находятся в пространстве имён его идентификатора и используется двойное двоеточие, чтобы отделить их. Это полезно потому, что сейчас оба значения `IpAddrKind::V4`  и `IpAddrKind::V6`  имеют одинаковый тип: `IpAddrKind`. Затем мы можем, например, определить функцию, которая принимает любой вариант `IpAddrKind`:

```rust
 enum IpAddrKind {
     V4,
     V6,
 }

fn route(ip_type: IpAddrKind) { }
```

Можно вызвать эту функцию с любым из вариантов:

```rust
# enum IpAddrKind {
#     V4,
#     V6,
# }
#
# fn route(ip_kind: IpAddrKind) { }
#
route(IpAddrKind::V4);
route(IpAddrKind::V6);
```

Использование перечислений имеет даже больше преимуществ. Размышляя о нашем типе IP-адреса в данный момент, у нас нет способа сохранить фактические *данные* IP-адреса; мы только знаем, каким он является *вариантом*. Учитывая то, что вы недавно узнали о структурах в главе 5, можно решить эту проблему как показано в листинге 6-1.

```rust
enum IpAddrKind {
    V4,
    V6,
}

struct IpAddr {
    kind: IpAddrKind,
    address: String,
}

let home = IpAddr {
    kind: IpAddrKind::V4,
    address: String::from("127.0.0.1"),
};

let loopback = IpAddr {
    kind: IpAddrKind::V6,
    address: String::from("::1"),
};
```

<span class="caption">Листинг 6-1: Сохранение данных  и вариантов <code>IpAddrKind</code> IP адреса используя структуру <code>struct</code></span>

Здесь мы определили структуру `IpAddr`, которая имеет два поля: `kind` имеющее тип `IpAddrKind` (перечисление, которое мы определили ранее) и поле `address` типа `String`. У нас есть два экземпляра этой структуры. Первый, `home`, имеет значение `IpAddrKind::V4` с его `kind` и  связанными данными значения адреса внутри `127.0.0.1`. Второй экземпляр, `loopback`, имеет другой вариант `IpAddrKind`, где в качестве значения `kind` типа `V6` находится значение ассоциированного адреса `::1`. Мы использовали структуру для объединения значений `kind` и `address`, поэтому теперь вариант связан со значением.

Мы можем представить ту же концепцию в более сжатой форме, используя только перечисление, вместо перечисления внутри структуры и помещая данные непосредственно в каждый  вариант перечисления. Это новое определение перечисления `IpAddr` говорит, что оба варианта `V4` и `V6` будут иметь связанные с ними значения типа `String` :

```rust
enum IpAddr {
    V4(String),
    V6(String),
}

let home = IpAddr::V4(String::from("127.0.0.1"));

let loopback = IpAddr::V6(String::from("::1"));
```

Мы добавили данные в каждый вариант перечисления. Таким образом, мы упростили наш предыдущий код и получили тот же результат.

Ещё одно преимущество использования перечисления вместо структуры заключается в том, что каждый вариант перечисления может иметь различные типы и разное количество ассоциированных данных. Версия 4 для типа IP адресов всегда будет содержать четыре цифровых компонента, которые будут иметь значения между 0 и 255. При необходимости сохранить адреса типа `V4` как четыре значения типа `u8`, а также описать адреса типа `V6` как единственное значение типа  `String`, мы не смогли бы с помощью структуры. Перечисления решают эту задачу легко:

```rust
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}

let home = IpAddr::V4(127, 0, 0, 1);

let loopback = IpAddr::V6(String::from("::1"));
```

Мы показали несколько способов определения структур данных для хранения IP-адресов стандарта версии четыре и версии шесть. Однако, как выясняется, желающих хранить IP-адреса и кодировать какого они типа, настолько распространены, что [в стандартной библиотеке есть определение, которое мы можем использовать!] <comment> Давайте посмотрим, как стандартная библиотека определяет тип <code>IpAddr</code>: она имеет точно перечисление и варианты, которые мы определили и использовали, но она встраивает адресные данные в варианты в форме двух разных структур, которые определяются по-разному для каждого варианта.</comment>

```rust
struct Ipv4Addr {
    // details elided
}

struct Ipv6Addr {
    // details elided
}

enum IpAddr {
    V4(Ipv4Addr),
    V6(Ipv6Addr),
}
```

Этот код иллюстрирует возможность добавления любого типа данных в значение перечисления: строки, числа, структуры и пр. Вы даже можете включить в него другие перечисления! Стандартные типы данных не очень сложны, хотя, потенциально, могут быть очень сложными (вложенность данных может быть очень глубокой).

Обратите внимание, что хотя определение перечисления `IpAddr` есть в стандартной библиотеке, можно объявлять и использовать свою собственную реализацию без каких-либо конфликтов, потому что мы не добавили определение стандартной библиотеки в область видимости кода. Подробнее об этом поговорим в главе 7.

Рассмотрим другой листинг 6-2: в этом примере каждый элемент перечисления имеете свой, особый тип данных внутри:

```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}
```

<span class="caption">Пример 6-2: Перечисление <code>Message</code>, в котором каждый элемент хранит различные типы и данные (наиболее удобные и нужные для использования)</span>

Это перечисление имеет 4 элемента:

- `Quit` - пустой элемент без ассоциированных данных.
- `Move` - имеющий внутри анонимную структуру.
- `Write` имеет единственную строку `String`.
- `ChangeColor` имеет кортеж из трёх `i32` значений.

Определение перечисления с вариантами, такими как в листинге 6-2, похоже на определение различных видов определений структур, за исключением того, что перечисление не использует ключевое слово `struct` и все варианты сгруппированы внутри типа `Message`. Следующие структуры могут содержать те же данные, что и предыдущие варианты перечислений:

```rust
struct QuitMessage; // unit struct
struct MoveMessage {
    x: i32,
    y: i32,
}
struct WriteMessage(String); // tuple struct
struct ChangeColorMessage(i32, i32, i32); // tuple struct
```

Но когда мы использовали различные структуры, которые имеют свои собственные типы, мы не могли легко определять функции, которые принимают любые типы сообщений, как можно сделать с помощью перечисления типа `Message`, объявленного в листинге 6-2, который является единым типом.

Есть ещё одно сходство между перечислениями и структурами: так же, как мы можем определять методы для структур с помощью `impl` блока, мы также можем определять методы для перечисления. Вот метод с именем `call`, который мы могли бы определить в нашем перечислении  `Message` :

```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}

impl Message {
    fn call(&self) {
        // method body would be defined here
    }
}
fn main() {
    let m = Message::Write(String::from("hello"));
    m.call();
}
```

Тело метода будет использовать `self` , чтобы получить значение из объекта на котором мы вызвали этот метод. В этом примере мы создали переменную `m` которой назначено значение из выражения `Message::Write(String::from("hello"))` , и это то чем будет `self` в теле метода `call` при вызове `m.call()`.

Теперь посмотрим на другое наиболее часто используемое перечисление из стандартной библиотеки, которое является очень распространённым и полезным: `Option`.

### Перечисление `Option` и его преимущества перед нулевыми значениями

В предыдущем разделе, мы рассмотрели как перечисление `IpAddr` позволяет нам использовать систему типов Rust для кодирования больше информации, чем обычные данные в программе. В данном разделе изучается тип `Option`, ещё одно перечисление объявленное в стандартной библиотеке. Тип `Option` использован во множестве мест, потому что он используется в самых распространённых сценариях, когда значение может быть чем-то или быть ничем (отсутствовать). Выражение этой концепции в терминах системы типов означает, что компилятор может проверить, обрабатываются ли все случаи, которые должны обрабатываться; эта функция позволяет предотвратить ошибки, которые очень распространены в других языках программирования.

Дизайн языка программирования часто рассматривается с точки зрения того, какие функции вы включаете, но функции, которые вы исключаете тоже важны. Rust не имеет функциональности NULL, которая есть во многих других языках. *Null* это значение, которое означает, что нет никакой ценности. В языках с нулём переменные всегда могут быть в одном из два состояния: null или не-null.

В своей презентации 2009 года «Нулевые ссылки: ошибка в миллиард долларов», Tony Hoare, изобретатель null, сказал следующее:

> Я называю это моей ошибкой в миллиард долларов. В то время когда я проектировал первую комплексную систему типов для ссылок на объектно-ориентированном языке. Моя цель состояла в том, чтобы гарантировать, что любое использование ссылок должно быть абсолютно безопасным с проверкой, выполняющейся автоматически компилятором. Но я не мог устоять перед искушением вставить нулевую ссылку, просто потому что это было так легко для реализации. Это привело к неисчислимым ошибкам, уязвимостям и падениям систем, которые вероятно, вызвали миллиард долларов боли и ущерба за последние сорок лет.

Проблема с null значениями заключается в том, что если вы попытаетесь использовать его значение в качестве ненулевого, то получите какую-то ошибку. Из-за того, что это null или не-null свойство всеобъемлющее, то очень легко сделать такую ошибку.

Тем не менее, концепция, которую null пытается выразить является полезной: null - это значение, которое в настоящее время по какой-то причине недействительное или отсутствует.

Проблема на самом деле не в концепции, а в реализации. Таким образом, у Rust нет null, но есть перечисление, которое может закодировать понятие присутствующего или отсутствующего значения. Это перечисление `Option<T>`, и оно [ определяется стандартной библиотекой](https://doc.rust-lang.org/std/option/enum.Option.html)<comment> следующим образом:</comment>

```rust
enum Option<T> {
    Some(T),
    None,
}
```

Перечисление `Option<T>` настолько полезно, что даже подключено авто-импортом; его не нужно явно подключать в область видимости. А дополнительно подключены также его варианты: можно использовать `Some` и `None` напрямую без префикса `Option::`. Перечисление `Option<T>` все ещё является обычным перечислением, а `Some(T)` и `None` являются вариантами типа `Option<T>`.

Синтаксис `<T>` - это особенность Rust, о которой мы ещё не говорили. Это параметр обобщённого типа, и мы подробнее рассмотрим обобщённые типы в главе 10. Пока все, что вам нужно знать, это то, что `<T>` означает вариант `Some` перечисления типа `Option` , который может содержать фрагмент данных любого типа. Вот несколько  примеров использования значения `Option`  для хранения числовых и строковых типов:

```rust
let some_number = Some(5);
let some_string = Some("a string");

let absent_number: Option<i32> = None;
```

Если используется `None`, а не  `Some` , то нужно сообщить Rust, какой тип `Option<T>` у нас есть, потому что компилятор не может определить тип варианта который будет содержать  `Some`, глядя только на значение `None`.

Когда есть значение `Some`, мы знаем, что значение присутствует и содержится внутри `Some`. Когда есть значение `None`, это означает то же самое, что и null в некотором смысле: у нас нет действительного значения. Так почему наличие `Option<T>` лучше, чем null?

Если кратко, так как `Option<T>` и `T` (где `T` может быть любого типа) разные типы, компилятор не позволит нам использовать значение `Option<T>` как если бы оно было действительным значением. Например, этот код не будет компилироваться, потому что он пытается добавить `i8` к `Option<i8>`:

```rust,ignore,does_not_compile
let x: i8 = 5;
let y: Option<i8> = Some(5);

let sum = x + y;
```

Запуск данного кода даст ошибку ниже:

```text
error[E0277]: the trait bound `i8: std::ops::Add<std::option::Option<i8>>` is
not satisfied
 -->
  |
5 |     let sum = x + y;
  |                 ^ no implementation for `i8 + std::option::Option<i8>`
  |
```

Сильно! По сути, это сообщение об ошибке означает, что Rust не понимает, как добавить `i8` к `Option<i8>` , потому что они разного типа.  В Rust, когда мы имеет значение типа `i8`, компилятор будет гарантировать, что мы всегда имеем действительное значение. Мы можем действовать уверенно, не проверяя на null перед использованием значения. Только когда есть `Option<i8>` (или любой другой тип) мы должны беспокоиться о возможности отсутствия значения, а компилятор позаботится о том, чтобы мы рассмотрели этот случай перед использованием значения.

Другими словами, вы должны преобразовать `Option<T>` к типу `T`, прежде чем можно выполнять  операции с `T`. Как правило, это помогает поймать одну из самых общих проблем с null: предположение того, что что-то не является null, хотя оно им является.

Не нужно беспокоиться о неправильном предположении касательно не-null значения, что помогает чувствовать себя более уверенно. Для того, чтобы иметь значение, которое может быть null, вы должны явно выбрать, указав тип этого значения `Option<T>` . Затем, когда вы используете это значение, вы обязаны явно обрабатывать случай когда значение равно null. Везде, где значение имеет тип не являющий `Option<T>`, вы *можете*  смело предположить, что значение не является null. Это поведение было продуманным, проектным решением в Rust, ограничивающим распространение null и увеличивающим безопасность Rust кода.

Итак, как вы получаете значение `T` из `Some` варианта, когда у вас есть значение типа `Option<T>` и вы могли бы использовать это значение? `Option<T>` имеет большое количество методов, которые полезны в различных ситуациях; можно проверить их в [документации]<comment data-md-type="raw_html"> . Знакомство с методами в `Option<T>` будет чрезвычайно полезным в вашем путешествии с Rust.</comment>

В общем случае, чтобы использовать значение `Option<T>` , нужен код, который будет обрабатывать каждый вариант. Вы хотите выполнить некоторый код, который будет работать только тогда, когда у вас есть значение в `Some(T)` , и этому коду разрешено использовать внутреннее `T` . Или нужен немного другой код, если у вас есть значение `None` , и этот код не имеет доступного значения `T` . Выражение `match` представляет собой конструкцию потока управления, которая выполняет это при использовании с перечислениями: она будет запускать разный код в зависимости от того, какой вариант перечисления имеется, и этот код может использовать значения данных внутри совпавшего варианта.


[в стандартной библиотеке есть определение, которое мы можем использовать!]: ../std/net/enum.IpAddr.html
[документации]: ../std/option/enum.Option.html

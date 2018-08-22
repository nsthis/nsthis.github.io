---
title: PHP7新特性(上)
date: 2018-01-20 17:10:18
tags: PHP
categories: PHP
---

PHP7出来也很久了，都说PHP7的性能有了很大的提高，现在介绍下PHP7的新特性

<!-- more -->

##	Group use declarations

从同一 namespace 导入的类、函数和常量现在可以通过单个 use 语句 一次性导入了
```
//PHP7之前
use some\namespace\ClassA;
use some\namespace\ClassB;
use some\namespace\ClassC as C;
use function some\namespace\fn_a;
use function some\namespace\fn_b;
use function some\namespace\fn_c;
use const some\namespace\ConstA;
use const some\namespace\ConstB;
use const some\namespace\ConstC;
 
// PHP7之后
use some\namespace\{ClassA, ClassB, ClassC as C};
use function some\namespace\{fn_a, fn_b, fn_c};
use const some\namespace\{ConstA, ConstB, ConstC};
```

##	支持延迟静态绑定

static关键字来引用当前类，即实现了延迟静态绑定
```
class A {    
    public static function who() {    
        echo __CLASS__;    
    }    
    public static function test() {    
        static::who(); // 这里实现了延迟的静态绑定    
    }    
}    
class B extends A {    
    public static function who() {    
         echo __CLASS__;    
    }    
}
B::test();    
```
输出结果:B

##	支持goto语句

多数计算机程序设计语言中都支持无条件转向语句goto，当程序执行到goto语句时，即转向由goto语句中的标号指出的程序位置继续执行。尽管goto语句有可能会导致程序流程不清晰，可读性减弱，但在某些情况下具有其独特的方便之处，例如中断深度嵌套的循环和 if 语句。
```
goto a;    
echo 'Foo';    
a:    
echo 'Bar';    
for($i=0,$j=50; $i<100; $i++) {    
  while($j--) {    
    if($j==17) goto end;    
  }     
}    
echo "i = $i";    
end:    
echo 'j hit 17'; 
```

##	支持闭包、Lambda/Anonymous函数

闭包（Closure）函数和Lambda函数的概念来自于函数编程领域。例如JavaScript 是支持闭包和 lambda 函数的最常见语言之一。 
在PHP中，我们也可以通过create_function()在代码运行时创建函数。但有一个问题：创建的函数仅在运行时才被编译，而不与其它代码同时被编译成执行码，因此我们无法使用类似APC这样的执行码缓存来提高代码执行效率。 
在PHP5.3中，我们可以使用Lambda/匿名函数来定义一些临时使用（即用即弃型）的函数，以作为array_map()/array_walk()等函数的回调函数。
```
echo preg_replace_callback('~-([a-z])~', function ($match) {    
    return strtoupper($match[1]);    
}, 'hello-world');    
// 输出 helloWorld    
$greet = function($name)    
{    
    printf("Hello %s\r\n", $name);    
};    
$greet('World');    
$greet('PHP');    
//...在某个类中    
$callback =      function ($quantity, $product) use ($tax, &$total)         {    
   $pricePerItem = constant(__CLASS__ . "::PRICE_" .  strtoupper($product));    
   $total += ($pricePerItem * $quantity) * ($tax + 1.0);    
 };    
```

##	魔术方法__callStatic()和__invoke()

PHP中原本有一个魔术方法__call()，当代码调用对象的某个不存在的方法时该魔术方法会被自动调用。新增的__callStatic()方法则只用于静态类方法。当尝试调用类中不存在的静态方法时，__callStatic()魔术方法将被自动调用。
```
class MethodTest {    
    public function __call($name, $arguments) {    
        // 参数 $name 大小写敏感    
        echo "调用对象方法 '$name' "    
             . implode(' -- ', $arguments). "\n";    
    }    
    /**  PHP 5.3.0 以上版本中本类方法有效  */    
    public static function __callStatic($name, $arguments) {    
        // 参数 $name 大小写敏感    
        echo "调用静态方法 '$name' "    
             . implode(' -- ', $arguments). "\n";    
    }    
}    
 
$obj = new MethodTest;    
$obj->runTest('通过对象调用');    
MethodTest::runTest('静态调用');  // As of PHP 5.3.0
```

以上代码执行后输出如下： 
调用对象方法’runTest’ –- 通过对象调用调用静态方法’runTest’ –- 静态调用 
以函数形式来调用对象时，__invoke()方法将被自动调用。

```
class MethodTest {    
    public function __call($name, $arguments) {    
        // 参数 $name 大小写敏感    
        echo "Calling object method '$name' "    
             . implode(', ', $arguments). "\n";    
    }    
 
    /**  PHP 5.3.0 以上版本中本类方法有效  */    
    public static function __callStatic($name, $arguments) {    
        // 参数 $name 大小写敏感    
        echo "Calling static method '$name' "    
             . implode(', ', $arguments). "\n";    
    }    
}    
$obj = new MethodTest;    
$obj->runTest('in object context');    
MethodTest::runTest('in static context');  // As of PHP 5.3.0  
```

##	Nowdoc语法

用法和Heredoc类似，但使用单引号。Heredoc则需要通过使用双引号来声明。 
Nowdoc中不会做任何变量解析，非常适合于传递一段PHP代码。
```
// Nowdoc 单引号 PHP 5.3之后支持    
$name = 'MyName';    
echo <<<'EOT'    
My name is "$name".    
EOT;    
//上面代码输出 My name is "$name". ((其中变量不被解析)    
// Heredoc不加引号    
echo <<<FOOBAR    
Hello World!    
FOOBAR;    
//或者 双引号 PHP 5.3之后支持    
echo <<<"FOOBAR"    
Hello World!    
FOOBAR;  
```
支持通过Heredoc来初始化静态变量、类成员和类常量。

```
// 静态变量    
function foo()    
{    
    static $bar = <<<LABEL    
Nothing in here...    
LABEL;    
}    
// 类成员、常量    
class foo    
{    
    const BAR = <<<FOOBAR    
Constant example    
FOOBAR;    
 
    public $baz = <<<FOOBAR    
Property example    
FOOBAR;    
}  
```

##	在类外也可使用const来定义常量

```
//PHP中定义常量通常是用这种方式  
define("CONSTANT", "Hello world.");  
 
//并且新增了一种常量定义方式  
const CONSTANT = 'Hello World'; 
```

##	三元运算符增加了一个快捷书写方式

原本格式为是(expr1) ? (expr2) : (expr3) 
如果expr1结果为True，则返回expr2的结果。 
新增一种书写方式，可以省略中间部分，书写为expr1 ?: expr3 
如果expr1结果为True,则返回expr1的结果
```
$expr1=1;
$expr2=2;
//原格式  
$expr=$expr1?$expr1:$expr2  
//新格式  
$expr=$expr1?:$expr2
```
输出结果： 
1 
1

##	空合并运算符（??）

简化判断
```
$param = $_GET['param'] ?? 1;
```
相当于：
```
$param = isset($_GET['param']) ? $_GET['param'] : 1;
```

##	Json更懂中文(JSON_UNESCAPED_UNICODE)

```
echo json_encode("中文", JSON_UNESCAPED_UNICODE);  
//输出："中文" 
```

##	二进制

```
$bin  = 0b1101;  
echo $bin;  
//13 
```

##	Unicode codepoint 转译语法

这接受一个以16进制形式的 Unicode codepoint，并打印出一个双引号或heredoc包围的 UTF-8 编码格式的字符串。 可以接受任何有效的 codepoint，并且开头的 0 是可以省略的。
```
 echo "\u{9876}"
```

##	使用 ** 进行幂运算

加入右连接运算符 * 来进行幂运算。 同时还支持简写的 *= 运算符，表示进行幂运算并赋值。
```
printf("2 ** 3 ==      %d\n", 2 ** 3);
printf("2 ** 3 ** 2 == %d\n", 2 ** 3 ** 2);
 
$a = 2;
$a **= 3;
printf("a ==           %d\n", $a);
```
输出 
2 ** 3 == 8 
2 * 3 * 2 == 512 
a == 8

##	太空船操作符（组合比较符）

太空船操作符用于比较两个表达式。当 b 时它分别返回 -1 、 0 或 1 。 比较的原则是沿用 PHP 的常规比较规则进行的。
```
// Integers
echo 1 <=> 1; // 0
echo 1 <=> 2; // -1
echo 2 <=> 1; // 1
// Floats
echo 1.5 <=> 1.5; // 0
echo 1.5 <=> 2.5; // -1
echo 2.5 <=> 1.5; // 1
// Strings
echo "a" <=> "a"; // 0
echo "a" <=> "b"; // -1
echo "b" <=> "a"; // 1
```

##	Traits

Traits提供了一种灵活的代码重用机制，即不像interface一样只能定义方法但不能实现，又不能像class一样只能单继承。至于在实践中怎样使用，还需要深入思考。 
魔术常量为TRAIT
```
官网的一个例子：  
trait SayWorld {  
        public function sayHello() {  
                parent::sayHello();  
                echo "World!\n";  
                echo 'ID:' . $this->id . "\n";  
        }  
}  
 
class Base {  
        public function sayHello() {  
                echo 'Hello ';  
        }  
}  
 
class MyHelloWorld extends Base {  
        private $id;  
 
        public function __construct() {  
                $this->id = 123456;  
        }  
 
        use SayWorld;  
}  
 
$o = new MyHelloWorld();  
$o->sayHello();  
 
/*will output: 
Hello World! 
ID:123456 
 */  
```

##	array 数组简写语法
```
$arr = [1,'james', 'james@fwso.cn'];  
$array = [  
　　"foo" => "bar",  
　　"bar" => "foo"  
　　]; 
```

##	array 数组中某个索引值简写

```
function myfunc() {  
    return array(1,'james', 'james@fwso.cn');  
}
echo myfunc()[1];  
 
$name = explode(",", "Laruence,male")[0];  
explode(",", "Laruence,male")[3] = "phper";  
```

##	非变量array和string也能支持下标获取了

```
echo array(1, 2, 3)[0];  
echo [1, 2, 3][0];  
echo "foobar"[2];  
```

##	支持为负的字符串偏移量

现在所有接偏移量的内置的基于字符串的函数都支持接受负数作为偏移量，包括数组解引用操作符([]).
```
var_dump("abcdef"[-2]);
var_dump(strpos("aabbcc", "b", -3));
```
以上例程会输出
```
string (1) "e"
int(3)
```

##	常量引用

“常量引用”意味着数组可以直接操作字符串和数组字面值。举两个例子:
```
function randomHexString($length) {    
    $str = '';    
    for ($i = 0; $i < $length; ++$i) {    
        $str .= "0123456789abcdef"[mt_rand(0, 15)]; // direct dereference of string    
    }    
}    
function randomBool() {    
    return [false, true][mt_rand(0, 1)]; // direct dereference of array    
}   
```

##	常量增强

允许常量计算,允许使用包含数字、字符串字面值和常量的标量表达式
```
const A = 2;  
const B = A + 1;  
class C  
{  
    const STR = "hello";  
    const STR2 = self::STR + ", world";  
}
```
允许常量作为函数参数默认
```
function test($arg = C::STR2)
```
类常量可见性 
现在起支持设置类常量的可见性。
```
class ConstDemo
{
    const PUBLIC_CONST_A = 1;
    public const PUBLIC_CONST_B = 2;
    protected const PROTECTED_CONST = 3;
    private const PRIVATE_CONST = 4;
}
```

##	通过define()定义常量数组

```
define('ANIMALS', ['dog', 'cat', 'bird']);
echo ANIMALS[1]; // outputs "cat"
```

##	函数变量类型声明

两种模式 : 强制 ( 默认 ) 和 严格模式 
类型：array,object(对象),string、int、float和 bool
```
class bar {  
function foo(bar $foo) {  
}  
//其中函数foo中的参数规定了传入的参数必须为bar类的实例，否则系统会判断出错。同样对于数组来说，也可以进行判断，比如：  
function foo(array $foo) {  
}  
}  
　　foo(array(1, 2, 3)); // 正确，因为传入的是数组  
　　foo(123); // 不正确，传入的不是数组
 
function add(int $a) 
{ 
    return 1+$a; 
} 
var_dump(add(2));
 
function foo(int $i) { ... }  
foo(1);      // $i = 1  
foo(1.0);    // $i = 1  
foo("1");    // $i = 1  
foo("1abc"); // not yet clear, maybe $i = 1 with notice  
foo(1.5);    // not yet clear, maybe $i = 1 with notice  
foo([]);     // error  
foo("abc");  // error  
```

##	可变函数参数

代替 func_get_args()
```
function add(...$args)  
{  
    $result = 0;  
    foreach($args as $arg)  
        $result += $arg;  
    return $result;  
} 
```

##	可为空（Nullable）类型

类型现在允许为空，当启用这个特性时，传入的参数或者函数返回的结果要么是给定的类型，要么是 null 。可以通过在类型前面加上一个问号来使之成为可为空的。
```
function test(?string $name)
{
    var_dump($name);
}
```
以上例程会输出：
```
string(5) "tpunt"
NULL
Uncaught Error: Too few arguments to function test(), 0 passed in...
```

##	Void 函数

在PHP 7 中引入的其他返回值类型的基础上，一个新的返回值类型void被引入。 返回值声明为 void 类型的方法要么干脆省去 return 语句，要么使用一个空的 return 语句。 对于 void 函数来说，null 不是一个合法的返回值。
```
function swap(&$left, &$right) : void
{
    if ($left === $right) {
        return;
    }
    $tmp = $left;
    $left = $right;
    $right = $tmp;
}
$a = 1;
$b = 2;
var_dump(swap($a, $b), $a, $b);
```
以上例程会输出：
```
null
int(2)
int(1)
```
试图去获取一个 void 方法的返回值会得到 null ，并且不会产生任何警告。这么做的原因是不想影响更高层次的方法。

##	实例化类

```
class test{  
    function show(){  
return 'test';  
    }  
}  
echo (new test())->show();
```

##	支持 Class::{expr}() 语法

```
foreach ([new Human("Gonzalo"), new Human("Peter")] as $human) {  
    echo $human->{'hello'}();  
} 
```

##	Getter 和 Setter

如果你从不喜欢写这些getXYZ()和setXYZ($value)方法，那么这应该是你最受欢迎的改变。提议添加一个新的语法来定义一个属性的设置/读取:
```
class TimePeriod {  
    public $seconds;  
    public $hours {  
        get { return $this->seconds / 3600; }  
        set { $this->seconds = $value * 3600; }  
    }  
}  
$timePeriod = new TimePeriod;  
$timePeriod->hours = 10;  
var_dump($timePeriod->seconds); // int(36000)  
var_dump($timePeriod->hours);   // int(10)  
```
















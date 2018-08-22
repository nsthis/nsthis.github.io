---
title: PHP7新特性（下）
date: 2018-02-03 19:45:45
tags: PHP
categories: PHP

---

好奇怪,hexo居然不支持英文状态下的()。

<!-- more -->

##	迭代器 yield

目前，自定义迭代器很少使用，因为它们的实现，需要大量的样板代码。生成器解决这个问题，并提供了一种简单的样板代码来创建迭代器。 
例如，你可以定义一个范围函数作为迭代器:
```
function *xrange($start, $end, $step = 1) {  
    for ($i = $start; $i < $end; $i += $step) {  
        yield $i;  
    }  
}  
foreach (xrange(10, 20) as $i) {  
    // ...  
}  
```
上述xrange函数具有与内建函数相同的行为，但有一点区别：不是返回一个数组的所有值，而是返回一个迭代器动态生成的值。

##	列表解析和生成器表达式

列表解析提供一个简单的方法对数组进行小规模操作:
```
$firstNames = [foreach ($users as $user) yield $user->firstName];  
```
上述列表解析相等于下面的代码：
```
$firstNames = [];  
foreach ($users as $user) {  
    $firstNames[] = $user->firstName;  
}
```
也可以这样过滤数组:
```
$underageUsers = [foreach ($users as $user) if ($user->age < 18) yield $user];  
```
生成器表达式也很类似，但是返回一个迭代器(用于动态生成值)而不是一个数组。

##	生成器的返回值

在PHP5.5引入生成器的概念。生成器函数每执行一次就得到一个yield标识的值。在PHP7中，当生成器迭代完成后，可以获取该生成器函数的返回值。通过Generator::getReturn()得到。
```
function generator()
{
    yield 1;
    yield 2;
    yield 3;
    return "a";
}
 
$generatorClass = ("generator")();
foreach ($generatorClass as $val) {
    echo $val ." ";
 
}
echo $generatorClass->getReturn();
```
输出为：1 2 3 a

##	生成器中引入其他生成器

在生成器中可以引入另一个或几个生成器，只需要写yield from functionName1
```
function generator1()
{
    yield 1;
    yield 2;
    yield from generator2();
    yield from generator3();
}
 
function generator2()
{
    yield 3;
    yield 4;
}
 
function generator3()
{
    yield 5;
    yield 6;
}
 
foreach (generator1() as $val) {
    echo $val, " ";
}
```

输出：1 2 3 4 5 6

##	finally关键字

这个和java中的finally一样，经典的try … catch … finally 三段式异常处理。

##	多异常捕获处理
```
一个catch语句块现在可以通过管道字符(|)来实现多个异常的捕获。 这对于需要同时处理来自不同类的不同异常时很有用。

try {
    // some code
} catch (FirstException | SecondException $e) {
    // handle first and second exceptions
} catch (\Exception $e) {
    // ...
} finally{
//
}

```

##	foreach 支持list()

对于“数组的数组”进行迭代，之前需要使用两个foreach，现在只需要使用foreach + list了，但是这个数组的数组中的每个数组的个数需要一样。看文档的例子一看就明白了。
```
$array = [  
    [1, 2],  
    [3, 4],  
];  
foreach ($array as list($a, $b)) {  
    echo "A: $a; B: $b\n";  
} 
```

##	短数组语法 Symmetric array destructuring

短数组语法（[]）现在可以用于将数组的值赋给一些变量（包括在foreach中）。 这种方式使从数组中提取值变得更为容易。
```
$data = [
    ['id' => 1, 'name' => 'Tom'],
    ['id' => 2, 'name' => 'Fred'],
];
while (['id' => $id, 'name' => $name] = $data) {
    // logic here with $id and $name
}
```

##	list()现在支持键名

现在list()支持在它内部去指定键名。这意味着它可以将任意类型的数组 都赋值给一些变量（与短数组语法类似）
```
$data = [
    ['id' => 1, 'name' => 'Tom'],
    ['id' => 2, 'name' => 'Fred'],
];
while (list('id' => $id, 'name' => $name) = $data) {
    // logic here with $id and $name
}
```

##	iterable 伪类

现在引入了一个新的被称为iterable的伪类 (与callable类似)。 这可以被用在参数或者返回值类型中，它代表接受数组或者实现了Traversable接口的对象。 至于子类，当用作参数时，子类可以收紧父类的iterable类型到array 或一个实现了Traversable的对象。对于返回值，子类可以拓宽父类的 array或对象返回值类型到iterable。
```
function iterator(iterable $iter)
{
    foreach ($iter as $val) {
        //
    }
}
```

##	ext/openssl 支持 AEAD

通过给openssl_encrypt()和openssl_decrypt() 添加额外参数，现在支持了AEAD (模式 GCM and CCM)。 
通过 Closure::fromCallable() 将callables转为闭包 
Closure新增了一个静态方法，用于将callable快速地 转为一个Closure 对象。
```
class Test
{
    public function exposeFunction()
    {
        return Closure::fromCallable([$this, 'privateFunction']);
    }
    private function privateFunction($param)
    {
        var_dump($param);
    }
}
$privFunc = (new Test)->exposeFunction();
$privFunc('some value');
```
以上例程会输出：
```
string(10) "some value"
```

##	匿名类

现在支持通过 new class 来实例化一个匿名类，这可以用来替代一些“用后即焚”的完整类定义。
```
interface Logger
{
    public function log(string $msg);
}
 
class Application
{
    private $logger;
 
    public function getLogger(): Logger
    {
        return $this->logger;
    }
 
    public function setLogger(Logger $logger)
    {
        $this->logger = $logger;
    }
}
 
$app = new Application;
$app->setLogger(new class implements Logger
{
    public function log(string $msg)
    {
        echo $msg;
    }
});
var_dump($app->getLogger());
```

##	Closure::call()

Closure::call() 现在有着更好的性能，简短干练的暂时绑定一个方法到对象上闭包并调用它。
```
class Test
{
    public $name = "lixuan";
}
 
//PHP7和PHP5.6都可以
$getNameFunc = function () {
    return $this->name;
};
$name = $getNameFunc->bindTo(new Test, 'Test');
echo $name();
//PHP7可以,PHP5.6报错
$getX = function () {
    return $this->name;
};
echo $getX->call(new Test);
```

##	为unserialize()提供过滤

这个特性旨在提供更安全的方式解包不可靠的数据。它通过白名单的方式来防止潜在的代码注入。
```
//将所有对象分为__PHP_Incomplete_Class对象
$data = unserialize($foo, ["allowed_classes" => false]);
//将所有对象分为__PHP_Incomplete_Class 对象 除了ClassName1和ClassName2
$data = unserialize($foo, ["allowed_classes" => ["ClassName1", "ClassName2"]);
//默认行为，和 unserialize($foo)相同
$data = unserialize($foo, ["allowed_classes" => true]);
```

##	IntlChar

新增加的 IntlChar 类旨在暴露出更多的 ICU 功能。这个类自身定义了许多静态方法用于操作多字符集的 unicode 字符。Intl是Pecl扩展，使用前需要编译进PHP中，也可apt-get/yum/port install php5-intl
```
printf('%x', IntlChar::CODEPOINT_MAX);
echo IntlChar::charName('@');
var_dump(IntlChar::ispunct('!'));
```
以上例程会输出： 
10ffff 
COMMERCIAL AT 
bool(true)

##	预期

预期是向后兼用并增强之前的 assert() 的方法。 它使得在生产环境中启用断言为零成本，并且提供当断言失败时抛出特定异常的能力。 老版本的API出于兼容目的将继续被维护，assert()现在是一个语言结构，它允许第一个参数是一个表达式，而不仅仅是一个待计算的 string或一个待测试的boolean。
```
ini_set('assert.exception', 1);
class CustomError extends AssertionError {}
assert(false, new CustomError('Some error message'));
```
以上例程会输出： 
Fatal error: Uncaught CustomError: Some error message

##	intdiv()

接收两个参数作为被除数和除数，返回他们相除结果的整数部分。
```
var_dump(intdiv(7, 2));
```
输出int(3)

##	CSPRNG

新增两个函数: random_bytes() and random_int().可以加密的生产被保护的整数和字符串。总之随机数变得安全了。 
random_bytes — 加密生存被保护的伪随机字符串 
random_int —加密生存被保护的伪随机整数

##	preg_replace_callback_array()

新增了一个函数preg_replace_callback_array()，使用该函数可以使得在使用preg_replace_callback()函数时代码变得更加优雅。在PHP7之前，回调函数会调用每一个正则表达式，回调函数在部分分支上是被污染了。

##	Session options

现在，session_start()函数可以接收一个数组作为参数，可以覆盖php.ini中session的配置项。 
比如，把cache_limiter设置为私有的，同时在阅读完session后立即关闭。
```
session_start(['cache_limiter' => 'private',
               'read_and_close' => true,
]);
```

##	$_SERVER[“REQUEST_TIME_FLOAT”]

这个是用来统计服务请求时间的，并用ms(毫秒)来表示
```
echo "脚本执行时间 ", round(microtime(true) - $_SERVER["REQUEST_TIME_FLOAT"], 2), "s";  
```

##	empty() 支持任意表达式

empty() 现在支持传入一个任意表达式，而不仅是一个变量
```
function always_false() {
    return false;
}
 
if (empty(always_false())) {
    echo 'This will be printed.';
}
 
if (empty(true)) {
    echo 'This will not be printed.';
}
```

输出 
This will be printed.

##	php://input 可以被复用

php://input 开始支持多次打开和读取，这给处理POST数据的模块的内存占用带来了极大的改善。

##	Upload progress 文件上传

Session提供了上传进度支持，通过$_SESSION[“upload_progress_name”]就可以获得当前文件上传的进度信息，结合Ajax就能很容易实现上传进度条了。 

##	大文件上传支持

可以上传超过2G的大文件。

##	GMP支持操作符重载

GMP 对象支持操作符重载和转换为标量，改善了代码的可读性，如：
```
$a = gmp_init(42);  
$b = gmp_init(17);  
 
// Pre-5.6 code:  
var_dump(gmp_add($a, $b));  
var_dump(gmp_add($a, 17));  
var_dump(gmp_add(42, $b));  
 
// New code:  
var_dump($a + $b);  
var_dump($a + 17);  
var_dump(42 + $b);  
```

##	JSON 序列化对象

实现了JsonSerializable接口的类的实例在json_encode序列化的之前会调用jsonSerialize方法，而不是直接序列化对象的属性。 

##	HTTP状态码在200-399范围内均被认为访问成功

##	支持动态调用静态方法

```
class Test{    
    public static function testgo()    
    {    
         echo "gogo!";    
    }    
}    
$class = 'Test';    
$action = 'testgo';    
$class::$action();  //输出 "gogo!"
```

##	弃用e修饰符

e修饰符是指示preg_replace函数用来评估替换字符串作为PHP代码，而不只是仅仅做一个简单的字符串替换。不出所料，这种行为会源源不断的出现安全问题。这就是为什么在PHP5.5 中使用这个修饰符将抛出一个弃用警告。作为替代，你应该使用preg_replace_callback函数。你可以从RFC找到更多关于这个变化相应的信息。

##	新增函数 boolval

PHP已经实现了strval、intval和floatval的函数。为了达到一致性将添加boolval函数。它完全可以作为一个布尔值计算，也可以作为一个回调函数。

##	新增函数hash_pbkdf2

PBKDF2全称“Password-Based Key Derivation Function 2”，正如它的名字一样，是一种从密码派生出加密密钥的算法。这就需要加密算法，也可以用于对密码哈希。更广泛的说明和用法示例

##	array_column

```
//从数据库获取一列，但返回是数组。  
$userNames = [];  
foreach ($users as $user) {  
    $userNames[] = $user['name'];  
}  
//以前获取数组某列值，现在如下  
$userNames = array_column($users, 'name');  
```

##	一个简单的密码散列API

```
$password = "foo";    
// creating the hash    
$hash = password_hash($password, PASSWORD_BCRYPT);    
// verifying a password    
if (password_verify($password, $hash)) {    
    // password correct!    
} else {    
    // password wrong!    
}   
```

##	异步信号处理 Asynchronous signal handling

A new function called pcntl_async_signals() has been introduced to enable asynchronous signal handling without using ticks (which introduce a lot of overhead). 
增加了一个新函数 pcntl_async_signals()来处理异步信号，不需要再使用ticks(它会增加占用资源)
```
pcntl_async_signals(true); // turn on async signals
pcntl_signal(SIGHUP,  function($sig) {
    echo "SIGHUP\n";
});
posix_kill(posix_getpid(), SIGHUP);
```
以上例程会输出：
```
SIGHUP
```

##	HTTP/2 服务器推送支持 ext/curl

Support for server push has been added to the CURL extension (requires version 7.46 and above). This can be leveraged through the curl_multi_setopt() function with the new CURLMOPT_PUSHFUNCTION constant. The constants CURL_PUST_OK and CURL_PUSH_DENY have also been added so that the execution of the server push callback can either be approved or denied. 
蹩脚英语： 
对于服务器推送支持添加到curl扩展（需要7.46及以上版本）。 
可以通过用新的CURLMOPT_PUSHFUNCTION常量 让curl_multi_setopt()函数使用。 
也增加了常量CURL_PUST_OK和CURL_PUSH_DENY，可以批准或拒绝 服务器推送回调的执行

##	php.ini中可使用变量

##	PHP default_charset 默认字符集 为UTF-8

##	ext/phar、ext/intl、ext/fileinfo、ext/sqlite3和ext/enchant等扩展默认随PHP绑定发布。其中Phar可用于打包PHP程序，类似于Java中的jar机制

##	PHP7.1不兼容性

###	当传递参数过少时将抛出错误

在过去如果我们调用一个用户定义的函数时，提供的参数不足，那么将会产生一个警告(warning)。 现在，这个警告被提升为一个错误异常(Error exception)。这个变更仅对用户定义的函数生效， 并不包含内置函数。例如：
```
function test($param){}
test();
```
输出:
```
Uncaught Error: Too few arguments to function test(), 0 passed in %s on line %d and exactly 1 expected in %s:%d
```

###	禁止动态调用函数
禁止动态调用函数如下 
assert() - with a string as the first argument 
compact() 
extract() 
func_get_args() 
func_get_arg() 
func_num_args() 
get_defined_vars() 
mb_parse_str() - with one arg 
parse_str() - with one arg
```
(function () {
    'func_num_args'();
})();
```
输出:
```
Warning: Cannot call func_num_args() dynamically in %s on line %d
```

###	无效的类，接口，trait名称命名

以下名称不能用于 类，接口或trait 名称命名： 
void 
iterable









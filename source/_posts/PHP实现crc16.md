---
title: PHP实现crc16
date: 2019-03-23 19:22:29
tags: PHP
categories: PHP
---

今天接到客户需求，需要将某序列号转为十进制、然后十六进制，将得到的十六进制不足4位时前面补零，之后进行crc16转换后再转十进制。

<!-- more -->

##  PHP进制转换

这个如果不清楚的同学可以去[php官网](http://php.net/)进行查看，奇怪的是PHP官网居然不是https的...

##  crc16

1、PHP官网目前的话关于crc16的demo没有，所以以下是我从网上找的demo

````
$string = 57;
//十进制转16进制
$hexadecimal = base_convert($string,10,16);
//自动补全0
$hexadecimal_leng = strlen($hexadecimal);

switch (4 - $hexadecimal_leng)
{
    case '1' :
        $completion = '0' . $hexadecimal;
        break;
    case '2' :
        $completion = '00' . $hexadecimal;
        break;
    case '3' :
        $completion = '000' . $hexadecimal;
        break;
    case '4' :
        $completion = '0000';
        break;
}
//调用crc16
$pach = pack('H*', $completion);
$num = crc16($pack);

$sprintf =  sprintf('%04X', $num); 
$base = base_convert($a,16,10);
echo $base ;exit;



function crc16($string) {
    $crc = 0xFFFF;
    for ($x = 0; $x < strlen ($string); $x++) {
        $crc = $crc ^ ord($string[$x]);
        for ($y = 0; $y < 8; $y++) {
            if (($crc & 0x0001) == 0x0001) {
                $crc = (($crc >> 1) ^ 0xA001);
            } else { $crc = $crc >> 1; }
        }
    }
    return $crc;
}
````

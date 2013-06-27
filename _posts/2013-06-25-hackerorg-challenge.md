---
layout: post
title: "HACKER.ORG Challenge"
description: ""
category: Geek 
tags: []
---

或许会持续更新？

## Crypto\#Who goes there?

先问你的用户名。输了之后错误提示是一个倒过来的串。所以把用户名逆序就行。

## Crypto\#Didactic Byte

让你把233转成16进制。

{% highlight ruby %}
puts "%x" % 233
{% endhighlight %}

## Crypto\#Dit Dah

    - .... . .- -. ... .-- . .-. .. ... .... --- .- .-. ... .

明摆着的摩尔斯电码嘛。查下表就行了。

## Crypto\#Newsgroup Cipher

    Guvf zrffntr vf rapelcgrq va ebg 13. Lbhe nafjre vf svfupnxr.

观察到有两个vf，猜测是is，然后发现这在字母表中是平移了13位。

{% highlight ruby %}
src = gets.split("")

des = src.collect do |x|
  x.downcase!
  if x >= "a" && x <= "z"
    x = ((x.ord - "a".ord + 13) % 26 + "a".ord).chr
  end
  x
end

puts des.join("")
{% endhighlight %}

## Crypto\#Ones and Zeroes

    01110101 01110011 01100101 00100000 01110111 01100101 
	01100100 01101110 01100101 01110011 01100100 01100001 
	01111001 00100000 01100110 01101111 01110010 00100000 
	01110100 01101000 01100101 00100000 01100001 01101110 
	01110011 01110111 01100101 01110010

猜是一坨二进制数。转成十进制后发现都是100左右的数。果断替换成ASCII码。

{% highlight ruby %}
src = gets.split()

des = src.collect do |x|
  x.to_i(2).chr
end

puts des.join("")
{% endhighlight %}

## Crypto\#The Lightest Touch

     . .  .     .  ..  .  . .  .      .  .     . .  .  .. .. .  ..  .  . .  .  .. .. .  
    .. ..  .        . .  ..  . ..    .  .     .   .     .  .  . .  .  .  .   .  .     . 
    .              .  .   .    .        .     .  .  .. .     .     .     .     .        

看得出每个单位是一个3x2的点阵。盲文。

## Crypto\#Didactic Bytes

让你把三个数写成8位二进制再拼起来。

{% highlight ruby %}
puts [199, 77, 202].collect { |x| "%08d" % x.to_s(2).to_i }.join("").to_i(2)
{% endhighlight %}

## Crypto\#Substitute Teacher

    ISS NVVK DIPXYWA PIT AVSUY QIAOP PWZEHVNWIEDZ. CDYT ZVM LOTK HDY AVSMHOVT HV HDOA HYFH,
	ZVM COSS QY IQSY HV NYH HDY ITACYW, CDOPD OA IKMGQWIHY.

先以为是维吉尼亚密码，然后各种找关键词，无果。后来根据题目名称想到替换密码，果断从ZVM还有HDY等入手。还有一个规律是总会出现THE ANSWER这类的字样。

破出来是

    ALL GOOD HACKERS CAN SOLVE BASIC CRYPTOGRAPHY. WHEN YOU FIND THE SOLUTION TO THIS TEXT,
	YOU WILL BE ABLE TO GET THE ANSWER, WHICH IS ADUMBRATE.

## Crypto\#Didactic XOR

就让你把两个十六进制数xor一下的值对应的ASCII码输出。

{% highlight ruby %}
puts ("9f".to_i(16) ^ "c7".to_i(16)).chr
{% endhighlight %}

{% include JB/setup %}

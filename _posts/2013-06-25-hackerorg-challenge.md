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

{% include JB/setup %}

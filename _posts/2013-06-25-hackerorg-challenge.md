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
  if x.between?("a", "z")
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

## Crypto\#Didactic Text

给了一段话，提示你找出原文作对比。搜出来一篇Gettysburg Address。果然对比一下就发现答案了。

## Crypto\#Didactic XOR Cipher

    3d2e212b20226f3c2a2a2b

告诉你每个单位都xor了79。把上面这个串分成两个一组，xor回去，再转成ASCII码就可以了。

{% highlight ruby %}
src = "3d 2e 21 2b 20 22 6f 3c 2a 2a 2b".split()

des = src.collect do |x|
  (x.to_i(16) ^ 79).chr
end

puts des.join("")
{% endhighlight %}

## Crypto\#Didactic XOR Cipher 2

跟上面那个一样，只不过给了一个更长的串，而且没告诉你xor的值。统计之后发现81和c4都很多，猜测中间一个是e，因为有一说是英文中出现频率最高的字母是e。然后81果然对应的e。

## Crypto\#Didactic XOR Cipher 3

这个更凶残。每次xor的数是上一次xor的数加x模256。想了一下发现可以枚举前3个字符是什么，解决。

{% highlight ruby %}
lis = []
("a".."z").each { |x| lis.push(x) }
lis.push(" ")
lis.push("'")

lis.each do |a|
  lis.each do |b|
    lis.each do |c|
      _a = a.ord ^ src[0]
      _b = b.ord ^ src[1]
      _c = c.ord ^ src[2]
      d = ((_b - _a) % 256 + 256) % 256
      if (_b + d) % 256 == _c
        pwd, add = _a, d
        chk = true
        des = src.collect do |x|
          y = x ^ pwd
          pwd = (pwd + add) % 256
          chk = false unless lis.include?(y.chr)
          y.chr
        end
        puts des.join("") if chk
      end
    end
  end
end
{% endhighlight %}

## Crypto\#Didactic RGB

给了个像素让你把RGB三个值按某种方式连起来。这里偷懒用的gimp直接看。

{% highlight ruby %}
puts [156, 84, 198].collect { |x| "%08d" % x.to_s(2).to_i }.join("").to_i(2)
{% endhighlight %}

## Crypto\#Didactic Red

变成了四个像素。分别提取R值。虽然有个提示但是没用啊。继续用gimp。

{% highlight ruby %}
puts [222, 250, 206, 209].inject("") { |s, x| s + x.to_s(16) }
{% endhighlight %}

## Crypto\#Didactic Green

这次有84个元素要提取G值。下载了一个ChunkyPNG库来用。把提取出来的值转成ASCII码就行了。

{% highlight ruby %}
require 'chunky_png'

img = ChunkyPNG::Image.from_file("greenline.png")
des = ""

img.dimension.width.times.with_index do |i|
  des += ChunkyPNG::Color.g(img[i, 0]).chr
end

puts des
{% endhighlight %}

## Crypto\#Didactic Bits

给了一个很长的ab串。提示是让你看一下长度。发现是8的倍数。拆出来后用01替换再对应成ASCII码即可。

{% highlight ruby %}
des = []

src.each_char.with_index do |c, i|
  des.push("") if i % 8 == 0
  des[-1] += c == "a" ? "0" : "1"
end

puts des.inject("") { |s, x| s + x.to_i(2).chr }
{% endhighlight %}

## Crypto\#Didactic Vampire Text

给了一坨文字。发现基本上都是小写字母。然后把大写字母找出来拼起就可以了。

{% highlight ruby %}
File.open("text", "r") do |f|
  src = f.readlines[0].split("")
  puts src.select { |x| x.between?("A", "Z") }.join("")
end
{% endhighlight %}

## Crypto\#Didactic Feedback Cipher

还是2个一组的16进制码。加密方式是，除了第一个，每一个都xor了前一个密文。这种加密方式除了第一个确定不了其他都可以直接确定。

{% highlight ruby %}
src = "751a6f1d3d5c3241365321016c05620a7e5e34413246660461412e5a2e412c49254a24"
des = []

src.each_char.with_index do |c, i|
  des.push("") if i % 2 == 0
  des[-1] += c
  des[-1] = des[-1].to_i(16) if i % 2 == 1
end

(1..(des.size - 1)).reverse_each do |i|
  des[i] ^= des[i - 1]
end

puts des.inject("") { |s, x| s + x.chr }
{% endhighlight %}

## Crypto\#Didactic Text 2

Google了一下，发现这些都来自圣经旧约，而且每一句出处还不同。果断搜出所有句子的来源，章节号+索引号，发现索引号都是1到26的数，弄成字母就好。

{% highlight ruby %}
puts src.inject("") { |s, x| s + (x + "a".ord - 1).chr }
{% endhighlight %}

## Crypto\#Didactic Text Combo

把文章中的大写字母提出来，得到`XBGRWICGDQICGRXBGDLRWG`。根据文本内容猜测是替换密码。字母频度统计发现`G`代表`E`，果断猜前缀是`THEANSWER`。最后有个字母得不出来，还跑去查词典……

## Crypto\#Didactic XOR Long Cipher

跟XOR Cipher 2类似，不过这次的密码从长度为1变成了长度为4。同样可以枚举前几个字符是什么，配合人脑还是很容易解决的。

## Crypto\#Didactic Feedback Cipher 2

比这个系列的上一道题目复杂了一些。不过因为变量还是很少的缘故，枚举第一个字符和题目中的`x`就可以了。

## Crypto\#Didactic Feedback Cipher Long

除了前4后，第i个都xor了第i-4个的密文。同理除了前4个确定不了其他都可以直接确定。

## Crypto\#Broken XOR 3

跟XOR 3一样是每次异或的数是上一次加x模256，第一次异或的是b，只不过每一位密文的16进制在输出时如果第1位是0，这个0就被省略了。

然后发现可以dp。记f[i][j]表示前i个字符实际构成了j个密文是否可能。至于判断是否可能就是说原文的每一位都在一个特定的字符集中。当然需要枚举一下b和x。

## Crypto\#Didactic Feedback Cipher Long 2

这次是以长度为4作为单位来加密。第一次的密钥是k，给定x，那么下一次的密钥是这一次的密文加上x模0x100000000。

本着试一试的态度我先考虑1、5、9这样的位置，发现对于不同的k，x总是一样的。于是依次就可一把x确定下来了。然后除了前4个就都确定了。

不过这里有个蛋疼的地方是`txt[i] -> txt[i+3]`这样的东西居然应该理解成`txt[i]`作为最低位……

## Crypto\#Didactic Scrambled Egg Cipher Pre-Warmup

密钥的长度是4，每3位作为一个单元，加密变换重复3次以下：考虑二进制，中间8位异或上密钥的某一位（由当前串的最后两位确定），然后循环左移7位。

容易发现在密钥确定时，这个过程是可逆的。所以直接爆搜，我先是只考虑前21位，然后把可能的打出来肉眼。

{% highlight cpp %}
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <algorithm>

using namespace std;

int key[4];
bool done[4];
char buf[1024];
int seq[1024], n;
int dec[1024];
bool can[256];

void dfs(int step, int loop, int err_cnt) {
    if (err_cnt >= 1) return;
    if (step == 21) {
        for (int i = 0; i < n; ++ i) printf("%c", dec[i]);
        puts("");
        return;
    }
    if (loop == 0) {
        dec[step] = seq[step];
        dec[step + 1] = seq[step + 1];
        dec[step + 2] = seq[step + 2];
    }
    int msk = dec[step] << 16 | dec[step + 1] << 8 | dec[step + 2];
    if (loop == 3) {
        err_cnt += (!can[dec[step]]) + (!can[dec[step + 1]]) + (!can[dec[step + 2]]);
        dfs(step + 3, 0, err_cnt);
    }
    else {
        msk = (msk >> 7) | ((msk & ((1 << 7) - 1)) << 17);
        int e = msk & 3;
        if (done[e]) {
            msk ^= key[e] << 8;
            dec[step] = msk >> 16 & 255;
            dec[step + 1] = msk >> 8 & 255;
            dec[step + 2] = msk & 255;
            dfs(step, loop + 1, err_cnt);
            msk ^= key[e] << 8;
        }
        else {
            for (key[e] = 0; key[e] < 256; ++ key[e]) {
                done[e] = true;
                msk ^= key[e] << 8;
                dec[step] = msk >> 16 & 255;
                dec[step + 1] = msk >> 8 & 255;
                dec[step + 2] = msk & 255;
                dfs(step, loop + 1, err_cnt);
                msk ^= key[e] << 8;
                done[e] = false;
            }
        }
    }
}

int main() {
    scanf("%s", buf);
    n = (int)strlen(buf) >> 1;
    for (int i = 0; i < n; ++ i) {
        static char tmp[4];
        tmp[0] = buf[i << 1];
        tmp[1] = buf[i << 1 | 1];
        tmp[2] = '\0';
        seq[i] = (int)strtol(tmp, NULL, 16);
    }
    for (int i = 'a'; i <= 'z'; ++ i) can[i] = true;
    for (int i = 'A'; i <= 'Z'; ++ i) can[i] = true;
    for (int i = '0'; i <= '9'; ++ i) can[i] = true;
    can[' '] = can[','] = can['.'] = true;
    can['\''] = true;
    dfs(0, 0, 0);
    return 0;
}
{% endhighlight %}

## Crypto\#Didactic Feedback Cipher Long 3

跟Cipher Long 2类似，不过这次的密钥是上一次的密文乘上m再加x，再模0x100000000。

那么做法其实也是类似的。从低位开始枚举，即先枚举m和x的最低位，这样逐位暴力出m和x，那么除了前4个字母就都确定了。

## Coding\#XOR Eval

没啥好说的。

## Coding\#A Little PHP

虽说是PHP，但是可以直接看出代码在干什么，即使没学过。

## Coding\#A Little Python

跑代码。

## Misc\#Type Faster

就是个复制粘贴。但由于我网太烂试了好几次才过= =

# Coding\#BCD

二进制转十进制。

{% include JB/setup %}

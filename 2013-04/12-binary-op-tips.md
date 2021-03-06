Binary Processing Tips(二进制处理技巧)
======

最近在做协议分析(RTMP)，程序里时常要处理二进制。我网上搜索了一下，没有发现对二进制处理技巧的总结或者说明，自己尝试着总结一下。

### 1，基础
计算机在底层基本上都是以二进制来传递和存储的。类似于这样的序列：
```text
0010 1100, 0001 1101
```

在使用的时候在把这些二进制翻译成“有意义”的格式，比如翻译成英语字母。在表示二进制的时候如果直接用0和1来显示，那样会太长，不太好表示。程序中我们一般会将二进制表示成十进制或十六进制。比如上面的二进制就可以表示成：

```text
44, 29        =>十进制
0x2c, 0x1d    =>十六进制
```

因为我们通常将每8位(bits)视为为1个字节(byte)，也就是1byte = 8bits。字节是计算机传输的存储的大小和边界，也就是我们通常说的最小单位。为此我们在程序中通常是以8bits来处理，保存，传输二进制的。

在计算机中十六进制和二进制是天然适配的，也就是说每4个二进制(4bits)正好对应一个十六进制的数(从0~F)。所以程序语言中大多数直接支持十六进制字面量的，比如在很多语言中我们可以这样定义byte(也就是2个4bits):

```text
buf[0] = 0x2c
```

我们说过每个byte是8bits，这用十进制来表示的话就是0~255，编程语言中没有直接对于的类型来表示这个范围的数字，所以在C语言中我们用了大小一致的char类型，因为char类型也是8bits来表示的。但我们通常都是处理一串二进制，所以我们还可以用int(4bytes)作为单位大小来表示二进制，这样一个int值就可以表示4bytes。

### 2，二进制算法

我们处理二进制的需求通常有：

1. 取特定的一位或几位(这在协议分析中时常使用)
1. 生成特定一位或几位
1. 二进制编码格式用于传输和存储
1. 二进制解码成特定格式

#### 2.1 取特定的位(Mask + & + >>)

在程序里使用掩码(Mask)来取得特定的位，比如对于下面的二进制：

`0010 1100`

我们如果从左开始的第三位，那么首先创建这样的掩码:0010 0000，也是除了第三位以外都置0。这时我们就可以“按位与”得到具体的一位，再结合移位运算就可以得到结果：

```text
0010 1100
0010 0000 &
--------------------
0010 0000 >> 5  = 0000 0001
```

如果我们取中间的4位也一样，先生成掩码:0011 1100，然后：

```text
0010 1100
0011 1100 &
-------------
 
0010 1100 >> 2  = 0000 1011
```

#### 2.2 生成特定的位(Mask + | + >>)

生成特定的位也类似，只不过将&换成|。因为0和0或1的“或操作”不影响原来的值。比如，我们想把下面这个二进制从左边开始的3~5变成010:

`0010 1100`

我们的算法分为两步，第一步先将3~5位置0，为此我们生成掩码11(00 0)111,然后使用&将中间3~5位置0。置0后我们用掩码00(01 0)000和

```text
0010 1100
1100 0111 &    => 中间位置0掩码
-------------------
0000 0100       => 3~5位已置0
0001 0000 |     => 中间位操作掩码
-------------------
0001 0100  =>00(01 0)100 中间已置位的结果
```

#### 2.3 二进制编码

既然谈到编码和解码，那么在编码和解码之间必须达成协议，这个协议我们通常称为码表。在编码和解码过程中我们需要查码表来编解码。其实编码就是中映射，常用的映射有：

```text
8bits => char(ASCII)
32bits, 4bytes => unsigned integer(uint)
```

比如我们对以下二进制编码：

`0010 1100, 0101 1101, 0011 0001, 0110 1110`

结果：

```text
ASCII  => ,]1n
uint   => 744305006
```

#### 2.4 二进制解码

解码和编码的方式类似，是编码的逆向操作。

### 3，程序操作实例

待添加…

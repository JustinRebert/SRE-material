OpenSSL：

[TOC]

#  一、密码学简介

据记载，公元前400年，古希腊人发明了置换密码。1881年世界上的第一个电话保密专利出现。在第二次世界大战期间，德国军方启用“恩尼格玛”密码机，密码学在战争中起着非常重要的作用。

随着信息化和数字化社会的发展，人们对信息安全和保密的重要性认识不断提高，于是在1997年，美国国家标准局公布实施了“美国数据加密标准（DES）”，民间力量开始全面介入密码学的研究和应用中，采用的加密算法有DES、RSA、SHA等。随着对加密强度需求的不断提高，近期又出现了AES、ECC等。

使用密码学可以达到以下目的：

保密性：防止用户的标识或数据被读取。

数据完整性：防止数据被更改。

身份验证：确保数据发自特定的一方。

# 二、加密算法介绍

根据密钥类型不同将现代密码技术分为两类：对称加密算法（秘密钥匙加密）和非对称加密算法（公开密钥加密）。

对称钥匙加密系统是加密和解密均采用同一把秘密钥匙，而且通信双方都必须获得这把钥匙，并保持钥匙的秘密。

非对称密钥加密系统采用的加密钥匙（公钥）和解密钥匙（私钥）是不同的。

## 2.1：对称加密算法

对称加密算法用来对敏感数据等信息进行加密，常用的算法包括：

DES（Data Encryption Standard）：数据加密标准，速度较快，适用于加密大量数据的场合。

3DES（Triple DES）：是基于DES，对一块数据用三个不同的密钥进行三次加密，强度更高。

AES（Advanced Encryption Standard）：高级加密标准，是下一代的加密算法标准，速度快，安全级别高；

### 2.1.1：AES

2000年10月，NIST（美国国家标准和技术协会）宣布通过从15种侯选算法中选出的一项新的密匙加密标准。Rijndael被选中成为将来的AES。 Rijndael是在 1999 年下半年，由研究员 Joan Daemen 和 Vincent Rijmen 创建的。AES 正日益成为加密各种形式的电子数据的实际标准。

美国标准与技术研究院 (NIST) 于 2002 年 5 月 26 日制定了新的高级加密标准 (AES) 规范。

### 2.1.2：算法原理

AES 算法基于排列和置换运算。排列是对数据重新进行安排，置换是将一个数据单元替换为另一个。AES 使用几种不同的方法来执行排列和置换运算。

AES 是一个迭代的、对称密钥分组的密码，它可以使用128、192 和 256 位密钥，并且用 128 位（16字节）分组加密和解密数据。与公共密钥密码使用密钥对不同，对称密钥密码使用相同的密钥加密和解密数据。通过分组密码返回的加密数据的位数与输入数据相同。迭代加密使用一个循环结构，在该循环中重复置换和替换输入数据。

![1553773926284](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553773926284.png)

## 2.2：非对称算法

常见的非对称加密算法如下：

RSA：由 RSA 公司发明，是一个支持变长密钥的公共密钥算法，需要加密的文件块的长度也是可变的；

DSA（Digital Signature Algorithm）：数字签名算法，是一种标准的 DSS（数字签名标准）；

ECC（Elliptic Curves Cryptography）：椭圆曲线密码编码学。

### 2.2.1：ECC

在1976年，由于对称加密算法已经不能满足需要，Diffie 和Hellman发表了一篇叫《密码学新动向》的文章，介绍了公匙加密的概念，由Rivet、Shamir、Adelman提出了RSA算法。

随着分解大整数方法的进步及完善、计算机速度的提高以及计算机网络的发展，为了保障数据的安全，RSA的密钥需要不断增加，但是，密钥长度的增加导致了其加解密的速度大为降低，硬件实现也变得越来越难以忍受，这对使用RSA的应用带来了很重的负担，因此需要一种新的算法来代替RSA。

1985年N.Koblitz和Miller提出将椭圆曲线用于密码算法，根据是有限域上的椭圆曲线上的点群中的离散对数问题ECDLP。ECDLP是比因子分解问题更难的问题，它是指数级的难度。

### 2.2.1：算法原理——椭圆曲线上的难题

 椭圆曲线上离散对数问题ECDLP定义如下：给定素数p和椭圆曲线E，对Q＝kP，在已知P，Q 的情况下求出小于p的正整数k。可以证明由k和P计算Q比较容易，而由Q和P计算k则比较困难。

将椭圆曲线中的加法运算与离散对数中的模乘运算相对应，将椭圆曲线中的乘法运算与离散对数中的模幂运算相对应，我们就可以建立基于椭圆曲线的对应的密码体制。

例如，对应Diffie-Hellman公钥系统，我们可以通过如下方式在椭圆曲线上予以实现：在E上选取生成元P，要求由P产生的群元素足够多，通信双方A和B分别选取a和b，a和b 予以保密，但将aP和bP公开，A和B间通信用的密钥为abP，这是第三者无法得知的。

对应ELGamal密码系统可以采用如下的方式在椭圆曲线上予以实现：

将明文m嵌入到E上Pm点，选一点B∈E，每一用户都选一整数a，0＜a＜N，N为阶数已知，a保密，aB公开。欲向A送m，可送去下面一对数偶：［kB，Pm+k(aAB)］，k是随机产生的整数。A可以从kB求得k(aAB)。通过：Pm+k(aAB)- k(aAB)=Pm恢复Pm。同样对应DSA，考虑如下等式：

K=kG  [其中 K，G为Ep(a,b)上的点，k为小于n（n是点G的阶）的整数]

不难发现，给定k和G，根据加法法则，计算K很容易；但给定K和G，求k就相对困难了。

这就是椭圆曲线加密算法采用的难题。我们把点G称为基点（base point），k（k<n，n为基点G的阶）称为私有密钥（privte key），K称为公开密钥（public key)。

ECC与RSA的比较

ECC和RSA相比，在许多方面都有对绝对的优势，主要体现在以下方面：

Ø  抗攻击性强。相同的密钥长度，其抗攻击性要强很多倍。

Ø  计算量小，处理速度快。ECC总的速度比RSA、DSA要快得多。

Ø  存储空间占用小。ECC的密钥尺寸和系统参数与RSA、DSA相比要小得多，意味着它所占的存贮空间要小得多。这对于加密算法在IC卡上的应用具有特别重要的意义。

Ø  带宽要求低。当对长消息进行加解密时，三类密码系统有相同的带宽要求，但应用于短消息时ECC带宽要求却低得多。带宽要求低使ECC在无线网络领域具有广泛的应用前景。

ECC的这些特点使它必将取代RSA，成为通用的公钥加密算法。比如SET协议的制定者已把它作为下一代SET协议中缺省的公钥密码算法。

下面两张表示是RSA和ECC的安全性和速度的比较：

![1553773962141](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553773962141.png)



![1553773980368](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553773980368.png)



## 2.3：散列算法

散列是信息的提炼，通常其长度要比信息小得多，且为一个固定长度。加密性强的散列一定是不可逆的，这就意味着通过散列结果，无法推出任何部分的原始信息。任何输入信息的变化，哪怕仅一位，都将导致散列结果的明显变化，这称之为雪崩效应。散列还应该是防冲突的，即找不出具有相同散列结果的两条信息。具有这些特性的散列结果就可以用于验证信息是否被修改。

单向散列函数一般用于产生消息摘要，密钥加密等，常见的有：

Ø MD5（Message Digest Algorithm 5）：是RSA数据安全公司开发的一种单向散列算法。

Ø SHA（Secure Hash Algorithm）：可以对任意长度的数据运算生成一个160位的数值；

### 2.3.1：SHA-1

在1993年，安全散列算法（SHA）由美国国家标准和技术协会(NIST)提出，并作为联邦信息处理标准（FIPS PUB 180）公布；1995年又发布了一个修订版FIPS PUB 180-1，通常称之为SHA-1。SHA-1是基于MD4算法的，并且它的设计在很大程度上是模仿MD4的。现在已成为公认的最安全的散列算法之一，并被广泛使用。

2.3.2：算法原理

SHA-1是一种数据加密算法，该算法的思想是接收一段明文，然后以一种不可逆的方式将它转换成一段（通常更小）密文，也可以简单的理解为取一串输入码（称为预映射或信息），并把它们转化为长度较短、位数固定的输出序列即散列值（也称为信息摘要或信息认证代码）的过程。

单向散列函数的安全性在于其产生散列值的操作过程具有较强的单向性。如果在输入序列中嵌入密码，那么任何人在不知道密码的情况下都不能产生正确的散列值，从而保证了其安全性。ＳＨＡ将输入流按照每块５１２位（６４个字节）进行分块，并产生２０个字节的被称为信息认证代码或信息摘要的输出。

该算法输入报文的最大长度不超过264位，产生的输出是一个160位的报文摘要。输入是按512 位的分组进行处理的。SHA-1是不可逆的、防冲突，并具有良好的雪崩效应。

通过散列算法可实现数字签名实现，数字签名的原理是将要传送的明文通过一种函数运算（Hash）转换成报文摘要（不同的明文对应不同的报文摘要），报文摘要加密后与明文一起传送给接受方，接受方将接受的明文产生新的报文摘要与发送方的发来报文摘要解密比较，比较结果一致表示明文未被改动，如果不一致表示明文已被篡改。

MAC (信息认证代码)就是一个散列结果，其中部分输入信息是密码，只有知道这个密码的参与者才能再次计算和验证MAC码的合法性。MAC的产生参见下图。

![1553774161128](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553774161128.png)

信息认证代码

因为二者均由MD4导出，SHA-1和MD5彼此很相似。相应的，他们的强度和其他特性也是相似，但还有以下几点不同：

Ø  对强行供给的安全性：最显著和最重要的区别是SHA-1摘要比MD5摘要长32 位。使用强行技术，产生任何一个报文使其摘要等于给定报摘要的难度对MD5是2128数量级的操作，而对SHA-1则是2160数量级的操作。这样，SHA-1对强行攻击有更大的强度。

Ø  对密码分析的安全性：由于MD5的设计，易受密码分析的攻击，SHA-1显得不易受这样的攻击。

Ø  速度：在相同的硬件上，SHA-1的运行速度比MD5慢。

### 2.3.2:对称与非对称算法比较

以上综述了两种加密方法的原理，总体来说主要有下面几个方面的不同：

Ø  在管理方面：公钥密码算法只需要较少的资源就可以实现目的，在密钥的分配上，两者之间相差一个指数级别（一个是n一个是n2）。所以私钥密码算法不适应广域网的使用，而且更重要的一点是它不支持数字签名。

Ø  在安全方面：由于公钥密码算法基于未解决的数学难题，在破解上几乎不可能。对于私钥密码算法，到了AES虽说从理论来说是不可能破解的，但从计算机的发展角度来看。公钥更具有优越性。

Ø  从速度上来看：AES的软件实现速度已经达到了每秒数兆或数十兆比特。是公钥的100倍，如果用硬件来实现的话这个比值将扩大到1000倍。

## 2.4：加密算法的选择

前面的章节已经介绍了对称解密算法和非对称加密算法，有很多人疑惑：那我们在实际使用的过程中究竟该使用哪一种比较好呢？

我们应该根据自己的使用特点来确定，由于非对称加密算法的运行速度比对称加密算法的速度慢很多，当我们需要加密大量的数据时，建议采用对称加密算法，提高加解密速度。

对称加密算法不能实现签名，因此签名只能非对称算法。

由于对称加密算法的密钥管理是一个复杂的过程，密钥的管理直接决定着他的安全性，因此当数据量很小时，我们可以考虑采用非对称加密算法。

在实际的操作过程中，我们通常采用的方式是：采用非对称加密算法管理对称算法的密钥，然后用对称加密算法加密数据，这样我们就集成了两类加密算法的优点，既实现了加密速度快的优点，又实现了安全方便管理密钥的优点。

如果在选定了加密算法后，那采用多少位的密钥呢？一般来说，密钥越长，运行的速度就越慢，应该根据的我们实际需要的安全级别来选择，一般来说，RSA建议采用1024位的数字，ECC建议采用160位，AES采用128为即可。

## 2.5：密码学在现代的应用

随着密码学商业应用的普及，公钥密码学受到前所未有的重视。除传统的密码应用系统外，PKI系统以公钥密码技术为主，提供加密、签名、认证、密钥管理、分配等功能。

保密通信：保密通信是密码学产生的动因。使用公私钥密码体制进行保密通信时，信息接收者只有知道对应的密钥才可以解密该信息。

数字签名：数字签名技术可以代替传统的手写签名，而且从安全的角度考虑，数字签名具有很好的防伪造功能。在政府机关、军事领域、商业领域有广泛的应用环境。

秘密共享：秘密共享技术是指将一个秘密信息利用密码技术分拆成n个称为共享因子的信息，分发给n个成员，只有k(k≤n)个合法成员的共享因子才可以恢复该秘密信息，其中任何一个或m(m≤k)个成员合作都不知道该秘密信息。利用秘密共享技术可以控制任何需要多个人共同控制的秘密信息、命令等。

认证功能：在公开的信道上进行敏感信息的传输，采用签名技术实现对消息的真实性、完整性进行验证，通过验证公钥证书实现对通信主体的身份验证。

密钥管理：密钥是保密系统中更为脆弱而重要的环节，公钥密码体制是解决密钥管理工作的有力工具；利用公钥密码体制进行密钥协商和产生，保密通信双方不需要事先共享秘密信息；利用公钥密码体制进行密钥分发、保护、密钥托管、密钥恢复等。

基于公钥密码体制可以实现以上通用功能以外，还可以设计实现以下的系统：安全电子商务系统、电子现金系统、电子选举系统、电子招投标系统、电子彩票系统等。

公钥密码体制的产生是密码学由传统的政府、军事等应用领域走向商用、民用的基础，同时互联网、电子商务的发展为密码学的发展开辟了更为广阔的前景。

## 2.6：加密算法的未来

随着计算方法的改进，计算机运行速度的加快，网络的发展，越来越多的算法被破解。

在2004年国际密码学会议(Crypto’2004)上，来自中国山东大学的王小云教授做的破译MD5、HAVAL-128、MD4和RIPEMD算法的报告，令在场的国际顶尖密码学专家都为之震惊，意味着这些算法将从应用中淘汰。随后，SHA-1也被宣告被破解。

历史上有三次对DES有影响的攻击实验。1997年，利用当时各国 7万台计算机，历时96天破解了DES的密钥。1998年，电子边境基金会（EFF）用25万美元制造的专用计算机，用56小时破解了DES的密钥。1999年，EFF用22小时15分完成了破解工作。因此。曾经有过卓越贡献的DES也不能满足我们日益增长的需求了。

最近，一组研究人员成功的把一个512位的整数分解因子，宣告了RSA的破解。

我们说数据的安全是相对的，可以说在一定时期一定条件下是安全的，随着硬件和网络的发展，或者是另一个王小云的出现，目前的常用加密算法都有可能在短时间内被破解，那时我们不得不使用更长的密钥或更加先进的算法，才能保证数据的安全，因此加密算法依然需要不断发展和完善，提供更高的加密安全强度和运算速度。

纵观这两种算法一个从DES到3DES再到AES，一个从RSA到ECC。其发展角度无不是从密钥的简单性，成本的低廉性，管理的简易性，算法的复杂性，保密的安全性以及计算的快速性这几个方面去考虑。因此，未来算法的发展也必定是从这几个角度出发的，而且在实际操作中往往把这两种算法结合起来，也需将来一种集两种算法优点于一身的新型算法将会出现，到那个时候，电子商务的实现必将更加的快捷和安全。

# 三、OpenSSL

在计算机网络上，OpenSSL是一个开放源代码的软件库包，应用程序可以使用这个包来进行安全通信，避免窃听，同时确认另一端连线者的身份。这个包广泛被应用在互联网的网页服务器上。

其主要库是以C语言所写成，实现了基本的加密功能，实现了SSL与TLS协议。OpenSSL可以运行在OpenVMS、 Microsoft Windows以及绝大多数类Unix操作系统上（包括Solaris，Linux，Mac OS X与各种版本的开放源代码BSD操作系统）。它也提供了一个移植版本，可以在IBM i（OS/400）上运作。

虽然此软件是开放源代码的，但其许可书条款与GPL有冲突之处，故GPL软件使用OpenSSL时（如Wget）必须对OpenSSL给予例外。

libcrypto: 通用加密库
libssl: 实现了TLS/SSL等功能库
openssl: 多用途的命令行工具

## 3.1：安装：

```
[root@localhost ~]# yum install openssl -y
```

## 3.2：加密功能与加密算法

加密解密技术常用的功能及算法：

### 3.2.1：对称加密：

​	算法：DES, 3DES, AES, Blowfish, Twofish, RC6, CAST5
​	工具：gpg, openssl enc

加密文件：

```
[root@localhost ~]# openssl enc -des3 -a -salt -in /path/to/input_file -out /path/to/cipher_file

#举例
[root@localhost ~]# cat fstab

#
# /etc/fstab
# Created by anaconda on Tue Mar 26 15:05:38 2019
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                       xfs     defaults        0 0
UUID=2255d217-435b-4fe7-90a4-c3bba0413c54 /boot                   xfs     defaults        0 0
/dev/mapper/centos-data /data                   xfs     defaults        0 0
/dev/mapper/centos-swap swap                    swap    defaults        0 0

[root@localhost ~]# openssl enc -des3 -a -salt -in fstab -out fstab.cipher
enter des-ede3-cbc encryption password:
Verifying - enter des-ede3-cbc encryption password:

[root@localhost ~]# cat fstab.cipher 
U2FsdGVkX19RtsuE9ThBdbIldGa+BeyZUYyMcomLF078Hc6HrZfm2A6s6MInEcqT
xEMxuyug/y9cLSct+aHxlWey3KbiVrZHQQUTsVjVUYqQZ/ZZngW98bDgeJguGW1d
N3lq1IxHZSAqV8Mzq56QMpp90M4V8Gk5XVvJC+I2TGSaRca0oYnEjXrWkPG6/lFd
mHfFMg8FCm0HNIUVjpLt8E1IE103tZc/01COsPa4YwdCCS+loFa2AdVHb2bi8xhj
2nkDBCXp3nGpuVD3L0rqiNYBOvjxO+/ilmhgKgXlB71/ZGkFS26d6a1uKDpnombZ
vNzZssnIV+oGYuu755/CstDzCGtJTXevyZ8VcjOkDewrNXCpAHukAYdKI2sjD4Ru
ougU0yrqezJPx4iAZ8G4JzZQqu7dlr79sXvNpDUJiwAb3DFgTUzRltc0GXhQKYyT
ljZfnnIaTjd+SR2NAuqD1EkzM91b9TlMOXY2pVMmq3OuDz4fKoJy08trjK/fDont
xX+uac8WJeS/3kfR04BHByaMwOH1D4zBVa74hTUCGOAsJhVQAp9s9KjJSZwcz2x2
i2RkKE25A6isYlRSHpUUO0KmGWuxwbGF634WCyTFXj/R5D9Iur9fUmW16daav8DC
VzV+kb17Mwq6ziP+92Hrd5Z8xGyuYMSK6l2EyB3AXWs1kLLauvoPy2Y56GVS2BjJ
HK/V6TMLScrNSMKQQaLgMMER3XeSdWseshjhs1tJVHw=

```

解密文件：

```
openssl enc -d -des3 -a -salt -in /path/to/cipher_file -out /path/to/clear_file

#举例
[root@localhost ~]# openssl enc -d -des3 -a -salt -in fstab.cipher -out fstab.txt
enter des-ede3-cbc decryption password:

[root@localhost ~]# cat fstab.txt 

#
# /etc/fstab
# Created by anaconda on Tue Mar 26 15:05:38 2019
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                       xfs     defaults        0 0
UUID=2255d217-435b-4fe7-90a4-c3bba0413c54 /boot                   xfs     defaults        0 0
/dev/mapper/centos-data /data                   xfs     defaults        0 0
/dev/mapper/centos-swap swap                    swap    defaults        0 0
```

### 3.2.2：单向加密：

​	特性：
​		One-Way
​		Collision-free
​	算法：
​		md5: 128bits
​		sha1: 160bits
​		sha512: 512bits
​	工具：sha1sum, md5sum, cksum, openssl dgst

​	dgst用法：

​		openssl dgst [-md5|-md4|-md2|-sha1|-sha|-mdc2|-ripemd160|-dss1] [-out filename] /path/to/somefile

```
[root@localhost ~]# sha1sum fstab.txt 
9c01397d3126964e5995c575afdde8fc278cbdd3  fstab.txt

[root@localhost ~]# openssl dgst -sha1 fstab.txt 
SHA1(fstab.txt)= 9c01397d3126964e5995c575afdde8fc278cbdd3
```

### 3.2.3：MAC: 

消息摘要码，单向加密的延伸应用

应用：用于实现在网络通信中保证所传输的数据完整性；
机制：
	CBC-MAC
	HMAC：使用md5和sha1算法；

### 3.2.4：用户认证：

​	工具：passwd, openssl passwd

```
[root@localhost ~]# openssl passwd -1
Password: 
Verifying - Password: 
$1$6TuXuwYP$l7QuJMq9V6OqoeBkZI3Hy.

#使用相同的密码，因为salt不一致，所以生成的加密后的密码不会相同
[root@localhost ~]# openssl passwd -1
Password: 
Verifying - Password: 
$1$q/H999dF$.vLuc3ysdP20UCrYNJYXV1
```

公钥加密：公钥加密、私钥解密
	密钥对儿：
		公钥：pkey
		私钥：skey

​	算法：RSA, EIGamal
​	工具：gpg, openssl rsautl

数字签名：私钥加密、公钥解密
	算法：RSA, EIGamal, DSA

​	DSA: Digital Signature Algorithm
​	DSS: Digital Signature Standard

密钥交换：IKE
	算法：DH, 公钥加密
		Diffie-Hellman 

数字证书：
	证书格式：x509、pkcs
	x509格式：
		公钥和有效期限；
		持有者的个人合法身份信息；（主机名）
		证书的使用方式；
		CA的信息；
		CA的数字签名；

​	谁给CA发证：自签署证书

### 3.2.5：用openssl实现私有CA：

配置文件：/etc/pki/tls/openssl.cnf的默认配置

```
[root@localhost ~]# grep -v "^#" /etc/pki/tls/openssl.cnf | grep -v "^$"
HOME			= .
RANDFILE		= $ENV::HOME/.rnd
oid_section		= new_oids
[ new_oids ]
tsa_policy1 = 1.2.3.4.1
tsa_policy2 = 1.2.3.4.5.6
tsa_policy3 = 1.2.3.4.5.7
[ ca ]
default_ca	= CA_default		# The default ca section
[ CA_default ]
dir		= /etc/pki/CA		# Where everything is kept
certs		= $dir/certs		# Where the issued certs are kept
crl_dir		= $dir/crl		# Where the issued crl are kept
database	= $dir/index.txt	# database index file.
					# several ctificates with same subject.
new_certs_dir	= $dir/newcerts		# default place for new certs.
certificate	= $dir/cacert.pem 	# The CA certificate
serial		= $dir/serial 		# The current serial number
crlnumber	= $dir/crlnumber	# the current crl number
					# must be commented out to leave a V1 CRL
crl		= $dir/crl.pem 		# The current CRL
private_key	= $dir/private/cakey.pem# The private key
RANDFILE	= $dir/private/.rand	# private random number file
x509_extensions	= usr_cert		# The extentions to add to the cert
name_opt 	= ca_default		# Subject Name options
cert_opt 	= ca_default		# Certificate field options
default_days	= 365			# how long to certify for
default_crl_days= 30			# how long before next CRL
default_md	= sha256		# use SHA-256 by default
preserve	= no			# keep passed DN ordering
policy		= policy_match
[ policy_match ]
countryName		= match
stateOrProvinceName	= match
organizationName	= match
organizationalUnitName	= optional
commonName		= supplied
emailAddress		= optional
[ policy_anything ]
countryName		= optional
stateOrProvinceName	= optional
localityName		= optional
organizationName	= optional
organizationalUnitName	= optional
commonName		= supplied
emailAddress		= optional
[ req ]
default_bits		= 2048
default_md		= sha256
default_keyfile 	= privkey.pem
distinguished_name	= req_distinguished_name
attributes		= req_attributes
x509_extensions	= v3_ca	# The extentions to add to the self signed cert
string_mask = utf8only
[ req_distinguished_name ]
countryName			= Country Name (2 letter code)
countryName_default		= XX
countryName_min			= 2
countryName_max			= 2
stateOrProvinceName		= State or Province Name (full name)
localityName			= Locality Name (eg, city)
localityName_default		= Default City
0.organizationName		= Organization Name (eg, company)
0.organizationName_default	= Default Company Ltd
organizationalUnitName		= Organizational Unit Name (eg, section)
commonName			= Common Name (eg, your name or your server\'s hostname)
commonName_max			= 64
emailAddress			= Email Address
emailAddress_max		= 64
[ req_attributes ]
challengePassword		= A challenge password
challengePassword_min		= 4
challengePassword_max		= 20
unstructuredName		= An optional company name
[ usr_cert ]
basicConstraints=CA:FALSE
nsComment			= "OpenSSL Generated Certificate"
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid,issuer
[ v3_req ]
basicConstraints = CA:FALSE
keyUsage = nonRepudiation, digitalSignature, keyEncipherment
[ v3_ca ]
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid:always,issuer
basicConstraints = CA:true
[ crl_ext ]
authorityKeyIdentifier=keyid:always
[ proxy_cert_ext ]
basicConstraints=CA:FALSE
nsComment			= "OpenSSL Generated Certificate"
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid,issuer
proxyCertInfo=critical,language:id-ppl-anyLanguage,pathlen:3,policy:foo
[ tsa ]
default_tsa = tsa_config1	# the default TSA section
[ tsa_config1 ]
dir		= ./demoCA		# TSA root directory
serial		= $dir/tsaserial	# The current serial number (mandatory)
crypto_device	= builtin		# OpenSSL engine to use for signing
signer_cert	= $dir/tsacert.pem 	# The TSA signing certificate
					# (optional)
certs		= $dir/cacert.pem	# Certificate chain to include in reply
					# (optional)
signer_key	= $dir/private/tsakey.pem # The TSA private key (optional)
default_policy	= tsa_policy1		# Policy if request did not specify it
					# (optional)
other_policies	= tsa_policy2, tsa_policy3	# acceptable policies (optional)
digests		= sha1, sha256, sha384, sha512	# Acceptable message digests (mandatory)
accuracy	= secs:1, millisecs:500, microsecs:100	# (optional)
clock_precision_digits  = 0	# number of digits after dot. (optional)
ordering		= yes	# Is ordering defined for timestamps?
				# (optional, default: no)
tsa_name		= yes	# Must the TSA name be included in the reply?
				# (optional, default: no)
ess_cert_id_chain	= no	# Must the ESS cert id chain be included?
				# (optional, default: no)
```

CA server：

生成密钥对儿：

```
openssl genrsa [-out filename] [-passout arg] [-aes128] [-aes192] [-aes256] [-camellia128] [-camellia192] [-camellia256] [-des] [-des3] [-idea] [-f4] [-3] [-rand file(s)] [-engine id] [numbits]

[root@localhost ~]# cd /etc/pki/CA/

[root@localhost CA]# (umask 077; openssl genrsa -out private/cakey.pem 2048)
Generating RSA private key, 2048 bit long modulus
........................................................................+++
...........................+++
e is 65537 (0x10001)

[root@localhost CA]# ll private/
total 4
-rw------- 1 root root 1675 Mar 28 21:26 cakey.pem
```

查看密钥信息

```
[root@localhost CA]# openssl rsa -in private/cakey.pem -pubout -text
Private-Key: (2048 bit)
modulus:
    00:c2:c0:35:18:f2:12:be:c1:08:57:bb:ed:0e:33:
    4c:6f:dd:52:9d:e1:b6:3d:30:45:c7:28:5c:58:fe:
    ed:42:89:c2:8a:8e:5d:2b:a3:d8:00:06:1d:58:27:
    1e:d7:69:ce:93:c4:b8:50:54:d6:61:b5:54:10:c7:
    d1:9b:eb:47:b9:42:6c:a7:b8:16:b4:4a:87:1c:1c:
    d5:0b:db:9f:ec:24:cf:53:78:e0:85:a6:1a:e6:75:
    d2:2a:df:25:3c:52:70:c0:a8:9f:1f:d7:75:2b:8b:
    60:5d:13:1d:19:8f:10:5b:28:67:3b:2b:5d:1c:ab:
    16:3d:bd:34:c4:e5:67:e4:8d:29:49:03:53:fe:9e:
    b3:ba:02:51:87:4c:ef:d6:ac:58:d3:d5:81:c1:9a:
    71:f5:9e:ae:3f:b5:c9:81:73:9e:fc:10:07:89:0f:
    46:11:ea:ff:20:80:78:e7:99:15:cc:a0:60:c5:ff:
    05:9a:74:97:2f:57:a9:7a:de:07:69:3c:c6:f9:da:
    cf:c4:01:90:6e:26:e2:c4:40:ea:f7:54:6d:0a:46:
    98:9e:d2:39:09:be:91:79:1c:2f:04:76:1d:56:22:
    7e:cb:1a:73:e1:4a:c9:04:ab:1d:55:f8:0b:2c:72:
    6f:10:33:4b:cc:77:1a:66:80:ca:c6:88:63:6c:3b:
    3c:69
publicExponent: 65537 (0x10001)
privateExponent:
    6e:4e:6f:c4:b1:57:ec:b3:69:06:48:6a:fc:a0:11:
    ab:4a:b0:80:bc:57:7c:50:db:34:f2:a7:5b:c1:08:
    a1:a7:65:89:15:19:b0:9e:1c:b9:e6:bc:b8:31:70:
    46:92:a7:6f:0a:fd:5a:82:b2:ea:a4:c9:f8:b7:35:
    02:1b:41:84:ad:1a:dd:e4:66:f7:b7:3e:cf:ad:84:
    fe:32:67:62:59:77:14:d0:62:5b:50:b9:eb:ce:d0:
    3b:78:28:9f:02:12:db:0c:a7:14:61:f4:fb:b4:f5:
    df:c1:f3:b9:86:4e:bd:53:25:25:5e:6a:5e:d2:88:
    c6:6f:64:74:21:7b:b6:f7:3f:d2:3d:81:53:7b:e1:
    97:1b:bf:4e:57:b1:21:66:22:58:ad:b1:74:ad:15:
    e7:52:4a:a7:0a:bf:ae:3a:9d:27:a2:56:e6:6e:18:
    2c:e8:79:15:8c:7d:c5:fa:94:9d:8e:66:cd:50:19:
    06:8c:e1:8a:b6:04:7c:62:80:a4:ca:1b:2b:eb:68:
    61:f1:d3:91:c8:57:39:18:43:b1:b9:d2:7e:ab:70:
    c8:4a:82:ad:5d:1d:f6:ef:c9:74:07:a4:89:53:e5:
    64:0a:b8:8c:59:a2:bc:f7:56:80:86:02:57:ef:c3:
    43:a5:78:c0:9d:0c:34:6b:a9:03:88:6c:ac:ec:84:
    01
prime1:
    00:ef:f8:34:2c:01:03:a5:1d:bf:b0:c5:04:9f:fa:
    2e:fe:b1:03:d7:ca:63:09:04:b8:56:70:2c:1e:43:
    fd:99:4b:a9:70:15:59:5f:b1:7e:e7:76:02:a5:c9:
    be:28:67:60:c0:cd:c8:c8:81:f9:6c:16:dc:17:27:
    35:1c:f8:12:d0:23:be:f1:10:5c:23:1a:28:49:24:
    71:5d:a5:34:a2:b0:00:42:bb:0b:3b:4f:36:9e:88:
    1d:d2:4a:db:21:e6:03:bd:f1:09:1c:40:ec:ae:ed:
    0e:b7:4c:eb:f7:52:94:44:c7:24:f5:32:ca:45:61:
    8e:bb:18:a1:27:21:bc:94:73
prime2:
    00:cf:c2:b4:18:dd:d8:7b:f1:06:01:21:76:38:13:
    0f:6c:3d:a1:ec:d1:7d:13:2c:35:72:56:e4:c4:72:
    77:44:91:f2:f5:d8:c0:80:ac:55:c2:ef:4e:e7:bb:
    eb:f5:2c:82:af:3a:0a:47:17:56:41:e2:78:d1:89:
    ae:3b:9b:05:54:7a:41:b0:43:cb:e6:3d:bb:ab:45:
    00:20:3b:30:88:a3:8e:d5:f9:6f:f6:b8:fc:d6:1d:
    09:64:dd:3d:3d:87:1f:c1:59:4b:68:3d:5f:8a:ed:
    f1:02:56:f9:85:14:39:c8:d3:cf:3e:f1:cc:f6:e2:
    92:f6:31:f9:e1:e1:b6:d0:b3
exponent1:
    61:47:05:a7:3c:f6:04:4e:4d:f1:d3:bc:2f:39:d5:
    29:2c:0a:f9:c4:32:67:6a:81:6b:90:e7:61:91:80:
    51:4f:49:ad:85:0a:08:ef:c4:19:02:4e:ca:74:40:
    e0:d2:1f:77:a9:34:16:8c:11:d7:4e:61:04:05:c1:
    d3:2f:81:38:dc:f4:bf:76:39:53:85:7c:05:42:7f:
    ae:4d:95:4b:c9:fb:21:11:ff:d8:98:20:f5:1d:0a:
    b1:bf:e4:9b:16:4a:38:0a:68:fe:0a:17:a8:45:b2:
    ec:7d:83:3e:6c:3c:6c:b1:29:fb:38:0e:a5:17:8a:
    2b:b0:2d:4b:d6:b2:42:9f
exponent2:
    02:9d:87:d8:dc:4a:12:5d:59:5f:00:45:d5:b4:df:
    b7:d4:1a:ca:b9:64:62:ae:66:c9:d6:b0:30:8c:e4:
    55:77:a4:e0:55:67:2e:f7:7c:68:e4:cc:50:7c:7c:
    1e:ec:fd:84:29:2d:e1:f1:38:43:c2:0f:15:4c:2f:
    66:4e:b0:45:7c:f8:80:b8:89:c3:8f:39:62:91:8e:
    cb:d9:12:e6:55:70:c1:de:ce:fb:03:1f:23:ae:df:
    e4:66:28:94:32:b4:42:a1:d6:22:2c:cb:9c:1b:1c:
    b2:93:d7:b6:46:27:e8:10:4a:86:97:52:a6:34:f9:
    f6:a1:70:6c:d8:83:8d:a3
coefficient:
    00:a9:b0:57:ae:95:cb:da:9b:80:44:38:6c:d5:f2:
    80:ca:4a:49:02:f8:76:16:8a:cf:d6:8a:4a:e8:1d:
    ed:0b:17:44:23:d6:20:5f:2f:77:26:31:75:77:12:
    d6:1f:c4:19:a1:a3:69:02:84:24:0e:40:7b:a5:ac:
    95:e9:ca:60:98:8f:82:0d:ed:8f:90:5b:f9:06:09:
    e9:b2:72:cb:23:7e:ad:5c:2a:35:91:9a:49:f3:66:
    08:cc:f5:64:1e:06:e9:05:e0:31:97:5b:15:b4:75:
    7b:fa:d5:9a:09:a5:5f:f4:81:57:54:3e:9a:af:c3:
    31:c0:85:8f:b3:ee:77:ba:78
writing RSA key
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAwsA1GPISvsEIV7vtDjNM
b91SneG2PTBFxyhcWP7tQonCio5dK6PYAAYdWCce12nOk8S4UFTWYbVUEMfRm+tH
uUJsp7gWtEqHHBzVC9uf7CTPU3jghaYa5nXSKt8lPFJwwKifH9d1K4tgXRMdGY8Q
WyhnOytdHKsWPb00xOVn5I0pSQNT/p6zugJRh0zv1qxY09WBwZpx9Z6uP7XJgXOe
/BAHiQ9GEer/IIB455kVzKBgxf8FmnSXL1epet4HaTzG+drPxAGQbibixEDq91Rt
CkaYntI5Cb6ReRwvBHYdViJ+yxpz4UrJBKsdVfgLLHJvEDNLzHcaZoDKxohjbDs8
aQIDAQAB
-----END PUBLIC KEY-----
```

如果想查看公钥：

```
[root@localhost CA]# openssl rsa -in private/cakey.pem -pubout -text -noout
Private-Key: (2048 bit)
modulus:
    00:c2:c0:35:18:f2:12:be:c1:08:57:bb:ed:0e:33:
    4c:6f:dd:52:9d:e1:b6:3d:30:45:c7:28:5c:58:fe:
    ed:42:89:c2:8a:8e:5d:2b:a3:d8:00:06:1d:58:27:
    1e:d7:69:ce:93:c4:b8:50:54:d6:61:b5:54:10:c7:
    d1:9b:eb:47:b9:42:6c:a7:b8:16:b4:4a:87:1c:1c:
    d5:0b:db:9f:ec:24:cf:53:78:e0:85:a6:1a:e6:75:
    d2:2a:df:25:3c:52:70:c0:a8:9f:1f:d7:75:2b:8b:
    60:5d:13:1d:19:8f:10:5b:28:67:3b:2b:5d:1c:ab:
    16:3d:bd:34:c4:e5:67:e4:8d:29:49:03:53:fe:9e:
    b3:ba:02:51:87:4c:ef:d6:ac:58:d3:d5:81:c1:9a:
    71:f5:9e:ae:3f:b5:c9:81:73:9e:fc:10:07:89:0f:
    46:11:ea:ff:20:80:78:e7:99:15:cc:a0:60:c5:ff:
    05:9a:74:97:2f:57:a9:7a:de:07:69:3c:c6:f9:da:
    cf:c4:01:90:6e:26:e2:c4:40:ea:f7:54:6d:0a:46:
    98:9e:d2:39:09:be:91:79:1c:2f:04:76:1d:56:22:
    7e:cb:1a:73:e1:4a:c9:04:ab:1d:55:f8:0b:2c:72:
    6f:10:33:4b:cc:77:1a:66:80:ca:c6:88:63:6c:3b:
    3c:69
publicExponent: 65537 (0x10001)
privateExponent:
    6e:4e:6f:c4:b1:57:ec:b3:69:06:48:6a:fc:a0:11:
    ab:4a:b0:80:bc:57:7c:50:db:34:f2:a7:5b:c1:08:
    a1:a7:65:89:15:19:b0:9e:1c:b9:e6:bc:b8:31:70:
    46:92:a7:6f:0a:fd:5a:82:b2:ea:a4:c9:f8:b7:35:
    02:1b:41:84:ad:1a:dd:e4:66:f7:b7:3e:cf:ad:84:
    fe:32:67:62:59:77:14:d0:62:5b:50:b9:eb:ce:d0:
    3b:78:28:9f:02:12:db:0c:a7:14:61:f4:fb:b4:f5:
    df:c1:f3:b9:86:4e:bd:53:25:25:5e:6a:5e:d2:88:
    c6:6f:64:74:21:7b:b6:f7:3f:d2:3d:81:53:7b:e1:
    97:1b:bf:4e:57:b1:21:66:22:58:ad:b1:74:ad:15:
    e7:52:4a:a7:0a:bf:ae:3a:9d:27:a2:56:e6:6e:18:
    2c:e8:79:15:8c:7d:c5:fa:94:9d:8e:66:cd:50:19:
    06:8c:e1:8a:b6:04:7c:62:80:a4:ca:1b:2b:eb:68:
    61:f1:d3:91:c8:57:39:18:43:b1:b9:d2:7e:ab:70:
    c8:4a:82:ad:5d:1d:f6:ef:c9:74:07:a4:89:53:e5:
    64:0a:b8:8c:59:a2:bc:f7:56:80:86:02:57:ef:c3:
    43:a5:78:c0:9d:0c:34:6b:a9:03:88:6c:ac:ec:84:
    01
prime1:
    00:ef:f8:34:2c:01:03:a5:1d:bf:b0:c5:04:9f:fa:
    2e:fe:b1:03:d7:ca:63:09:04:b8:56:70:2c:1e:43:
    fd:99:4b:a9:70:15:59:5f:b1:7e:e7:76:02:a5:c9:
    be:28:67:60:c0:cd:c8:c8:81:f9:6c:16:dc:17:27:
    35:1c:f8:12:d0:23:be:f1:10:5c:23:1a:28:49:24:
    71:5d:a5:34:a2:b0:00:42:bb:0b:3b:4f:36:9e:88:
    1d:d2:4a:db:21:e6:03:bd:f1:09:1c:40:ec:ae:ed:
    0e:b7:4c:eb:f7:52:94:44:c7:24:f5:32:ca:45:61:
    8e:bb:18:a1:27:21:bc:94:73
prime2:
    00:cf:c2:b4:18:dd:d8:7b:f1:06:01:21:76:38:13:
    0f:6c:3d:a1:ec:d1:7d:13:2c:35:72:56:e4:c4:72:
    77:44:91:f2:f5:d8:c0:80:ac:55:c2:ef:4e:e7:bb:
    eb:f5:2c:82:af:3a:0a:47:17:56:41:e2:78:d1:89:
    ae:3b:9b:05:54:7a:41:b0:43:cb:e6:3d:bb:ab:45:
    00:20:3b:30:88:a3:8e:d5:f9:6f:f6:b8:fc:d6:1d:
    09:64:dd:3d:3d:87:1f:c1:59:4b:68:3d:5f:8a:ed:
    f1:02:56:f9:85:14:39:c8:d3:cf:3e:f1:cc:f6:e2:
    92:f6:31:f9:e1:e1:b6:d0:b3
exponent1:
    61:47:05:a7:3c:f6:04:4e:4d:f1:d3:bc:2f:39:d5:
    29:2c:0a:f9:c4:32:67:6a:81:6b:90:e7:61:91:80:
    51:4f:49:ad:85:0a:08:ef:c4:19:02:4e:ca:74:40:
    e0:d2:1f:77:a9:34:16:8c:11:d7:4e:61:04:05:c1:
    d3:2f:81:38:dc:f4:bf:76:39:53:85:7c:05:42:7f:
    ae:4d:95:4b:c9:fb:21:11:ff:d8:98:20:f5:1d:0a:
    b1:bf:e4:9b:16:4a:38:0a:68:fe:0a:17:a8:45:b2:
    ec:7d:83:3e:6c:3c:6c:b1:29:fb:38:0e:a5:17:8a:
    2b:b0:2d:4b:d6:b2:42:9f
exponent2:
    02:9d:87:d8:dc:4a:12:5d:59:5f:00:45:d5:b4:df:
    b7:d4:1a:ca:b9:64:62:ae:66:c9:d6:b0:30:8c:e4:
    55:77:a4:e0:55:67:2e:f7:7c:68:e4:cc:50:7c:7c:
    1e:ec:fd:84:29:2d:e1:f1:38:43:c2:0f:15:4c:2f:
    66:4e:b0:45:7c:f8:80:b8:89:c3:8f:39:62:91:8e:
    cb:d9:12:e6:55:70:c1:de:ce:fb:03:1f:23:ae:df:
    e4:66:28:94:32:b4:42:a1:d6:22:2c:cb:9c:1b:1c:
    b2:93:d7:b6:46:27:e8:10:4a:86:97:52:a6:34:f9:
    f6:a1:70:6c:d8:83:8d:a3
coefficient:
    00:a9:b0:57:ae:95:cb:da:9b:80:44:38:6c:d5:f2:
    80:ca:4a:49:02:f8:76:16:8a:cf:d6:8a:4a:e8:1d:
    ed:0b:17:44:23:d6:20:5f:2f:77:26:31:75:77:12:
    d6:1f:c4:19:a1:a3:69:02:84:24:0e:40:7b:a5:ac:
    95:e9:ca:60:98:8f:82:0d:ed:8f:90:5b:f9:06:09:
    e9:b2:72:cb:23:7e:ad:5c:2a:35:91:9a:49:f3:66:
    08:cc:f5:64:1e:06:e9:05:e0:31:97:5b:15:b4:75:
    7b:fa:d5:9a:09:a5:5f:f4:81:57:54:3e:9a:af:c3:
    31:c0:85:8f:b3:ee:77:ba:78

```

生成自签证书：

```
[root@localhost CA]# openssl req -new -x509 -key private/cakey.pem -out cacert.pem -days 3655
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:CN
State or Province Name (full name) []:BeiJing
Locality Name (eg, city) [Default City]:BeiJing
Organization Name (eg, company) [Default Company Ltd]:Ma 
Organizational Unit Name (eg, section) []:DevOps
Common Name (eg, your name or your server's hostname) []:ca.ma.com
Email Address []:caadmin@gmail.com
```

创建需要的文件：

```
[root@localhost CA]# touch index.txt serial crlnumber
```

Web Server用openssl实现证书申请：

在主机上生成密钥，保存至应用此证书的服务的配置文件目录下, 例如：

生成证书签署请求：

```
[root@localhost ~]# yum install httpd -y

[root@localhost ~]# mkdir -v /etc/httpd/ssl
mkdir: created directory ‘/etc/httpd/ssl’

[root@localhost ~]# cd /etc/httpd/ssl/

[root@localhost ssl]# (umask 077; openssl genrsa -out httpd.key 1024)
Generating RSA private key, 1024 bit long modulus
.....................++++++
..............................++++++
e is 65537 (0x10001)

[root@localhost ssl]# ls
httpd.key

[root@localhost ssl]# openssl req -new -key httpd.key -out httpd.csr
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:CN
State or Province Name (full name) []:BeiJing
Locality Name (eg, city) [Default City]:BeiJing
Organization Name (eg, company) [Default Company Ltd]:Ma
Organizational Unit Name (eg, section) []:DevOps
Common Name (eg, your name or your server's hostname) []:www.ma.com
Email Address []:webmaster@gmail.com

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
```



将请求文件发往CA；

```
[root@localhost ssl]# scp httpd.csr 192.168.112.2:/tmp/
```

CA签署证书：

```
openssl ca -in /path/to/somefile.csr -out /path/to/somefile.crt -days DAYS

[root@localhost CA]# openssl ca -in /tmp/httpd.csr -out /tmp/httpd.crt -days 3655
Using configuration from /etc/pki/tls/openssl.cnf
Check that the request matches the signature
Signature ok
Certificate Details:
        Serial Number: 1 (0x1)
        Validity
            Not Before: Mar 28 14:00:38 2019 GMT
            Not After : Mar 30 14:00:38 2029 GMT
        Subject:
            countryName               = CN
            stateOrProvinceName       = BeiJing
            organizationName          = Ma
            organizationalUnitName    = DevOps
            commonName                = www.ma.com
            emailAddress              = webmaster@gmail.com
        X509v3 extensions:
            X509v3 Basic Constraints: 
                CA:FALSE
            Netscape Comment: 
                OpenSSL Generated Certificate
            X509v3 Subject Key Identifier: 
                BA:1C:26:7E:96:2D:68:97:18:BF:BD:39:B3:14:7D:1B:81:F8:90:62
            X509v3 Authority Key Identifier: 
                keyid:20:1F:C4:D6:EF:29:E3:2E:C5:5E:B4:C6:DF:B5:7E:B4:53:77:F0:58

Certificate is to be certified until Mar 30 14:00:38 2029 GMT (3655 days)
Sign the certificate? [y/n]:y


1 out of 1 certificate requests certified, commit? [y/n]y
Write out database with 1 new entries
Data Base Updated
```

将证书传回请求者

```
[root@localhost CA]# scp /tmp/httpd.crt 192.168.112.3:/etc/httpd/ssl/
```

吊销证书：

```
openssl ca -revoke /path/to/somefile.crt

[root@localhost ~]# openssl ca -revoke /tmp/httpd.crt 
Using configuration from /etc/pki/tls/openssl.cnf
Revoking Certificate 01.
Data Base Updated
```

标准命令：enc, dgst, genrsa, rsautl, req, ca, rsa, passwd

​	speed: 基准性能测试工具 
​	version：版本查看
​	s_client：SSL/TLS client program
rand: 生成伪随机数

```
openssl rand -hex

[root@localhost ~]# openssl rand -base64 12
xY/cVbQDzxAqFE0T
```

openssl: 私有CA
	证书格式：
		公钥及有效期限；
		持有者合法信息；（用户或主机）
		证书使用方式；
		CA的信息；
		CA的数字签名；

​	对称加密：数据私密性
​	公钥加密：密钥交换（对方的公钥）、数据加密（对方的公钥）、身份认证（自己的私钥）
​	单向加密：hash码， One-Way hash

# 四、ssl:

​	传输层安全性协议（英语：Transport Layer Security，缩写作 TLS），及其前身安全套接层（Secure Sockets Layer，缩写作SSL）是一种安全协议，目的是为互联网通信提供安全及数据完整性保障。网景公司（Netscape）在1994年推出首版网页浏览器，网景导航者时，推出HTTPS协议，以SSL进行加密，这是SSL的起源。IETF将SSL进行标准化，1999年公布第一版TLS标准文件。随后又公布RFC 5246 （2008年8月）与 RFC 6176（2011年3月）。在浏览器、电子邮件、即时通信、VoIP、网络传真等应用程序中，广泛支持这个协议。主要的网站，如Google、Facebook等也以这个协议来创建安全连线，发送数据。当前已成为互联网上保密通信的工业标准。
​	SSL包含记录层（Record Layer）和传输层，记录层协议确定传输层数据的封装格式。传输层安全协议使用X.509认证，之后利用非对称加密演算来对通信方做身份认证，之后交换对称密钥作为会谈密钥（Session key）。这个会谈密钥是用来将通信两方交换的数据做加密，保证两个应用间通信的保密性和可靠性，使客户与服务器应用之间的通信不被攻击者窃听。

TLS: Transport Layer Security
	TLSv1

​	http --> https
​	ldap --> ldaps
​	ftp --> ftps
​	smtp --> smtps
​	pop3 --> pop3s
​	imap --> imaps

openssl补充材料：

​	openssl中有如下后缀名的文件
​	.key格式：私有的密钥
​	.crt格式：证书文件，certificate的缩写
​	.csr格式：证书签名请求（证书请求文件），含有公钥信息，certificate signing request的缩写
​	.crl格式：证书吊销列表，Certificate Revocation List的缩写
​	.pem格式：用于导出，导入证书时候的证书的格式，有证书开头，结尾的格式
​	 
​	常用证书协议
​	x509v3: IETF的证书标准
​	x.500:目录的标准
​	SCEP:  简单证书申请协议，用http来进行申请，数据有PKCS#7封装，数据其实格式也是PKCS#10的
​	PKCS#7:  是封装数据的标准，可以放置证书和一些请求信息
​	PKCS#10:  用于离线证书申请的证书申请的数据格式，注意数据包是使用PKCS#7封装这个数据
​	PKCS#12:  用于一个单一文件中交换公共和私有对象，就是公钥，私钥和证书，这些信息进行打包，加密放在存储目录中，CISCO放在NVRAM中，用户可以导出，以防证书服务器挂掉可以进行相应恢复。思科是.p12,微软是.pfx

# 五、telnet服务：

Telnet协议是一种应用层协议，使用于互联网及局域网中，使用虚拟终端机的形式，提供双向、以文字字符串为主的命令行接口交互功能。属于TCP/IP协议族的其中之一，是Internet远程登录服务的标准协议和主要方式，常用于服务器的远程控制，可供用户在本地主机运行远程主机上的工作。
Telnet在1969年开发出来，在 RFC 15 定义， RFC 854 定义了扩展。互联网工程任务组（IETF），在STD 8中，将其加以标准化，是最早形成的互联网协议之一。

用户首先在计算机运行Telnet程序，连线至目的地服务器，然后输入账号和密码以验证身份。用户可以在本地主机输入命令，然后让已连接的远程主机运行，就像直接在对方的控制台上输入一样。
传统Telnet会话所传输的数据并未加密，账号和密码等敏感数据容易会被窃听，因此很多服务器都会屏蔽Telnet服务，改用更安全的SSH。

```
[root@localhost ~]# yum install telnet -y

[root@localhost ~]# rpm -ql telnet-server 
/usr/lib/systemd/system/telnet.socket
/usr/lib/systemd/system/telnet@.service
/usr/sbin/in.telnetd
/usr/share/man/man5/issue.net.5.gz
/usr/share/man/man8/in.telnetd.8.gz
/usr/share/man/man8/telnetd.8.gz
```

# 七、SSH：Secure Shell

Secure Shell（安全外壳协议，简称SSH）是一种加密的网络传输协议，可在不安全的网络中为网络服务提供安全的传输环境。SSH通过在网络中创建安全隧道来实现SSH客户端与服务器之间的连接。虽然任何网络服务都可以通过SSH实现安全传输，SSH最常见的用途是远程登录系统，人们通常利用SSH来传输命令行界面和远程执行命令。使用频率最高的场合类Unix系统，但是Windows操作系统也能有限度地使用SSH。2015年，微软宣布将在未来的操作系统中提供原生SSH协议支持。
在设计上，SSH是Telnet和非安全shell的替代品。Telnet和Berkeley rlogin、rsh、rexec等协议采用明文传输，使用不可靠的密码，容易遭到监听、嗅探和中间人攻击。SSH旨在保证非安全网络环境（例如互联网）中信息加密完整可靠。
不过，SSH也被指出有被嗅探甚至解密的漏洞。早在2011年，中国的互联网审查机构已经有能力针对SSH连线的刺探及干扰。而后爱德华·斯诺登泄露的文件也指出，美国国家安全局有时能够把SSH协议传输的信息解密出来，从而读出SSH会话的传输内容。2017年7月6日，非营利组织维基解密确认美国中央情报局已经开发出能够在Windows或Linux操作系统中窃取SSH会话的工具。

架构图

![1553860947242](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553860947242.png)

## 7.1：Windows客户端：

xmanager（xshell）、SecureCRT、Putty、sshshellclient

```
[root@localhost ~]# rpm -ql openssh-clients 
/etc/ssh/ssh_config
/usr/bin/scp
/usr/bin/sftp
/usr/bin/slogin
/usr/bin/ssh
/usr/bin/ssh-add
/usr/bin/ssh-agent
/usr/bin/ssh-copy-id
/usr/bin/ssh-keyscan

[root@localhost ~]# rpm -ql openssh-server 
/etc/pam.d/sshd
/etc/ssh/sshd_config
/etc/sysconfig/sshd
```

## 7.2：客户端组件：

### 7.2.1：ssh

ssh加密方式是client与server基于双方主机协商选择最安全的MAC实现机制；基于RSA与DSA实现身份认证；客户通过检查服务端的主机密钥来判定是否与其进一步通信。

基于口令：在登录的过程中，直接输入密码

	#ssh username@HOST [COMMAND]
	[root@localhost ~]# ssh -l root 192.168.112.2
	root@192.168.112.2's password: 
	Last login: Fri Mar 29 20:08:32 2019 from 192.168.112.3
	
	[root@localhost ~]# ssh 192.168.112.2
	root@192.168.112.2's password: 
	Last login: Fri Mar 29 20:09:47 2019 from 192.168.112.3
	
	[root@localhost ~]# ssh 192.168.112.2 -p 22
	root@192.168.112.2's password: 
	Last login: Fri Mar 29 20:10:24 2019 from 192.168.112.3
	
	[root@localhost ~]# ssh root@192.168.112.2 'echo $UID,$HOSTNAME'
	root@192.168.112.2's password: 
	0,localhost.main
### 7.2.2：使用公钥认证机制

基于密钥：先生成一对密钥，把公钥放到服务器上，登录的时候用私钥直接登录即可

1.制作密钥对

首先在服务器上制作密钥对。首先用密码登录到你打算使用密钥登录的账户，然后执行以下命令：

```
[root@host ~]$ ssh-keygen  <== 建立密钥对
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): <== 按 Enter
Created directory '/root/.ssh'.
Enter passphrase (empty for no passphrase): <== 输入密钥锁码，或直接按 Enter 留空
Enter same passphrase again: <== 再输入一遍密钥锁码
Your identification has been saved in /root/.ssh/id_rsa. <== 私钥
Your public key has been saved in /root/.ssh/id_rsa.pub. <== 公钥
The key fingerprint is:
0f:d3:e7:1a:1c:bd:5c:03:f1:19:f1:22:df:9b:cc:08 root@host
```

密钥锁码在使用私钥时必须输入，这样就可以保护私钥不被盗用。当然，也可以留空，实现无密码登录。

现在，在 root 用户的家目录中生成了一个 .ssh 的隐藏目录，内含两个密钥文件。id_rsa 为私钥，id_rsa.pub 为公钥。

2.在服务器上安装公钥

键入以下命令，在服务器上安装公钥：

```
[root@host ~]$ cd .ssh
[root@host .ssh]$ cat id_rsa.pub >> authorized_keys
```

如此便完成了公钥的安装。为了确保连接成功，请保证以下文件权限正确：

```
[root@host .ssh]$ chmod 600 authorized_keys
[root@host .ssh]$ chmod 700 ~/.ssh
```

3.设置 SSH，打开密钥登录功能

编辑 /etc/ssh/sshd_config 文件，进行如下设置：

```
RSAAuthentication yes
PubkeyAuthentication yes
```

另外，请留意 root 用户能否通过 SSH 登录：

```
PermitRootLogin yes
```

当你完成全部设置，并以密钥方式登录成功后，再禁用密码登录：

```
PasswordAuthentication no
```

最后，重启 SSH 服务：

```
[root@host .ssh]$ service sshd restart
```

4.将私钥下载到客户端，然后点击Tools -->User Key Manager... 

![1553865997202](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553865997202.png)



![1553866219850](C:\Users\10096\AppData\Roaming\Typora\typora-user-images\1553866219850.png)

### 7.2.3：其他工具

scp：类cp命令

```
#scp SR1 SR2 ... DEST
#分类：
#源文件在本机，目标在远程
#scp /path/TO/SOMEFILE ... Username@HOST：/PATH/TO/SOMEFILE


#源文件在远程，目标在本机
#scp USERNAME@HOST：/PATH/TO/SOMEFILE /PATH/TO/SOMEFILE
scp -r ：#源文件目录时使用，以实现递归复制
scp -p：保留源文件的复制及修改时间戳，以及权限
scp -q：静默模式
scp -P PORT：指定服务端端口
```

sftp：是基于ssh的ftp协议

``` 
[root@localhost ~]# tail /etc/ssh/sshd_config 

# override default of no subsystems
Subsystem	sftp	/usr/libexec/openssh/sftp-server #启用了这项就标明支出sftp

# Example of overriding settings on a per-user basis
#Match User anoncvs
#	X11Forwarding no
#	AllowTcpForwarding no
#	PermitTTY no
#	ForceCommand cvs server

#sftp [username@] [HOST]
```

### 7.2.4：Server端：

```
[root@localhost ~]# cat /etc/ssh/sshd_config 
#	$OpenBSD: sshd_config,v 1.100 2016/08/15 12:32:04 naddy Exp $

# This is the sshd server system-wide configuration file.  See
# sshd_config(5) for more information.

# This sshd was compiled with PATH=/usr/local/bin:/usr/bin

# The strategy used for options in the default sshd_config shipped with
# OpenSSH is to specify options with their default value where
# possible, but leave them commented.  Uncommented options override the
# default value.

# If you want to change the port on a SELinux system, you have to tell
# SELinux about this change.
# semanage port -a -t ssh_port_t -p tcp #PORTNUMBER
#
#Port 22 #指定的Port号
#AddressFamily any
#ListenAddress 0.0.0.0 #当前主机配置的IP地址
#ListenAddress ::

#指定主机密钥
HostKey /etc/ssh/ssh_host_rsa_key 
#HostKey /etc/ssh/ssh_host_dsa_key
HostKey /etc/ssh/ssh_host_ecdsa_key
HostKey /etc/ssh/ssh_host_ed25519_key

# Ciphers and keying
#RekeyLimit default none

# Logging
#SyslogFacility AUTH
SyslogFacility AUTHPRIV #记录日志
#LogLevel INFO #日志级别

# Authentication:

#LoginGraceTime 2m
#PermitRootLogin yes #是否允许管理员登录
#StrictModes yes #严格模式
#MaxAuthTries 6 #最大认证尝试
#MaxSessions 10 #最多允许建立会话

#PubkeyAuthentication yes #是否允许公钥认证

# The default is to check both .ssh/authorized_keys and .ssh/authorized_keys2
# but this is overridden so installations will only check .ssh/authorized_keys
AuthorizedKeysFile	.ssh/authorized_keys

#AuthorizedPrincipalsFile none

#AuthorizedKeysCommand none
#AuthorizedKeysCommandUser nobody

# For this to work you will also need host keys in /etc/ssh/ssh_known_hosts
#HostbasedAuthentication no #是否允许启用远程主机的认证
# Change to yes if you don't trust ~/.ssh/known_hosts for
# HostbasedAuthentication
#IgnoreUserKnownHosts no #忽略用户已知的主机
# Don't read the user's ~/.rhosts and ~/.shosts files
#IgnoreRhosts yes

# To disable tunneled clear text passwords, change to no here!
PasswordAuthentication yes #是否允许用户输入账号、密码方式登录
#PermitEmptyPasswords no #是否允许空密码登录
PasswordAuthentication yes #是否允许挑战式握手认证协议

# Change to no to disable s/key passwords
#ChallengeResponseAuthentication yes
ChallengeResponseAuthentication no

#kerberos认证相关：做统一的认证登录，令牌认证机制
# Kerberos options
#KerberosAuthentication no
#KerberosOrLocalPasswd yes
#KerberosTicketCleanup yes
#KerberosGetAFSToken no
#KerberosUseKuserok yes

#基于编程接口的认证机制
# GSSAPI options
GSSAPIAuthentication no 
GSSAPICleanupCredentials no #是否清除认证过程
#GSSAPIStrictAcceptorCheck yes
#GSSAPIKeyExchange no
#GSSAPIEnablek5users no

# Set this to 'yes' to enable PAM authentication, account processing,
# and session processing. If this is enabled, PAM authentication will
# be allowed through the ChallengeResponseAuthentication and
# PasswordAuthentication.  Depending on your PAM configuration,
# PAM authentication via ChallengeResponseAuthentication may bypass
# the setting of "PermitRootLogin without-password".
# If you just want the PAM account and session checks to run without
# PAM authentication, then enable this but set PasswordAuthentication
# and ChallengeResponseAuthentication to 'no'.
# WARNING: 'UsePAM no' is not supported in Red Hat Enterprise Linux and may cause several
# problems.
UsePAM yes #是否使用pam认证

#AllowAgentForwarding yes #是否转发图形窗口
#AllowTcpForwarding yes
#GatewayPorts no
X11Forwarding yes
#X11DisplayOffset 10
#X11UseLocalhost yes
#PermitTTY yes
#PrintMotd yes
#PrintLastLog yes
#TCPKeepAlive yes
#UseLogin no
#UsePrivilegeSeparation sandbox
#PermitUserEnvironment no
#Compression delayed
#ClientAliveInterval 0
#ClientAliveCountMax 3
#ShowPatchLevel no
UseDNS no
#PidFile /var/run/sshd.pid
#MaxStartups 10:30:100
#PermitTunnel no
#ChrootDirectory none
#VersionAddendum none

# no default banner path
#Banner none

# Accept locale-related environment variables
AcceptEnv LANG LC_CTYPE LC_NUMERIC LC_TIME LC_COLLATE LC_MONETARY LC_MESSAGES
AcceptEnv LC_PAPER LC_NAME LC_ADDRESS LC_TELEPHONE LC_MEASUREMENT
AcceptEnv LC_IDENTIFICATION LC_ALL LANGUAGE
AcceptEnv XMODIFIERS

# override default of no subsystems
Subsystem	sftp	/usr/libexec/openssh/sftp-server

# Example of overriding settings on a per-user basis
#Match User anoncvs
#	X11Forwarding no
#	AllowTcpForwarding no
#	PermitTTY no
#	ForceCommand cvs server
```

重点：

```
ListernAddress 当前主机的监听地址
Protocol 协议版本
# HostKeys for protocol version 2
#HostKey /etc/ssh/ssh_host_rsa_key
#HostKey /etc/ssh/ssh_host_dsa_key
验证主机的公钥
#KeyRegenerationInterval 1h  密钥的使用时间
#ServerKeyBits 1024 密钥长度
#LogLevel INFO 日志级别
#LoginGraceTime 2m 登陆宽限期
#PermitRootLogin yes root是否可以登录
#StrictModes yes 严格模式，家目录 属主 属组
#MaxAuthTries 6 最多可以承受的次数
#MaxSessions 10
最大的连接数
PasswordAuthentication yes 是否使用用户名和密码的方式
Kerberos集中认证管理
X11Forwarding yes 是否转发图形窗口
AllowUsers user1 user2
用户白名单
Allowgroups 组白名单
DenyUser 用户黑名单
```

## 7.3：使用ssh的最佳实践

1）only use ssh protocol 2
2）limit users SSh access
	Allowusers root vivek jerry
	白名单
	denyUser saroj anjali foo
	黑名单
3）configure  Idle Log out timeout Interval 配置空闲超时长。
	ClientAliveCountMax 300
	ClientAliveInterval  0
4）Firwall SSH Port # 22
	使用iptables设置ssh服务安全访问策略
5）change ssh port and Limit IP binding
	port 300
	Listaddress 192.168.1.5
	ListenAddress 202.54.1.5
	勿使用默认22端口
6）use Strong SSH passwords and passphrase
	genpasswd（）{
		local 1=$1
			[ "$1" == "" ]&& 1=20
			tr -dc A-Za-z0-9_</dev/urandom | head -c ${1} | xargs
	}
使用足够长足够复杂的密码，且定期更换。
7）use Public key Based AUthentication #使用公钥认证。
8）disable Empty password
9）thwart ssh crakers （Brute  Force attack）
google：ssh best practice
10）rate-limit incoming port # 22 COnnections #限制ssh访问频度
11）use log analyzer #记录好日志，经常做日志分析。

## 7.4：用户登录信息获取

/var/log/wtmp：用户成功登录的日志信息；last

/vat/log/btmp：用户登录尝试失败的日志信息：lastb

lastlog：每个用户最近一次成功登录的信息；


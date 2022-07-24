# ryzen3600-R9nano-Hackintosh
## 前提背景
在升级主力机之后有CPU和显卡的闲置，一直想要重新再安装一台黑苹果。上一次的黑苹果在主板升级为B550后一直没能正确驱动，保留在西数蓝盘中，也想尝试一下能不能直接通过修改EFI启动硬盘中的10.15.6.
## 硬件摘要
自从苹果出了M1后感觉黑苹果的热度确实有明显的下降，很多主流的教程更新于2019-2020年，虽然B站上也有不少今年的更新教程，但是从安装原理和方式上和两三年前的并没有特别大的区别，总体上趋于更加简单化，这也带来了一个很直接的问题，就是出现问题后往往还是要去网上翻看一些更老的教程才能大概知道出问题的地方，总体上还是相当耗费时间和精力。作为一个第三次安装黑苹果的半瓶子用户，这次也耗费了差不多一天半的时间，重新在物理机和虚拟机上各安装了一下黑苹果，体验了一下不同的感受，我相信很多折腾黑苹果的老玩家都是手里有一些闲置的硬件，像我自己也有两台白苹果，就是想着要折腾一下，因此我选用的配件是:
- CPU：Ryzen 3600
- 主板: MSI B450I GAMING PLUS AC
- 硬盘: WD500G SATA; Intel Optane 900p
- 内存: 枭鲸 3200mHz 2*16G
- GPU: XFX r9nano
- 网卡：板载螃蟹8111H 和 intel的无线网卡（因为不准备使用无线的相关功能，有需求的可以更换，在实体机黑苹果的条件下蓝牙可以用，wifi勉强凑合bug超多不太推荐，虚拟机下没试过怕直接把虚拟机本体弄趴下了）
### 老配件带来的问题
这套配件主要有两个比较麻烦的问题，1.使用的是Ryzen CPU相对来说安装麻烦一点，出BUG的可能性更加高一些，网上现成的EFI也少一些 2. 使用的是980同时代的R9nano,按照网上各路大神给出的指南，这张显卡在优美胜地之后应该是一直免驱动的，经过实测在10.13-12之间的MACOSX都是可以原生支持的。2022年黑苹果的主流引导方式还是Opencore和clover，前者明显发展的会更加好一些，主流的教程也一般都是基于前者，从官方的维护文档的可读性来说也明显前者更强，因此本次的安装基本上集中于Opencore。

主要参考教程
1.  https://www.bilibili.com/video/BV1uq4y1g7ui/?spm_id_from=333.788.recommend_more_video.  司波图，讲的很仔细的UP主，也确实帮助避了很多的坑，后面使用的虚拟机安装基本参照他的教程
2. https://www.nicksherlock.com/2021/10/installing-macos-12-monterey-on-proxmox-7/
3. OSX-KVM Github项目：https://github.com/thenickdude/OSX-KVM

以下为基于实体机的黑苹果安装参考教程
1. 微信公众号：黑果小兵的部落阁；独行秀才黑苹果安装维护
2. OC官方文档:https://dortania.github.io/OpenCore-Install-Guide/

有需要使用无线相关功能更换免驱网卡的可以看这篇选购指南
【黑苹果无线网卡到底怎么选（Intel 因特尔or BCM博通）？以及如何驱动和优化建议-哔哩哔哩】 https://b23.tv/bxVSkHp

## 省流版本
网络上各种关于不同配件的直接安装黑苹果的教程很多，基本安装过程也都比较仔细，大家可以看前面的几个链接，主要方法都大同小异，我主要列举一下自己踩过坑的一些点，希望对安装过程中出现问题的朋友能够有一些帮助。

大家初次安装的话尽量选择和自己配置接近的，本人主要都是安装AMD黑苹果，按照我的经验AMD的系列EFI通用性较强，A320 B450 X470 X570的EFI基本上可以通用（B550好像又要不一样一点，我实测A320M和B450I在其它配置不变的情况下可以用同一个EFI顺利引导且不用重新安装系统，但是B550就不行），理论上的配置没有很大的区别，在Opencore的早期版本中（大多数Github上的10.15及之前的EFI都更新于2020年及之前）甚至不需要再conplist中编辑AMD具体的核心数目，只需要打个patch就可以，所以基本上八核六核的amd的efi可以通用。但是相信大家在安装的过程中会发现事情并没有这么简单，下面就主要总结一下踩坑经验。
1. **关于WIFI蓝牙**,这个首先建立在能够正常进入系统之后，可以在进入系统之后再进行配置，一般使用博通的免驱网卡都没有很大的问题，我试过Intel AX200可以正常使用蓝牙但是因为wifi无法驱动不能使用Airdrop和carside,所以建议在挑选EFI的时候尽量选择网卡和自己接近的或者是免驱网卡的EFI，虽然这中间的机理比较复杂，我也并没有完全了解，但是实测是加载了很多kexts的版本的OC直接拿来用都驱动不起来，卡代码报错概率极大。
2. 安装盘的制作，我试过两种方式
- 使用etcher配合黑果小兵的双EFI镜像制作启动盘，这里不知道是因为我的水平比较菜还是什么原因，etcher这个软件首先就很迷，普通的USB 3.0写入大约12G的系统镜像大约需要30-40min，中间经常会报错（如果连续报错两次，建议换一个镜像文件再写入，有可能是镜像文件出错，这里有点玄学，有些时候MD5检验是通过的，但是写入的时候就是会报错，建议直接换一个镜像，一般小版本内的EFI都是通用的），然后如果是AMD的话自带的OC是不能用的（也可能是我比较菜不会用），从2009年的10.14到现在的12虽然基于AMD的conplist看起来越来越个性化，但是实测不能引导进入安装界面。
- 使用gib等软件制作原装镜像后加入OpenCORE，相对而言操作复杂一些，网上有很多教程教配置Opencore，一般来说有一定基础的话几个小时可以学会，但是这里面有一些暗坑，还是建议直接用现成的EFI进行一定的修改，先确保可以进入系统之后再进行深入定制，所以我是在黑果小兵的镜像中替换了EFI文件后安装了10.15
3. **关于AMD的Patch补丁**,这个现在已经基本成熟，常见的软件一般没有什么问题，如果出现在使用过程中的内核报错什么偶偶的还是会有，出现频率很高的还是要考虑一下GPU驱动（黑苹果玄学BUG很多）
4. **核心点**，网上很多教程都说AMD免驱显卡建议配合使用Whatevergreen避免出现启动后黑屏的状况，一般的描述是RX470（仿冒）及RX580及之后的显卡实践的比较多，经过实测R9nano加载Whatevergreen之后可以被正常识别为R9Fury 4G,但是会出现卡顿甚至卡死的状况，出现的频率很高，一般在打开新安装软件的时候很容易出现，一开始我还以为是软件的不兼容，后面发现覆盖的范围很广，而且删除这个插件之后就可以顺利运行。  <font color="red">{R9 nano免驱不要加载Whatevergreen，虽然这样型号会识别成X 4g但是实测各项功能正常且可以解决卡死问题}</font>
5. <b><font color="red">关于使用体验和优化</font></b>  
- KVM虚拟机模式下黑苹果的优点：安装和调试比较简单，只要显卡支持免驱，对于CPU基本上没有特别大的要求，安装过程傻瓜化，修改EFI只需要在网页上执行重启命令。众所周知在实体机上安装黑苹果因为EFI无法进入系统后没有直接重启需要按电源，令人抓狂。 可以利用PVE进行快速的备份，比较适合小白刚上手调整EFI，出了错可以马上回滚，甚至可以复制好几个相同的MACosx一旦出错后调用另一个的磁盘启动，反正就是很方面就可以恢复到出错之前的状态。
- KVM虚拟机模式下黑苹果的缺点: 优点很明显，缺点也非常明显。首先因为中间存在一层虚拟机，显卡直通还是硬件直通中比较简单的操作，声卡和网卡的直通操作起来还是比较抓狂的，而且可能会引起虚拟主机的崩溃，这个我没有实操过，但是看网上的各种教程一般都说不行，所以装完黑苹果之后WIFI 蓝牙还有声音都无法使用，因为本身的使用背景是基于有线网条件下的使用，也没有配置免驱网卡，所以前两条还好，USB直通比较简单，可以淘宝几块钱一个的USB声卡直通后解决没有声音的问题。
- **KVM虚拟机使用过程中的一些小tips**:这个我在网上的各种教程中看到的比较少，因为我是主力机（windows)和黑苹果两台机器同时使用的，如果直接安装的话需要准备两套键鼠，或者使用KVM键鼠切换（不能同时使用），其实可以在Windows下利用PVE的网络管理页面进入虚拟黑苹果（显示输出选择VMWARE兼容），这个在网上UP主的教程中建议调成none，但实测保留VMware兼容的输出模式在现在的使用过程中不会引起崩溃等问题，这样虽然输出是黑屏但是可以通过主力机输出黑苹果的声音，并且可以直接使用主力机的键鼠输入（当然一般这么使用的前提最好是要有双显示器，或者显示器要支持多端输入的切换）。这样在桌面上可以通过一套键鼠完成对两台设备的控制，还是相当不错（搭配双屏幕可以实现一个屏幕一个鼠标共享一个键盘的操作，个人觉得这样还是比较爽的）。
- **小结**：个人感觉虚拟化的黑苹果因为在启动模式和交互模式上的一些局限（比如如果要在PVE中同时安装黑苹果和WINDOWS的话，两者的文件系统都是基于虚拟机下的虚拟磁盘后模拟出来的NTFS和HFS+,因此没有办法进行直接的文件交换，而在直接安装黑苹果分方式中可以通过安装一个软件就可以解决两个系统盘直接文件读取的功能，所以如果是在主力机下加一块硬盘体验黑苹果的话推荐直接安装，如果是已经有主力机或是捡垃圾又凑了一台想体验一下的话可以试一下虚拟机安装黑苹果。

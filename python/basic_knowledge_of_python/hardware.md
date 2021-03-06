#  计算机硬件

##  底层硬件、操作系统、用户程序（软件）
- 操作系统Operating System（OS）是管理和控制计算机硬件和软件资源的计算机程序，是直接运行在裸机上最基本的系统软件，任何其他软件都必须在操作系统的支持下才能运行。
- 软件必须运行在操作系统之上
	- 如果没有操作系统，那么程序员就必须掌握如何操作硬件的所有具体细节，严重影响了开发效率。
	- 操作系统运行于硬件之上，用来控制硬件。
	- 在开发的时候，只需要调用操作系统为应用程序提供的方便的抽象接口即可。

##  一套完整的计算机系统
- 应用软件（QQ、暴风影音）
	- 控制操作系统（最终还是控制了底层的硬件）
	- 必须遵循操作系统提供的标准
	- 最终都是交由底层硬件来执行
- 操作系统（windows、linux）
	- 控制所有硬件
	- 操作系统本身也是一种软件
	- 如果没有操作系统，在编写应用软件的时候就需要同时把那些能够控制对应硬件的程序，这样导致非常麻烦和低效
- 底层硬件（CPU、内存、硬盘）

##  计算机硬件
![](http://i.imgur.com/NT4MVoF.png)
- CPU、内存以及IO设备都有一条总线连接起来并通过与其他设备通信
	- CPU是人的大脑，负责运算
	- 内存是人的记忆
	- 硬盘是人的笔记本，负责永久存储
	- 输入设备是耳朵和眼睛，负责接受外部信息来传递给CPU
	- 输出设备是表情，负责输出经过处理后的结果
	- 以上设备都是通过总线连接，总线相当于人的神经

###  CPU
- 每一个CPU都有一套可执行的专门指令集，任何软件的执行最终都要转化成CPU能识别的指令去执行，其中包含操作其他硬件的命令
- 应用被CPU执行的过程
	- 从存储介质中取出指令 ——> 解码 ——> 执行（周而复始，直到整个程序被执行完成）
- 寄存器（最快的一种存储设备）
	- 用来保存关键变量和临时数据
	- 存取速度和CPU几乎相同（与CPU相同材质制造，CPU访问它无时延），访问内存并获取指令或数据所需要花费的时间远长与CPU执行命令花费的时间
	- **程序状态字寄存器PSW**
		- 包含CPU的优先级、模式（内核态、用户态）、条码位（由比较指令设置）以及其他控制位
		- 在系统调用中，程序状态字寄存器非常重要。
- CPU指令集中的命令可以成两个部分
	- 算术运算和逻辑运算指令
	- 操作其他计算机硬件的指令
	> 对于操作系统而言，其指令被CPU转换成操作其他硬件的指令；但对于用户的应用软件而言，其指令被CPU转换成算术运算和逻辑运算这类计算指令
- **内核态与用户态**（CPU运行的两种状态）
	- 内核态：操作系统被CPU执行的状态
	- 用户态：应用程序被CPU执行的状态
	- 两者区别？
		- 处于内核态的CPU执行的是操作系统，可以控制硬件，此状态下可以获取CPU的所有指令集（包括控制其他硬件的指令集）
		- 处于用户态的CPU执行的是玉壶软件，不可以操作硬件，而且只能获取CPU指令集的一个子集，该子集中不包括操作硬件的指令集
	- 内核态与用户态的互换
		- 应用程序想要控制底层硬件，就需要将CPU所处的用户态转换成内核态，当应用程序操作完硬件之后，再切换回内核态来继续运行应用程序
		- 用户态下工作的软件不能操作硬件，但是有些软件比如暴风影音，一定会由操作硬件的需求，比如从磁盘上读取一个电影文件，那就必须经历从用户态切换到内核态的过程。因此，用户程序必须使用系统调用（system call ），系统调用会先入内核并调用操作系统，CPU的状态从用户态切换到内核态，并启用操作系统而会的服务。
- 多线程与多核芯片
	- 一个CPU中的处理逻辑增多，称为多线程。对于用户来说每一个拥有两线程的CPU就相当于两个CPU。（进程是资源单位，而线程才是CPU的执行单位）
	- 一个物理CPU中可以模拟出多个虚拟的CPU来工作
	- 一个计算机上可以同时有多个物理级别的CPU


### 存储器
- 高速缓存L2
	- 内存中有高速缓存行按照0~64字节为行0，64~127为行1。。。最常用的高速缓存行放置在cpu内部或者非常接近cpu的高速缓存中
	- 缓存在计算机科学的许多领域中起着重要的作用，并不仅仅只是RAM（随机存取存储器）的缓存行。**只要存在大量的资源可以划分为小的部分，那么这些资源中的某些部分肯定会比其他部分更频发地得到使用，此时用缓存可以带来性能上的提升**
	>  一个典型的例子就是操作系统一直在使用缓存，比如，多数操作系统在内存中保留频繁使用的文件（的一部分），以避免从磁盘中重复地调用这些文件，类似的/root/a/b/c/d/e/f/a.txt的长路径名转换成该文件所在的磁盘地址的结果然后放入缓存，可以避免重复寻找地址，还有一个web页面的url地址转换为网络地址(IP)地址后，这个转换结果也可以缓存起来供将来使用。

- 内存
	- 主存（随机访问存储RAM，存储器系统主力）
		- 易失性存储，**断电后，保存的数据就消失了**
		- 所有不能再高速缓存中找到的，都会到主存中找
	- ROM（read only memory，非易失性随机访问存储）
		- 在电源切断之后，非易失性存储的内容并不会丢失
		- ROM只读存储器在工厂中就被编程完毕，然后再也不能修改
		-  在有些计算机中，用于启动计算机的引导加载模块就存放在ROM中，另外一些I/O卡也采用ROM处理底层设备的控制 
	- EEPROM（electrically erasable PROM ，电可擦除可编程ROM）和闪存（flash memory）
		- 非易失性
		- 可以擦除和重写(重写花费时间比写入RAM时间久)
		- 在便携式电子设备中中，闪存通常作为存储媒介（固态硬盘）
		- 闪存在速度上介于RAM和磁盘之间，但与磁盘不同的是，闪存擦除的次数过多，就被磨损了
	- CMOS存储器
		- 易失性存储
		- CMOS可以用来保存配置的参数，比如计算机启动时其中就保存了启动磁盘的配置信息

### 磁盘
- 磁头：通过磁性原理读取磁性介质上数据的部件（利用特殊材料的电阻值随着磁场变化的原理来读写盘片上的数据）
- 磁道：磁盘旋转时，磁头在磁盘表面画出的一个圆形轨迹
- 扇区：磁盘上的每一个磁道被分为若干个弧段，这些弧段就是磁盘的山区。磁盘的读写以扇区为基本单位（512字节）
- 柱面：上下一串盘片中，相同半径的刺刀所组成的一个圆柱形环壁。
- 数据都存放于一段段的扇区，从磁盘读取一段数据需要经历寻道时间和延迟时间
	-** 平均寻道时间**：机械手臂从一个柱面随机移动到相邻柱面的时间就是寻道时间，找到了磁道就意味着磁头找到了数据所在的磁道，但是此时还无法确认数据具体在磁道上的扇区
	- **平均延迟时间**：机械手臂到达正确的磁道之后还必须等待旋转到数据所在的扇区下，这段时间就是延迟时间

### 总线
- 北桥（PCI桥）连接高速设备，包括L2高速缓存、CPU 、主存储器（内存）
- 南桥（ISA桥）链接慢速设备，包括SCSI/IDE硬盘、USB接口、图形适配器、声卡、打印机、调制解调器等

### 计算机的启动
- 硬盘中，电脑启动时，**最终操作系统的代码要给CPU去运行**。也就是说，硬盘中操作系统的代码首先会加载到内存，CPU读取内存中操作系统的指令之后再执行。但是必须有一种机制明确的告诉计算机哪一个硬盘是启动盘，而这个机制就是**BIOS程序**（基本输出输出程序）。
- BIOS就相当于一个系统操作系统，它有底层的I/O软件，包括读键盘，写屏幕，进行磁盘I/O。BIOS回到它对应的存储设备CMOS存储器中去读取上次认为配置的启动盘位置，因此BIOS可以确定启动盘是那一块硬盘。


1. 计算机加电
2. BIOS开始运行，检测硬件：CPU、内存、硬盘
3. BIOS读取CMOS存储器中的参数，选择启动设备（BIOS告诉启动硬盘在哪儿）
4. 从启动设备上读取第一个扇区的内容（MBR主引导记录512字节，前446为引导信息，后64为分区信息，最后两个为标志位）
5. 根据MBR读入grub（一种bootloader）之后找到内核，将内核代码读入内存，之后CPU加载代码并运行，进而启动操作系统
6. 操作系统询问BIOS，获取配置信息。对于每种设备，系统会检查其设备驱动程序是否存在，如果没有，系统则会要求用户安装设备驱动程序。一旦有了全部的设备驱动程序，操作系统就将它们调入内核，然后初始有关的表格（如进程表），创建需要的进程，并在每一个终端上启动登陆程序或GUI

## 用户应用软件的启动
- **前提** ：应用程序运行在操作系统之上


1. 双击软件快捷方式，操作系统会按照快捷方式给出的路径去硬盘上找该目标文件。
2. 由硬盘将该文件加载到内存当中，再被CPU调用、运行
3. 软件向操作系统发出操作硬件的命令，使CPU状态从用户态切换到内核态，操作系统就帮软件去硬盘中寻找该文件，读取文件完毕之后，CPU的状态再由内核态切换到用户态，应用软件启动成功。
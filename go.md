1. 一个运行服务如何做到动态更新配置  
    goroutine监听配置系统如etcd/consule，如果有变化通知，则比较老的配置和新的配置，如果某一项配置发生变化则修改这一配置，但是不同goroutine去读写同一变量可能会有问题，解决这一问题方法很多比如使用mutex，但是mutex相对更耗时，可以使用atmoic包来实现，atmoic是使用cpu指令来实现的，效率更高
2. atomic可以对哪几种数据类型做操作  
    - int32  
    - int64
    - uint32
    - uint64
    - uintptr
    - unsafe.Pointer
3. atomic可以做哪几种操作
    - add
    - compare and swap
    - swap
    - load
    - store
4. 使用yaml.Unmarshal做反序列化时候，structure的成员需要是public，也就是成员首字母需要是大写  
    ```
    type abc struct {
        A string
        B int
        C []string
    }
    ```
5. golang中new和make的区别
    - make仅用来分配及初始化类型为slice、map、chan的数据
    - new可以分配任意类型数据，根据传入类型申请一块内存，返回指向这块内存的指针
    - make返回引用，new分配的空间被清零，make分配空间后进行初始化
6. golang中对nil的slice和空slice处理是一致的吗  
   golang中json库对nil的slice和空slice处理是不同的
    - slice := make([]int, 0), slice不为nil，但是slice底层空间是空的
    - slice := new([]int)，slice的值是nil，可用于需返回slice的函数
7. golang内存模型中为什么小对象多了会造成gc压力
    通常小对象多会造成三色法小号过多的gpu，优化思路是减少对象分配
8. struct能不能比较
    - 相同类型stuct可以比较
    - 不同类型struct不可以比较
9. golang的slice如何扩容
    - 如果新申请容量是原来容量2x，最终容量就是新申请容量
    - 如果旧容量小于1024，最终容量是原来2x
    - 如果旧容量大于1024，最终容量是原来1.25x
10. 什么事rune类型
    - uint8，或叫byte，表示一个ascii码
    - rune表示一个UTF-8字符，等价于int32
11. struct{}占用空间吗？用途是什么？
    - 不占用空间
    - 将map用作set使用时，可以将值类型设为struct{}
    - 使用channel仅用于通知作用时候，可以定义chan struct{}
    - 结构体只包含方法
12. %v %+v %#v区别
    - %v仅输出值
    - %+不输出key和value
    - %#不输出结构以及key和value
13. 两个interface可以比较吗？
    - 判断类型是否一样  
    reflect.TyepOf(a).Kind() == reflect.TyepOf(a).Kind()
    - 判断两个interface是否先等  
    reflect.DeepEqual(a, b interface{})
    - 将一个interface赋值给另外一个interface  
    reflect.ValueOf(a).Elem().Set(reflect.ValueOf(b))
14. go中两个nil可能不相等吗？  
    - 接口是对非接口的封装，包含两个字段T和V，一个接口等于nil相当于T和V都是unset
    - 两个接口比较时先比较T，再比较v
    - 接口和非接口比较时，会尝试先将非接口值转换为接口值再比较
15. go函数中为什么会发生内存泄漏？发生内存泄漏如何检测？
    - goroutine需要维护用户执行代码的上下文，在运行过程中需要消耗一定的内存来保存这些信息，如果一个程序不断得创建goroutine且不结束之，就会曹正内存泄漏
    - 使用pprof来检测go进程占用资源
16. context结构原理
    - context用于控制多个gorouine之间的协作，取消操作
    - Deadline返回截止时间，到了这个时间点，context会自动发起取消请求
    - Done返回一个只读channel，类型为struct{}，如果可读说明已经发起取消请求
    - Value获取contet上绑定的值，是一个key value对
17. new和make区别
    - new可以创建任何类型对象，make只能用来创建slice、chan、map
    - new返回对象指针，make返回对象引用
    - new分配的空间会清零，make分配空间后会初始化
18. slice和array的区别
    - array是定长，slice可以扩容
    - 数组是值类型，slice是引用类型，每个slice引用一个底层数组，slice一旦扩容指向一个新的数组
19. for range时候其地址会改变吗  
    不会改变，for i, item := range x，i和item的地址是固定的，每次用心的值给其赋值，所以在for中启动goroutine时不要直接使用i和item的地址，可以新建一个变量引用之
20. go defer
    - defer用于释放资源：关闭文件、关闭网络连接、释放锁、捕获异常等
    - defer用在打开资源之前，否则起不到释放资源目的
    - defer的调用方式是后进先出
    - return、return value、defer的调用顺序是return、return value、 defer，defer可以用于修改返回值
21. int类型
    - int32有符号，32位机器上32位，64位机器上64位
    - uint32无符号，32位机器上32位，64位机器上64位
    - byte，等价于uint8，用于表示ascii
    - rune，等价于int32
22. rune
    - 相当于int32
    - 中文字符在unicode下占两个字节，在utf-8下占三个字符，golang的编码是utf-8
23. golang中tag如何实现的
    - 反射
24. slice的一些特性
    - slice有length和capacity
    - slice指向一个底层数组
    - slice作为参数传递给函数，如果不发生扩容，内外slice都指向同一个数组，但是如果发生扩容内外slice指向的是不同的数组
25. defer的一些底层实现
    - 每个defer都对应一个_defer实例，多个实例使用指针连接成一个链表，新增加一个defer在链表的头部插入一个新的实例，函数结束的时候从链表头部按顺序执行实例，形成了后入先出的效果
    - defer函数的参数是defer函数出现的时候就确定了
26. 单引号、双引号、反引号
    - 单引号表示rune
    - 双引号字符串
    - 反引号不做转义，双引号会转义
27. 如何对map并发访问
    - sync.map
    - 锁
28. nil map和空map有什么不同
    - nil map可以取值，返回nil，nil map赋值会panic
    - fmt打印nil map和空map时都打印map{}
29. go多返回值如何实现的
    - go函数参数和返回值都是通过FP+offset实现的，保存在在调用函数的栈帧中，FP指向一个函数调用栈的顶部
30. go中如何比较不同类型对象  
    int, float, interface使用等号和reflect.DeepEqual来比较，slice, map, struct一般使用reflect.DeepEqual来比较
31. init函数
    - 一个包可以有多个init
    - 一个文件也可以有多个init
    - 一个包中init的执行顺序是按照其所在文件名顺序执行
    - 程序初始化时最内层的包的init最先被执行
    - 包级别变量初始化优先于包中init函数
32. uniptr和unsafe.Pointer区别
    - unsafe.Pointer是通用指针类型，任何类型指针都可以转换为unsafe.Pointer，unsafe.Pointer也可以转换为任何类型指针
    - uniptr可以和unsafe.Pointer互相转换，但是不能和
    - uniptr是指针运算的工具，但是它不能持有指针对象
33. go内存泄漏
    - go内存泄漏一般是goroutine没有关闭，一个goroutine栈大小最小2kb
34. 内存逃逸
    - 本该分配到栈上的内存分配到堆上了
    - 栈是从高地址到低地址，栈上的变量，函数结束后会跟着被回收掉，不会有额外性能消耗
    - 变量从栈上逃到怼上，一定要做gc，gc就会有性能消耗
35. 内存逃逸情况如下
    - 函数返回局部变量指针
    - 向channel发送指针变量
    - 在闭包内引用包外的值
    - 在slice或map中存储指针
    - 切片长度太大
    - 在interface上调用方法
36. channel分配在堆上还是栈上
    - channel用于goroutine之间通信，其作用域和生命周期不可能限于某个函数内部，所以golang将其分配在堆上
37. 介绍下大小对象，为什么小对象多了会造成gc压力
    - 小于32k的对象就是小对象，其余都是大对象
    - 小于32k的对象通过mspan分配内存，小于32k的对象由mheap分配内存
    - 小对象过多会导致gc三色法消耗过多CPU
38. slice可以用作map的key吗
    - 能作为map的key的条件是可比较
    - slice，map，function不能比较
39. context作用
    - 超时控制
    - goroutine之间交互数据
    - 调用调用交互数据
40. channel为什么线程安全？怎么设计为线程安全？
    - channel是多个goroutine之间共享数据，为了保证数据一致性必须线程安全
    - chan中的hchan结构使用mutex来保证数据读写安全，在对循环buf中的数据做入队出队操作时必须先获得mutex
41. channel底层实现原理
    - buf
    - lock
    - sendq发送队列
    - recvq接受队列
    - sendx下一个发送数据下表
    - recvx下一个接受数据下表
42. nil channel、无数据channel、有数据channel读写关闭会怎么样
    - channel有两种类型：无缓冲、有缓冲
    - channel有三种模式：写模式、读模式、读写模式：make(chan<- int), make(<-chan int>, make(chan int))
    - channel有三种状态：未初始化、关闭、未关闭  
    - 一个channel不能多次关闭否则panic
    - 多个gorouine监听同一个channel，channel中数据会随机被某个gorouine取走
    - 多个gorouine监听同一个channel，如果channel关闭，所有gorouine收到退出信号
    
    |      | 未初始化  | 关闭 | 未关闭|  
    | ---- | ----   | ---- | ---- |    
    | 关闭  | panic  | panic | 正常关闭 |  
    | 发送  | block  | panic | block或发送成功 |
    | 接收  | block  | 如果为空返回0值否则正常返回 | block或接收成功 |

43. 进程、线程、协程区别
    - 进程是应用程序的启动实例，每个进程有独立的内存空间，不同进程通过进程通信方式来通信
    - 从属于进程，每个进程至少包含一个线程，线程是cpu调度的基本单位，线程共享进程的资源
    - 协程式轻量级线程，不是cpu调度单位，协程调度器在应用层实现，协程调度器把协程调度到线程中运行
44. 抢占式调用是如何抢占的  
    就像操作系统负责线程调度一样，golang的runtime负责协程的调度，现代操作系统调度线程都是抢占式的。我们不能依赖用户代码主动让出活io、锁等待让出CPU，这样会让调度不公平。基于经典的时间片算法，当线程的时间片用完后，会被时钟中断给打断，调度器会将当前线程的上下文保存，然后恢复下个线程的上下文，分配新的时间片令其执行。这种抢占对于线程本身是无感知的，系统底层支持，开发人员不需要干预  
    基于时间片抢占调度有个明显优点：能够避免cpu资源被少数线程占有。goroutine调度器也用到了时间片调用算法，但是和操作系统调度线程还是有区别的，因为go程序都是运行在用户态，不能像操作系统那样使用时钟中断来打断goroutine的执行，也得益于gorouine的调度在用户态实现，所以比较轻量
45. go语言特点
    - go支持跨平台编译，编译出二进制直接部署到对应平台即可运行
    - go在语言层次上支持高并发，通过goroutine和channel实现。channel的理论依据是CSP并发模型，即通过通信实现共享内存。
    - go在runtime里面实现了自己的调度机制：GMP，避免了内核态和用户态的频繁切换
    - go的代码风格是强制性统一，没按照规范来会编译不过
46. goroutine和线程相比有什么特点
    - goroutine非常轻量，初始分配只有2kb，当栈空间不足时会自动扩容；同时自身存储了执行stack信息，用于在调度时恢复上下文
    - 线程比较重，初始分配有几个M，线程是os调度的基本单位，而golang实现了自己的调度策略，goroutine是其调度的基本单位
47. go垃圾回收机制  
    go使用的是三色标记法，将变量标记为三种颜色
    - 白色，未被使用的对象
    - 灰色，当前对象由引用对象，但是还没对引用对象扫描过
    - 黑色，对于灰色对象的引用对象已经全部扫描过了，不用再扫描了  
    当垃圾回收开始，会把根对象标记为灰色，其他对象标记为白色。接下来对根对象开始遍历搜索，按照上面的规则不断对灰色对象做扫描标记，当没有灰色对象时候表示扫描结束，最后清除白色对象
48. go内存分配机制  
    go内存分配机制借鉴了google的tcmalloc机制，核心是内存池+多级对象管理。内存池主要是预先分配以减少向os申请的频率。多级对象有mheap、mspan、arenas、mcentral、mcache，他们以mheap作为基本分配单位，分配策略如下：
    - 当分配大于32k对象时，从mheap分配
    - 当分配小于16b对象时，从mcache的微型分配器分配
    - 当分配对象大小介于16b和32k之间时，从p的mcache上分配，如果mcache不够则从mcentral分配，如果mcentral不够则从mheap分配
49. channel的内部实现是怎么样的
    - channel内部维护了一个读goroutine队列和一个写goroutine队列
    - 每当对channnel的操作超过了可缓冲的gorouine数量，那么当前goroutine就会被悬挂到相应队列上，直到有其他goroutine执行了相反的读写操作才会将其唤醒
50. map为什么不是线程安全的
    - map在扩缩容时要做数据迁移，迁移时候并没使用锁机制防止并发操作，对正在迁移的数据标志位1，如果有其它goroutine对map做写操作，检查到标志1会panic
    - 如果想要并发安全的map，请使用sync.map
51. map的key为什么是可比较的类型  
    - map的key和value保存在buckets里面，每个buckets里面保存8对key和value。当要插入一个key - value对时，对key做hash，根据hash值的低几位决定命中哪个bucket，又根据hash值的高8位是8个位置里面的哪一个位置，如果发生了hash冲突则会去寻找其它位置，如果全部满员则使用overflow指向一个新的bucket，然后重复以上过程
    - 在判断hash冲突时候，要判断两个key是否相等，所以key必须是可比较的，像function、slice、map不能作为key
52. mutext的正常模式，饥饿模式，自旋模式？
    - 正常模式。当mutex调用unlock释放锁资源时，发现有正在阻塞等待唤起的队列时，会将队头的goroutine唤起，队头的goroutine被唤起后，采用CAS这种方式去修改占有标识位，如果修改成功表示占有资源成功了，占有成功的goroutine继续往下执行
    - 饥饿模式。上面唤醒的goroutine不是直接占有资源，二是使用CAS尝试去占有资源。如果此时有新来的goroutine也会尝试去占有资源，对于go的调度机制来讲，会比较偏向于占有CPU时间短的goroutine去执行，而队头的goroutine一直占有不了资源导致饿死。针对这种情况，go采用饥饿模式，对于队头goroutine一直获取不到资源时间超过一个阈值后，会在unlock的时候直接让队头goroutine占有资源，并将当前状态修改为饥饿模式。其它新来的goroutine看到饥饿模式直接被放盗队列尾
    - 自旋模式。如果goroutine占用资源时间很短，释放锁的时候通过信号量去唤醒正在阻塞等待的goroutine，将会很消耗资源。因此在符合一定条件后，mutext会让等待的gorouine去空转CPU，空转完成后使用CAS尝试性的去占有锁资源，直到不满足自旋条件为止
53. 逃逸行为
    - 在传统的变成语言里面，变量被分配在stack还是heap上是由用户来决定的，申明的变量在stack分配空间，new出来对象在heap分配空间
    - 但是go变量的内存分配方式是由编译器决定的，如果函数里面定义一个值变量且被函数外部引用了，我们称这种case为发生了逃逸行为，将其分配在heap上而不是stack上，同样如果一个变量使用new构造，但是没有发生逃逸行为会被分配在stack上
54. context如何通知子context
    - 当ctx, canel := context.WithCancel(pCtx)时，会将自己挂在在父context下，并启动一个goroutine去监听父context，一旦收到父context通知，也会触发通知子context
55. waitgroup原理
    - waitgroup维护了一个计数器，当计数器减为0的时候就回唤起之前调用wait的goroutine
56. sync.Once原理
    - Once内部维持了一个标识位，使用锁来访问此标识位，如果标识位已经置为1，则不再执行此函数
57. 定时器原理
    - 一开始timer会被分配到一个timerbucket里面去，为了防止多个timer分配到同一个tiemrbucket，go会创建64个这样的bucket，根据timer所在gotoutine对应P的id计算hash到某一个bucket
    - timerbucket对这些timer维护一个最小堆，每次挑出时间最快到达的timer，如果timer未到就sleep，sleep后执行timer对应函数，并且往timer对应channel发送数据，用于通知timer对应goroutine
58. range slice
    - range不会一直执行下去，range执行时会copy slice的length，根据这个lenght去遍历slice，即便遍历中有新的元素加入，也不会去改变这个length
    - range遍历时候是值拷贝，所以直接修改遍历变量不会修改到slice本身
59. defer、panic、recover三者用法
    - panic时会执行panic之前的defer
    - 一般在defer里面调用recover
60. :=和=的区别
    - :=是定义变量，=是赋值
61. go中如何表示枚举
    - itoa
62. 空struct{}的用途
    - map[int]struct{}表示set
    - channel传递信息
63. 两个interface可以比较吗？
    - 两个nil的interface相等
    - 类型相同，值相同
64. 两个nil可能不等吗？
    - nil的interface{}和nil的指针就不相等
65. go gc工作原理
    - 1.3标记清除法；1.5三色标记法；1.8三色标记法+混合写屏障
66. 标记清除法
    - 标记阶段：从根节点开始寻找标记存活的对象
    - 清除阶段：遍历堆中对象，回收未标记对象
    - 需要stw
67. 三色标记法
    - 将对象标记为白色、黑色、灰色
    - 标记开始时将所有对象加入白色集合但是需要stw，将根对象放入灰色集合
    - 从灰色集合中取出一个对象，将其引用对象放入灰色集合，同时将此对象放入黑色集合
    - 重复上一步，直到灰色集合为空
    - 白色集合中对象就是要清理的对象
    - 这种方法有一个缺陷，如果对象的引用被修改了，那么之前的标记就无效了。因此go采用了写屏障技术，当新增对象或修改对象引用将其设为灰色
68. 非接口类型T可以调用*T的方法吗？反过来呢？
    - T可以调用*T的方法，当T可寻址即可
    - *T可以调用T的方法
69. 可以限制运行时操作系统线程数量吗？常见goroutine操作函数有哪些
    - 可以使用runtime.GOMAXPROCS设置线程数量
    - runtime.Gosched让出当前goroutine执行，调度器安排其他goroutine执行，在下次某个时候从该位置继续执行
    - runtime.Goexit退出当前goroutine，退出前会先执行defer。千万别在主函数中执行此函数，否则会panic
70. go如何实现面向对象
    - 封装：对于同一个包，对象对于包内文件可见；对于不同包，对象名首字母大写可对其他包可见
    - 继承：在结构体中加入需要继承的类型即可
    - 多态：多态是运行时特征，go多态通过interface实现，go对象可以赋值给任何它实现了所有方法的interface对象

一、注意函数fread，fwrite,read,write,fgets fputs gets puts sprintf,snprintf,fgetc fputc getchar putchar等的使用（APUE第五章）；
    C语言不管带不带参数，函数后面都要加上括号；文件的操作以字节为最小单位

二、accep函数：
int accept（int sockfd,struct sockaddr *cliaddr,socklen_t *addrlen)，常把它的第一个参数称为监听套接口，把它的返回值称为已连接套接口

三、有名管道通信：
有名管道间通信注意：1、程序不能以O_RDWR模式打开FIFO；2、mkfifo参数中是完整的路径名比如"/tmp/text"，而不能是"./text"，且不能是已经存在的文件；
3、阻塞问题
对于以只读方式（O_RDONLY）打开的FIFO文件，如果open调用是阻塞的（即第二个参数为O_RDONLY），除非有一个进程以写方式打开同一个FIFO，否则它不会返回；如果open调用是非阻塞的的（即第二个参数为O_RDONLY | O_NONBLOCK），则即使没有其他进程以写方式打开同一个FIFO文件，open调用将成功并立即返回。
对于以只写方式（O_WRONLY）打开的FIFO文件，如果open调用是阻塞的（即第二个参数为O_WRONLY），open调用将被阻塞，直到有一个进程以只读方式打开同一个FIFO文件为止；如果open调用是非阻塞的（即第二个参数为O_WRONLY | O_NONBLOCK），open总会立即返回，但如果没有其他进程以只读方式打开同一个FIFO文件，open调用将返回-1，并且FIFO也不会被打开。


四、 文件锁：1、当一个进程终止时，它所建立的锁全部释放，任何时候关闭一个描述符时，
             则该进程通过这一描述符可以存访的文件上的任何一把锁都被释放
          2、由fork产生的子程序不继承父进程所设置的锁
          3、在执行exec之后，新程序可以继承原来执行程序的锁
          
 五、父子进程中，每个（每个进程分别执行一次进程创建之后，后面的代码，谁先谁后，中间相互切换执行都是可能的）进程会按照顺序执行代码的，
       当遇到其它进程抢占资源时，系统先保存当前进程环境，执行其它进程，然后再跳转来执行本进程（这里和线程比较像了）
       
 六、可变宏定义：例子#define debug(fmt,arg...) do{printf("%s in %d"fmt,__FUNCTION__,__LINE__,##arg);}while(0)，然后可以直接调用debug("这里是想增加打印的消息\n");
 
 七、在访问同一共享内存的多个进程先后调用ftok()时间段中，如果fname指向的文件或者目录被删除而且又重新创建，那么文件系统会赋予这个同名文件新的i节点信息，于是这些进程调用的ftok()都能正常返回，但键值key却不一定相同了。由此可能造成的后果是，原本这些进程意图访问一个相同的共享内存对象，然而由于它们各自得到的键值不同，实际上进程指向的共享内存不再一致；如果这些共享内存都得到创建，则在整个应用运行的过程中表面上不会报出任何错误，然而通过一个共享内存对象进行数据传输的目 的将无法实现。这是一个很重要的问题，希望能谨记！！！mkfifo也有一样的问题，创建之后，路径不能删除
 
 八、管道是随进程持续的，也就是进程关闭了管道也会关闭；共享内存和消息队列是随内核持续的，就是一个进程发送了消息队列或者共享内存之后关闭进程，其它进程只要有相应的shmid或者msgid也能读取消息队列的数据或者共享内存的数据，只是消息队列的数据读取一条之后会删除一条。

九、注意结构体中Char a[0]用法——柔性数组：C99中，结构中的最后一个元素允许是未知大小的数组，这就叫做柔性数组成员，但结构中的柔性数组成员前面必须至少一个其他成员。柔性数组成员允许结构中包含一个大小可变的数组。sizeof返回的这种结构大小不包括柔性数组的内存。包含柔性数组成员的结构用malloc ()函数进行内存的动态分配，并且分配的内存应该大于结构的大小，以适应柔性数组的预期大小。例如：
struct _msg_xml {
	ucnode_s	node_list;
	char		pMsg_data[0];
};
十、注意这种写法(unsigned long)(&(type *)0)->member) )定义为把0地址转化为type类型的指针，可以得到结构体内各个元素的相对地址空间

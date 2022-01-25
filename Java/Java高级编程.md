

## Java高级编程

### 多线程

每个线程独立拥有一份虚拟机栈和程序计数器，而一个进程包含多个线程的话则一个进程只有一份方法区和堆，系统运行为每个进程分配独立的内存区域

一个Java程序运行时至少执行三个线程：一个main()主线程，一个gc()垃圾回收线程，一个异常处理线程。

**并行与并发**

并行：多个CPU执行多个任务；

并发：一个CPU执行多个任务；

#### 多线程创建：

**①**

1.先声明一个类继承于Thread类

2.重写Thread中的run方法，方法体内是执行的操作的内容

3.创建一个Thread的子类的对象，调用Thread中的start()方法

//不能通过调用run方法来启动线程，这样就不是多线程了，是单线程执行，必须调用start方法启动

**②**：实现Runnable接口

1.创建一个实现Runnable接口的类

2.让类内实现run

3.通过类创建对象

创建Thread的对象，将类创建的对象作为参数传递到Thread的构造器中

3.通过Thread创建的对象调用start

③：Callable接口

可以有返回值，可以抛出异常，需要借助FutureTask类来获取返回结果

FutureTask是Future接口的唯一实现类

```
NumThread numthread=new NumThread();
FutureTask futuretask=new FutureTask(numthread);//numthread是callable的实现类对象
futuretask.get();
```

get的返回值即为Callable实现类的对象重写的call方法的返回值

最后将futuretask作为参数传到Thread的构造器中创建一个对象来调用start方法

④：使用线程池

提前创建好多个线程，将他放在线程池中，使用时直接获取，使用完放回，避免重复创建和销毁

```
ExecutorService es=Executors.newFixedThreadPool(10);//创造一个线程数为10的线程池
es.execute();//将实现Runnable接口的类的对象放入参数列表中执行
es.submit();//将实现Callable接口的类的对象放入参数列表中执行
es.shutdown();//将线程池
```

**Thread中的常用方法：**

currentThread()：静态方法，返回当前代码执行的线程

get,setName():获取或设置当前线程的名字

yield():执行时释放当前CPU的执行权，一放弃，执行权就被另一个线程拿到，接着立刻执行另一个线程，但也有可能一放弃下一刻又拿回了执行权

join():在线程a中立即执行一个调用join方法的线程b，直到这个线程a执行完再执行另一个线程b

stop():强制让你的线程结束

sleep(long millitime):让程序阻塞一段millitime时间

isAlive():判断当前线程是否还存活

**线程的优先级：**

最大优先级(MAX_PRIORITY):10

最小优先级(MIN_PRIORITY):1

一般优先级(NORM_PRIORITY):5

可以通过get，set来获得和改变优先级

**Thread.state类中定义的线程的几种状态：**

新建（new），就绪（调用start方法），运行，阻塞（sleep和wait方法，wait方法可以通过notify方法唤醒），死亡（调用stop或者等run执行完，出现error或exception且没处理）

就绪到运行会获得CPU的执行权，运行时可以阻塞，但阻塞完要先回到就绪状态再运行

#### **解决线程的安全问题：**

**①同步代码块：**

`synchronized(同步监视器){`

`//需要被同步的代码（就是需要操作共享数据的代码）`

`}`

同步监视器：俗称锁，任何一个对象都能充当锁例如Object创建的对象也可以，但是多个线程要公用同一把锁，也

补充：在实现Runnable接口创建多线程的方式中，可以使用当前类的对象，比如this充当同步监视器

​			在继承Thread类创建多线程的方式中，考虑使用当前类充当同步监视器

**②同步方法：**

Runnable：将需要同步的代码封装为一个方法，并在前面加上synchronized变成同步方法，但是synchronized不能加在run方法前，方法中也有同步监视器，这里的同步监视器是this

继承Thread：与Runnable的方式相同，但是要把方法声明为static的，使同步监视器一致，这里的同步监视器是“类明.class”，类本身。

**③Lock锁：**

1.实例化Reentrantlock类的对象lock

2.`try{`

`		lock.lock（）//调用锁定方法`		

`//接着写需要同步的代码`

`}finally{`

​		`lock.unlock（）//调用解锁方法`

`}`

**##synchronized和lock有什么不同？答：lock需要手动unlock解锁，而synchronized可以在执行完同步代码块后自动释放同步监视器**

#### **死锁：**

指多个进程在运行过程中因争夺资源而造成的一种僵局，不会出现异常，但是线程都会发生堵塞

#### **线程通信：**

用wait方法堵塞，notify方法唤醒，notifyAll方法唤醒所有线程，但只能使用在同步代码块或同步方法中，这三个方法的调用者必须是同步代码块或同步方法中的同步监视器

**##这三个方法是定义在object类中的**

### Java常用类

#### String类

是一个final类，表示不可变的字符序列，字符串的值在创建后不可改

实现了Swrializable接口，表示字符串是支持序列化的；实现了comparable接口，表示字符串可以比较大小

可以通过字面量给string类型的对象赋值；方法区储存有字符串常量池，而字符串常量池不会生成一样的字符串String 

```java
String s3=s1.replace('b','c');//通过调用replace方法对s1字符串中的一个字符或字符串进行替换，在方法区中新造了一个字符串，让s指向它
```

**String对象创建**

①字面量赋值，**储存在方法区字符串常量池中**

②new方式创建String的构造器，空参构造器相当于付了一个char[0]，**储存在堆区中**

③`String s4=new String("Hello");`相当于new了一个长度为5的char型数组

④ `char[] arr=new char[5];
String s4=new String(arr);`

⑤ `char[] arr=new char[5];
String s4=new String(arr，2,2);`相当于从arr数组中角标为2的位置开始取2个字符形成一个字符串给s4

```
String s1="abc";
String s3="abcdef";
String s2=s1+"def";//这种情况下s2不在字符串常量池中，在堆区new了一个对象，所以这里s2不等于s3
//字符串常量与变量拼接结果在堆中
```

```
String s3="abcdef";
String s4=s2.intern();//此时调用了intern，一个字符串的方法，返回值在常量池中
```

**常用方法**

charAt（）：获取字符串某个角标的字符

toLowerCase（），toUpperCase（）：返回字符串全小写或全大写

trim（）：返回一个字符串，去除首尾空格

equalsIgnoreCase（）：忽略大小写比较是否相等

concat()：等价于+

compareTo（）：比较两个字符串相差多少，若不等长，则比较等长部分

```
String s1="jello";
String s2="jellwo";
System.out.println(s1.compareTo(s2));
//输出为-8
```

subString（i，j）：返回一个字符串从下标i到j，左闭右开

endWith（），startsWith（）：是否以指定字符串开始或结束，区分大小写

contains（）：是否包含当前字符串

replace（a，b）：将字符串中的子串a替换为b

**字符串转换为char型数组：**toCharArray（）方法，返回一个char[]

**char型数组转换为字符串:**调用string构造器，将数组作为参数放入构造器中

**字符串转换为byte型数组：**调用getBytes（）方法

**byte型数组转换为字符串:**调用string构造器，将数组作为参数放入构造器中

**编码：字符串-->字节，解码反之**



**StringBuffer类和StringBuilder类**

都是可变的字符序列，底层也是使用char[]存储；StringBuffer类是线程安全的，效率低

```
StringBuffer stringBuffer=new StringBuffer();//底层创建了一个长度为16的char型数组，可以扩容，默认情况下扩容为原来的2倍+2，同时将原有数组复制到新数组中
```

方法：

delete（i，j），replace（i，j，“###”），reverse（）逆转，insert（i，“###”），indexOf（“###”）返回字符串首次出现的位置，setCharAt（i，‘#’）修改一个字符

**Date类**

date类的对象调用toString方法返回当前时间，调用getTime方法返回1970年1月1日到现在的时间(毫秒)



如果将一个string类的null，append到stringbuffer类型中，stringbuffer类型会储存一个“null”的字符串，长度为4；但是如果将string类的null作为参数传到stringbuffer构造器中，会抛空指针异常

**SimpleDateFormat类**

是对date类的格式化和解析

格式化：日期--->字符串；调用format()方法格式化date类对象

解析：字符串--->日期；调用parse()方法解析string类对象，返回date

实例化：空参构造器实例化，`SimpleDateFormat simpleDateFormat=new SimpleDateFormat();`

使用带参构造器实例化：`SimpleDateFormat simpleDateFormat=new SimpleDateFormat("yyyy-MM-dd hh:mm:mm");`

则输出格式为2019-08-15 22:55:55秒格式的字符串

**Calendar日历类的使用**

抽象类，实例化方式①：调用其子类的构造器，`Calendar c=new GregorianCalendar();`

实例化方式②：调用静态方法getInstance（），`Calendar c=Calendar.getInstance();`

get()方法：`int day=c.get(Calendar.DAY_OF_MONTH);//获取calendar类中的属性`

set()方法：返回值类型为void，`c.set(Calendar.DAY_OF_YEAR,22);//相当于把calendar自身的属性给改了`

add()方法：返回值类型为void，`c.add(Calendar.DAY_OF_YEAR,22);//相当于把calendar自身的属性加上22天`

getTime（）方法：日历类-->date类，`Date d=c.getTime();`

setTime（）方法：date类-->日历类，修改calendar的属性，`Date d=new Date();c.setTime(d);`

**LocalDate，LocalTime，LocalDateTime类**

三个都是不可变的

now()方法：类的实例化，获取当前时间

of（）方法：没有偏移量，设置时间，`LocalDateTime localDateTime = LocalDateTime.of(2020,11,20,11,30);`

#### Java比较器

**comparable接口**

对于自定义类，如果要排序，可以让自定义类实现comparable接口，重写comparaTo方法

**comparator接口定制排序**

重写compare（Object c1，Object c2）方法

返回正整数，表示c1>c2；返回0，相等；返回负数，表示c1<c2；



### 枚举类

类的对象只有有限个的类称为枚举类，可以用于定义一组常量

**自定义枚举类：**

```
class Season{
    private final String seasonName;
    private final String seasonDesc;
    private Season(String seasonName,String seasonDesc){
        this.seasonDesc=seasonDesc;
        this.seasonName=seasonName;
    }
    public static final Season spring=new Season("春天","春暖花开");
    public static final Season summer=new Season("夏天","夏日炎炎");
    public static final Season autumn=new Season("秋天","秋高气爽");
    public static final Season winter=new Season("冬天","冰天雪地");
}
```

**使用enum关键字定义枚举类**

定义的enum枚举类默认继承于Enum

```
enum Season{
    //提供多个对象，对象之间用逗号隔开
    spring("春天","春暖花开"),
    summer("夏天","夏日炎炎"),
    autumn("秋天","秋高气爽"),
    winter("冬天","冰天雪地");
    //声明对象的属性
    private final String seasonName;
    private final String seasonDesc;
    //为对象的属性赋值
    private Season(String seasonName,String seasonDesc){
        this.seasonDesc=seasonDesc;
        this.seasonName=seasonName;
    }
}
```

values（）方法：返回枚举类型的对象数组 `Season[] enums=Season.values();`

valueOf（String str）方法：返回枚举类中与str同名的对象

toString（）方法：返回当前枚举类对象常量的名称

### 注解

代码里的特殊标记

在注解前加上@，当做一个修饰符来使用，如`@author，@version`

@override：限定重写父类方法

**自定义注解**：

```
public @interface MyAnnotation {
    String value();
}

//使用时
@MyAnnotation(value="hello");
```

没有成员变量的注解称为标记，可以用default定义成员的默认值



元注解：对现有注解进行说明解释的注解

四种元注解：retention，target，documented，inherited

@Retention：指定修饰的注解的生命周期，SOURCE/CLASS(默认)/RUNTIME（可以通过反射获取）

```
@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnnotation {
    String value();
}
```

@Target：指定被修饰的注解能修饰那些程序元素`@Target({ElementType.TYPE,ElementType.CONSTRUCTOR})`

### Java集合

类似于动态的容器，分为collection和map两大体系

collection存储单列数据，map储存双列数据（key->value）

collection接口下的list接口：动态数组，有序，可重复，又下分为：vector，arraylist，linkedlist

collection接口下的set接口：无序不可重复，下分为：treeset，hashset，linkedhashset，hashtable，properties



collection中的方法：（向collection中添加obj时，需要在obj所在类中重写equals方法）

isEmpty（）判断集合是否为空，contains（），containsAll(collection c),

增：add（），addAll（collection c）将另一个collection中的元素加过来

删：clear（）清空集合，remove（）移除元素,返回一个bool型，removeAll(collection c)将两个集合中共有的元素移除,

改：retainAll（collection c）将当前集合元素改为两个集合的交集

查：equals（collection c）**有序地**比较两个集合中元素是否相等



toArray():集合转换为数组

asList（）：数组转换为集合，`List list=Arrays.asList(new String[]{"aa","bb","cc"});`



集合元素中的遍历使用iterator接口，next可以依次遍历集合中的元素

```
Collection collection =new ArrayList();
collection.add(123);
collection.add(456);
collection.add("aa");

Iterator iterator=collection.iterator();//调用集合的iterator（）方法返回一个Iterator对象，指向容器第一个元素之前的位置
System.out.println(iterator.next());//next一次就指针往后移一位并返回值
```

用hashnext（）如果next未到容器尾部返回true来遍历数组

```
while(iterator.hasNext()){
    System.out.println(iterator.next());
}
```

remove（）可移除迭代器目前指向的元素



foreach循环用于遍历集合和数组，但是不能用这个方法修改集合中的元素

```
//for(集合元素的类型 局部变量名:集合/数组对象){}
for(Object obj:collection){
	System.out.println(obj);
}
```



#### list接口：

arraylist线程不安全，效率高，vector线程安全效率低，两者底层都是使用Object[]存储；linkedlist底层使用双向链表存储

常用方法：

add（int index,object c）在index上插入c

get（int index）返回index上的元素

indexOf（object c）返回c在集合中首次出现的位置

remove（int index）删除index上的元素

set（int index,object c）将index上的元素设置为c

sublist（int i，int j）返回一个从i到j 的list



#### set接口：

hashset线程不安全，可以存储null值；linkedhashset遍历其内部数据时可以按照添加顺序遍历；treeset可以按照添加对象的指定属性进行排序

set的无序性不等同于随机性，无序性是指插入时的无序，存储的数据并非按照数组索引顺序添加，按照数据的哈希值决定，不可重复性是指添加的元素按照equals方法判断是不能相等，向hashset添加元素时先查看元素的哈希值，计算出在hashset的位置再插入hashset中，若位置上已有元素，则比较哈希值，如果不相同，添加成功，并以链表的方式与存在的元素相连；如果相同则用equals方法比较，返回false则添加成功，也以链表的方式与存在的元素相连；

向hashset中添加的元素的类中一定要重写equals方法和hashcode方法

treeset的排序分为自然排序和定制排序，自然排序需要对象的类实现comparable接口并重写compareTo方法，定制排序利用comparator（创建一个comparator对象，作为参数放入treeset构造器中）

#### map接口：

双列数据，存储key-value对的数据

hashmap线程不安全，效率高，可以存储null的key和value；hashtable线程安全，效率低；linkedhashmap保证遍历时按照添加顺序进行遍历；treemap按照key-value进行排序，对key进行自然排序或者定制排序；properties常用来处理配置文件，key和value都是string



map中的key相当于用set存储，无序；而value相当于用collection存储，可重复，无序；一个entry包含一个键值对；要求key和value所在的类要重写equals方法，而key的类还要重写hashcode方法；添加数据的方法：**put（key，value）**



hashmap实例化以后相当于创建了一个长度为16的Entry[]数组（jdk 8以后是Node[]数组），如果用put插入一个数据与原有的数组上的相同。则依次比较hashcode和使用equals方法比较，若仍相同，则用插入的value替换原有的value，说明put有修改功能；

当数组某一个索引位置上的元素以链表的方式存储的结点数大于8且数组长度大于64，则此索引上的所有元素改为使用红黑树存储

数组扩容临界值等于容量乘以填充因子，超过这个值就扩容

**map的方法：**

添加put，putAll（map），删除remove（key）和clear，clear不等同于map=null；查询：get（key）获取key对应的value，containsKey（key），containsValue（value），containsxxx方法返回boolean值

Set keySet（）返回所有key构成的set集合； Collection values（）返回所有value构成的collection集合；Set entrySet（）返回所有entry构成的set集合



#### Collections工具类

是一个可以操作set，list，map的工具类

方法：

reverse（List），shuffle（List）随机排序，sort（List），swap（List，i，j）将list中的i和j位置元素交换位置

max（Collection）获取最大值，min（）获取最小值，frequency（Collection，object）出现次数，

synchronizedxxx（xxx）将线程不安全的集合转换为安全的



### 泛型

类似于标签，给容器贴标签指定放入元素的类型，泛型是类型，不能是基本数据类型。

```
ArrayList<Integer> arrayList=new ArrayList<Integer>();
```

用迭代器遍历时迭代器也要加泛型

```
Iterator<Integer> iterator=arrayList.iterator();
```

map的泛型可以声明为两个

```
Map<Iterator,String> map=new HashMap<Iterator,String>();
```

Entry是Map的内部接口，使用时要用 `Map.Enrty`



泛型类：

```
class user<T>{
    int age;
    String name;
    T t;
}
//在实例化时决定T是什么类型的
//建议实例化时指明类的泛型
User<Integer> user=new User<>();
```

子类在继承父类时如果指明了泛型类型则实例化时不需要再指明泛型。

```
class Person extends User<Integer>{
    
}
Person person =new Person();
//这里的Person不在是泛型类了
```

静态方法中不能使用类的泛型，异常类不能是泛型类

从创造一个T类型的数组：`T[] t1=(T[])new Object[10];`



泛型方法：

在方法中出现了泛型结构，但出现的泛型参数与类的泛型结参数不同，甚至泛型方法所属的类可以不是泛型类

```
public <E> List<E> copyList(E[] e){
    return null;
}
```

泛型方法可以声明为static的



```
List<Object> list1 =null;
List<String> list2 =null;
//次数list1和list2不具有继承关系
```



通配符使用：

```
List<Object> list1 =null;
List<String> list2 =null;
List<?> list3=null;

list3=list1;
//相当于list3是list1和list2的通用父类

//遍历方法：
public void print(List<?> list3){
        Iterator<?> iterator=list3.iterator();
        while(iterator.hasNext()){
            Object o=iterator.next();//使用Object接收
            System.out.println(o);
        }
    }
```

使用通配符的list3不能被添加数据，但是可以添加null；允许读取数据，读取的数据类型是object

有限制条件的通配符的使用：

```
List<Object> list1 =null;
List<String> list2 =null;
List<? extends Object> list3=null;
List<? super String> list4=null;
list3=list1;
list3=list2;
list4=list1;
list4=list2;//会报错

//G<? extends A>可以作为G<A>和G<B>的父类，其中B是A的子类
//G<? super A>可以作为G<A>和G<B>的父类，其中B是A的父类
//extends类似于<=,super类似于>=
```

### IO流

#### file类

file类的一个对象代表一个文件或文件目录

```
File file=new File("hello.txt");//相对路径，相对于当前module下的
File file1=new File("D:\\Program Files\\JiJiDown\\Download\\he.txt");//绝对路径
```

常用方法：

getAbsolutePath获取绝对路径，getPath获取相对路径，getName，getParent获取上一级目录，lastModified获取最近一次修改时间，renameTo返回boolean类型，参数为指定的文件路径

判断是文件还是文件目录：isFile，isDirectory

createNewFile：存在不创建，返回false，不存在就创建文件；mkdir：创建文件目录，若上层目录不存在就不造了，返回false；mkdirs：上层目录不存在的话也造一个上层目录；

#### IO流

处理流是来处理节点流的

![image-20220101154214946](C:\Users\GrandeLai\AppData\Roaming\Typora\typora-user-images\image-20220101154214946.png)

节点流(文件流)：FileInputStream，Filexxx。。。。

缓冲流(处理流的一种)：BufferedInputStream，Buffered。。。

读入：

```
File file=new File("hello.txt");
        FileReader fr=null;//提供具体的流
        //read()返回读入的一个字符，如果文件到末尾了就返回-1
        try{
            fr=new FileReader(file);
            int read= 0;
            read = fr.read();
            while(read!=-1){
                System.out.println((char)read);
                read=fr.read();
            }
        }catch (IOException e) {
            e.printStackTrace();
        }finally{
            //最后流要手动关闭
            try {
                if(fr!=null){
                    fr.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
```

写出：

基本与以上相同，输出的文件可以不存在，会直接创建；如果存在，构造器使用的是FileWriter（file，false）或FileWriter（file）就会覆盖原有文件，若第二个参数改成true则追加；

```
fw.write("iloveyou");
```



图片和字节只能用字节流处理

字节流读取文件：

```
FileInputStream fileInputStream=null;
try {
    File file=new File("hello.txt");
    fileInputStream=new FileInputStream("file");
    byte[] buffer=new byte[5];
    int len;
    while((len=fileInputStream.read(buffer))!=-1){
        String str=new String(buffer,0,len);
        System.out.println(str);
    }
} catch (IOException e) {
    e.printStackTrace();
} finally {
    try {
        fileInputStream.close();
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

使用字节流赋值图片：

```
FileInputStream fis=null;
FileOutputStream fos=null;
try {
    File srcFile=new File("ntzx.jpg");
    File destFile=new File("ntzx1.jpg");

    fis=new FileInputStream(srcFile);
    fos=new FileOutputStream(destFile);

    byte[] buffer=new byte[5];
    int len;
    while((len=fis.read(buffer))!=-1){
        fos.write(buffer,0,len);
    }
} catch (IOException e) {
    e.printStackTrace();
} finally {
    if(fos!=null){
        try {
            fos.close();
        } catch (IOException e) {
            e.printStackTrace();
        }

    }
    if(fis!=null){
        try {
            fis.close();
        } catch (IOException e) {
            e.printStackTrace();
        }

    }

}
```

#### 缓冲流：

提高流读取和写入的速度

```
BufferedInputStream bis= null;
BufferedOutputStream bos= null;
try {
    File srcFile=new File("ntzx.jpg");
    File destFile=new File("ntzx1.jpg");
    //先造节点流
    FileInputStream fis=new FileInputStream(srcFile);
    FileOutputStream fos=new FileOutputStream(destFile);
    //造缓冲流
    bis = new BufferedInputStream(fis);
    bos = new BufferedOutputStream(fos);

    //其他省略，都一样
} catch (FileNotFoundException e) {
    e.printStackTrace();
} finally {
    //关闭时先关闭缓冲流再关闭节点流，关闭缓冲流会自动关闭节点流，节点流关闭可以省略
    if(bos!=null){
        try {
            bos.close();
        } catch (IOException e) {
            e.printStackTrace();
        }

    }
    if(bis!=null){
        try {
            bis.close();
        } catch (IOException e) {
            e.printStackTrace();
        }

    }
}
```

BufferedReader提供了一次读一行的方法readline



#### 转换流

InputStreamReader（将一个字节的输入流转换为字符的输入流），OutputStreamWriter（将一个字符的输出流转换为字节的输出流），属于字符流，提供字节流与字符流之间的转换

```
InputStreamReader inputStreamReader=new InputStreamReader(参数1：字符的输入流,参数2：字符集，若不填默认为UTF-8);
```

#### 标准输入输出流

System.in标准输入流类型是InputStream，System.out标准输出流类型是PrintStream

System的setIn（InputStream is）和setOut（PrintStream ps）方法可以重新指定输入输出流

```
BufferedReader br=null;
try {
    InputStreamReader isr=new InputStreamReader(System.in);
    br=new BufferedReader(isr);
    while (true){
        String data=br.readLine();
        if(data.equalsIgnoreCase("e")||data.equalsIgnoreCase("exit")){
            System.out.println("程序结束");
            break;
        }
        String upperCase=data.toUpperCase();
        System.out.println(upperCase);
    }
} catch (IOException e) {
    e.printStackTrace();
} finally {
    if(br!=null){
        try {
            br.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

#### **对象流**

ObjectInputSteam和ObjectOnputSteam，用于存储读取基本数据类型和对象的处理流；

序列化：out，保存；反序列化：in，读取

序列化：

```
ObjectOutputStream oos= null;
try {
    oos = new ObjectOutputStream(new FileOutputStream("object.dat"));
    oos.writeObject(new String("ilove"));
    oos.flush();//刷新操作
} catch (IOException e) {
    e.printStackTrace();
} finally {
    if(oos!=null){
        try {
            oos.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

反序列化：

```
ObjectInputStream ois=null;
try {
    ois=new ObjectInputStream(new FileInputStream("object.dat"));
    Object obj=ois.readObject();
    String str=(String)obj;
    System.out.println(str);
} catch (IOException e) {
    e.printStackTrace();
} catch (ClassNotFoundException e) {
    e.printStackTrace();
} finally {
    if(ois!=null){
        try {
            ois.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

要保证对象的类是可序列化的，类可序列化的条件：

1.实现Serializable

2.需要类提供一个全局常量：`public static final long serialVersionUID=458756987514L;`值不固定

3.需要保证类内的所有属性也都是可序列化的

ObjectInputSteam和ObjectOnputSteam不能序列化static和transient修饰的成员变量



### 网络编程

#### IP和端口号

IP是唯一的标识internet上的计算机，java中使用InetAddress类代表ip，本机回路地址：127.0.0.1

实例化InetAddress：

```
InetAddress inet1=InetAddress.getByName("142.145.8.5");//可以是ip地址也可以是域名
```

端口号用来标识正在计算机上运行的程序，端口号范围是0~65535

端口号和Ip地址组合构成Socket

#### 网络协议

TCP协议：可以进行大数据量的传输，传输前使用“三次握手”的方式确保通信可靠，需要先进行链接，传输完毕需要释放链接“四次挥手”，效率低

UDP协议：不需要进行链接，每个数据报小于64k，是不可靠的，速率快

#### TCP网络编程

```
//客户端
@Test
public void client() {
    Socket socket= null;
    OutputStream os= null;
    try {
        //创建socke对象，指明服务器端的ip和端口
        InetAddress inet=InetAddress.getByName("127.0.0.1");
        socket = new Socket(inet,8899);
        //获取输出流用于输出数据
        os = socket.getOutputStream();
        os.write("nihao".getBytes());
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        if(os!=null){
            try {
                os.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        if(socket!=null){
            try {
                socket.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

}

//服务端
@Test
public void server(){

    try {
        //创建服务器端的ServerSocket，指明自己的端口号
        ServerSocket ss=new ServerSocket("8899");
        Socket socket=ss.accept();
        InputStream is=socket.getInputStream();
        ByteArrayOutputStream baos=new ByteArrayOutputStream();
        byte[] buffer =new byte[5];
        int len;
        while((len=is.read(buffer))!=-1){
            baos.write(buffer,0,len);
        }
        System.out.println(baos.toString());
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
    }

}
```

URL编程

url表示网络上某一资源的地址，一个URL包含五个部分：

![image-20220102193931639](C:\Users\GrandeLai\AppData\Roaming\Typora\typora-user-images\image-20220102193931639.png)

URL提供了各种getxxx的方法获取各个名字，如主机名等等



### 反射

反射机制运行在程序运行时通过ReflectionAPI来获取任何类的内部信息，能操作任意对象内部的属性和方法，视为“**动态语言**”的关键

通过反射可以调用类的私有属性，构造器和方法

#### Class类

一个类被解释运行后，被加载到内存中的就称为运行时类，就作为Class类的一个实例；类本身也可以作为一个对象

```
//获取Class实例的方式1:调用运行时类的属性.class
Class<Person> clazz1= Person.class;
//2：通过运行时类的对象
Person p1=new Person();
Class clazz2=p1.getClass();
//3.调用Class的静态方法：forname(String classPath)
Class clazz3=Class.forName("com.java15.Person");
```

类，接口，枚举类，数组，注解，基本数据类型，void也算Class类的对象

**读取配置文件的方式：**

```
//Properties:用来读取配置文件
Properties pros=new Properties();
//读取配置文件的方式1：
//此时文件默认在module下
FileInputStream fis=new FileInputStream("jdbc.properties");
pros.load(fis);
//读取配置文件的方式2：使用classloader做
//此时文件默认在src下
ClassLoader classLoader=ReflectionTest.class.getClassLoader();//获取当前类的类加载器
InputStream is = classLoader.getResourceAsStream("jdbc.properties");
pros.load(is);

String user =pros.getProperty("user");
String password =pros.getProperty("password");
```

**通过反射创建对应的运行时类的对象**

```
Class clazz = Person.class;
//调用newInstance创建类的对象，本质上也是调用了类的空参构造器（访问权限要至少为缺省）
Person obj=(Person) clazz.newInstance();
```

**获取类的结构：**

```
Class clazz= Person.class;
//获取属性结构
//getFields()获取当前运行时类及其父类所有public的属性
Field[] fields=clazz.getFields();
//getDeclaredFields():获取运行时类所有声明过的属性，不考虑权限
Field[] declaredFields=clazz.getDeclaredFields();
//通过获取的属性使用getModifier获取权限修饰符(返回int类型)，getType获取数据类型，getName获取变量名

//获取方法与获取属性相似，使用getMethod
```

**调用运行时类的指定结构：属性，方法，构造器**

调用属性：

```
Class clazz= Person.class;
Person p=(Person)clazz.newInstance();
Field id = clazz.getDeclaredField("id");
//保证当前属性可访问
id.setAccessible(true);
//set():参数一指明哪个对象，参数二设置的新值
id.set(p,1001);
//get():参数一指明哪个对象
int pId=(Integer) id.get(p);
```

调用方法：

```
Class clazz= Person.class;
Person p=(Person)clazz.newInstance();
//getDeclaredMethod：参数一指明哪个对象，参数二是指明获取方法的形参列表
Method show = clazz.getDeclaredMethod("show", String.class);
show.setAccessible(true);
//invoke():参数一指明哪个对象，参数二是给形参赋值的实参，返回值即为类中调用方法的返回值
show.invoke(p,"cnn");
```

 

#### 动态代理

通过代理类来调用其他的对象的方法，根据类的不同在运行期间创建不同的代理类

**静态代理：**

```
//接口类
interface ClothFactory{
    void produceCloth();
}

//代理类
class ProxyClothFactory implements ClothFactory{

    private ClothFactory factory;//拿被代理类进行实例化
    public ProxyClothFactory(ClothFactory factory){
        this.factory=factory;
    }

    @Override
    public void produceCloth() {
        System.out.println("代理工厂做一些准备工作");
        factory.produceCloth();
        System.out.println("代理工厂做一些收尾工作");
    }
}

//被代理类
class NikeClothFactory implements ClothFactory{

    @Override
    public void produceCloth() {
        System.out.println("nike生产运动服");
    }
}

public class StaticProxyTest {
    public static void main(String[] args) {
        NikeClothFactory nike=new NikeClothFactory();
        ProxyClothFactory proxyClothFactory=new ProxyClothFactory(nike);
        proxyClothFactory.produceCloth();
    }
}
```

**动态代理：**

```
interface Human{
    String getBelief();
    void eat(String food);
}

class SuperMan implements Human{

    @Override
    public String getBelief() {
        return "SuperMan's belief";

    }

    @Override
    public void eat(String food) {
        System.out.println("我喜欢吃"+food);
    }
}

class ProxyFactory{
    public static Object getProxyInstance(Object object){
        MyInvocationHandler handler=new MyInvocationHandler();
        handler.bind(object);
        return Proxy.newProxyInstance(object.getClass().getClassLoader(), object.getClass().getInterfaces(),handler);
    }
}

class MyInvocationHandler implements InvocationHandler{
    private Object obj;//需要使用被代理类的对象进行赋值
    //赋值
    public void bind(Object obj){
        this.obj=obj;
    }

    //当我们使用代理类的对象调用方法a时，就会自动调用如下方法：invoke
    //将被代理类要执行的方法a封装在invoke中
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //method是代理类调用的方法，此方法也就作为被代理类调用的方法，obj就是被代理类的对象
        Object returnValue=method.invoke(obj,args);
        return returnValue;
    }
}

public class ProxyTest {
    public static void main(String[] args) {
        SuperMan superMan=new SuperMan();
        //代理类的对象
        Human proxyInstance = (Human)ProxyFactory.getProxyInstance(superMan);
        System.out.println(proxyInstance.getBelief());
        proxyInstance.eat("饭");
    }
}
```



### java8的新特性

### Lambda表达式

可以理解为可以传递的代码，类似于一段数据

本质是作为**函数式接口**的一个实例，针对的都是接口

**如果一个接口只声明了一个抽象方法就称为函数式接口**

举例:

```
(o1,o2)-> Integer.compare(o1,o2);
```

->:lambda操作符

->的左边：形参列表，参数类型可以省略

->的右边：方法体，只有一条语句可以省略{}

```
//无参无返回值
Runnable r1=()-> {System.out.println("你好");};
//需要一个参数但是没有返回值
Consumer<String> con=(String str)->{System.out.println(str);};
//参数的数据类型可以忽略，可推断出
Consumer<String> con1=(str)->{System.out.println(str);};
//需要一个参数时小括号可以忽略
Consumer<String> con2=str->{System.out.println(str);};
//需要两个或以上的参数，多条执行语句，有返回值
Comparator<Integer> com=(x,y)->{
    System.out.println("");
    return Integer.compare(x,y);
//只有一条语句，return和大括号若有都可以省略：
Comparator<Integer> com1=(x,y)->Integer.compare(x,y);
```



java内置的四大函数式接口

消费型接口Consumer<T> 方法：accept()只接收，不返回

供给型接口Supplier<T> 方法：T get()只返回

函数式接口Functional<T，R>  方法：R appley（T  t）接收T返回R

断定型接口Predicate<T> boolean test(T t)

#### 方法引用

本质也是作为**函数式接口**的一个实例，本质上也是lambda表达式

使用情景：当要传给Lambda体的操作已经有了实现方法，如consumer的accept已经有了实现方法println

使用格式：类(或对象)：：方法名

使用要求：要求接口中抽象方法的形参列表和返回值与方法引用非方法的形参列表和返回值相同

```
//情况一：对象::非静态方法
PreatSteam ps=System.out;
Consumer<T> con=ps::println;
//情况二：类::静态方法
Comparator<Integer> com2=Integer::compare;
//情况三：类::实例方法
//Comparator中的 int compare(T ti,T t2)
//String中的 int t1.compareTo(t2)
Comparator<String> com3=String::compareTo;
```

#### 构造器引用

```
Supplier<Employee> sup1=()-> new Employee();
Supplier<Employee> sup2=Employee :: new;
```



#### Stream API

Stream不会存储元素，不可变，关注的是数据的运算，而集合关注的是数据的存储

执行流程：

1.stream的实例化 2.一系列中间操作 3.终止操作

一旦执行终止操作，久治县中间操作链，并产生结果

**stream的实例化**

```
//创建 Stream方式一：通过集合
    @Test
    public void test1(){
        List<Employee> employees = EmployeeData.getEmployees();

//        default Stream<E> stream() : 返回一个顺序流
        Stream<Employee> stream = employees.stream();

//        default Stream<E> parallelStream() : 返回一个并行流
        Stream<Employee> parallelStream = employees.parallelStream();

    }

    //创建 Stream方式二：通过数组
    @Test
    public void test2(){
        int[] arr = new int[]{1,2,3,4,5,6};
        //调用Arrays类的static <T> Stream<T> stream(T[] array): 返回一个流
        IntStream stream = Arrays.stream(arr);

        Employee e1 = new Employee(1001,"Tom");
        Employee e2 = new Employee(1002,"Jerry");
        Employee[] arr1 = new Employee[]{e1,e2};
        Stream<Employee> stream1 = Arrays.stream(arr1);

    }
    //创建 Stream方式三：通过Stream的of()
    @Test
    public void test3(){

        Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5, 6);

    }
```

**中间操作**

```
//1-筛选与切片
    @Test
    public void test1(){
        List<Employee> list = EmployeeData.getEmployees();
//        filter(Predicate p)——接收 Lambda ， 从流中排除某些元素。
        Stream<Employee> stream = list.stream();
        //练习：查询员工表中薪资大于7000的员工信息
        stream.filter(e -> e.getSalary() > 7000).forEach(System.out::println);

        System.out.println();
//        limit(n)——截断流，使其元素不超过给定数量。
        list.stream().limit(3).forEach(System.out::println);
        System.out.println();

//        skip(n) —— 跳过元素，返回一个扔掉了前 n 个元素的流。若流中元素不足 n 个，则返回一个空流。与 limit(n) 互补
        list.stream().skip(3).forEach(System.out::println);

        System.out.println();
//        distinct()——筛选，通过流所生成元素的 hashCode() 和 equals() 去除重复元素

        list.add(new Employee(1010,"刘强东",40,8000));
        list.add(new Employee(1010,"刘强东",41,8000));
        list.add(new Employee(1010,"刘强东",40,8000));
        list.add(new Employee(1010,"刘强东",40,8000));
        list.add(new Employee(1010,"刘强东",40,8000));

//        System.out.println(list);

        list.stream().distinct().forEach(System.out::println);
    }

    //映射
    @Test
    public void test2(){
//        map(Function f)——接收一个函数作为参数，将元素转换成其他形式或提取信息，该函数会被应用到每个元素上，并将其映射成一个新的元素。
        List<String> list = Arrays.asList("aa", "bb", "cc", "dd");
        list.stream().map(str -> str.toUpperCase()).forEach(System.out::println);

//        练习1：获取员工姓名长度大于3的员工的姓名。
        List<Employee> employees = EmployeeData.getEmployees();
        Stream<String> namesStream = employees.stream().map(Employee::getName);
        namesStream.filter(name -> name.length() > 3).forEach(System.out::println);
        System.out.println();
        //练习2：
        Stream<Stream<Character>> streamStream = list.stream().map(StreamAPITest1::fromStringToStream);
        streamStream.forEach(s ->{
            s.forEach(System.out::println);
        });
        System.out.println();
//        flatMap(Function f)——接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有流连接成一个流。
        Stream<Character> characterStream = list.stream().flatMap(StreamAPITest1::fromStringToStream);
        characterStream.forEach(System.out::println);

    }

    //将字符串中的多个字符构成的集合转换为对应的Stream的实例
    public static Stream<Character> fromStringToStream(String str){//aa
        ArrayList<Character> list = new ArrayList<>();
        for(Character c : str.toCharArray()){
            list.add(c);
        }
       return list.stream();

    }



    @Test
    public void test3(){
        ArrayList list1 = new ArrayList();
        list1.add(1);
        list1.add(2);
        list1.add(3);

        ArrayList list2 = new ArrayList();
        list2.add(4);
        list2.add(5);
        list2.add(6);

//        list1.add(list2);
        list1.addAll(list2);
        System.out.println(list1);

    }

    //3-排序
    @Test
    public void test4(){
//        sorted()——自然排序
        List<Integer> list = Arrays.asList(12, 43, 65, 34, 87, 0, -98, 7);
        list.stream().sorted().forEach(System.out::println);
        //抛异常，原因:Employee没有实现Comparable接口
//        List<Employee> employees = EmployeeData.getEmployees();
//        employees.stream().sorted().forEach(System.out::println);


//        sorted(Comparator com)——定制排序

        List<Employee> employees = EmployeeData.getEmployees();
        employees.stream().sorted( (e1,e2) -> {

           int ageValue = Integer.compare(e1.getAge(),e2.getAge());
           if(ageValue != 0){
               return ageValue;
           }else{
               return -Double.compare(e1.getSalary(),e2.getSalary());
           }

        }).forEach(System.out::println);
    }
```

**终止操作**

```
/1-匹配与查找
    @Test
    public void test1(){
        List<Employee> employees = EmployeeData.getEmployees();

//        allMatch(Predicate p)——检查是否匹配所有元素。
//          练习：是否所有的员工的年龄都大于18
        boolean allMatch = employees.stream().allMatch(e -> e.getAge() > 18);
        System.out.println(allMatch);

//        anyMatch(Predicate p)——检查是否至少匹配一个元素。
//         练习：是否存在员工的工资大于 10000
        boolean anyMatch = employees.stream().anyMatch(e -> e.getSalary() > 10000);
        System.out.println(anyMatch);

//        noneMatch(Predicate p)——检查是否没有匹配的元素。
//          练习：是否存在员工姓“雷”
        boolean noneMatch = employees.stream().noneMatch(e -> e.getName().startsWith("雷"));
        System.out.println(noneMatch);
//        findFirst——返回第一个元素
        Optional<Employee> employee = employees.stream().findFirst();
        System.out.println(employee);
//        findAny——返回当前流中的任意元素
        Optional<Employee> employee1 = employees.parallelStream().findAny();
        System.out.println(employee1);

        List<Employee> employees = EmployeeData.getEmployees();
        // count——返回流中元素的总个数
        long count = employees.stream().filter(e -> e.getSalary() > 5000).count();
        System.out.println(count);
//        max(Comparator c)——返回流中最大值
//        练习：返回最高的工资：
        Stream<Double> salaryStream = employees.stream().map(e -> e.getSalary());
        Optional<Double> maxSalary = salaryStream.max(Double::compare);
        System.out.println(maxSalary);
//        min(Comparator c)——返回流中最小值
//        练习：返回最低工资的员工
        Optional<Employee> employee = employees.stream().min((e1, e2) -> Double.compare(e1.getSalary(), e2.getSalary()));
        System.out.println(employee);
        System.out.println();
//        forEach(Consumer c)——内部迭代
        employees.stream().forEach(System.out::println);

        //使用集合的遍历操作
        employees.forEach(System.out::println);
    }

    //2-归约
    @Test
    public void test3(){
//        reduce(T identity, BinaryOperator)——可以将流中元素反复结合起来，得到一个值。返回 T
//        练习1：计算1-10的自然数的和
        List<Integer> list = Arrays.asList(1,2,3,4,5,6,7,8,9,10);
        Integer sum = list.stream().reduce(0, Integer::sum);
        System.out.println(sum);


//        reduce(BinaryOperator) ——可以将流中元素反复结合起来，得到一个值。返回 Optional<T>
//        练习2：计算公司所有员工工资的总和
        List<Employee> employees = EmployeeData.getEmployees();
        Stream<Double> salaryStream = employees.stream().map(Employee::getSalary);
//        Optional<Double> sumMoney = salaryStream.reduce(Double::sum);
        Optional<Double> sumMoney = salaryStream.reduce((d1,d2) -> d1 + d2);
        System.out.println(sumMoney.get());

    }

    //3-收集
    @Test
    public void test4(){
//        collect(Collector c)——将流转换为其他形式。接收一个 Collector接口的实现，用于给Stream中元素做汇总的方法
//        练习1：查找工资大于6000的员工，结果返回为一个List或Set

        List<Employee> employees = EmployeeData.getEmployees();
        List<Employee> employeeList = employees.stream().filter(e -> e.getSalary() > 6000).collect(Collectors.toList());

        employeeList.forEach(System.out::println);
        System.out.println();
        Set<Employee> employeeSet = employees.stream().filter(e -> e.getSalary() > 6000).collect(Collectors.toSet());

        employeeSet.forEach(System.out::println);
    }
```

#### Optional类

是一个容器类，解决空指针问题

```
Optional op=Optional.of(T t);//必须t是非空的
//of()不能只存储单个null值
Optional op1=Optional.empty();//创建一个空的Optional实例
```



### Java9

#### **接口的私有方法**

接口的实现类不能调用自己的静态方法，只能由接口自己调用。

接口的私有方法不能在接口外调用。

#### 钻石操作符<>使用

钻石操作符<>可以在匿名实现类中使用

```
Comparator<Integer> con=new Comparator<Integer>() {
    @Override
    public int compare(Integer o1, Integer o2) {
        return 0;
    }
};
```

#### 集合工厂方法创建只读集合

java8创建只读集合：

```
list= Collections.unmodifiableList(list);//把list变为只读的集合
//用Array.aslist()得到的list也是只读的
```

java9之后：

使用of方法

```
List<Integer> l=List.of(1,2,5,6);
```

### Java10的局部变量类型推断

```
//声明变量时根据所赋的值推断变量的类型
var map=5;
var n=new ArrayList<>();
```

不适用情况：初始值为null，lambda表达式左边的函数式接口不能声明为var，方法引用，数组的静态初始化如下情况不可用`var map= {5,2,6};`，方法的返回类型，方法的参数类型，构造器的参数类型，类的属性不行

var不是关键字，可以用它命名变量

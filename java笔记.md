# 1.多线程
    * 进程：一个正在执行中的程序；
            每个进程都有一个执行顺序，该顺序是以个执行路径，或者叫一
            个控制单元
    * 线程：进程中的一i个独立的控制单元，
            线程控制着进程的执行。
            main 中定义的是主线程。
    * jvm 启动不止一个线程，还有负责垃圾回收机制的线程。

## 创建线程
### 1，第一种方式：继承Thread类
    步骤：
    1. 定义类继承Thread.
    2.复写Thread类中的run方法。
    复写run方法的目的是：将自定义的代码存储在run方法中，让线程运行。
    3.调用线程的start方法，该方法有两个作用：启动线程，调用run方法。
    理解：
    Thread 类用于描述线程，该类定义了一个功能，用于存储线程要运行的代码，该存储功能就是run 方法。
### 2. 理解线程与CPU之间的关系。
    在单核cpu中，某一时刻只能有一个程序在执行，cpu 在多个程序之间做着快速的切换，以达到看上去是同时运行的结果。
    我们可以形象的把多线程的运行行为看成是各个线程在抢夺cpu 的执行权
>具体代码如下
``` java
class Demo extends Thread{
    public  void run(){
        for (int x=0;x<60;x++){
            System.out.println("demo run ---"+x);
        }
    }
}
public class ThreadDemo {
    public static void main(String[] args) {
        Demo d = new Demo();
        d.start();
        for(int x=0;x<60;x++){
            System.out.println("hello world---"+x);
        }
    }
}
```
### 3.创建线程的第二种实现方法：实现Runable接口
    步骤：
    1.定义类实现Runnable接口
    2.覆盖Runnable中的run方法
    3.通过Thread类建立线程对象
    4.将Runnable接口的子类对象作为实际参数传递给Thread类的构造方法
        >>为什么要将Runnable接口的子类对象传递给Thread的构造函数；
        >>因为，自定义的run方法所属的对象是Runnable接口的子类对象，
        >>所以要让线程去指定指定对象的run方法。就必须明确该run方法
        >>所属对象。
    5.通过调用Thread类的start方法开启线程并调用Runnable接口子类的run方法。
### 4.实现方法与继承方法的区别
    实现方法的好处：1.避免了单继承的局限性，2.如果没有父类可以使用继承方法，3.在定义线程时建议使用实现方法，4.资源共享。
    区别：
    继承Thread:线程代码存放在Thread子类的run方法中。
    实现Runnable:线程代码存在接口的子类的run方法中。
## 线程的五种状态
    1.被创建：  继承Thread类，调用start方法。
    2.运行状态：  sleep（time）和wait()方法 进入冻结状态
    3.冻结状态：  sleep时间到和notify()进入运行状态，放弃执行资格
    4.消亡状态：  调用stop()和run方法结束进入消亡状态。
    5.临时状态(阻塞)：    在线程运行时进行判断是否具有执行权，当冻结
                        结束后还是需要进入临时状态进行判断。
## 线程常用方法
    1.getName()返回线程名称
    2.currentThread()返回当前正在执行的线程的对象引用
    3.设置线程名称：setName或者构造函数。
>> 注意：异常在复写了接口中的方法中不能抛出，只能try catch.
## 线程的安全问题
    1.原因： 当多条语句在操作一个线程共享的数据时，一个线程对多条语句只执行了一部分，还没有执行完，但是另一个线程却参与进来，导致共享数据的错误。
    2. 解决办法： 对多条操作共享数据的语句，只能让一个线程都执行完，在此期间，其他线程不可以参与执行。
    3.同步代码块：
        synchronized(对象){
            需要同步的代码块
        }
        对象如同锁，持有锁的线程可以在同步中执行。没有持有锁的线程即使获取cpu 的执行权，也进不去，因为没有获取锁。
---
### *分析依据 

    4.同步的前提：
            * 必须要有两个或者两个以上的线程。
            * 必须是多个线程使用同一个锁。
            * 必须保证同步中只能有一个线程在运行
---
    5.好处：解决了多线程的安全问题。 
        弊端： 多个线程都需要判断锁，较为消耗资源。
    6.同步有两种方法： 同步代码块，同步函数。
        同步函数使用的锁是  this。
    7.静态同步方法： 使用锁是该方法所在类的字节码文件对象。即 类名.class
## 线程安全问题之 单例设计模式
1.饿汉式
```
class Single {
    private static final Single s = new Single();
    private Single(){}
    public static Single getInstance(){
        return s;
    }
}

```
2.懒汉式
```
/*懒汉式 会出现实例的延迟加载  如果多线程访问会出现安全问题  解决：同步代码块，但会低效 ，使用双重判断可以解决低效这个问题，使用的锁是该类所属的字节码文件。*/

class Single {
    private static  Single s = null;
    private Single (){}
    public ststic Single getInstance(){
        if (s==null){    //双重判断可以
             synchronized(Single.class){
                if(s==null)
                s = new Single(); //延迟加载 对象被延迟  
            }
        }
            return s;
    }
}
```
## 等待唤醒机制
 * wait();
 * notify();唤醒 通常唤醒第一个等待的线程。
 * notifyAll();   
    1.三种方法都使用在同步中，因为要对持有监听器（锁）的线程操作。所以要使用在同步中，因为只有同步才具有锁。

    2.为什么这些操作线程的方法要定义在Object中？

    * 因为这些方法在操作同步中的线程时，都必须要标识它们所操作的线持有的锁，
    * 只有同一锁上的被等待线程，可以被同一锁上的notify唤醒。不可以对不同锁中的线程进行唤醒。
    * 也就是说，等待和唤醒必须是同一个锁。
    * 而锁可以是任意对象，所以可以被任意对象调用的方法定义在Object类中。
## Lock 锁
* jdk 1.5 中提供了多线程升级解决方案。
    * 将同步synchronized替换成现实Lock操作。
    * 将Object中的wait，notify,notifyAll替换成了Condition对象。
    * 该对象可以Lock锁进行获取。
    * 可以实现一方只唤醒另一方的操作。
## 停止线程  ------ interrupt();
* stop方法已经过时。
* 如何停止线程?
    * 原理只有一种那就是run()方法结束。
    * 开启多线程运行，运行代码通常是循环结构。只要控制循环，就可以让run()方法结束，也就是线程结束。
    * 使用boolean 标记。
* 特殊情况：
    * 当线程处于了冻结状态{使用wait(),sleep()方法}就不会读取到标记，那么线程就不会结束。
    * 当没有指定的方式让冻结的线程恢复到运行状态时，这时需要对冻结进行清除。强制让线程恢复到运行状态中来，这样就可以操作标记让线程结束。
    * Thread类提供该方法：interrupt();

## 线程中常用的方法
* join()
    * 当线程A执行到了B线程的.join()方法时，A就会等待。等B线程都执行完，A才会执行。
    * join方法 可以用来临时加入线程执行。
* setPriority()
    * 设置线程优先级
    * MAX_PRIORITY ; MIN_PRIORITY ; NORM_PRIORTY ; 三个常用级别

# 2. String 
``` java
String s2 = new String("abc");
String s1 = "abc"; //s1是一个类类型变量，"abc"是一个对象。字符串是一个特殊的对象。
                    //字符串最大特点：一旦被初始化就不可以被改变。
```
* s1和s2有什么区别？
    * s1在内存中有一个对象
    * s2在内存中有两个对象
* 常见操作：
    * 获取：
        * int length()：获取字符串的长度。
        * char charAt(int index)：根据位置获取位置上的某个字符。
        * int indexOf(int ch) : 返回的是ch在字符串中第一次出现的位置。
        * int indexOf(int ch,int fromIndex)：从fromIndex指定位置开始，获取ch在字符串中出现的位置。
        * int indexOf(String str) : 返回的是str在字符串中第一次出现的位置。
        * int indexOf(String str,int fromIndex)：从fromIndex指定位置开始，获取str在字符串中出现的位置。
        * int lastIndexOf(int ch) : 反向索引ch在字符串中第一次出现的位置。
    * 判断：
        * 字符串是否包含某个子串；boolean contains(str);
        * 字符串中是否有内容：boolean isEmpty() :判断length是否为0;
        * 字符串是否以指定内容开头：boolean startsWith(String str)
        * 字符串是否以指定内容结尾： boolean endsWith(String suffix) 
        * 判断字符串内容是否相同，复写了Object类中的equals方法：boolean equals(str);
        * 判断内容是否相同，并忽略大小写：Boolean equalsIgnoreCase();
    * 转换：
        * 将字符数组转成字符串：
            * 构造函数：String(char[])  
            String(char[],offset,count):将字符数组中的 一部风转为字符串。
            * 静态方法：static String copyValueOf(char[]);  
            staic String copyValueOf(char[],int offset,int count).  
            static String valueOf(char[]).
        * 将字符串转成字符数组： char[] toCharArray();
        * 将字节数组转为字符串：
            String（byte[]);  
            String(byte[],offset,count):将字节中的一部分转为字符串。
        * 将i字符串转成字节数组：byte[] getBytes();
        * 将基本数据类型转为字符串：  
        static String valueOf(int)  
        static Stirng valueOf(double)  
        字符串和字节数组在转化过程中，是可以指定编码表的。
    * 替换
        * String replace (oldchar,newchar);
    * 切割
        * String[] split(regex)
    * 获取字符串中的一部分
        * String substring(begin);
        * String substring(begin,end);
    * 转换，去除空格，比较
        *  将字符串转成大写或者小写:String toUpperCase();  
        String toLowerCase();
        * 将字符串两端的多余空格去掉： String trim();
        * 对两个字符串进行自然顺序比较： int compareTo(String);
# 集合
## 1. Collection
    * add方法的参数类型是Object，以便于接收任意类型对象。
    * 集合中存储的都是对象的引用(地址)。
    * Iterator 接口  获取迭代器，用于取出集合的元素。
* 迭代器 
    * 就是集合取出元素的方式。
### 1.1 List:元素是有序的，元素可以重复。应为该集合体系有索引。
    *>> ArrayList:底层的数据结构使用的是数组结构。特点：查询速度很快，但增删稍慢。线程不同步。
    *>> LinkedList: 底层使用的是链表数据结构。特点：增删速度很快，查询稍慢。
    *>> Vector:底层是数组数据结构。线程同步。被ArrayList 替代了。但其有枚举。
* 特有方法：凡是可以操作角标的方法都是该体系特有的方法。
* 增：add(index,element)  
addAll(index,Collection);
* 删： remove(index)
* 改： set(index,element);
* 查： get(index);  
subList(from,to);
ListIterator();
    * List集合特有的迭代器。ListIterator是Iterator的子接口。
    在迭代时，不可以通过集合对象的方法操作集合中的元素，因为会发生ConcurrentModificationException异常。  
    所以，在迭代器时，只能用迭代器的方法操作元素，可是Iterator方法是有限的，如果想要其他的操作 如 增加、修改等，就需要使用其子接口，ListIterator。  
    该接口只能通过List集合的listiterator方法获取。
### 1.2 Set:元素是无序的（存入和取出的顺序不一定一致），元素不可以重复。
* HashSet:底层数据结构是哈希表。线程是非同步的。
    * HashSet是如何保证元素唯一性的？
    >通过元素的两个方法，hashCode和equals来完成。  

    >如果元素的HashCode值相同，才会判断equals是否为ture.  

    >如果元素的hashCode值不同，不会调用equals。  

    >*注意： 对于判断元素是否存在，以及删除等操作，依赖的方法是元素的hashcode和equals方法。
* TreeSet:底层数据结构是二叉树。
    * 可以对Set集合中的元素进行排序。
    * 保证元素唯一性的依据：
    > compareTo 方法   return 0;
    * TreeSet 排序的方式：  
    > 1.让元素自身具备比较性。元素需要实现comparable接口，覆盖compareTo方法。这种方式也称为元素的自然顺序，或者叫做默认顺序。  

    >2.当元素自身不具备比较性，或者具备的比较性不是所需要的，这时就需要让集合自身具备比较性。
    >* 要让容器自身具备比较性，需要定义比较器，将比较器对象作为参数传递给TreeSet集合的构造函数。
    > * 当两种排序都存在时，以比较器为主。
    > * 步骤：定义一个类，实现Comparator接口，覆盖compare方法。
---
### 泛型：
* JDk1.5版本以后出现的新特性，用于解决安全问题，是一个安全机制。使用<>表示。
* 好处：
    * 1.将运行时期出现的ClassCastException问题转移到了编译时期，方便于程序员解决问题。
    * 2.避免了强制转换。
* 格式：
    * 通过<>来定义要操作的引用数据类型。
* 当使用集合时，将集合中要存储的数据类型作为参数传递到<>即可。
```java
---泛型类

class Utils <QQ>{
    private QQ q;
    public void setObject(QQ q){
        this.q = q;
    }
    public  QQ getObject(){
        return q;
    }
}
```
* 泛型类定义的泛型，在整个类中有效。如果被方法使用，那么泛型类的对象明确要操作的具体类型后，所有操作的类型就已经固定了。为了让不同的方法可以操作不同的类型，而且类型还不确定，那么可以将泛型定义在方法上。
* 静态方法不可以访问类上定义的泛型，如果静态方法操作的应用数据类型不确定，可以将泛型定义在方法上。（原因：类上的泛型会在建立对象的时候被明确，但静态泛型方法存在的时候还没有建立对象）。
```java 
---泛型方法

class Demo{
    public <T> void show(T t){
        
    }
    public <Q> void pritn(Q q){

    }
}

--- 泛型类和泛型方法

class Demo1<T>{
    public void show(T t){

    }
    public <Q> void print(Q q{

    }
    public static <W> void method(W w){

    }
}
```
* ？表示通配符： 也可以理解为占位符。
* 泛型的限定：
    * <? extends E>：可以接收E类型或者E的子类型。上限
    * <? super E>: 可以接收E类型或者E的父类型。下限
---
# Map
* 该集合存储键值对，一对一对存入。必须要保证键的唯一性。
* 方法：
    * 1.添加：
        * put(K key,V value)--添加元素，如果出现相同的键，那么添加后的值会覆盖原有键对应值，并且put方法会返回被覆盖的值。
        * putAll(Map<? extends K,? extends V> m);
    * 2.删除：
        * clear();
        * remove(Object key)
    * 3。判断：
        * containsValue(Object value)
        * containKey(Object key)
        * isEmpty();
    * 4.获取：
        * get (Object key)--可以通过get方法的返回值来判断一个键是否存在，通过返回null来判断。
        * size();
        * values();
        * Set<Map.Entry<k,v>> entrySet();--将map集合中的映射关系存入到了Set集合中。而这个关系类型就是：Map.Entry。获取到Map.Entry后就可以通过Map.Entry中的getKey()和getValue()方法获取关系中的键和值。

        * Set<k> keySet();--将map中所有的键存入到Set集合中，因为set具备迭代器，所以可以用迭代的方式取出所有的键，在通过get方法，获取每一个键对应的值。
* 三个常见使用对象：
    * Hashtable: 底层是哈希表数据结构，不可以存入Null键和null值。该集合是线程同步的。效率低
    * HashMap：底层是哈希表数据结构，允许使用null值和null键，该集合是不同步的。效率高
    * TreeMap：底层是二叉树数据结构，线程不同步。可以用于给map集合中的键进行排序。
*   >Set底层就是使用了Map集合。


 














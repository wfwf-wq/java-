JAVA 

1.hashmap 线程不同步  hashtable 同步
2.arraylist线程不同步，vector线程同步
3.vector增长为100%，arraylist为50%

## JDK1.8新特性

1.lambda表达式

```java
  //匿名内部类
  Comparator<Integer> cpt = new Comparator<Integer>() {
      @Override
      public int compare(Integer o1, Integer o2) {
          return Integer.compare(o1,o2);
      }
  };

  TreeSet<Integer> set = new TreeSet<>(cpt);

  System.out.println("=========================");

  //使用lambda表达式
  Comparator<Integer> cpt2 = (x,y) -> Integer.compare(x,y);
  TreeSet<Integer> set2 = new TreeSet<>(cpt2);

```

2.Stream流：首先对stream的操作可以分为两类，中间操作(intermediate operations)和结束操作(terminal operations):

中间操作总是会惰式执行，调用中间操作只会生成一个标记了该操作的新stream。
结束操作会触发实际计算，计算发生时会把所有中间操作积攒的操作以pipeline的方式执行，这样可以减少迭代次数。计算完成之后stream就会失效。 

就是简化一些业务的逻辑的写法，提升编码效率 

```java
List<String> list = Arrays.asList("ab", "cd", "ef");
    Stream<String> colStream = list.stream();
// 3、值
    Stream<String> stream = Stream.of("ab", "cd", "ef");
 list.stream().forEach(user -> System.out.println(user));

```



3.函数式接口，只定义了一个抽象方法的接口，更方便使用lambda表达式。 @FunctionalInterface 

4.接口可以定义默认方法。

5.可以使用：：引用构造函数或者静态方法

## 集合

### ArrayList和linkedList底层实现原理

 Arraylist是基于动态数组实现的，所以查找速度快，但是增删操作的速度会比较慢 。当我们使用new来创建一个数组，实际上是在堆上申请了一段连续的大内存，我们知道我们在java中创建数组的时候，会给他一个固定的大小，不能适应数据的动态增删，那么这个时候就有了所谓的动态数组，动态数组的本质就是，当数据超出当前数组的内存范围，会先试着比较数据长度和数组长度的1.5倍的大小，如果1.5倍大于元素长度(小于的话，则创建一个长度为数据长度的数组)，那么直接通过Arrays.copyOf得到一个新长度的数组，把原数据拷贝过去，释放掉旧的数组， 这个时候内存有很多是没有使用的，这个时候就可以执行增删操作。



 LinkedList是基于双向链表的数据结构实现的，链表是可以占用一段不连续的内存空间的。双向链表有前驱节点和后驱节点，里面存储的是上一个元素和后一个元素所在的位置，中间的黄色部分就是业务数据了，当我们需要执行插入的任务，比如第一个元素和第二个元素之间，只需要改变他们的前驱节点和后驱节点的指向就可以了，不要像动态数组那么麻烦，删除也是同样的操作，但是因为是不连续的内存空间，当需要执行查找，需要从第一个元素开始查找，直到找到我们需要的数据。

## 反射

一种解释操作，得告诉jvm，希望他做什么。



![1585791566811](D:\java-\java基础.assets\1585791566811.png)



 **Java的反射**就是利用上面第二步加载到jvm中的.class文件来进行操作的。.class文件中包含java类的所有信息，当你不知道某个类具体信息时，可以使用反射获取class，然后进行各种操作。 在java中，只要给定类的名字， 那么就可以通过反射机制来获得类的所有信息。 

### 缺点

先去方法区看这个类有没有加载过，如果没有会有一个类加载的过程，可能会在一定程度上影响性能。反射是一种解释操作，得告诉jvm，希望他做什么，比直接写代码慢。

只能在没有安全限制的环境中使用。

反射包括一些动态类型，JVM无法对这些代码进行优化。

### 反射机制

1.通过一个对象获取完整的包名和类名。

```java
package Reflect;

class Demo{
    //other codes...
}

class hello{
    public static void main(String[] args) {
        Demo demo=new Demo();
        System.out.println(demo.getClass().getName());
    }
}
//【运行结果】：Reflect.Demo
```

2.实例化Class类对象

```java
package Reflect;

class Demo{
    //other codes...
}

class hello{
    public static void main(String[] args) {
        Class<?> demo1=null;
        Class<?> demo2=null;
        Class<?> demo3=null;
        try{
            //一般尽量采用这种形式
            demo1=Class.forName("Reflect.Demo");
        }catch(Exception e){
            e.printStackTrace();
        }
        demo2=new Demo().getClass();
        demo3=Demo.class;

        System.out.println("类名称   "+demo1.getName());
        System.out.println("类名称   "+demo2.getName());
        System.out.println("类名称   "+demo3.getName());
    }
}
//【运行结果】：
//类名称   Reflect.Demo
//类名称   Reflect.Demo
//类名称   Reflect.Demo
```

3.通过Class实例化其他类的对象

```java
package Reflect;

class Person{
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }
    @Override
    public String toString(){
        return "["+this.name+"  "+this.age+"]";
    }
    private String name;
    private int age;
}

class hello{
    public static void main(String[] args) {
        Class<?> demo=null;
        try{
            demo=Class.forName("Reflect.Person");
        }catch (Exception e) {
            e.printStackTrace();
        }
        Person per=null;
        try {
            per=(Person)demo.newInstance();
        } catch (InstantiationException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        per.setName("Rollen");
        per.setAge(20);
        System.out.println(per);
    }
}
//【运行结果】：
//[Rollen  20]
```

但是不能自定义有参构造函数。



4.通过反射调用其他类的中的构造函数

```java
package Reflect;

import java.lang.reflect.Constructor;

class Person{
    public Person() {   

    }
    public Person(String name){
        this.name=name;
    }
    public Person(int age){
        this.age=age;
    }
    public Person(String name, int age) {
        this.age=age;
        this.name=name;
    }
    public String getName() {
        return name;
    }
    public int getAge() {
        return age;
    }
    @Override
    public String toString(){
        return "["+this.name+"  "+this.age+"]";
    }
    private String name;
    private int age;
}

class hello{
    public static void main(String[] args) {
        Class<?> demo=null;
        try{
            demo=Class.forName("Reflect.Person");
        }catch (Exception e) {
            e.printStackTrace();
        }
        Person per1=null;
        Person per2=null;
        Person per3=null;
        Person per4=null;
        //取得全部的构造函数
        Constructor<?> cons[]=demo.getConstructors();
        try{
            per1=(Person)cons[0].newInstance();
            per2=(Person)cons[1].newInstance("Rollen");
            per3=(Person)cons[2].newInstance(20);
            per4=(Person)cons[3].newInstance("Rollen",20);
        }catch(Exception e){
            e.printStackTrace();
        }
        System.out.println(per1);
        System.out.println(per2);
        System.out.println(per3);
        System.out.println(per4);
    }
}
//【运行结果】：
//[null  0]
//[Rollen  0]
//[null  20]
//[Rollen  20]
```

5.可以调用其他类的接口。

6.取得父类（getSuperclass）

7.取得其它类全部属性

```java
class hello {
    public static void main(String[] args) {
        Class<?> demo = null;
        try {
            demo = Class.forName("Reflect.Person");
        } catch (Exception e) {
            e.printStackTrace();
        }
        System.out.println("===============本类属性========================");
        // 取得本类的全部属性
        Field[] field = demo.getDeclaredFields();
        for (int i = 0; i < field.length; i++) {
            // 权限修饰符
            int mo = field[i].getModifiers();
            String priv = Modifier.toString(mo);
            // 属性类型
            Class<?> type = field[i].getType();
            System.out.println(priv + " " + type.getName() + " "
                    + field[i].getName() + ";");
        }
        System.out.println("===============实现的接口或者父类的属性========================");
        // 取得实现的接口或者父类的属性
        Field[] filed1 = demo.getFields();
        for (int j = 0; j < filed1.length; j++) {
            // 权限修饰符
            int mo = filed1[j].getModifiers();
            String priv = Modifier.toString(mo);
            // 属性类型
            Class<?> type = filed1[j].getType();
            System.out.println(priv + " " + type.getName() + " "
                    + filed1[j].getName() + ";");
        }
    }
}
//【运行结果】：
//===============本类属性========================
//private java.lang.String sex;
//===============实现的接口或者父类的属性========================
//public static final java.lang.String name;
//public static final int age;
```



```java
   getName()：获得类的完整名字。
   
　　getFields()：获得类的public类型的属性。
　　
　　getDeclaredFields()：获得类的所有属性。包括private 声明的和继承类
　　
　　getMethods()：获得类的public类型的方法。
　　
　　getDeclaredMethods()：获得类的所有方法。包括private 声明的和继承类
　　
　　getMethod(String name, Class[] parameterTypes)：获得类的特定方法，name参数指定方法的名字，parameterTypes 参数指定方法的参数类型。
　　
　　getConstructors()：获得类的public类型的构造方法。
　　
　　getConstructor(Class[] parameterTypes)：获得类的特定构造方法，parameterTypes 参数指定构造方法的参数类型。
　　
　　newInstance()：通过类的不带参数的构造方法创建这个类的一个对象。

```



## String、StringBuffer、StringBuilder

StringBuffer（很多方法是synchronized）和StringBuilder（线程不安全，速度快）

 String所有属性都被final修饰、私有的并且没有提供修改方法。 

String不可变：

​	字符串线程池的需求。

​	安全性考虑。

​	hash缓存。保持hash码的唯一性。





## 值传递和引用传递

```java
/**
 * 值传递和引用传递
 * @author: 陌溪
 * @create: 2020-03-14-18:25
 */
class Person {
    private Integer id;
    private String personName;

    public Person(String personName) {
        this.personName = personName;
    }
}
public class TransferValueDemo {

    public void changeValue1(int age) {
        age = 30;
    }

    public void changeValue2(Person person) {
        person.setPersonName("XXXX");
    }
    public void changeValue3(String str) {
        str = "XXX";
    }

    public static void main(String[] args) {
        TransferValueDemo test = new TransferValueDemo();

        // 定义基本数据类型
        int age = 20;
        test.changeValue1(age);
        System.out.println("age ----" + age);

        // 实例化person类
        Person person = new Person("abc");
        test.changeValue2(person);
        System.out.println("personName-----" + person.getPersonName());

        // String
        String str = "abc";
        test.changeValue3(str);
        System.out.println("string-----" + str);

    }
}
```

最后输出结果

```java
age ----20
personName-----XXXX
string-----abc
```



### changeValue1的执行过程

八种基本数据类型，在栈里面分配内存，属于值传递

```
栈管运行，堆管存储
```

当们执行 changeValue1的时候，因为int是基本数据类型，所以传递的是int = 20这个值，相当于传递的是一个副本，main方法里面的age并没有改变，因此输出的结果 age还是20，属于值传递

![image-20200314185317851](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/5_TransferValue%E6%98%AF%E4%BB%80%E4%B9%88/images/image-20200314185317851.png)

### changeValue2的执行过程

因为Person是属于对象，传递的是内存地址，当执行changeValue2的时候，会改变内存中的Person的值，属于引用传递，两个指针都是指向同一个地址

![image-20200314185528034](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/5_TransferValue%E6%98%AF%E4%BB%80%E4%B9%88/images/image-20200314185528034.png)

### changeValue3的执行过程

String不属于基本数据类型，但是为什么执行完成后，还是abc呢？

这是因为String的特殊性，当我们执行String str = "abc"的时候，它会把 `abc` 放入常量池中

![image-20200314190021466](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/5_TransferValue%E6%98%AF%E4%BB%80%E4%B9%88/images/image-20200314190021466.png)

当我们执行changeValue3的时候，会重新新建一个xxx，并没有销毁abc，然后指向xxx，然后最后我们输出的是main中的引用，还是指向的abc，因此最后输出结果还是abc

## 拷贝

假设B复制了A，当A修改时，如果B变了就是浅拷贝，如果没变就是深拷贝。

## Integer和int

 Ingeter是int的包装类，int的初值为0，Ingeter的初值为null。 

int是基本类型，Integer是对象

 Integer变量必须实例化后才能使用，而int变量不需要  。

 Integer实际是对象的引用，当new一个Integer时，实际上是生成一个指针指向此对象；而int则是直接存储数据值  

Integer包装类的数据存储在堆内存中。

```
Integer.parseInt（进制转换）
Integer.toBinaryString（转二进制）
Integer.MIN_VALUE
Integer.MAX_VALUE
```

## AtomicInteger

incrementAndGet(); // ++i
getAndIncrement(); // i++
decrementAndGet(); // --i
getAndDecrement(); // i--



### 原理

 其内部存储了`volatile`修饰的`value`作为具体值，`volatile`避免指令重排，保证了该`value`的多线程可见性，即修改时其它线程能获取到最新值。 

 以`incrementAndGet()`方法为例 

public final int getAndAddInt(Object paramObject, long paramLong, int paramInt) {
    int i;
    do {
        i = getIntVolatile(paramObject, paramLong); // 获取主存的值
    } while (!compareAndSwapInt(paramObject, paramLong, i, i + paramInt));
            // CAS操作，从内存中取出偏移量，比较数据与期望值一致，并修改新值。
    return i;
}

 CAS是乐观锁的一种实现方式，利用CPU自身特性保证原子性，避免类似悲观锁的线程切换，性能较强。 

## hashcode

 **hashcode代表对象的地址说的是对象在hash表中的位置，物理地址说的对象存放在内存中的地址** 

 **HashCode的存在主要是为了查找的快捷性，HashCode是用来在散列存储结构中确定对象的存储地址的(后半句说的用hashcode来代表对象就是在hash表中的位置)** 



## Hashmap

1.7使用头插法，在扩容的过程，resize方法调用transfer方法把entry进行了一个rehash，过程中可能会造成链表的循环，出现死循环。同时多个线程并发情况下，不能保证线程安全。

1.8变成了数组+链表+红黑树这么一个结构，尾插法不会改变数据插入的顺序。Entry结点变成Node结点，整个put过程也做了优化，





不用数组

​		扩容很不方便。

​		ArrayList的源码，看add和remove   size会变

### LinkedList

查询很慢。可以插入删除简单

### hashmap

abstractmap  抽象骨架类

![1584276029707](D:\java-\java基础.assets\1584276029707.png)

### Hash是什么？

散列：输出是固定长度

· 目的寻找数组下标

1.求余hash%16得到下标  hash&（n-1）

max=1111=16-1

min=0  

![1584278088567](D:\java-\java基础.assets\1584278088567.png)

![1584278265676](D:\java-\java基础.assets\1584278265676.png)

![1584279008971](D:\java-\java基础.assets\1584279008971.png)



![1584279023838](D:\java-\java基础.assets\1584279023838.png)

### 扩容

默认数组大小16，元素超过12个，就要扩容一倍。

换一个大的数组。

### 一致性哈希算法

#### hash算法缺陷

**分布式架构缓存处理：（如果有4台缓存服务器）**

Hash算法分散数据存储hash（n）%4=

同时可以快速查找数据而不用遍历所有的服务器。

如果加一台缓存服务器 就得重新计算了。于是得使用一致性hash算法。

#### hash环

一致性hash算法是对2^32取模，得到一个值K1，hash环上顺时针找到服务器节点。因为一致性hash是对服务器的负载均衡问题的，服务器IP是32位，所以对2^32取模。

![1586590816782](D:\java-\java基础.assets\1586590816782.png)

**优势：**如果B失效了，可以迁移至C。

通过减少影响范围的方式解决了增减服务器导致的数据散列问题，从而解决分布式环境下的负载均衡问题。

### 为什么使用红黑树？

红黑树插入修改数据时会需要最多两次旋转使其达到平衡 

 平衡AVL树可能需要O（log n）旋转、

 AVL树是更加严格的平衡，因此可以提供更快的查找速度，一般读取查找密集型任务，适用AVL树。 

### HashTable

synchronized多线程共享安全问题，修饰方法

HashTable线程安全。多线程不用HashTable，效率低，锁太重了。

### ConcurrentHashMap 

结构--HashMap一样   --数组+链表+红黑树

只会锁住我目前获取到的Entry结点，使用CAS+synchronized，支持并发度更高。

线程安全，

多线程，有多个栈.

线程安全：CAS和synchronized，synchronized修饰静态代码块。

1.7版本有一个segment锁（继承与reentrantlock），分片锁机制。

1.8之后改为和hashmap一样的数据结构。

### Collections.synchronizedMap

 对象排斥锁mutex  	

## 接口和抽象类

### 接口

接口是一种特殊的抽象类，只包含抽象方法。不可以定义成员变量。一个类可以**实现**接口，重写接口所有方法。一个类可以实现多个接口。

一个接口类型的变量可以引用实现了改接口的类的对象，通过该变量可以调用该接口中定义的方法。

接口也可以继承接口、



### 抽象类和接口的区别

1. 定义抽象类的关键字是abstract class，定义接口的关键字interface。
2. 继承抽象类的关键字是extends，实现接口的关键字是implements。
3. 继承抽象类支持单继承，实现接口可以多实现。
4. 抽象类中可以有构造方法，而接口中不可以有构造方法。
5. 抽象类中可以有成员变量，接口中只可以有常量。
6. 抽象类中可以有成员方法，接口中只可以有抽象方法。
7. 抽象类中增加方法可以不影响子类，接口中增加方法通常影响子类。
8. 从jdk1.8开始，允许接口中出现非抽象方法，但需要使用default关键字修饰。



## 集合类不安全

### Arraylist线程不安全

1、当我们执行下面语句的时候，底层进行了什么操作

```
new ArrayList<Integer>();
```

底层创建了一个空的数组，伴随着初始值为10

当执行add方法后，如果超过了10，那么会进行扩容，扩容的大小为原值的一半，也就是5个，使用下列方法扩容

```
Arrays.copyOf(elementData, netCapacity)
```

####  单线程环境下

单线程环境的ArrayList是不会有问题的

```
public class ArrayListNotSafeDemo {
    public static void main(String[] args) {

        List<String> list = new ArrayList<>();
        list.add("a");
        list.add("b");
        list.add("c");

        for(String element : list) {
            System.out.println(element);
        }
    }
}
```

####  多线程环境

为什么ArrayList是线程不安全的？因为在进行写操作的时候，方法上为了保证并发性，是没有添加synchronized修饰，所以并发写的时候，就会出现问题

![image-20200312202720715](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/4_ArrayList%E4%B8%BA%E4%BB%80%E4%B9%88%E7%BA%BF%E7%A8%8B%E4%B8%8D%E5%AE%89%E5%85%A8/ArrayList%E7%BA%BF%E7%A8%8B%E4%B8%8D%E5%AE%89%E5%85%A8%E7%9A%84%E4%B8%BE%E4%BE%8B/images/image-20200312202720715.png)

当我们同时启动30个线程去操作List的时候

```java
/**
 * 集合类线程不安全举例
 * @author: 陌溪
 * @create: 2020-03-12-20:15
 */
public class ArrayListNotSafeDemo {
    public static void main(String[] args) {

        List<String> list = new ArrayList<>();

        for (int i = 0; i < 30; i++) {
            new Thread(() -> {
                list.add(UUID.randomUUID().toString().substring(0, 8));
                System.out.println(list);
            }, String.valueOf(i)).start();
        }
    }
}
```

这个时候出现了错误，也就是java.util.ConcurrentModificationException

![image-20200312205142763](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/4_ArrayList%E4%B8%BA%E4%BB%80%E4%B9%88%E7%BA%BF%E7%A8%8B%E4%B8%8D%E5%AE%89%E5%85%A8/ArrayList%E7%BA%BF%E7%A8%8B%E4%B8%8D%E5%AE%89%E5%85%A8%E7%9A%84%E4%B8%BE%E4%BE%8B/images/image-20200312205142763.png)

这个异常是 并发修改的异常

#### 解决方案

**方案一：Vector**

第一种方法，就是不用ArrayList这种不安全的List实现类，而采用Vector，线程安全的

关于Vector如何实现线程安全的，而是在方法上加了锁，即synchronized

![image-20200312210401865](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/4_ArrayList%E4%B8%BA%E4%BB%80%E4%B9%88%E7%BA%BF%E7%A8%8B%E4%B8%8D%E5%AE%89%E5%85%A8/ArrayList%E7%BA%BF%E7%A8%8B%E4%B8%8D%E5%AE%89%E5%85%A8%E7%9A%84%E4%B8%BE%E4%BE%8B/images/image-20200312210401865.png)

这样就每次只能够一个线程进行操作，所以不会出现线程不安全的问题，但是因为加锁了，导致并发性基于下降

 **方案二：Collections.synchronized()**

```
List<String> list = Collections.synchronizedList(new ArrayList<>());
```

采用Collections集合工具类，在ArrayList外面包装一层 同步 机制

 **方案三：采用JUC里面的方法**

CopyOnWriteArrayList：写时复制，主要是一种读写分离的思想

写时复制，CopyOnWrite容器即写时复制的容器，往一个容器中添加元素的时候，不直接往当前容器Object[]添加，而是先将Object[]进行copy，复制出一个新的容器object[] newElements，然后新的容器Object[] newElements里添加原始，添加元素完后，在将原容器的引用指向新的容器 setArray(newElements)；这样做的好处是可以对copyOnWrite容器进行并发的度，而不需要加锁，因为当前容器不需要添加任何元素。所以CopyOnWrite容器也是一种读写分离的思想，读和写不同的容器

就是写的时候，把ArrayList扩容一个出来，然后把值填写上去，在通知其他的线程，ArrayList的引用指向扩容后的

查看底层add方法源码

```
    public boolean add(E e) {
        final ReentrantLock lock = this.lock;
        lock.lock();
        try {
            Object[] elements = getArray();
            int len = elements.length;
            Object[] newElements = Arrays.copyOf(elements, len + 1);
            newElements[len] = e;
            setArray(newElements);
            return true;
        } finally {
            lock.unlock();
        }
    }
```

首先需要加锁

```
final ReentrantLock lock = this.lock;
lock.lock();
```

然后在末尾扩容一个单位

```
Object[] elements = getArray();
int len = elements.length;
Object[] newElements = Arrays.copyOf(elements, len + 1);
```

然后在把扩容后的空间，填写上需要add的内容

```
newElements[len] = e;
```

最后把内容set到Array中



### HashSet线程不安全

**CopyOnWriteArraySet**

底层还是使用CopyOnWriteArrayList进行实例化

![image-20200312221602095](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/4_ArrayList%E4%B8%BA%E4%BB%80%E4%B9%88%E7%BA%BF%E7%A8%8B%E4%B8%8D%E5%AE%89%E5%85%A8/ArrayList%E7%BA%BF%E7%A8%8B%E4%B8%8D%E5%AE%89%E5%85%A8%E7%9A%84%E4%B8%BE%E4%BE%8B/images/image-20200312221602095.png)

**HashSet底层结构**

同理HashSet的底层结构就是HashMap

![image-20200312221735178](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/4_ArrayList%E4%B8%BA%E4%BB%80%E4%B9%88%E7%BA%BF%E7%A8%8B%E4%B8%8D%E5%AE%89%E5%85%A8/ArrayList%E7%BA%BF%E7%A8%8B%E4%B8%8D%E5%AE%89%E5%85%A8%E7%9A%84%E4%B8%BE%E4%BE%8B/images/image-20200312221735178.png)

但是为什么我调用 HashSet.add()的方法，只需要传递一个元素，而HashMap是需要传递key-value键值对？

首先我们查看hashSet的add方法

```java
    public boolean add(E e) {
        return map.put(e, PRESENT)==null;
    }
```

我们能发现但我们调用add的时候，存储一个值进入map中，只是作为key进行存储，而value存储的是一个Object类型的常量，也就是说HashSet只关心key，而不关心value



###  HashMap线程不安全

同理HashMap在多线程环境下，也是不安全的

```java
    public static void main(String[] args) {

        Map<String, String> map = new HashMap<>();

        for (int i = 0; i < 30; i++) {
            new Thread(() -> {
                map.put(Thread.currentThread().getName(), UUID.randomUUID().toString().substring(0, 8));
                System.out.println(map);
            }, String.valueOf(i)).start();
        }
    }
```

#### 解决方法

1、使用Collections.synchronizedMap(new HashMap<>());

2、使用 ConcurrentHashMap

```java
Map<String, String> map = new ConcurrentHashMap<>();
```

## JUC包

###  CountDownLatch

让一些线程阻塞直到另一些线程完成一系列操作才被唤醒

CountDownLatch主要有两个方法，当一个或多个线程调用await方法时，调用线程就会被阻塞。其它线程调用CountDown方法会将计数器减1（调用CountDown方法的线程不会被阻塞），当计数器的值变成零时，因调用await方法被阻塞的线程会被唤醒，继续执行



#### 场景

现在有这样一个场景，假设一个自习室里有7个人，其中有一个是班长，班长的主要职责就是在其它6个同学走了后，关灯，锁教室门，然后走人，因此班长是需要最后一个走的，那么有什么方法能够控制班长这个线程是最后一个执行，而其它线程是随机执行的

#### 解决方案

这个时候就用到了CountDownLatch，计数器了。我们一共创建6个线程，然后计数器的值也设置成6

```java
// 计数器
CountDownLatch countDownLatch = new CountDownLatch(6);
```

然后每次学生线程执行完，就让计数器的值减1

```java
for (int i = 0; i <= 6; i++) {
    new Thread(() -> {
        System.out.println(Thread.currentThread().getName() + "\t 上完自习，离开教室");
        countDownLatch.countDown();
    }, String.valueOf(i)).start();
}
```

最后我们需要通过CountDownLatch的await方法来控制班长主线程的执行，这里 countDownLatch.await()可以想成是一道墙，只有当计数器的值为0的时候，墙才会消失，主线程才能继续往下执行

```java
countDownLatch.await();

System.out.println(Thread.currentThread().getName() + "\t 班长最后关门");
```

不加CountDownLatch的执行结果，我们发现main线程提前已经执行完成了

```
1	 上完自习，离开教室
0	 上完自习，离开教室
main	 班长最后关门
2	 上完自习，离开教室
3	 上完自习，离开教室
4	 上完自习，离开教室
5	 上完自习，离开教室
6	 上完自习，离开教室
```

引入CountDownLatch后的执行结果，我们能够控制住main方法的执行，这样能够保证前提任务的执行

```
0	 上完自习，离开教室
2	 上完自习，离开教室
4	 上完自习，离开教室
1	 上完自习，离开教室
5	 上完自习，离开教室
6	 上完自习，离开教室
3	 上完自习，离开教室
main	 班长最后关门
```



完整代码



```java
package com.moxi.interview.study.thread;

import java.util.concurrent.CountDownLatch;

/**
 * @author: 陌溪
 * @create: 2020-03-15-19:03
 */
public class CountDownLatchDemo {
    public static void main(String[] args) throws InterruptedException {

        // 计数器
        CountDownLatch countDownLatch = new CountDownLatch(6);

        for (int i = 0; i <= 6; i++) {
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName() + "\t 上完自习，离开教室");
                countDownLatch.countDown();
            }, String.valueOf(i)).start();
        }

        countDownLatch.await();

        System.out.println(Thread.currentThread().getName() + "\t 班长最后关门");
    }
}
```

#### 和Thread.join的区别

join是必须等待线程结束以后才能继续向下，不断的检查thread是否存活，如果存活，那么让当前线程一直wait，直到thread线程终止，线程的this.notifyAll 就会被调用。

countdownlatch原理是只要计数器为0了 就可以继续向下执行。比如A需要BC线程中执行完一部分就执行，join就没办法实现。



###  CyclicBarrier

和CountDownLatch相反，需要集齐七颗龙珠，召唤神龙。也就是做加法，开始是0，加到某个值的时候就执行

CyclicBarrier的字面意思就是可循环（cyclic）使用的屏障（Barrier）。它要求做的事情是，让一组线程到达一个屏障（也可以叫同步点）时被阻塞，直到最后一个线程到达屏障时，屏障才会开门，所有被屏障拦截的线程才会继续干活，线程进入屏障通过CyclicBarrier的await方法

####  案例

集齐7个龙珠，召唤神龙的Demo，我们需要首先创建CyclicBarrier

```java
/**
* 定义一个循环屏障，参数1：需要累加的值，参数2 需要执行的方法
*/
CyclicBarrier cyclicBarrier = new CyclicBarrier(7, () -> {
	System.out.println("召唤神龙");
});
```

然后同时编写七个线程，进行龙珠收集，但一个线程收集到了的时候，我们需要让他执行await方法，等待到7个线程全部执行完毕后，我们就执行原来定义好的方法

```java
        for (int i = 0; i < 7; i++) {
            final Integer tempInt = i;
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName() + "\t 收集到 第" + tempInt + "颗龙珠");

                try {
                    // 先到的被阻塞，等全部线程完成后，才能执行方法
                    cyclicBarrier.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }, String.valueOf(i)).start();
        }
```

完整代码

```java
/**
 * CyclicBarrier循环屏障
 *
 * @author: 陌溪
 * @create: 2020-03-16-14:40
 */
public class CyclicBarrierDemo {


    public static void main(String[] args) {
        /**
         * 定义一个循环屏障，参数1：需要累加的值，参数2 需要执行的方法
         */
        CyclicBarrier cyclicBarrier = new CyclicBarrier(7, () -> {
            System.out.println("召唤神龙");
        });

        for (int i = 0; i < 7; i++) {
            final Integer tempInt = i;
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName() + "\t 收集到 第" + tempInt + "颗龙珠");

                try {
                    // 先到的被阻塞，等全部线程完成后，才能执行方法
                    cyclicBarrier.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }, String.valueOf(i)).start();
        }
    }
}
```

### Semaphore：信号量

信号量主要用于两个目的

- 一个是用于共享资源的互斥使用
- 另一个用于并发线程数的控制

我们模拟一个抢车位的场景，假设一共有6个车，3个停车位

那么我们首先需要定义信号量为3，也就是3个停车位

```java
/**
* 初始化一个信号量为3，默认是false 非公平锁， 模拟3个停车位
*/
Semaphore semaphore = new Semaphore(3, false);
```

然后我们模拟6辆车同时并发抢占停车位，但第一个车辆抢占到停车位后，信号量需要减1

```java
// 代表一辆车，已经占用了该车位
semaphore.acquire(); // 抢占
```

同时车辆假设需要等待3秒后，释放信号量

```java
// 每个车停3秒
try {
	TimeUnit.SECONDS.sleep(3);
} catch (InterruptedException e) {
	e.printStackTrace();
}
```

最后车辆离开，释放信号量

```java
// 释放停车位
semaphore.release();
```

完整代码

```java
/**
 * 信号量Demo
 * @author: 陌溪
 * @create: 2020-03-16-15:01
 */
public class SemaphoreDemo {

    public static void main(String[] args) {

        /**
         * 初始化一个信号量为3，默认是false 非公平锁， 模拟3个停车位
         */
        Semaphore semaphore = new Semaphore(3, false);

        // 模拟6部车
        for (int i = 0; i < 6; i++) {
            new Thread(() -> {
                try {
                    // 代表一辆车，已经占用了该车位
                    semaphore.acquire(); // 抢占

                    System.out.println(Thread.currentThread().getName() + "\t 抢到车位");

                    // 每个车停3秒
                    try {
                        TimeUnit.SECONDS.sleep(3);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    System.out.println(Thread.currentThread().getName() + "\t 离开车位");

                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    // 释放停车位
                    semaphore.release();
                }
            }, String.valueOf(i)).start();
        }
    }
}
```

运行结果

```
0	 抢到车位
2	 抢到车位
1	 抢到车位
2	 离开车位
1	 离开车位
3	 抢到车位
0	 离开车位
4	 抢到车位
5	 抢到车位
4	 离开车位
3	 离开车位
5	 离开车位
```

看运行结果能够发现，0 2 1 车辆首先抢占到了停车位，然后等待3秒后，离开，然后后面 3 4 5 又抢到了车位

# 阻塞队列（BlockingQueue）

## 队列

队列就可以想成是一个数组，从一头进入，一头出去，排队买饭

##  阻塞队列

BlockingQueue 阻塞队列，排队拥堵，首先它是一个队列，而一个阻塞队列在数据结构中所起的作用大致如下图所示：

![image-20200316152120272](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/8_%E9%98%BB%E5%A1%9E%E9%98%9F%E5%88%97/images/image-20200316152120272.png)

线程1往阻塞队列中添加元素，而线程2从阻塞队列中移除元素

- `当阻塞队列是空时，从队列中获取元素的操作将会被阻塞`
  - 当蛋糕店的柜子空的时候，无法从柜子里面获取蛋糕
- `当阻塞队列是满时，从队列中添加元素的操作将会被阻塞`
  - 当蛋糕店的柜子满的时候，无法继续向柜子里面添加蛋糕了

也就是说 试图从空的阻塞队列中获取元素的线程将会被阻塞，直到其它线程往空的队列插入新的元素

同理，试图往已经满的阻塞队列中添加新元素的线程，直到其它线程往满的队列中移除一个或多个元素，或者完全清空队列后，使队列重新变得空闲起来，并后续新增

## 为什么要用？

去海底捞吃饭，大厅满了，需要进候厅等待，但是这些等待的客户能够对商家带来利润，因此我们非常欢迎他们阻塞

在多线程领域：所谓的阻塞，在某些清空下会挂起线程（即阻塞），一旦条件满足，被挂起的线程又会自动唤醒

### 为什么需要BlockingQueue

好处是我们不需要关心什么时候需要阻塞线程，什么时候需要唤醒线程，因为这一切BlockingQueue都帮你一手包办了

在concurrent包发布以前，在多线程环境下，我们每个程序员都必须自己取控制这些细节，尤其还要兼顾效率和线程安全，而这会给我们的程序带来不小的复杂度。

## 架构

```
// 你用过List集合类

// ArrayList集合类熟悉么？

// 还用过 CopyOnWriteList  和 BlockingQueue
```

BlockingQueue阻塞队列是属于一个接口，底下有七个实现类

- ArrayBlockQueue：由数组结构组成的有界阻塞队列
- LinkedBlockingQueue：由链表结构组成的有界（但是默认大小 Integer.MAX_VALUE）的阻塞队列
  - 有界，但是界限非常大，相当于无界，可以当成无界
- PriorityBlockQueue：支持优先级排序的无界阻塞队列
- DelayQueue：使用优先级队列实现的延迟无界阻塞队列
- SynchronousQueue：不存储元素的阻塞队列，也即单个元素的队列
  - 生产一个，消费一个，不存储元素，不消费不生产
- LinkedTransferQueue：由链表结构组成的无界阻塞队列
- LinkedBlockingDeque：由链表结构组成的双向阻塞队列

这里需要掌握的是：ArrayBlockQueue、LinkedBlockingQueue、SynchronousQueue

## BlockingQueue核心方法

![image-20200316154442756](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/8_%E9%98%BB%E5%A1%9E%E9%98%9F%E5%88%97/images/image-20200316154442756.png)



| 抛出异常 | 当阻塞队列满时：在往队列中add插入元素会抛出 IIIegalStateException：Queue full 当阻塞队列空时：再往队列中remove移除元素，会抛出NoSuchException |
| -------- | ------------------------------------------------------------ |
| 特殊性   | 插入方法，成功true，失败false 移除方法：成功返回出队列元素，队列没有就返回空 |
| 一直阻塞 | 当阻塞队列满时，生产者继续往队列里put元素，队列会一直阻塞生产线程直到put数据or响应中断退出， 当阻塞队列空时，消费者线程试图从队列里take元素，队列会一直阻塞消费者线程直到队列可用。 |
| 超时退出 | 当阻塞队列满时，队里会阻塞生产者线程一定时间，超过限时后生产者线程会退出 |

### 抛出异常组

但执行add方法，向已经满的ArrayBlockingQueue中添加元素时候，会抛出异常

```
// 阻塞队列，需要填入默认值
BlockingQueue<String> blockingQueue = new ArrayBlockingQueue<>(3);

System.out.println(blockingQueue.add("a"));
System.out.println(blockingQueue.add("b"));
System.out.println(blockingQueue.add("c"));

System.out.println(blockingQueue.add("XXX"));
```

运行后：

```
true
true
true
Exception in thread "main" java.lang.IllegalStateException: Queue full
	at java.util.AbstractQueue.add(AbstractQueue.java:98)
	at java.util.concurrent.ArrayBlockingQueue.add(ArrayBlockingQueue.java:312)
	at com.moxi.interview.study.queue.BlockingQueueDemo.main(BlockingQueueDemo.java:25)
```

同时如果我们多取出元素的时候，也会抛出异常，我们假设只存储了3个值，但是取的时候，取了四次

```
// 阻塞队列，需要填入默认值
BlockingQueue<String> blockingQueue = new ArrayBlockingQueue<>(3);
System.out.println(blockingQueue.add("a"));
System.out.println(blockingQueue.add("b"));
System.out.println(blockingQueue.add("c"));

System.out.println(blockingQueue.remove());
System.out.println(blockingQueue.remove());
System.out.println(blockingQueue.remove());
System.out.println(blockingQueue.remove());
```

那么出现异常

```
true
true
true
a
b
c
Exception in thread "main" java.util.NoSuchElementException
	at java.util.AbstractQueue.remove(AbstractQueue.java:117)
	at com.moxi.interview.study.queue.BlockingQueueDemo.main(BlockingQueueDemo.java:30)
```

### 布尔类型组

我们使用 offer的方法，添加元素时候，如果阻塞队列满了后，会返回false，否者返回true

同时在取的时候，如果队列已空，那么会返回null

```java
BlockingQueue blockingQueue = new ArrayBlockingQueue(3);

System.out.println(blockingQueue.offer("a"));
System.out.println(blockingQueue.offer("b"));
System.out.println(blockingQueue.offer("c"));
System.out.println(blockingQueue.offer("d"));

System.out.println(blockingQueue.poll());
System.out.println(blockingQueue.poll());
System.out.println(blockingQueue.poll());
System.out.println(blockingQueue.poll());
```

运行结果

```
true
true
true
false
a
b
c
null
```

### 阻塞队列组

我们使用 put的方法，添加元素时候，如果阻塞队列满了后，添加消息的线程，会一直阻塞，直到队列元素减少，会被清空，才会唤醒

一般在消息中间件，比如RabbitMQ中会使用到，因为需要保证消息百分百不丢失，因此只有让它阻塞

```java
BlockingQueue<String> blockingQueue = new ArrayBlockingQueue<>(3);
blockingQueue.put("a");
blockingQueue.put("b");
blockingQueue.put("c");
System.out.println("================");

blockingQueue.take();
blockingQueue.take();
blockingQueue.take();
blockingQueue.take();
```

同时使用take取消息的时候，如果内容不存在的时候，也会被阻塞

###  不见不散组

offer( ) ， poll 加时间

使用offer插入的时候，需要指定时间，如果2秒还没有插入，那么就放弃插入

```java
BlockingQueue<String> blockingQueue = new ArrayBlockingQueue<>(3);
System.out.println(blockingQueue.offer("a", 2L, TimeUnit.SECONDS));
System.out.println(blockingQueue.offer("b", 2L, TimeUnit.SECONDS));
System.out.println(blockingQueue.offer("c", 2L, TimeUnit.SECONDS));
System.out.println(blockingQueue.offer("d", 2L, TimeUnit.SECONDS));
```

同时取的时候也进行判断

```java
System.out.println(blockingQueue.poll(2L, TimeUnit.SECONDS));
System.out.println(blockingQueue.poll(2L, TimeUnit.SECONDS));
System.out.println(blockingQueue.poll(2L, TimeUnit.SECONDS));
System.out.println(blockingQueue.poll(2L, TimeUnit.SECONDS));
```

如果2秒内取不出来，那么就返回null



##  SynchronousQueue

SynchronousQueue没有容量，与其他BlockingQueue不同，SynchronousQueue是一个不存储的BlockingQueue，每一个put操作必须等待一个take操作，否者不能继续添加元素

下面我们测试SynchronousQueue添加元素的过程

首先我们创建了两个线程，一个线程用于生产，一个线程用于消费

生产的线程分别put了 A、B、C这三个字段

```java
BlockingQueue<String> blockingQueue = new SynchronousQueue<>();

new Thread(() -> {
    try {       
        System.out.println(Thread.currentThread().getName() + "\t put A ");
        blockingQueue.put("A");
       
        System.out.println(Thread.currentThread().getName() + "\t put B ");
        blockingQueue.put("B");        
        
        System.out.println(Thread.currentThread().getName() + "\t put C ");
        blockingQueue.put("C");        
        
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}, "t1").start();
```

消费线程使用take，消费阻塞队列中的内容，并且每次消费前，都等待5秒

```java
        new Thread(() -> {
            try {

                try {
                    TimeUnit.SECONDS.sleep(5);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                blockingQueue.take();
                System.out.println(Thread.currentThread().getName() + "\t take A ");

                try {
                    TimeUnit.SECONDS.sleep(5);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                blockingQueue.take();
                System.out.println(Thread.currentThread().getName() + "\t take B ");

                try {
                    TimeUnit.SECONDS.sleep(5);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                blockingQueue.take();
                System.out.println(Thread.currentThread().getName() + "\t take C ");

            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "t2").start();
```

最后结果输出为：

```
t1	 put A 
t2	 take A 

5秒后...

t1	 put B 
t2	 take B 

5秒后...

t1	 put C 
t2	 take C 
```

我们从最后的运行结果可以看出，每次t1线程向队列中添加阻塞队列添加元素后，t1输入线程就会等待 t2消费线程，t2消费后，t2处于挂起状态，等待t1在存入，从而周而复始，形成 一存一取的状态

## 阻塞队列的用处

### 生产者消费者模式

一个初始值为0的变量，两个线程对其交替操作，一个加1，一个减1，来5轮

关于多线程的操作，我们需要记住下面几句

- 线程 操作 资源类
- 判断 干活 通知
- 防止虚假唤醒机制

我们下面实现一个简单的生产者消费者模式，首先有资源类ShareData

```java
/**
 * 资源类
 */
class ShareData {

    private int number = 0;

    private Lock lock = new ReentrantLock();

    private Condition condition = lock.newCondition();

    public void increment() throws Exception{
        // 同步代码块，加锁
        lock.lock();
        try {
            // 判断
            while(number != 0) {
                // 等待不能生产
                condition.await();
            }

            // 干活
            number++;

            System.out.println(Thread.currentThread().getName() + "\t " + number);

            // 通知 唤醒
            condition.signalAll();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void decrement() throws Exception{
        // 同步代码块，加锁
        lock.lock();
        try {
            // 判断
            while(number == 0) {
                // 等待不能消费
                condition.await();
            }

            // 干活
            number--;

            System.out.println(Thread.currentThread().getName() + "\t " + number);

            // 通知 唤醒
            condition.signalAll();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

}
```

里面有一个number变量，同时提供了increment 和 decrement的方法，分别让number 加1和减1

但是我们在进行判断的时候，为了防止出现虚假唤醒机制，不能使用if来进行判断，而应该使用while

```java
// 判断
while(number != 0) {
    // 等待不能生产
    condition.await();
}
```

不能使用 if判断

```java
// 判断
if(number != 0) {
    // 等待不能生产
    condition.await();
}
```

完整代码

```java
/**
 * 生产者消费者 传统版
 * 题目：一个初始值为0的变量，两个线程对其交替操作，一个加1，一个减1，来5轮
 * @author: 陌溪
 * @create: 2020-03-16-21:38
 */
/**
 * 线程 操作 资源类
 * 判断 干活 通知
 * 防止虚假唤醒机制
 */

/**
 * 资源类
 */
class ShareData {

    private int number = 0;

    private Lock lock = new ReentrantLock();

    private Condition condition = lock.newCondition();

    public void increment() throws Exception{
        // 同步代码块，加锁
        lock.lock();
        try {
            // 判断
            while(number != 0) {
                // 等待不能生产
                condition.await();
            }

            // 干活
            number++;

            System.out.println(Thread.currentThread().getName() + "\t " + number);

            // 通知 唤醒
            condition.signalAll();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void decrement() throws Exception{
        // 同步代码块，加锁
        lock.lock();
        try {
            // 判断
            while(number == 0) {
                // 等待不能消费
                condition.await();
            }

            // 干活
            number--;

            System.out.println(Thread.currentThread().getName() + "\t " + number);

            // 通知 唤醒
            condition.signalAll();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

}
public class ProdConsumerTraditionDemo {

    public static void main(String[] args) {

        // 高内聚，低耦合    内聚指的是，一个空调，自身带有调节温度高低的方法

        ShareData shareData = new ShareData();

        // t1线程，生产
        new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                try {
                    shareData.increment();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }, "t1").start();

        // t2线程，消费
        new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                try {
                    shareData.decrement();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }, "t2").start();
    }
}
```

最后运行成功后，我们一个进行生产，一个进行消费

```
t1	 1
t2	 0
t1	 1
t2	 0
t1	 1
t2	 0
t1	 1
t2	 0
t1	 1
t2	 0
```

### 生成者和消费者3.0

在concurrent包发布以前，在多线程环境下，我们每个程序员都必须自己去控制这些细节，尤其还要兼顾效率和线程安全，则这会给我们的程序带来不小的时间复杂度

现在我们使用新版的阻塞队列版生产者和消费者，使用：volatile、CAS、atomicInteger、BlockQueue、线程交互、原子引用

```java
/**
 * 生产者消费者  阻塞队列版
 * 使用：volatile、CAS、atomicInteger、BlockQueue、线程交互、原子引用
 *
 * @author: 陌溪
 * @create: 2020-03-17-11:13
 */

class MyResource {
    // 默认开启，进行生产消费
    // 这里用到了volatile是为了保持数据的可见性，也就是当TLAG修改时，要马上通知其它线程进行修改
    private volatile boolean FLAG = true;

    // 使用原子包装类，而不用number++
    private AtomicInteger atomicInteger = new AtomicInteger();

    // 这里不能为了满足条件，而实例化一个具体的SynchronousBlockingQueue
    BlockingQueue<String> blockingQueue = null;

    // 而应该采用依赖注入里面的，构造注入方法传入
    public MyResource(BlockingQueue<String> blockingQueue) {
        this.blockingQueue = blockingQueue;
        // 查询出传入的class是什么
        System.out.println(blockingQueue.getClass().getName());
    }

    /**
     * 生产
     * @throws Exception
     */
    public void myProd() throws Exception{
        String data = null;
        boolean retValue;
        // 多线程环境的判断，一定要使用while进行，防止出现虚假唤醒
        // 当FLAG为true的时候，开始生产
        while(FLAG) {
            data = atomicInteger.incrementAndGet() + "";

            // 2秒存入1个data
            retValue = blockingQueue.offer(data, 2L, TimeUnit.SECONDS);
            if(retValue) {
                System.out.println(Thread.currentThread().getName() + "\t 插入队列:" + data  + "成功" );
            } else {
                System.out.println(Thread.currentThread().getName() + "\t 插入队列:" + data  + "失败" );
            }

            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

        System.out.println(Thread.currentThread().getName() + "\t 停止生产，表示FLAG=false，生产介绍");
    }

    /**
     * 消费
     * @throws Exception
     */
    public void myConsumer() throws Exception{
        String retValue;
        // 多线程环境的判断，一定要使用while进行，防止出现虚假唤醒
        // 当FLAG为true的时候，开始生产
        while(FLAG) {
            // 2秒存入1个data
            retValue = blockingQueue.poll(2L, TimeUnit.SECONDS);
            if(retValue != null && retValue != "") {
                System.out.println(Thread.currentThread().getName() + "\t 消费队列:" + retValue  + "成功" );
            } else {
                FLAG = false;
                System.out.println(Thread.currentThread().getName() + "\t 消费失败，队列中已为空，退出" );

                // 退出消费队列
                return;
            }
        }
    }

    /**
     * 停止生产的判断
     */
    public void stop() {
        this.FLAG = false;
    }

}
public class ProdConsumerBlockingQueueDemo {

    public static void main(String[] args) {
        // 传入具体的实现类， ArrayBlockingQueue
        MyResource myResource = new MyResource(new ArrayBlockingQueue<String>(10));

        new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + "\t 生产线程启动");
            System.out.println("");
            System.out.println("");
            try {
                myResource.myProd();
                System.out.println("");
                System.out.println("");
            } catch (Exception e) {
                e.printStackTrace();
            }
        }, "prod").start();


        new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + "\t 消费线程启动");

            try {
                myResource.myConsumer();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }, "consumer").start();

        // 5秒后，停止生产和消费
        try {
            TimeUnit.SECONDS.sleep(5);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("");
        System.out.println("");
        System.out.println("5秒中后，生产和消费线程停止，线程结束");
        myResource.stop();
    }
}
```

最后运行结果

```
java.util.concurrent.ArrayBlockingQueue
prod	 生产线程启动


consumer	 消费线程启动
prod	 插入队列:1成功
consumer	 消费队列:1成功
prod	 插入队列:2成功
consumer	 消费队列:2成功
prod	 插入队列:3成功
consumer	 消费队列:3成功
prod	 插入队列:4成功
consumer	 消费队列:4成功
prod	 插入队列:5成功
consumer	 消费队列:5成功


5秒中后，生产和消费线程停止，线程结束
prod	 停止生产，表示FLAG=false，生产介绍
```



# 锁

## 死锁

### 概念

死锁是指两个或多个以上的进程在执行过程中，因争夺资源而造成一种互相等待的现象，若无外力干涉那他们都将无法推进下去。如果资源充足，进程的资源请求都能够得到满足，死锁出现的可能性就很低，否则就会因争夺有限的资源而陷入死锁。

![image-20200318175441578](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/11_%E6%AD%BB%E9%94%81%E7%BC%96%E7%A0%81%E5%8F%8A%E5%BF%AB%E9%80%9F%E5%AE%9A%E4%BD%8D/images/image-20200318175441578.png)

### 产生死锁的原因

- 系统资源不足
- 进程运行推进的顺序不对
- 资源分配不当



### 产生死锁的必要条件

1. 互斥条件：进程占用资源的时候不允许其他进程占用。

2. 请求和保持条件：阻塞时，不放。

3. 不剥夺条件：未使用完成时，不能剥夺。

4. 环路等待条件：死锁时，必然存在一个进程-资源的环形链

5. 预防死锁：

   1.资源一次性分配：

   2.只要有一个资源得/到分配，其他资源也不给进程分配

   3.分配编号，每一个进程挨个来请求资源
   
   

###  死锁代码

我们创建了一个资源类，然后让两个线程分别持有自己的锁，同时在尝试获取别人的，就会出现死锁现象

```java
/**
 * 死锁小Demo
 * 死锁是指两个或多个以上的进程在执行过程中，
 * 因争夺资源而造成一种互相等待的现象，
 * 若无外力干涉那他们都将无法推进下去
 * @author: 陌溪
 * @create: 2020-03-18-17:58
 */

import java.util.concurrent.TimeUnit;

/**
 * 资源类
 */
class HoldLockThread implements Runnable{

    private String lockA;
    private String lockB;

    // 持有自己的锁，还想得到别人的锁

    public HoldLockThread(String lockA, String lockB) {
        this.lockA = lockA;
        this.lockB = lockB;
    }


    @Override
    public void run() {
        synchronized (lockA) {
            System.out.println(Thread.currentThread().getName() + "\t 自己持有" + lockA + "\t 尝试获取：" + lockB);

            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            synchronized (lockB) {
                System.out.println(Thread.currentThread().getName() + "\t 自己持有" + lockB + "\t 尝试获取：" + lockA);
            }
        }
    }
}
public class DeadLockDemo {
    public static void main(String[] args) {
        String lockA = "lockA";
        String lockB = "lockB";

        new Thread(new HoldLockThread(lockA, lockB), "t1").start();

        new Thread(new HoldLockThread(lockB, lockA), "t2").start();
    }
}
```

运行结果，main线程无法结束

```
t1	 自己持有lockA	 尝试获取：lockB
t2	 自己持有lockB	 尝试获取：lockA
```



###  如何排查死锁

当我们出现死锁的时候，首先需要使用jps命令查看运行的程序

```
jps -l
```

我们能看到DeadLockDemo这个类，一直在运行

![image-20200318181504703](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/11_%E6%AD%BB%E9%94%81%E7%BC%96%E7%A0%81%E5%8F%8A%E5%BF%AB%E9%80%9F%E5%AE%9A%E4%BD%8D/images/image-20200318181504703.png)

在使用jstack查看堆栈信息

```
jstack  7560   # 后面参数是 jps输出的该类的pid
```

得到的结果

```java
Found one Java-level deadlock:
=============================
"t2":
  waiting to lock monitor 0x000000001cfc0de8 (object 0x000000076b696e80, a java.lang.String),
  which is held by "t1"
"t1":
  waiting to lock monitor 0x000000001cfc3728 (object 0x000000076b696eb8, a java.lang.String),
  which is held by "t2"

Java stack information for the threads listed above:
===================================================
"t2":
        at com.moxi.interview.study.Lock.HoldLockThread.run(DeadLockDemo.java:42)
        - waiting to lock <0x000000076b696e80> (a java.lang.String)
        - locked <0x000000076b696eb8> (a java.lang.String)
        at java.lang.Thread.run(Thread.java:745)
"t1":
        at com.moxi.interview.study.Lock.HoldLockThread.run(DeadLockDemo.java:42)
        - waiting to lock <0x000000076b696eb8> (a java.lang.String)
        - locked <0x000000076b696e80> (a java.lang.String)
        at java.lang.Thread.run(Thread.java:745)

Found 1 deadlock.
```

通过查看最后一行，我们看到 Found 1 deadlock，即存在一个死锁







## 公平锁和非公平锁

**公平锁**

是指多个线程按照申请锁的顺序来获取锁，类似于排队买饭，先来后到，先来先服务，就是公平的，也就是队列

**非公平锁**

是指多个线程获取锁的顺序，并不是按照申请锁的顺序，有可能申请的线程比先申请的线程优先获取锁，在高并发环境下，有可能造成优先级翻转，或者饥饿的线程（也就是某个线程一直得不到锁）

###  如何创建

并发包中ReentrantLock的创建可以指定析构函数的boolean类型来得到公平锁或者非公平锁，默认是非公平锁

```
/**
* 创建一个可重入锁，true 表示公平锁，false 表示非公平锁。默认非公平锁
*/
Lock lock = new ReentrantLock(true);
```

### 两者区别

**公平锁**：就是很公平，在并发环境中，每个线程在获取锁时会先查看此锁维护的等待队列，如果为空，或者当前线程是等待队列中的第一个，就占用锁，否者就会加入到等待队列中，以后安装FIFO的规则从队列中取到自己

**非公平锁：** 非公平锁比较粗鲁，上来就直接尝试占有锁，如果尝试失败，就再采用类似公平锁那种方式。

**题外话**

Java ReenttrantLock通过构造函数指定该锁是否公平，默认是非公平锁，因为非公平锁的优点在于吞吐量比公平锁大，`对于synchronized而言，也是一种非公平锁`



## 可重入锁和递归锁

可重入锁就是递归锁

指的是同一线程外层函数获得锁之后，内层递归函数仍然能获取到该锁的代码，在同一线程在外层方法获取锁的时候，在进入内层方法会自动获取锁

也就是说：`线程可以进入任何一个它已经拥有的锁所同步的代码块`

ReentrantLock / Synchronized 就是一个典型的可重入锁

### 代码

可重入锁就是，在一个method1方法中加入一把锁，方法2也加锁了，那么他们拥有的是同一把锁

```java
public synchronized void method1() {
	method2();
}

public synchronized void method2() {

}
```

也就是说我们只需要进入method1后，那么它也能直接进入method2方法，因为他们所拥有的锁，是同一把。

### 作用

可重入锁的最大作用就是避免死锁

### 可重入锁验证

#### 证明Synchronized

```java
/**
 * 可重入锁（也叫递归锁）
 * 指的是同一线程外层函数获得锁之后，内层递归函数仍然能获取到该锁的代码，在同一线程在外层方法获取锁的时候，在进入内层方法会自动获取锁
 *
 * 也就是说：`线程可以进入任何一个它已经拥有的锁所同步的代码块`
 * @author: 陌溪
 * @create: 2020-03-15-12:12
 */

/**
 * 资源类
 */
class Phone {

    /**
     * 发送短信
     * @throws Exception
     */
    public synchronized void sendSMS() throws Exception{
        System.out.println(Thread.currentThread().getName() + "\t invoked sendSMS()");

        // 在同步方法中，调用另外一个同步方法
        sendEmail();
    }

    /**
     * 发邮件
     * @throws Exception
     */
    public synchronized void sendEmail() throws Exception{
        System.out.println(Thread.currentThread().getId() + "\t invoked sendEmail()");
    }
}
public class ReenterLockDemo {


    public static void main(String[] args) {
        Phone phone = new Phone();

        // 两个线程操作资源列
        new Thread(() -> {
            try {
                phone.sendSMS();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }, "t1").start();

        new Thread(() -> {
            try {
                phone.sendSMS();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }, "t2").start();
    }
}
```

在这里，我们编写了一个资源类phone，拥有两个加了synchronized的同步方法，分别是sendSMS 和 sendEmail，我们在sendSMS方法中，调用sendEmail。最后在主线程同时开启了两个线程进行测试，最后得到的结果为：

```
t1	 invoked sendSMS()
t1	 invoked sendEmail()
t2	 invoked sendSMS()
t2	 invoked sendEmail()
```

这就说明当 t1 线程进入sendSMS的时候，拥有了一把锁，同时t2线程无法进入，直到t1线程拿着锁，执行了sendEmail 方法后，才释放锁，这样t2才能够进入

```
t1	 invoked sendSMS()      t1线程在外层方法获取锁的时候
t1	 invoked sendEmail()    t1在进入内层方法会自动获取锁

t2	 invoked sendSMS()      t2线程在外层方法获取锁的时候
t2	 invoked sendEmail()    t2在进入内层方法会自动获取锁
```

#### 证明ReentrantLock

```java
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * 资源类
 */
class Phone implements Runnable{

    Lock lock = new ReentrantLock();

    /**
     * set进去的时候，就加锁，调用set方法的时候，能否访问另外一个加锁的set方法
     */
    public void getLock() {
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + "\t get Lock");
            setLock();
        } finally {
            lock.unlock();
        }
    }

    public void setLock() {
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + "\t set Lock");
        } finally {
            lock.unlock();
        }
    }

    @Override
    public void run() {
        getLock();
    }
}
public class ReenterLockDemo {


    public static void main(String[] args) {
        Phone phone = new Phone();

        /**
         * 因为Phone实现了Runnable接口
         */
        Thread t3 = new Thread(phone, "t3");
        Thread t4 = new Thread(phone, "t4");
        t3.start();
        t4.start();
    }
}
```

现在我们使用ReentrantLock进行验证，首先资源类实现了Runnable接口，重写Run方法，里面调用get方法，get方法在进入的时候，就加了锁

```java
    public void getLock() {
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + "\t get Lock");
            setLock();
        } finally {
            lock.unlock();
        }
    }
```

然后在方法里面，又调用另外一个加了锁的setLock方法

```java
    public void setLock() {
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + "\t set Lock");
        } finally {
            lock.unlock();
        }
    }
```

最后输出结果我们能发现，结果和加synchronized方法是一致的，都是在外层的方法获取锁之后，线程能够直接进入里层

```
t3	 get Lock
t3	 set Lock
t4	 get Lock
t4	 set Lock
```

**当我们在getLock方法加两把锁会是什么情况呢？** (阿里面试)

```java
    public void getLock() {
        lock.lock();
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + "\t get Lock");
            setLock();
        } finally {
            lock.unlock();
            lock.unlock();
        }
    }
```

最后得到的结果也是一样的，因为里面不管有几把锁，其它他们都是同一把锁，也就是说用同一个钥匙都能够打开

**当我们在getLock方法加两把锁，但是只解一把锁会出现什么情况呢？**

```java
public void getLock() {
    lock.lock();
    lock.lock();
    try {
        System.out.println(Thread.currentThread().getName() + "\t get Lock");
        setLock();
    } finally {
        lock.unlock();
        lock.unlock();
    }
}
```

得到结果

```
t3	 get Lock
t3	 set Lock
```

也就是说程序直接卡死，线程不能出来，也就说明我们申请几把锁，最后需要解除几把锁

**当我们只加一把锁，但是用两把锁来解锁的时候，又会出现什么情况呢？**

```java
    public void getLock() {
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + "\t get Lock");
            setLock();
        } finally {
            lock.unlock();
            lock.unlock();
        }
    }
```

这个时候，运行程序会直接报错

```
t3	 get Lock
t3	 set Lock
t4	 get Lock
t4	 set Lock
Exception in thread "t3" Exception in thread "t4" java.lang.IllegalMonitorStateException
	at java.util.concurrent.locks.ReentrantLock$Sync.tryRelease(ReentrantLock.java:151)
	at java.util.concurrent.locks.AbstractQueuedSynchronizer.release(AbstractQueuedSynchronizer.java:1261)
	at java.util.concurrent.locks.ReentrantLock.unlock(ReentrantLock.java:457)
	at com.moxi.interview.study.thread.Phone.getLock(ReenterLockDemo.java:52)
	at com.moxi.interview.study.thread.Phone.run(ReenterLockDemo.java:67)
	at java.lang.Thread.run(Thread.java:745)
java.lang.IllegalMonitorStateException
	at java.util.concurrent.locks.ReentrantLock$Sync.tryRelease(ReentrantLock.java:151)
	at java.util.concurrent.locks.AbstractQueuedSynchronizer.release(AbstractQueuedSynchronizer.java:1261)
	at java.util.concurrent.locks.ReentrantLock.unlock(ReentrantLock.java:457)
	at com.moxi.interview.study.thread.Phone.getLock(ReenterLockDemo.java:52)
	at com.moxi.interview.study.thread.Phone.run(ReenterLockDemo.java:67)
	at java.lang.Thread.run(Thread.java:745)
```

## 自旋锁

 线程的阻塞和唤醒需要CPU从用户态转为核心态，频繁的阻塞和唤醒对CPU来说是一件负担很重的工作，势必会给系统的并发性能带来很大的压力。 

 所谓自旋锁，就是让该线程等待一段时间，不会被立即挂起，看持有锁的线程是否会很快释放锁。怎么等待呢？执行一段无意义的循环即可（自旋）。  

原来提到的比较并交换，底层使用的就是自旋，自旋就是多次尝试，多次访问，不会阻塞的状态就是自旋。

![image-20200315154143781](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/6_Java%E7%9A%84%E9%94%81/Java%E9%94%81%E4%B9%8B%E8%87%AA%E6%97%8B%E9%94%81/images/image-20200315154143781.png)

**优缺点**

优点：循环比较获取直到成功为止，没有类似于wait的阻塞

缺点：当不断自旋的线程越来越多的时候，会因为执行while循环不断的消耗CPU资源

###  手写自旋锁

通过CAS操作完成自旋锁，A线程先进来调用myLock方法自己持有锁5秒，B随后进来发现当前有线程持有锁，不是null，所以只能通过自旋等待，直到A释放锁后B随后抢到

```java
/**
 * 手写一个自旋锁
 *
 * 循环比较获取直到成功为止，没有类似于wait的阻塞
 *
 * 通过CAS操作完成自旋锁，A线程先进来调用myLock方法自己持有锁5秒，B随后进来发现当前有线程持有锁，不是null，所以只能通过自旋等待，直到A释放锁后B随后抢到
 * @author: 陌溪
 * @create: 2020-03-15-15:46
 */
public class SpinLockDemo {

    // 现在的泛型装的是Thread，原子引用线程
    AtomicReference<Thread>  atomicReference = new AtomicReference<>();

    public void myLock() {
        // 获取当前进来的线程
        Thread thread = Thread.currentThread();
        System.out.println(Thread.currentThread().getName() + "\t come in ");

        // 开始自旋，期望值是null，更新值是当前线程，如果是null，则更新为当前线程，否者自旋
        while(!atomicReference.compareAndSet(null, thread)) {

        }
    }

    /**
     * 解锁
     */
    public void myUnLock() {

        // 获取当前进来的线程
        Thread thread = Thread.currentThread();

        // 自己用完了后，把atomicReference变成null
        atomicReference.compareAndSet(thread, null);

        System.out.println(Thread.currentThread().getName() + "\t invoked myUnlock()");
    }

    public static void main(String[] args) {

        SpinLockDemo spinLockDemo = new SpinLockDemo();

        // 启动t1线程，开始操作
        new Thread(() -> {

            // 开始占有锁
            spinLockDemo.myLock();


            try {
                TimeUnit.SECONDS.sleep(5);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            // 开始释放锁
            spinLockDemo.myUnLock();

        }, "t1").start();


        // 让main线程暂停1秒，使得t1线程，先执行
        try {
            TimeUnit.SECONDS.sleep(5);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        // 1秒后，启动t2线程，开始占用这个锁
        new Thread(() -> {

            // 开始占有锁
            spinLockDemo.myLock();
            // 开始释放锁
            spinLockDemo.myUnLock();

        }, "t2").start();

    }
}
```

最后输出结果

```
t1	 come in 
.....五秒后.....
t1	 invoked myUnlock()
t2	 come in 
t2	 invoked myUnlock()
```

首先输出的是 t1 come in

然后1秒后，t2线程启动，发现锁被t1占有，所有不断的执行 compareAndSet方法，来进行比较，直到t1释放锁后，也就是5秒后，t2成功获取到锁，然后释放

## 读写锁

独占锁：指该锁一次只能被一个线程所持有。对ReentrantLock和Synchronized而言都是独占锁

共享锁：指该锁可以被多个线程锁持有

对ReentrantReadWriteLock其读锁是共享，其写锁是独占

写的时候只能一个人写，但是读的时候，可以多个人同时读

### 为什么要有写锁和读锁

原来我们使用ReentrantLock创建锁的时候，是独占锁，也就是说一次只能一个线程访问，但是有一个读写分离场景，读的时候想同时进行，因此原来独占锁的并发性就没这么好了，因为读锁并不会造成数据不一致的问题，因此可以多个人共享读

```
多个线程 同时读一个资源类没有任何问题，所以为了满足并发量，读取共享资源应该可以同时进行，但是如果一个线程想去写共享资源，就不应该再有其它线程可以对该资源进行读或写
```

读-读：能共存

读-写：不能共存

写-写：不能共存

###  代码实现

实现一个读写缓存的操作，假设开始没有加锁的时候，会出现什么情况

```java
/**
 * 读写锁
 * 多个线程 同时读一个资源类没有任何问题，所以为了满足并发量，读取共享资源应该可以同时进行
 * 但是，如果一个线程想去写共享资源，就不应该再有其它线程可以对该资源进行读或写
 *
 * @author: 陌溪
 * @create: 2020-03-15-16:59
 */

import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.Lock;

/**
 * 资源类
 */
class MyCache {

    private volatile Map<String, Object> map = new HashMap<>();
    // private Lock lock = null;

    /**
     * 定义写操作
     * 满足：原子 + 独占
     * @param key
     * @param value
     */
    public void put(String key, Object value) {
        System.out.println(Thread.currentThread().getName() + "\t 正在写入：" + key);
        try {
            // 模拟网络拥堵，延迟0.3秒
            TimeUnit.MILLISECONDS.sleep(300);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        map.put(key, value);
        System.out.println(Thread.currentThread().getName() + "\t 写入完成");
    }

    public void get(String key) {
        System.out.println(Thread.currentThread().getName() + "\t 正在读取:");
        try {
            // 模拟网络拥堵，延迟0.3秒
            TimeUnit.MILLISECONDS.sleep(300);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        Object value = map.get(key);
        System.out.println(Thread.currentThread().getName() + "\t 读取完成：" + value);
    }


}
public class ReadWriteLockDemo {

    public static void main(String[] args) {

        MyCache myCache = new MyCache();
        // 线程操作资源类，5个线程写
        for (int i = 0; i < 5; i++) {
            // lambda表达式内部必须是final
            final int tempInt = i;
            new Thread(() -> {
                myCache.put(tempInt + "", tempInt +  "");
            }, String.valueOf(i)).start();
        }
        // 线程操作资源类， 5个线程读
        for (int i = 0; i < 5; i++) {
            // lambda表达式内部必须是final
            final int tempInt = i;
            new Thread(() -> {
                myCache.get(tempInt + "");
            }, String.valueOf(i)).start();
        }
    }
}
```

我们分别创建5个线程写入缓存

```java
        // 线程操作资源类，5个线程写
        for (int i = 0; i < 5; i++) {
            // lambda表达式内部必须是final
            final int tempInt = i;
            new Thread(() -> {
                myCache.put(tempInt + "", tempInt +  "");
            }, String.valueOf(i)).start();
        }
```

5个线程读取缓存，

```java
        // 线程操作资源类， 5个线程读
        for (int i = 0; i < 5; i++) {
            // lambda表达式内部必须是final
            final int tempInt = i;
            new Thread(() -> {
                myCache.get(tempInt + "");
            }, String.valueOf(i)).start();
        }
```

最后运行结果：

```
0	 正在写入：0
4	 正在写入：4
3	 正在写入：3
1	 正在写入：1
2	 正在写入：2
0	 正在读取:
1	 正在读取:
2	 正在读取:
3	 正在读取:
4	 正在读取:
2	 写入完成
4	 写入完成
4	 读取完成：null
0	 写入完成
3	 读取完成：null
0	 读取完成：null
1	 写入完成
3	 写入完成
1	 读取完成：null
2	 读取完成：null
```

我们可以看到，在写入的时候，写操作都没其它线程打断了，这就造成了，还没写完，其它线程又开始写，这样就造成数据不一致

###  解决方法

上面的代码是没有加锁的，这样就会造成线程在进行写入操作的时候，被其它线程频繁打断，从而不具备原子性，这个时候，我们就需要用到读写锁来解决了

```
/**
* 创建一个读写锁
* 它是一个读写融为一体的锁，在使用的时候，需要转换
*/
private ReentrantReadWriteLock rwLock = new ReentrantReadWriteLock();
```

当我们在进行写操作的时候，就需要转换成写锁

```
// 创建一个写锁
rwLock.writeLock().lock();

// 写锁 释放
rwLock.writeLock().unlock();
```

当们在进行读操作的时候，在转换成读锁

```
// 创建一个读锁
rwLock.readLock().lock();

// 读锁 释放
rwLock.readLock().unlock();
```

这里的读锁和写锁的区别在于，写锁一次只能一个线程进入，执行写操作，而读锁是多个线程能够同时进入，进行读取的操作

完整代码：

```java
/**
 * 读写锁
 * 多个线程 同时读一个资源类没有任何问题，所以为了满足并发量，读取共享资源应该可以同时进行
 * 但是，如果一个线程想去写共享资源，就不应该再有其它线程可以对该资源进行读或写
 *
 * @author: 陌溪
 * @create: 2020-03-15-16:59
 */

import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

/**
 * 资源类
 */
class MyCache {

    /**
     * 缓存中的东西，必须保持可见性，因此使用volatile修饰
     */
    private volatile Map<String, Object> map = new HashMap<>();

    /**
     * 创建一个读写锁
     * 它是一个读写融为一体的锁，在使用的时候，需要转换
     */
    private ReentrantReadWriteLock rwLock = new ReentrantReadWriteLock();

    /**
     * 定义写操作
     * 满足：原子 + 独占
     * @param key
     * @param value
     */
    public void put(String key, Object value) {

        // 创建一个写锁
        rwLock.writeLock().lock();

        try {

            System.out.println(Thread.currentThread().getName() + "\t 正在写入：" + key);

            try {
                // 模拟网络拥堵，延迟0.3秒
                TimeUnit.MILLISECONDS.sleep(300);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            map.put(key, value);

            System.out.println(Thread.currentThread().getName() + "\t 写入完成");

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 写锁 释放
            rwLock.writeLock().unlock();
        }
    }

    /**
     * 获取
     * @param key
     */
    public void get(String key) {

        // 读锁
        rwLock.readLock().lock();
        try {

            System.out.println(Thread.currentThread().getName() + "\t 正在读取:");

            try {
                // 模拟网络拥堵，延迟0.3秒
                TimeUnit.MILLISECONDS.sleep(300);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            Object value = map.get(key);

            System.out.println(Thread.currentThread().getName() + "\t 读取完成：" + value);

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 读锁释放
            rwLock.readLock().unlock();
        }
    }

    /**
     * 清空缓存
     */
    public void clean() {
        map.clear();
    }


}
public class ReadWriteLockDemo {

    public static void main(String[] args) {

        MyCache myCache = new MyCache();

        // 线程操作资源类，5个线程写
        for (int i = 1; i <= 5; i++) {
            // lambda表达式内部必须是final
            final int tempInt = i;
            new Thread(() -> {
                myCache.put(tempInt + "", tempInt +  "");
            }, String.valueOf(i)).start();
        }

        // 线程操作资源类， 5个线程读
        for (int i = 1; i <= 5; i++) {
            // lambda表达式内部必须是final
            final int tempInt = i;
            new Thread(() -> {
                myCache.get(tempInt + "");
            }, String.valueOf(i)).start();
        }
    }
}
```

运行结果：

```
1	 正在写入：1
1	 写入完成
2	 正在写入：2
2	 写入完成
3	 正在写入：3
3	 写入完成
4	 正在写入：4
4	 写入完成
5	 正在写入：5
5	 写入完成
2	 正在读取:
3	 正在读取:
1	 正在读取:
4	 正在读取:
5	 正在读取:
2	 读取完成：2
1	 读取完成：1
4	 读取完成：4
3	 读取完成：3
5	 读取完成：5
```

从运行结果我们可以看出，写入操作是一个一个线程进行执行的，并且中间不会被打断，而读操作的时候，是同时5个线程进入，然后并发读取操作



## 轻量级锁

 轻量级锁的目标是，减少无实际竞争情况下，使用重量级锁产生的性能消耗。就是使用CAS操作

使用轻量级锁时，不需要申请互斥量，仅仅将Mark Word中的部分字节CAS更新指向线程栈中的Lock Record（Lock Record：JVM检测到当前对象是无锁状态，则会在当前线程的栈帧中创建一个名为LOCKRECOD表空间用于copy Mark word 中的数据），如果更新成功，则轻量级锁获取成功，记录锁状态为轻量级锁；否则，说明已经有线程获得了轻量级锁，目前发生了锁竞争（不适合继续使用轻量级锁），接下来膨胀为重量级锁。

## 偏向锁

偏向锁假定将来只有第一个申请锁的线程会使用锁（不会有任何线程再来申请锁），因此，只需要在Mark Word中CAS记录owner（本质上也是更新，但初始值为空），如果记录成功，则偏向锁获取成功，记录锁状态为偏向锁，以后当前线程等于owner就可以零成本的直接获得锁；否则，说明有其他线程竞争，膨胀为轻量级锁



## 锁升级

刚开始判断是否有锁，锁支持偏向锁，获取到锁资源的线程会优先让它再去获取这个锁，如果没有获取到这个锁就升级成轻量级锁（CAS、乐观锁），如果CAS没有设置成功会进行自旋，自旋到一定次数会升级成synchronized重量级锁。

## synchronized

 **synchronized**（JVM中实现）是怎么做到的，synchronized 先锁住 共享的内存变量，然后线程A修改完之后将值返回到主内存，然后线程B获得锁以后才能获取值，所以通过代码层面的锁可以解决这个线程之间通信的 可见性问题。 

 对于声明了 volatile 的变量，进行写操作的时候， JVM 会向 处理器发送一条 lock 的前缀指令。 

### 如何实现synchronized

 JVM可以从方法常量池中的方法表结构(method_info Structure) 中的 ACC_SYNCHRONIZED 访问标志区分一个方法是否同步方法 。方法执行时候，会检查标志是否设置，设置了就进行如下操作。

### Java对象头

**在JVM中对象的存储**

![1586657194061](D:\java-\java基础.assets\1586657194061.png)

对象头主要由Mark Word 和Class Metadata Address组成。

**Mark Word**：存对象的hashcode，锁信息或分代年龄或GC标志。

**Class Metadata Address：**类型指针指向的类元数据，JVM通过这个指针确定该对象是什么类的实例。

### 同步代码块

底层通过monitorenter和monitorexit实现的。前者指向同步代码块开始位置，后者指向代码块结束位置。当执行monitorenter时，当前线程会尝试获取对象锁所对应的monitor持有权。取得monitor时，计数器就变为1。若之前取过monitor，会使得计数器加1.若不是当前线程拥有monitor，那么该线程会阻塞，等待其他线程使用结束为止。当前线程完成synchronized操作以后会把monitor归置为0.

monitor存在于对象头中。



### 同步方法

 JVM可以从方法常量池中的方法表结构(method_info Structure) 中的 ACC_SYNCHRONIZED 访问标志区分一个方法是否同步方法 。方法执行时候，会检查标志是否设置，设置了就进行如下操作。当方法调用时会检查方法的 ACC_SYNCHRONIZED 访问标志是否被设置，如果设置了，执行线程将先获取monitor，获取成功之后才能执行方法体，方法执行完后再释放monitor。在方法执行期间，其他任何线程都无法再获得同一个monitor对象。这种方式与语句块没什么本质区别，都是通过竞争monitor的方式实现的。只不过这种方式是隐式的实现方法。




 volatile可以修饰变量，共享变量 。

synchronized

 修饰一个代码块

  2.修饰一个方法

  3.修饰一个类

  4.修饰一个静态的方法



## **volatile**

它是轻量级的同步机制：

​	保证可见性、不保证原子性、禁止指令重排

### JMM Java Memory Model

​	1.什么是JMM？

​	JMM是Java内存模型，也就是Java Memory Model，简称JMM，本身是一种抽象的概念，实际上并不存在，它描述的是一组规则或规范，通过这组规范定义了程序中各个变量（包括实例字段，静态字段和构成数组对象的元素）的访问方式

JMM关于同步的规定：

- 线程解锁前，必须把共享变量的值刷新回主内存
- 线程解锁前，必须读取主内存的最新值，到自己的工作内存
- 加锁和解锁是同一把锁

**volatile**写操作时，将这个变量在自己工作内存中的值，写回到主内存，

 当对volatile标记的变量进行修改时，会将其他缓存中存储的修改前的变量清除，然后重新读取。一般来说应该是先在进行修改的缓存A中修改为新值，然后通知其他缓存清除掉此变量，当其他缓存B中的线程读取此变量时，会向总线发送消息，这时存储新值的缓存A获取到消息，将新值穿给B。最后将新值写入内存。当变量需要更新时都是此步骤，volatile的作用是被其修饰的变量，每次更新时，都会刷新上述步骤 



由于JVM运行程序的实体是线程，而每个线程创建时JVM都会为其创建一个工作内存（有些地方称为栈空间），工作内存是每个线程的私有数据区域，而Java内存模型中规定所有变量都存储在主内存，主内存是共享内存区域，所有线程都可以访问，`但线程对变量的操作（读取赋值等）必须在工作内存中进行，首先要将变量从主内存拷贝到自己的工作内存空间，然后对变量进行操作，操作完成后再将变量写会主内存`，不能直接操作主内存中的变量，各个线程中的工作内存中存储着主内存中的变量副本拷贝，因此不同的线程间无法访问对方的工作内存，线程间的通信（传值）必须通过主内存来完成，其简要访问过程：

![image-20200309153225758](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/1_%E8%B0%88%E8%B0%88Volatile/1_Volatile%E5%92%8CJMM%E5%86%85%E5%AD%98%E6%A8%A1%E5%9E%8B%E7%9A%84%E5%8F%AF%E8%A7%81%E6%80%A7/images/image-20200309153225758.png)

![image-20200309154435933](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/1_%E8%B0%88%E8%B0%88Volatile/1_Volatile%E5%92%8CJMM%E5%86%85%E5%AD%98%E6%A8%A1%E5%9E%8B%E7%9A%84%E5%8F%AF%E8%A7%81%E6%80%A7/images/image-20200309154435933.png)

即：JMM内存模型的可见性，指的是当主内存区域中的值被某个线程写入更改后，其它线程会马上知晓更改后的值，并重新得到更改后的值。

###  缓存一致性，总线嗅探技术

 每个处理器会 嗅探到 总线上的所传播的数据来检测自己缓存中的值是不是过期了，  处理器的缓存对应的内存地址被修改以后，它就会将当前的处理器缓存的值设置为失效状态，然后去读那个最新的值。 



为什么这里主线程中某个值被更改后，其它线程能马上知晓呢？其实这里是用到了总线嗅探技术

在说嗅探技术之前，首先谈谈缓存一致性的问题，就是当多个处理器运算任务都涉及到同一块主内存区域的时候，将可能导致各自的缓存数据不一。

为了解决缓存一致性的问题，需要各个处理器访问缓存时都遵循一些协议，在读写时要根据协议进行操作，这类协议主要有MSI、MESI等等。

**MESI**

当CPU写数据时，如果发现操作的变量是共享变量，即在其它CPU中也存在该变量的副本，会发出信号通知其它CPU将该内存变量的缓存行设置为无效，因此当其它CPU读取这个变量的时，发现自己缓存该变量的缓存行是无效的，那么它就会从内存中重新读取。

**总线嗅探**

那么是如何发现数据是否失效呢？

这里是用到了总线嗅探技术，就是每个处理器通过嗅探在总线上传播的数据来检查自己缓存值是否过期了，当处理器发现自己的缓存行对应的内存地址被修改，就会将当前处理器的缓存行设置为无效状态，当处理器对这个数据进行修改操作的时候，会重新从内存中把数据读取到处理器缓存中。

**总线风暴**

总线嗅探技术有哪些缺点？

由于Volatile的MESI缓存一致性协议，需要不断的从主内存嗅探和CAS循环，无效的交互会导致总线带宽达到峰值。因此不要大量使用volatile关键字，至于什么时候使用volatile、什么时候用锁以及Syschonized都是需要根据实际场景的。 



JMM的三大特性，volatile只保证了两个，即可见性和有序性，不满足原子性

- 可见性
- 原子性
- 有序性

### 可见性代码验证

```java
/**
 * Volatile Java虚拟机提供的轻量级同步机制
 *
 * 可见性（及时通知）
 * 不保证原子性
 * 禁止指令重排
 *
 * @author: 陌溪
 * @create: 2020-03-09-15:58
 */

import java.util.concurrent.TimeUnit;

/**
 * 假设是主物理内存
 */
class MyData {

    int number = 0;

    public void addTo60() {
        this.number = 60;
    }
}

/**
 * 验证volatile的可见性
 * 1. 假设int number = 0， number变量之前没有添加volatile关键字修饰
 */
public class VolatileDemo {

    public static void main(String args []) {

        // 资源类
        MyData myData = new MyData();

        // AAA线程 实现了Runnable接口的，lambda表达式
        new Thread(() -> {

            System.out.println(Thread.currentThread().getName() + "\t come in");

            // 线程睡眠3秒，假设在进行运算
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            // 修改number的值
            myData.addTo60();

            // 输出修改后的值
            System.out.println(Thread.currentThread().getName() + "\t update number value:" + myData.number);

        }, "AAA").start();

        while(myData.number == 0) {
            // main线程就一直在这里等待循环，直到number的值不等于零
        }

        // 按道理这个值是不可能打印出来的，因为主线程运行的时候，number的值为0，所以一直在循环
        // 如果能输出这句话，说明AAA线程在睡眠3秒后，更新的number的值，重新写入到主内存，并被main线程感知到了
        System.out.println(Thread.currentThread().getName() + "\t mission is over");

        /**
         * 最后输出结果：
         * AAA	 come in
         * AAA	 update number value:60
         * 最后线程没有停止，并行没有输出  mission is over 这句话，说明没有用volatile修饰的变量，是没有可见性
         */

    }
}
```

最后线程没有停止，并行没有输出 mission is over 这句话，说明没有用volatile修饰的变量，是没有可见性

当我们修改MyData类中的成员变量时，并且添加volatile关键字修饰

```
class MyData {
    /**
     * volatile 修饰的关键字，是为了增加 主线程和线程之间的可见性，只要有一个线程修改了内存中的值，其它线程也能马上感知
     */
    volatile int number = 0;

    public void addTo60() {
        this.number = 60;
    }
}
```

主线程也执行完毕了，说明volatile修饰的变量，是具备JVM轻量级同步机制的，能够感知其它线程的修改后的值。

### 不保证原子性

通过前面对JMM的介绍，我们知道，各个线程对主内存中共享变量的操作都是各个线程各自拷贝到自己的工作内存进行操作后在写回到主内存中的。

这就可能存在一个线程AAA修改了共享变量X的值，但是还未写入主内存时，另外一个线程BBB又对主内存中同一共享变量X进行操作，但此时A线程工作内存中共享变量X对线程B来说是不可见，这种工作内存与主内存同步延迟现象就造成了可见性问题。

#### 原子性

不可分割，完整性，也就是说某个线程正在做某个具体业务时，中间不可以被加塞或者被分割，需要具体完成，要么同时成功，要么同时失败。

数据库也经常提到事务具备原子性

#### 代码测试

为了测试volatile是否保证原子性，我们创建了20个线程，然后每个线程分别循环1000次，来调用number++的方法

```java
        MyData myData = new MyData();

        // 创建10个线程，线程里面进行1000次循环
        for (int i = 0; i < 20; i++) {
            new Thread(() -> {
                // 里面
                for (int j = 0; j < 1000; j++) {
                    myData.addPlusPlus();
                }
            }, String.valueOf(i)).start();
        }
```

最后通过 Thread.activeCount()，来感知20个线程是否执行完毕，这里判断线程数是否大于2，为什么是2？因为默认是有两个线程的，一个main线程，一个gc线程

```java
// 需要等待上面20个线程都计算完成后，在用main线程取得最终的结果值
while(Thread.activeCount() > 2) {
    // yield表示不执行
    Thread.yield();
}
```

然后在线程执行完毕后，我们在查看number的值，假设volatile保证原子性的话，那么最后输出的值应该是

20 * 1000 = 20000,

完整代码如下所示：

```java
/**
 * Volatile Java虚拟机提供的轻量级同步机制
 *
 * 可见性（及时通知）
 * 不保证原子性
 * 禁止指令重排
 *
 * @author: 陌溪
 * @create: 2020-03-09-15:58
 */

import java.util.concurrent.TimeUnit;

/**
 * 假设是主物理内存
 */
class MyData {
    /**
     * volatile 修饰的关键字，是为了增加 主线程和线程之间的可见性，只要有一个线程修改了内存中的值，其它线程也能马上感知
     */
    volatile int number = 0;

    public void addTo60() {
        this.number = 60;
    }

    /**
     * 注意，此时number 前面是加了volatile修饰
     */
    public void addPlusPlus() {
        number ++;
    }
}

/**
 * 验证volatile的可见性
 * 1、 假设int number = 0， number变量之前没有添加volatile关键字修饰
 * 2、添加了volatile，可以解决可见性问题
 *
 * 验证volatile不保证原子性
 * 1、原子性指的是什么意思？
 */
public class VolatileDemo {

    public static void main(String args []) {

        MyData myData = new MyData();

        // 创建20个线程，线程里面进行1000次循环
        for (int i = 0; i < 20; i++) {
            new Thread(() -> {
                // 里面
                for (int j = 0; j < 1000; j++) {
                    myData.addPlusPlus();
                }
            }, String.valueOf(i)).start();
        }

        // 需要等待上面20个线程都计算完成后，在用main线程取得最终的结果值
        // 这里判断线程数是否大于2，为什么是2？因为默认是有两个线程的，一个main线程，一个gc线程
        while(Thread.activeCount() > 2) {
            // yield表示不执行
            Thread.yield();
        }

        // 查看最终的值
        // 假设volatile保证原子性，那么输出的值应该为：  20 * 1000 = 20000
        System.out.println(Thread.currentThread().getName() + "\t finally number value: " + myData.number);

    }
}
```

最终结果我们会发现，number输出的值并没有20000，而且是每次运行的结果都不一致的，这说明了volatile修饰的变量不保证原子性

第一次：

![image-20200309172900462](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/1_%E8%B0%88%E8%B0%88Volatile/2_Volatile%E4%B8%8D%E4%BF%9D%E8%AF%81%E5%8E%9F%E5%AD%90%E6%80%A7/images/image-20200309172900462.png)

第二次：

![image-20200309172919295](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/1_%E8%B0%88%E8%B0%88Volatile/2_Volatile%E4%B8%8D%E4%BF%9D%E8%AF%81%E5%8E%9F%E5%AD%90%E6%80%A7/images/image-20200309172919295.png)

第三次：

![image-20200309172929820](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/1_%E8%B0%88%E8%B0%88Volatile/2_Volatile%E4%B8%8D%E4%BF%9D%E8%AF%81%E5%8E%9F%E5%AD%90%E6%80%A7/images/image-20200309172929820.png)



#### 为什么出现数值丢失

![image-20200309174220675](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/1_%E8%B0%88%E8%B0%88Volatile/2_Volatile%E4%B8%8D%E4%BF%9D%E8%AF%81%E5%8E%9F%E5%AD%90%E6%80%A7/images/image-20200309174220675.png)

#### 如何解决

- 在方法上加入 synchronized

```
    public synchronized void addPlusPlus() {
        number ++;
    }
```

除了引用synchronized关键字外，还可以使用JUC下面的原子包装类，即刚刚的int类型的number，可以使用AtomicInteger来代替

```java
    /**
     *  创建一个原子Integer包装类，默认为0
      */
    AtomicInteger atomicInteger = new AtomicInteger();

    public void addAtomic() {
        // 相当于 atomicInter ++
        atomicInteger.getAndIncrement();
    }
```

### 禁止指令重排

计算机在执行程序时，为了提高性能，编译器和处理器常常会对指令重排，一般分为以下三种：

```
源代码 -> 编译器优化的重排 -> 指令并行的重排 -> 内存系统的重排 -> 最终执行指令
```

单线程环境里面确保最终执行结果和代码顺序的结果一致

处理器在进行重排序时，必须要考虑指令之间的`数据依赖性`

多线程环境中线程交替执行，由于编译器优化重排的存在，两个线程中使用的变量能否保证一致性是无法确定的，结果无法预测。

####  例子1

int a,b,x,y = 0

| 线程1        | 线程2  |
| ------------ | ------ |
| x = a;       | y = b; |
| b = 1;       | a = 2; |
|              |        |
| x = 0; y = 0 |        |

因为上面的代码，不存在数据的依赖性，因此编译器可能对数据进行重排

| 线程1        | 线程2  |
| ------------ | ------ |
| b = 1;       | a = 2; |
| x = a;       | y = b; |
|              |        |
| x = 2; y = 1 |        |

这样造成的结果，和最开始的就不一致了，这就是导致重排后，结果和最开始的不一样，因此为了防止这种结果出现，volatile就规定禁止指令重排，为了保证数据的一致性



#### 例子2

比如下面这段代码

```java
public class ResortSeqDemo {
    int a= 0;
    boolean flag = false;

    public void method01() {
        a = 1;
        flag = true;
    }

    public void method02() {
        if(flag) {
            a = a + 5;
            System.out.println("reValue:" + a);
        }
    }
}
```

我们按照正常的顺序，分别调用method01() 和 method02() 那么，最终输出就是 a = 6

但是如果在多线程环境下，因为方法1 和 方法2，他们之间不能存在数据依赖的问题，因此原先的顺序可能是

```
a = 1;
flag = true;

a = a + 5;
System.out.println("reValue:" + a);
        
```

但是在经过编译器，指令，或者内存的重排后，可能会出现这样的情况

```
flag = true;

a = a + 5;
System.out.println("reValue:" + a);

a = 1;
```

也就是先执行 flag = true后，另外一个线程马上调用方法2，满足 flag的判断，最终让a + 5，结果为5，这样同样出现了数据不一致的问题

为什么会出现这个结果：多线程环境中线程交替执行，由于编译器优化重排的存在，两个线程中使用的变量能否保证一致性是无法确定的，结果无法预测。

这样就需要通过volatile来修饰，来保证线程安全性

####  Volatile针对指令重排做了啥

Volatile实现禁止指令重排优化，从而避免了多线程环境下程序出现乱序执行的现象

首先了解一个概念，内存屏障（Memory Barrier）又称内存栅栏，是一个CPU指令，它的作用有两个：

- 保证特定操作的顺序
- 保证某些变量的内存可见性（利用该特性实现volatile的内存可见性）

由于编译器和处理器都能执行指令重排的优化，如果在指令间插入一条Memory Barrier则会告诉编译器和CPU，不管什么指令都不能和这条Memory Barrier指令重排序，也就是说 `通过插入内存屏障禁止在内存屏障前后的指令执行重排序优化`。 内存屏障另外一个作用是刷新出各种CPU的缓存数，因此任何CPU上的线程都能读取到这些数据的最新版本。

###  线程安全获得保证

工作内存与主内存同步延迟现象导致的可见性问题

- 可通过synchronized或volatile关键字解决，他们都可以使一个线程修改后的变量立即对其它线程可见

对于指令重排导致的可见性问题和有序性问题

- 可以使用volatile关键字解决，因为volatile关键字的另一个作用就是禁止重排序优化

### volatile的应用

**单例模式**

单线程下的单例模式代码

```java
/**
 * SingletonDemo（单例模式）
 *
 * @author: 陌溪
 * @create: 2020-03-10-16:40
 */
public class SingletonDemo {

    private static SingletonDemo instance = null;

    private SingletonDemo () {
        System.out.println(Thread.currentThread().getName() + "\t 我是构造方法SingletonDemo");
    }

    public static SingletonDemo getInstance() {
        if(instance == null) {
            instance = new SingletonDemo();
        }
        return instance;
    }

    public static void main(String[] args) {
        // 这里的 == 是比较内存地址
        System.out.println(SingletonDemo.getInstance() == SingletonDemo.getInstance());
        System.out.println(SingletonDemo.getInstance() == SingletonDemo.getInstance());
        System.out.println(SingletonDemo.getInstance() == SingletonDemo.getInstance());
        System.out.println(SingletonDemo.getInstance() == SingletonDemo.getInstance());
    }
}
```

但是在多线程的环境下，我们的单例模式是否还是同一个对象了

```java
public class SingletonDemo {

    private static SingletonDemo instance = null;

    private SingletonDemo () {
        System.out.println(Thread.currentThread().getName() + "\t 我是构造方法SingletonDemo");
    }

    public static SingletonDemo getInstance() {
        if(instance == null) {
            instance = new SingletonDemo();
        }
        return instance;
    }

    public static void main(String[] args) {
        for (int i = 0; i < 10; i++) {
            new Thread(() -> {
                SingletonDemo.getInstance();
            }, String.valueOf(i)).start();
        }
    }
}
```

但不会运行10次。

#### 解决1

引入synchronized关键字

```java
    public synchronized static SingletonDemo getInstance() {
        if(instance == null) {
            instance = new SingletonDemo();
        }
        return instance;
    }
```

我们能够发现，通过引入Synchronized关键字，能够解决高并发环境下的单例模式问题

但是synchronized属于重量级的同步机制，它只允许一个线程同时访问获取实例的方法，但是为了保证数据一致性，而减低了并发性，因此采用的比较少。

#### 解决2

通过引入DCL Double Check Lock 双端检锁机制

就是在进来和出去的时候，进行检测

```java
    public static SingletonDemo getInstance() {
        if(instance == null) {
            // 同步代码段的时候，进行检测
            synchronized (SingletonDemo.class) {
                if(instance == null) {
                    instance = new SingletonDemo();
                }
            }
        }
        return instance;
    }
```

DCL（双端检锁）机制不一定是线程安全的，原因是有**指令重排**的存在，加入volatile可以禁止指令重排

原因是在某一个线程执行到第一次检测的时候，读取到 instance 不为null，instance的引用对象可能没有完成实例化。因为 instance = new SingletonDemo()；可以分为以下三步进行完成：

- memory = allocate(); // 1、分配对象内存空间
- instance(memory); // 2、初始化对象
- instance = memory; // 3、设置instance指向刚刚分配的内存地址，此时instance != null

但是我们通过上面的三个步骤，能够发现，步骤2 和 步骤3之间不存在 数据依赖关系，而且无论重排前 还是重排后，程序的执行结果在单线程中并没有改变，因此这种重排优化是允许的。

- memory = allocate(); // 1、分配对象内存空间
- instance = memory; // 3、设置instance指向刚刚分配的内存地址，此时instance != null，但是对象还没有初始化完成
- instance(memory); // 2、初始化对象

这样就会造成什么问题呢？

也就是当我们执行到重排后的步骤2，试图获取instance的时候，会得到null，因为对象的初始化还没有完成，而是在重排后的步骤3才完成，因此执行单例模式的代码时候，就会重新在创建一个instance实例

```
指令重排只会保证串行语义的执行一致性（单线程），但并不会关系多线程间的语义一致性
```

所以当一条线程访问instance不为null时，由于instance实例未必已初始化完成，这就造成了线程安全的问题

所以需要引入volatile，来保证出现指令重排的问题，从而保证单例模式的线程安全性

```
private static volatile SingletonDemo instance = null;
```

```java
public class SingletonDemo {

    private static volatile SingletonDemo instance = null;

    private SingletonDemo () {
        System.out.println(Thread.currentThread().getName() + "\t 我是构造方法SingletonDemo");
    }

    public static SingletonDemo getInstance() {
        if(instance == null) {
            // a 双重检查加锁多线程情况下会出现某个线程虽然这里已经为空，但是另外一个线程已经执行到d处
            synchronized (SingletonDemo.class) //b
            { 
           //c不加volitale关键字的话有可能会出现尚未完全初始化就获取到的情况。原因是内存模型允许无序写入
                if(instance == null) { 
                	// d 此时才开始初始化
                    instance = new SingletonDemo();
                }
            }
        }
        return instance;
    }

    public static void main(String[] args) {
//        // 这里的 == 是比较内存地址
//        System.out.println(SingletonDemo.getInstance() == SingletonDemo.getInstance());
//        System.out.println(SingletonDemo.getInstance() == SingletonDemo.getInstance());
//        System.out.println(SingletonDemo.getInstance() == SingletonDemo.getInstance());
//        System.out.println(SingletonDemo.getInstance() == SingletonDemo.getInstance());

        for (int i = 0; i < 10; i++) {
            new Thread(() -> {
                SingletonDemo.getInstance();
            }, String.valueOf(i)).start();
        }
    }
}
```

## CAS机制

### 概念

CAS的全称是Compare-And-Swap，它是CPU并发原语

它的功能是判断内存某个位置的值是否为预期值，如果是则更改为新的值，这个过程是原子的

CAS并发原语体现在Java语言中就是sun.misc.Unsafe类的各个方法。调用UnSafe类中的CAS方法，JVM会帮我们实现出CAS汇编指令，这是一种完全依赖于硬件的功能，通过它实现了原子操作，再次强调，由于CAS是一种系统原语，原语属于操作系统用于范畴，是由若干条指令组成，用于完成某个功能的一个过程，并且原语的执行必须是连续的，在执行过程中不允许被中断，也就是说CAS是一条CPU的原子指令，不会造成所谓的数据不一致的问题，也就是说CAS是线程安全的。

### 代码使用

首先调用AtomicInteger创建了一个实例， 并初始化为5

```
        // 创建一个原子类
        AtomicInteger atomicInteger = new AtomicInteger(5);
```

然后调用CAS方法，企图更新成2019，这里有两个参数，一个是5，表示期望值，第二个就是我们要更新的值

```
atomicInteger.compareAndSet(5, 2019)
```

然后再次使用了一个方法，同样将值改成1024

```
atomicInteger.compareAndSet(5, 1024)
```

完整代码如下：

```java
/**
 * CASDemo
 *
 * 比较并交换：compareAndSet
 *
 * @author: 陌溪
 * @create: 2020-03-10-19:46
 */
public class CASDemo {
    public static void main(String[] args) {
        // 创建一个原子类
        AtomicInteger atomicInteger = new AtomicInteger(5);

        /**
         * 一个是期望值，一个是更新值，但期望值和原来的值相同时，才能够更改
         * 假设三秒前，我拿的是5，也就是expect为5，然后我需要更新成 2019
         */
        System.out.println(atomicInteger.compareAndSet(5, 2019) + "\t current data: " + atomicInteger.get());

        System.out.println(atomicInteger.compareAndSet(5, 1024) + "\t current data: " + atomicInteger.get());
    }
}
```

上面代码的执行结果为

![image-20200310201327734](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/2_%E8%B0%88%E8%B0%88CAS/5_CAS%E5%BA%95%E5%B1%82%E5%8E%9F%E7%90%86/images/image-20200310201327734.png)

这是因为我们执行第一个的时候，期望值和原本值是满足的，因此修改成功，但是第二次后，主内存的值已经修改成了2019，不满足期望值，因此返回了false，本次写入失败

![image-20200310201311367](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/2_%E8%B0%88%E8%B0%88CAS/5_CAS%E5%BA%95%E5%B1%82%E5%8E%9F%E7%90%86/images/image-20200310201311367.png)

这个就类似于SVN或者Git的版本号，如果没有人更改过，就能够正常提交，否者需要先将代码pull下来，合并代码后，然后提交

![1584279678821](D:\java-\java基础.assets\1584279678821.png)

(只能确保一个共享变量的原子操作，循环时间长，ABA问题)

### CAS底层原理

首先我们先看看 atomicInteger.getAndIncrement()方法的源码

![image-20200310203030720](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/2_%E8%B0%88%E8%B0%88CAS/5_CAS%E5%BA%95%E5%B1%82%E5%8E%9F%E7%90%86/images/image-20200310203030720.png)

从这里能够看到，底层又调用了一个unsafe类的getAndAddInt方法

####  1、unsafe类

![image-20200310203350122](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/2_%E8%B0%88%E8%B0%88CAS/5_CAS%E5%BA%95%E5%B1%82%E5%8E%9F%E7%90%86/images/image-20200310203350122.png)

Unsafe是CAS的核心类，由于Java方法无法直接访问底层系统，需要通过本地（Native）方法来访问，Unsafe相当于一个后门，基于该类可以直接操作特定的内存数据。Unsafe类存在sun.misc包中，其内部方法操作可以像C的指针一样直接操作内存，因为Java中的CAS操作的执行依赖于Unsafe类的方法。

```
注意Unsafe类的所有方法都是native修饰的，也就是说unsafe类中的方法都直接调用操作系统底层资源执行相应的任务
```

为什么Atomic修饰的包装类，能够保证原子性，依靠的就是底层的unsafe类

####  2、变量valueOffset

表示该变量值在内存中的偏移地址，因为Unsafe就是根据内存偏移地址获取数据的。

![image-20200310203030720](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/2_%E8%B0%88%E8%B0%88CAS/5_CAS%E5%BA%95%E5%B1%82%E5%8E%9F%E7%90%86/images/image-20200310203030720.png)

从这里我们能够看到，通过valueOffset，直接通过内存地址，获取到值，然后进行加1的操作

#### 3、变量value用volatile修饰

保证了多线程之间的内存可见性

![image-20200310210701761](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/2_%E8%B0%88%E8%B0%88CAS/5_CAS%E5%BA%95%E5%B1%82%E5%8E%9F%E7%90%86/images/image-20200310210701761.png)

var5：就是我们从主内存中拷贝到工作内存中的值

那么操作的时候，需要比较工作内存中的值，和主内存中的值进行比较

假设执行 compareAndSwapInt返回false，那么就一直执行 while方法，直到期望的值和真实值一样

- val1：AtomicInteger对象本身
- var2：该对象值得引用地址
- var4：需要变动的数量
- var5：用var1和var2找到的内存中的真实值
  - 用该对象当前的值与var5比较
  - 如果相同，更新var5 + var4 并返回true
  - 如果不同，继续取值然后再比较，直到更新完成

这里没有用synchronized，而用CAS，这样提高了并发性，也能够实现一致性，是因为每个线程进来后，进入的do while循环，然后不断的获取内存中的值，判断是否为最新，然后在进行更新操作。

假设线程A和线程B同时执行getAndInt操作（分别跑在不同的CPU上）

1. AtomicInteger里面的value原始值为3，即主内存中AtomicInteger的 value 为3，根据JMM模型，线程A和线程B各自持有一份价值为3的副本，分别存储在各自的工作内存
2. 线程A通过getIntVolatile(var1 , var2) 拿到value值3，这是线程A被挂起（该线程失去CPU执行权）
3. 线程B也通过getIntVolatile(var1, var2)方法获取到value值也是3，此时刚好线程B没有被挂起，并执行了compareAndSwapInt方法，比较内存的值也是3，成功修改内存值为4，线程B打完收工，一切OK
4. 这是线程A恢复，执行CAS方法，比较发现自己手里的数字3和主内存中的数字4不一致，说明该值已经被其它线程抢先一步修改过了，那么A线程本次修改失败，只能够重新读取后在来一遍了，也就是在执行do while
5. 线程A重新获取value值，因为变量value被volatile修饰，所以其它线程对它的修改，线程A总能够看到，线程A继续执行compareAndSwapInt进行比较替换，直到成功。

Unsafe类 + CAS思想： 也就是自旋，自我旋转

### CAS缺点

CAS不加锁，保证一次性，但是需要多次比较

- 循环时间长，开销大（因为执行的是do while，如果比较不成功一直在循环，最差的情况，就是某个线程一直取到的值和预期值都不一样，这样就会无限循环）
- 只能保证一个共享变量的原子操作
  - 当对一个共享变量执行操作时，我们可以通过循环CAS的方式来保证原子操作
  - 但是对于多个共享变量操作时，循环CAS就无法保证操作的原子性，这个时候只能用锁来保证原子性
- 引出来ABA问题？

##  原子类AtomicInteger的ABA问题

从AtomicInteger引出下面的问题

CAS -> Unsafe -> CAS底层思想 -> ABA -> 原子引用更新 -> 如何规避ABA问题

### 什么是ABA

![image-20200311212442057](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/3_%E8%B0%88%E8%B0%88%E5%8E%9F%E5%AD%90%E7%B1%BB%E7%9A%84ABA%E9%97%AE%E9%A2%98/6_%E5%8E%9F%E5%AD%90%E7%B1%BBAtomicInteger%E7%9A%84ABA%E9%97%AE%E9%A2%98/images/image-20200311212442057.png)

假设现在有两个线程，分别是T1 和 T2，然后T1执行某个操作的时间为10秒，T2执行某个时间的操作是2秒，最开始AB两个线程，分别从主内存中获取A值，但是因为B的执行速度更快，他先把A的值改成B，然后在修改成A，然后执行完毕，T1线程在10秒后，执行完毕，判断内存中的值为A，并且和自己预期的值一样，它就认为没有人更改了主内存中的值，就快乐的修改成B，但是实际上 可能中间经历了 ABCDEFA 这个变换，也就是中间的值经历了狸猫换太子。

所以ABA问题就是，在进行获取主内存值的时候，该内存值在我们写入主内存的时候，已经被修改了N次，但是最终又改成原来的值了

###  CAS导致ABA问题

CAS算法实现了一个重要的前提，需要取出内存中某时刻的数据，并在当下时刻比较并替换，那么这个时间差会导致数据的变化。

比如说一个线程one从内存位置V中取出A，这时候另外一个线程two也从内存中取出A，并且线程two进行了一些操作将值变成了B，然后线程two又将V位置的数据变成A，这时候线程one进行CAS操作发现内存中仍然是A，然后线程one操作成功

```
尽管线程one的CAS操作成功，但是不代表这个过程就是没有问题的
```

### 原子引用

原子引用其实和原子包装类是差不多的概念，就是将一个java类，用原子引用类进行包装起来，那么这个类就具备了原子性

```java
/**
 * 原子引用
 * @author: 陌溪
 * @create: 2020-03-11-22:12
 */

class User {
    String userName;
    int age;

    public User(String userName, int age) {
        this.userName = userName;
        this.age = age;
    }

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "User{" +
                "userName='" + userName + '\'' +
                ", age=" + age +
                '}';
    }
}
public class AtomicReferenceDemo {

    public static void main(String[] args) {

        User z3 = new User("z3", 22);

        User l4 = new User("l4", 25);

        // 创建原子引用包装类
        AtomicReference<User> atomicReference = new AtomicReference<>();

        // 现在主物理内存的共享变量，为z3
        atomicReference.set(z3);

        // 比较并交换，如果现在主物理内存的值为z3，那么交换成l4
        System.out.println(atomicReference.compareAndSet(z3, l4) + "\t " + atomicReference.get().toString());

        // 比较并交换，现在主物理内存的值是l4了，但是预期为z3，因此交换失败
        System.out.println(atomicReference.compareAndSet(z3, l4) + "\t " + atomicReference.get().toString());
    }
}
```

### 基于原子引用的ABA问题

我们首先创建了两个线程，然后T1线程，执行一次ABA的操作，T2线程在一秒后修改主内存的值

```java
/**
 * ABA问题的解决，AtomicStampedReference
 * @author: 陌溪
 * @create: 2020-03-12-15:34
 */
public class ABADemo {

    /**
     * 普通的原子引用包装类
     */
    static AtomicReference<Integer> atomicReference = new AtomicReference<>(100);

    public static void main(String[] args) {

        new Thread(() -> {
            // 把100 改成 101 然后在改成100，也就是ABA
            atomicReference.compareAndSet(100, 101);
            atomicReference.compareAndSet(101, 100);
        }, "t1").start();

        new Thread(() -> {
            try {
                // 睡眠一秒，保证t1线程，完成了ABA操作
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            // 把100 改成 101 然后在改成100，也就是ABA
            System.out.println(atomicReference.compareAndSet(100, 2019) + "\t" + atomicReference.get());

        }, "t2").start();
    }
}
```

我们发现，它能够成功的修改，这就是ABA问题

![image-20200312154752973](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/3_%E8%B0%88%E8%B0%88%E5%8E%9F%E5%AD%90%E7%B1%BB%E7%9A%84ABA%E9%97%AE%E9%A2%98/6_%E5%8E%9F%E5%AD%90%E7%B1%BBAtomicInteger%E7%9A%84ABA%E9%97%AE%E9%A2%98/images/image-20200312154752973.png)



###  解决ABA问题

新增一种机制，也就是修改版本号，类似于时间戳的概念

T1： 100 1 2019 2

T2： 100 1 101 2 100 3

如果T1修改的时候，版本号为2，落后于现在的版本号3，所以要重新获取最新值，这里就提出了一个使用时间戳版本号，来解决ABA问题的思路

####  AtomicStampedReference

时间戳原子引用，来这里应用于版本号的更新，也就是每次更新的时候，需要比较期望值和当前值，以及期望版本号和当前版本号

```java
/**
 * ABA问题的解决，AtomicStampedReference
 * @author: 陌溪
 * @create: 2020-03-12-15:34
 */
public class ABADemo {

    /**
     * 普通的原子引用包装类
     */
    static AtomicReference<Integer> atomicReference = new AtomicReference<>(100);

    // 传递两个值，一个是初始值，一个是初始版本号
    static AtomicStampedReference<Integer> atomicStampedReference = new AtomicStampedReference<>(100, 1);

    public static void main(String[] args) {

        System.out.println("============以下是ABA问题的产生==========");

        new Thread(() -> {
            // 把100 改成 101 然后在改成100，也就是ABA
            atomicReference.compareAndSet(100, 101);
            atomicReference.compareAndSet(101, 100);
        }, "t1").start();

        new Thread(() -> {
            try {
                // 睡眠一秒，保证t1线程，完成了ABA操作
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            // 把100 改成 101 然后在改成100，也就是ABA
            System.out.println(atomicReference.compareAndSet(100, 2019) + "\t" + atomicReference.get());

        }, "t2").start();

        System.out.println("============以下是ABA问题的解决==========");

        new Thread(() -> {

            // 获取版本号
            int stamp = atomicStampedReference.getStamp();
            System.out.println(Thread.currentThread().getName() + "\t 第一次版本号" + stamp);

            // 暂停t3一秒钟
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            // 传入4个值，期望值，更新值，期望版本号，更新版本号
            atomicStampedReference.compareAndSet(100, 101, atomicStampedReference.getStamp(), atomicStampedReference.getStamp()+1);

            System.out.println(Thread.currentThread().getName() + "\t 第二次版本号" + atomicStampedReference.getStamp());

            atomicStampedReference.compareAndSet(101, 100, atomicStampedReference.getStamp(), atomicStampedReference.getStamp()+1);

            System.out.println(Thread.currentThread().getName() + "\t 第三次版本号" + atomicStampedReference.getStamp());

        }, "t3").start();

        new Thread(() -> {

            // 获取版本号
            int stamp = atomicStampedReference.getStamp();
            System.out.println(Thread.currentThread().getName() + "\t 第一次版本号" + stamp);

            // 暂停t4 3秒钟，保证t3线程也进行一次ABA问题
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            boolean result = atomicStampedReference.compareAndSet(100, 2019, stamp, stamp+1);

            System.out.println(Thread.currentThread().getName() + "\t 修改成功否：" + result + "\t 当前最新实际版本号：" + atomicStampedReference.getStamp());

            System.out.println(Thread.currentThread().getName() + "\t 当前实际最新值" + atomicStampedReference.getReference());


        }, "t4").start();

    }
}
```

运行结果为：

![image-20200312200434776](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/3_%E8%B0%88%E8%B0%88%E5%8E%9F%E5%AD%90%E7%B1%BB%E7%9A%84ABA%E9%97%AE%E9%A2%98/6_%E5%8E%9F%E5%AD%90%E7%B1%BBAtomicInteger%E7%9A%84ABA%E9%97%AE%E9%A2%98/images/image-20200312200434776.png)

我们能够发现，线程t3，在进行ABA操作后，版本号变更成了3，而线程t4在进行操作的时候，就出现操作失败了，因为版本号和当初拿到的不一样

## ThreadLocal 

 ThreadLocal是一个线程内部的数据存储类，通过它可以在指定的线程中存储数据，数据存储以后，只有在指定的线程中可以获取到存储的数据，对于其他线程来说则无法取到数据。 

多线程时候static一个对象 就不行啦。

#### 原理

set方法，获取线程的 ThreadLocalMap ，这个是hashmap， key是TheadLocal，value是对应存储的值。 

get方法，getMap获取ThreadLocalMap ，根据key==当前threadlocal来获取对应的value值。

从ThreadLocal的set和get方法可以看出，它们所操作的都是当前线程的ThreadLocalMap对象。

因此在不同线程中，访问同一个ThreadLocal的set和get方法，它们对ThreadLocal的读、写操作仅限于**各自线程的内部**，从而使ThreadLocal可以在多个线程中互不干扰地存储和修改数据。

## ReentrantLock

主要利用CAS+AQS队列来实现。支持公平锁和非公平锁。

 ReentrantLock的基本实现可以概括为：先通过CAS尝试获取锁。如果此时已经有线程占据了锁，那就加入AQS队列并且被挂起。当锁被释放之后，排在CLH队列队首的线程会被唤醒，然后CAS再次尝试获取锁。

## Sync和ReentrantLock的区别

Sync是JVM层面的关键字，lock是一个类；

sync不需要用户手动释放，lock需要手动释放；

sync不可以中断（除非抛异常），lock可中断（设置超时时间，调用interrupt（））；

sync是非公平锁，lock两者都可以；

sync没有condition，reentrantlock用来实现分组唤醒需要唤醒的线程们，可以精确唤醒。	 sync要么随机唤醒一个要不全部唤醒

###  举例

针对刚刚提到的区别的第5条，我们有下面这样的一个场景

```
题目：多线程之间按顺序调用，实现 A-> B -> C 三个线程启动，要求如下：
AA打印5次，BB打印10次，CC打印15次
紧接着
AA打印5次，BB打印10次，CC打印15次
..
来10轮
```

我们会发现，这样的场景在使用synchronized来完成的话，会非常的困难，但是使用lock就非常方便了

也就是我们需要实现一个链式唤醒的操作

当A线程执行完后，B线程才能执行，然后B线程执行完成后，C线程才执行

首先我们需要创建一个重入锁

```
// 创建一个重入锁
private Lock lock = new ReentrantLock();
```

然后定义三个条件，也可以称为锁的钥匙，通过它就可以获取到锁，进入到方法里面

```java
// 这三个相当于备用钥匙
private Condition condition1 = lock.newCondition();
private Condition condition2 = lock.newCondition();
private Condition condition3 = lock.newCondition();
```

然后开始记住锁的三部曲： 判断 干活 唤醒

这里的判断，为了避免虚假唤醒，一定要采用 while

干活就是把需要的内容，打印出来

唤醒的话，就是修改资源类的值，然后精准唤醒线程进行干活：这里A 唤醒B， B唤醒C，C又唤醒A

```java
    public void print5() {
        lock.lock();
        try {
            // 判断
            while(number != 1) {
                // 不等于1，需要等待
                condition1.await();
            }

            // 干活
            for (int i = 0; i < 5; i++) {
                System.out.println(Thread.currentThread().getName() + "\t " + number + "\t" + i);
            }

            // 唤醒 （干完活后，需要通知B线程执行）
            number = 2;
            // 通知2号去干活了
            condition2.signal();

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
/**
 * Synchronized 和 Lock的区别
 * @author: 陌溪
 * @create: 2020-03-17-10:18
 */

class ShareResource {
    // A 1   B 2   c 3
    private int number = 1;
    // 创建一个重入锁
    private Lock lock = new ReentrantLock();

    // 这三个相当于备用钥匙
    private Condition condition1 = lock.newCondition();
    private Condition condition2 = lock.newCondition();
    private Condition condition3 = lock.newCondition();


    public void print5() {
        lock.lock();
        try {
            // 判断
            while(number != 1) {
                // 不等于1，需要等待
                condition1.await();
            }

            // 干活
            for (int i = 0; i < 5; i++) {
                System.out.println(Thread.currentThread().getName() + "\t " + number + "\t" + i);
            }

            // 唤醒 （干完活后，需要通知B线程执行）
            number = 2;
            // 通知2号去干活了
            condition2.signal();

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void print10() {
        lock.lock();
        try {
            // 判断
            while(number != 2) {
                // 不等于1，需要等待
                condition2.await();
            }

            // 干活
            for (int i = 0; i < 10; i++) {
                System.out.println(Thread.currentThread().getName() + "\t " + number + "\t" + i);
            }

            // 唤醒 （干完活后，需要通知C线程执行）
            number = 3;
            // 通知2号去干活了
            condition3.signal();

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void print15() {
        lock.lock();
        try {
            // 判断
            while(number != 3) {
                // 不等于1，需要等待
                condition3.await();
            }

            // 干活
            for (int i = 0; i < 15; i++) {
                System.out.println(Thread.currentThread().getName() + "\t " + number + "\t" + i);
            }

            // 唤醒 （干完活后，需要通知C线程执行）
            number = 1;
            // 通知1号去干活了
            condition1.signal();

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}

public class SyncAndReentrantLockDemo {

    public static void main(String[] args) {

        ShareResource shareResource = new ShareResource();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                    shareResource.print5();
            }
        }, "A").start();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                shareResource.print10();
            }
        }, "B").start();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                shareResource.print15();
            }
        }, "C").start();
    }
}
```



## 手写Lock锁三部曲

任何锁的实现，都是这么实现的。	

1.实现Lock接口。

2.多线程场景--安全性。

syn锁的底层是CAS。

![1586007167144](D:\java-\java基础.assets\1586007167144.png)



![1586006917828](D:\java-\java基础.assets\1586006917828.png)



# Tomcat

**类加载机制，**

启动类加载器

系统类加载器

通用类加载器	

webapp加载器。WEB-INF/classes

webapp加载器。WEB-INF/lib



tomcat8可以配置不打破双亲委派机制

## 加载顺序

web加载器

不到再向上Common，System

 

不加载到启动类加载器是因为 **主要是为了防止一些基础类会被web中的类覆盖** 。



# 线程池

获取多线程的方法，我们都知道有三种，还有一种是实现Callable接口

- 实现Runnable接口（无返回值，不抛异常）
- 实现Callable接口
- 继承实例化Thread类
- 使用线程池获取

## Callable接口

Callable接口，是一种让线程执行完成后，能够返回结果的

在说到Callable接口的时候，我们不得不提到Runnable接口

```java
/**
 * 实现Runnable接口
 */
class MyThread implements Runnable {

    @Override
    public void run() {

    }
}
```

我们知道，实现Runnable接口的时候，需要重写run方法，也就是线程在启动的时候，会自动调用的方法

同理，我们实现Callable接口，也需要实现call方法，但是这个时候我们还需要有返回值，这个Callable接口的应用场景一般就在于批处理业务，比如转账的时候，需要给一会返回结果的状态码回来，代表本次操作成功还是失败

```java
/**
 * Callable有返回值
 * 批量处理的时候，需要带返回值的接口（例如支付失败的时候，需要返回错误状态）
 *
 */
class MyThread2 implements Callable<Integer> {

    @Override
    public Integer call() throws Exception {
        System.out.println("come in Callable");
        return 1024;
    }
}
```

最后我们需要做的就是通过Thread线程， 将MyThread2实现Callable接口的类包装起来

这里需要用到的是FutureTask类，他实现了Runnable接口，并且还需要传递一个实现Callable接口的类作为构造函数

```java
// FutureTask：实现了Runnable接口，构造函数又需要传入 Callable接口
// 这里通过了FutureTask接触了Callable接口
FutureTask<Integer> futureTask = new FutureTask<>(new MyThread2());
```

然后在用Thread进行实例化，传入实现Runnabnle接口的FutureTask的类

```java
Thread t1 = new Thread(futureTask, "aaa");
t1.start();
```

最后通过 utureTask.get() 获取到返回值

```java
// 输出FutureTask的返回值
System.out.println("result FutureTask " + futureTask.get());
```

这就相当于原来我们的方式是main方法一条龙之心，后面在引入Callable后，对于执行比较久的线程，可以单独新开一个线程进行执行，最后在进行汇总输出

最后需要注意的是 要求获得Callable线程的计算结果，如果没有计算完成就要去强求，会导致阻塞，直到计算完成

![image-20200317152541284](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/10_%E7%BA%BF%E7%A8%8B%E6%B1%A0/images/image-20200317152541284.png)



也就是说 futureTask.get() 需要放在最后执行，这样不会导致主线程阻塞

也可以使用下面算法，使用类似于自旋锁的方式来进行判断是否运行完毕

```java
// 判断futureTask是否计算完成
while(!futureTask.isDone()) {

}
```

###  注意

多个线程执行 一个FutureTask的时候，只会计算一次

```java
FutureTask<Integer> futureTask = new FutureTask<>(new MyThread2());

// 开启两个线程计算futureTask
new Thread(futureTask, "AAA").start();
new Thread(futureTask, "BBB").start();
```

如果我们要两个线程同时计算任务的话，那么需要这样写，需要定义两个futureTask

```java
FutureTask<Integer> futureTask = new FutureTask<>(new MyThread2());
FutureTask<Integer> futureTask2 = new FutureTask<>(new MyThread2());

// 开启两个线程计算futureTask
new Thread(futureTask, "AAA").start();

new Thread(futureTask2, "BBB").start();
```

## ThreadPoolExecutor

###  为什么用线程池

线程池做的主要工作就是控制运行的线程的数量，处理过程中，将任务放入到队列中，然后线程创建后，启动这些任务，如果线程数量超过了最大数量的线程排队等候，等其它线程执行完毕，再从队列中取出任务来执行。



每个线程都要通过new Thread(xxRunnable).start()的方式来创建并运行一个线程，线程少的话这不会是问题，而真实环境可能会开启多个线程让系统和程序达到最佳效率，当线程数达到一定数量就会耗尽系统的CPU和内存资源，也会造成GC频繁收集和停顿，因为每次创建和销毁一个线程都是要消耗系统资源的，如果为每个任务都创建线程这无疑是一个很大的性能瓶颈。所以，线程池中的线程复用极大节省了系统资源，当线程一段时间不再有任务处理时它也会自动销毁，而不会长驻内存 。



它的主要特点为：线程复用、控制最大并发数、管理线程

线程池中的任务是放入到阻塞队列中的



### 线程池的好处。

多核处理的好处是：省略的上下文的切换开销

原来我们实例化对象的时候，是使用 new关键字进行创建，到了Spring后，我们学了IOC依赖注入，发现Spring帮我们将对象已经加载到了Spring容器中，只需要通过@Autowrite注解，就能够自动注入，从而使用

因此使用多线程有下列的好处

- 降低资源消耗。通过重复利用已创建的线程，降低线程创建和销毁造成的消耗
- 提高响应速度。当任务到达时，任务可以不需要等到线程创建就立即执行
- 提高线程的可管理性。线程是稀缺资源，如果无线创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控

### 架构说明



![image-20200317175241007](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/10_%E7%BA%BF%E7%A8%8B%E6%B1%A0/images/image-20200317175241007.png)



### 创建线程池

- Executors.newFixedThreadPool(int i) ：创建一个拥有 i 个线程的线程池
  - 执行长期的任务，性能好很多
  - 创建一个定长线程池，可控制线程数最大并发数，超出的线程会在队列中等待
- Executors.newSingleThreadExecutor：创建一个只有1个线程的 单线程池
  - 一个任务一个任务执行的场景
  - 创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序执行
- Executors.newCacheThreadPool(); 创建一个可扩容的线程池
  - 执行很多短期异步的小程序或者负载教轻的服务器
  - 创建一个可缓存线程池，如果线程长度超过处理需要，可灵活回收空闲线程，如无可回收，则新建新线程

具体使用，首先我们需要使用Executors工具类，进行创建线程池，这里创建了一个拥有5个线程的线程池

```java
// 一池5个处理线程（用池化技术，一定要记得关闭）
ExecutorService threadPool = Executors.newFixedThreadPool(5);

// 创建一个只有一个线程的线程池
ExecutorService threadPool = Executors.newSingleThreadExecutor();

// 创建一个拥有N个线程的线程池，根据调度创建合适的线程
ExecutorService threadPool = Executors.newCacheThreadPool();
```

然后我们执行下面的的应用场景

```
模拟10个用户来办理业务，每个用户就是一个来自外部请求线程
```

我们需要使用 threadPool.execute执行业务，execute需要传入一个实现了Runnable接口的线程

```java
threadPool.execute(() -> {
	System.out.println(Thread.currentThread().getName() + "\t 给用户办理业务");
});
```

然后我们使用完毕后关闭线程池

```java
threadPool.shutdown();
```

完整代码为：

```java
/**
 * 第四种获取 / 使用 Java多线程的方式，通过线程池
 * @author: 陌溪
 * @create: 2020-03-17-15:59
 */
public class MyThreadPoolDemo {
    public static void main(String[] args) {

        // Array  Arrays(辅助工具类)
        // Collection Collections(辅助工具类)
        // Executor Executors(辅助工具类)


        // 一池5个处理线程（用池化技术，一定要记得关闭）
        ExecutorService threadPool = Executors.newFixedThreadPool(5);

        // 模拟10个用户来办理业务，每个用户就是一个来自外部请求线程
        try {

            // 循环十次，模拟业务办理，让5个线程处理这10个请求
            for (int i = 0; i < 10; i++) {
                final int tempInt = i;
                threadPool.execute(() -> {
                    System.out.println(Thread.currentThread().getName() + "\t 给用户:" + tempInt + " 办理业务");
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            threadPool.shutdown();
        }

    }
}
```

最后结果：

```
pool-1-thread-1	 给用户:0 办理业务
pool-1-thread-5	 给用户:4 办理业务
pool-1-thread-1	 给用户:5 办理业务
pool-1-thread-4	 给用户:3 办理业务
pool-1-thread-2	 给用户:1 办理业务
pool-1-thread-3	 给用户:2 办理业务
pool-1-thread-2	 给用户:9 办理业务
pool-1-thread-4	 给用户:8 办理业务
pool-1-thread-1	 给用户:7 办理业务
pool-1-thread-5	 给用户:6 办理业务
```

我们能够看到，一共有5个线程，在给10个用户办理业务

###  底层实现

我们通过查看源码，点击了Executors.newSingleThreadExecutor 和 Executors.newFixedThreadPool能够发现底层都是使用了ThreadPoolExecutor

![image-20200317182004293](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/10_%E7%BA%BF%E7%A8%8B%E6%B1%A0/images/image-20200317182004293.png)

我们可以看到线程池的内部，还使用到了LinkedBlockingQueue 链表阻塞队列

同时在查看Executors.newCacheThreadPool 看到底层用的是 SynchronousBlockingQueue阻塞队列

最后查看一下，完整的三个创建线程的方法

![image-20200317183202992](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/10_%E7%BA%BF%E7%A8%8B%E6%B1%A0/images/image-20200317183202992.png)



##  线程池的重要参数

![image-20200317183600957](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/10_%E7%BA%BF%E7%A8%8B%E6%B1%A0/images/image-20200317183600957.png)

线程池在创建的时候，一共有7大参数

- corePoolSize：核心线程数，线程池中的常驻核心线程数
  - 在创建线程池后，当有请求任务来之后，就会安排池中的线程去执行请求任务，近似理解为今日当值线程
  - 当线程池中的线程数目达到corePoolSize后，就会把到达的队列放到缓存队列中
- maximumPoolSize：线程池能够容纳同时执行的最大线程数，此值必须大于等于1、
  - 相当有扩容后的线程数，这个线程池能容纳的最多线程数
- keepAliveTime：多余的空闲线程存活时间
  - 当线程池数量超过corePoolSize时，当空闲时间达到keepAliveTime值时，多余的空闲线程会被销毁，直到只剩下corePoolSize个线程为止
  - 默认情况下，只有当线程池中的线程数大于corePoolSize时，keepAliveTime才会起作用
- unit：keepAliveTime的单位
- workQueue：任务队列，被提交的但未被执行的任务（类似于银行里面的候客区）
  - LinkedBlockingQueue：链表阻塞队列
  - SynchronousBlockingQueue：同步阻塞队列
- threadFactory：表示生成线程池中工作线程的线程工厂，用于创建线程池 一般用默认即可
- handler：拒绝策略，表示当队列满了并且工作线程大于线程池的最大线程数（maximumPoolSize3）时，如何来拒绝请求执行的Runnable的策略

当营业窗口和阻塞队列中都满了时候，就需要设置拒绝策略

![image-20200317201150197](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/10_%E7%BA%BF%E7%A8%8B%E6%B1%A0/images/image-20200317201150197.png)



## 线程池底层工作原理

### 线程池运行架构图

![image-20200318154414717](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/10_%E7%BA%BF%E7%A8%8B%E6%B1%A0/images/image-20200318154414717.png)

文字说明

1. 在创建了线程池后，等待提交过来的任务请求
2. 当调用execute()方法添加一个请求任务时，线程池会做出如下判断
   1. 如果正在运行的线程池数量小于corePoolSize，那么马上创建线程运行这个任务
   2. 如果正在运行的线程数量大于或等于corePoolSize，那么将这个任务放入队列
   3. 如果这时候队列满了，并且正在运行的线程数量还小于maximumPoolSize，那么还是创建非核心线程like运行这个任务；
   4. 如果队列满了并且正在运行的线程数量大于或等于maximumPoolSize，那么线程池会启动饱和拒绝策略来执行
3. 当一个线程完成任务时，它会从队列中取下一个任务来执行
4. 当一个线程无事可做操作一定的时间(keepAliveTime)时，线程池会判断：
   1. 如果当前运行的线程数大于corePoolSize，那么这个线程就被停掉
   2. 所以线程池的所有任务完成后，它会最终收缩到corePoolSize的大小

以顾客去银行办理业务为例，谈谈线程池的底层工作原理

1. 最开始假设来了两个顾客，因为corePoolSize为2，因此这两个顾客直接能够去窗口办理
2. 后面又来了三个顾客，因为corePool已经被顾客占用了，因此只有去候客区，也就是阻塞队列中等待
3. 后面的人又陆陆续续来了，候客区可能不够用了，因此需要申请增加处理请求的窗口，这里的窗口指的是线程池中的线程数，以此来解决线程不够用的问题
4. 假设受理窗口已经达到最大数，并且请求数还是不断递增，此时候客区和线程池都已经满了，为了防止大量请求冲垮线程池，已经需要开启拒绝策略
5. 临时增加的线程会因为超过了最大存活时间，就会销毁，最后从最大数削减到核心数



## 拒绝策略

以下所有拒绝策略都实现了RejectedExecutionHandler接口

- AbortPolicy：默认，直接抛出RejectedExcutionException异常，阻止系统正常运行
- DiscardPolicy：直接丢弃任务，不予任何处理也不抛出异常，如果允许任务丢失，这是一种好方案
- CallerRunsPolicy：该策略既不会抛弃任务，也不会抛出异常，而是将某些任务回退到调用者
- DiscardOldestPolicy：抛弃队列中等待最久的任务，然后把当前任务加入队列中尝试再次提交当前任务



## 为什么不用默认创建的线程池？

线程池创建的方法有：固定数的，单一的，可变的，那么在实际开发中，应该使用哪个？

我们一个都不用，在生产环境中是使用自己自定义的

为什么不用Executors中JDK提供的？

根据阿里巴巴手册：并发控制这章

- 线程资源必须通过线程池提供，不允许在应用中自行显式创建线程
  - 使用线程池的好处是减少在创建和销毁线程上所消耗的时间以及系统资源的开销，解决资源不足的问题，如果不使用线程池，有可能造成系统创建大量同类线程而导致消耗完内存或者“过度切换”的问题
- 线程池不允许使用Executors去创建，而是通过ThreadToolExecutors的方式，这样的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险
  - Executors返回的线程池对象弊端如下：
    - FixedThreadPool和SingleThreadPool：
      - 运行的请求队列长度为：Integer.MAX_VALUE，可能会堆积大量的请求，从而导致OOM
    - CacheThreadPool和ScheduledThreadPool
      - 运行的请求队列长度为：Integer.MAX_VALUE，可能会堆积大量的请求，从而导致OOM



## 手写线程池

### 采用默认拒绝策略

从上面我们知道，因为默认的Executors创建的线程池，底层都是使用LinkBlockingQueue作为阻塞队列的，而LinkBlockingQueue虽然是有界的，但是它的界限是 Integer.MAX_VALUE 大概有20多亿，可以相当是无界的了，因此我们要使用ThreadPoolExecutor自己手动创建线程池，然后指定阻塞队列的大小

下面我们创建了一个 核心线程数为2，最大线程数为5，并且阻塞队列数为3的线程池

```java
        // 手写线程池
        final Integer corePoolSize = 2;
        final Integer maximumPoolSize = 5;
        final Long keepAliveTime = 1L;

        // 自定义线程池，只改变了LinkBlockingQueue的队列大小
        ExecutorService executorService = new ThreadPoolExecutor(
                corePoolSize,
                maximumPoolSize,
                keepAliveTime,
                TimeUnit.SECONDS,
                new LinkedBlockingQueue<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy());
```

然后使用for循环，模拟10个用户来进行请求

```java
      // 模拟10个用户来办理业务，每个用户就是一个来自外部请求线程
        try {

            // 循环十次，模拟业务办理，让5个线程处理这10个请求
            for (int i = 0; i < 10; i++) {
                final int tempInt = i;
                executorService.execute(() -> {
                    System.out.println(Thread.currentThread().getName() + "\t 给用户:" + tempInt + " 办理业务");
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            executorService.shutdown();
        }
```

但是在用户执行到第九个的时候，触发了异常，程序中断

```
pool-1-thread-1	 给用户:0 办理业务
pool-1-thread-4	 给用户:6 办理业务
pool-1-thread-3	 给用户:5 办理业务
pool-1-thread-2	 给用户:1 办理业务
pool-1-thread-2	 给用户:4 办理业务
pool-1-thread-5	 给用户:7 办理业务
pool-1-thread-4	 给用户:2 办理业务
pool-1-thread-3	 给用户:3 办理业务
java.util.concurrent.RejectedExecutionException: Task com.moxi.interview.study.thread.MyThreadPoolDemo$$Lambda$1/1747585824@4dd8dc3 rejected from java.util.concurrent.ThreadPoolExecutor@6d03e736[Running, pool size = 5, active threads = 3, queued tasks = 0, completed tasks = 5]
	at java.util.concurrent.ThreadPoolExecutor$AbortPolicy.rejectedExecution(ThreadPoolExecutor.java:2047)
	at java.util.concurrent.ThreadPoolExecutor.reject(ThreadPoolExecutor.java:823)
	at java.util.concurrent.ThreadPoolExecutor.execute(ThreadPoolExecutor.java:1369)
	at com.moxi.interview.study.thread.MyThreadPoolDemo.main(MyThreadPoolDemo.java:34)
```

这是因为触发了拒绝策略，而我们设置的拒绝策略是默认的AbortPolicy，也就是抛异常的

触发条件是，请求的线程大于 阻塞队列大小 + 最大线程数 = 8 的时候，也就是说第9个线程来获取线程池中的线程时，就会抛出异常从而报错退出。

### 采用CallerRunsPolicy拒绝策略

当我们更好其它的拒绝策略时，采用CallerRunsPolicy拒绝策略，也称为回退策略，就是把任务丢回原来的请求开启线程着，我们看运行结果

```
pool-1-thread-1	 给用户:0 办理业务
pool-1-thread-5	 给用户:7 办理业务
pool-1-thread-4	 给用户:6 办理业务
main	 给用户:8 办理业务
pool-1-thread-3	 给用户:5 办理业务
pool-1-thread-2	 给用户:1 办理业务
pool-1-thread-3	 给用户:9 办理业务
pool-1-thread-4	 给用户:4 办理业务
pool-1-thread-5	 给用户:3 办理业务
pool-1-thread-1	 给用户:2 办理业务
```

我们发现，输出的结果里面出现了main线程，因为线程池出发了拒绝策略，把任务回退到main线程，然后main线程对任务进行处理

### 采用 DiscardPolicy 拒绝策略

```
pool-1-thread-1	 给用户:0 办理业务
pool-1-thread-3	 给用户:5 办理业务
pool-1-thread-1	 给用户:2 办理业务
pool-1-thread-2	 给用户:1 办理业务
pool-1-thread-1	 给用户:4 办理业务
pool-1-thread-5	 给用户:7 办理业务
pool-1-thread-4	 给用户:6 办理业务
pool-1-thread-3	 给用户:3 办理业务
```

采用DiscardPolicy拒绝策略会，线程池会自动把后面的任务都直接丢弃，也不报异常，当任务无关紧要的时候，可以采用这个方式

### 采用DiscardOldestPolicy拒绝策略

```
pool-1-thread-1	 给用户:0 办理业务
pool-1-thread-4	 给用户:6 办理业务
pool-1-thread-1	 给用户:4 办理业务
pool-1-thread-3	 给用户:5 办理业务
pool-1-thread-2	 给用户:1 办理业务
pool-1-thread-1	 给用户:9 办理业务
pool-1-thread-4	 给用户:8 办理业务
pool-1-thread-5	 给用户:7 办理业务
```

这个策略和刚刚差不多，会把最久的队列中的任务替换掉

## 线程池的合理参数

生产环境中如何配置 corePoolSize 和 maximumPoolSize

这个是根据具体业务来配置的，分为CPU密集型和IO密集型

- CPU密集型

CPU密集的意思是该任务需要大量的运算，而没有阻塞，CPU一直全速运行

CPU密集任务只有在真正的多核CPU上才可能得到加速（通过多线程）

而在单核CPU上，无论你开几个模拟的多线程该任务都不可能得到加速，因为CPU总的运算能力就那些

CPU密集型任务配置尽可能少的线程数量：

一般公式：CPU核数 + 1个线程数

- IO密集型

由于IO密集型任务线程并不是一直在执行任务，则可能多的线程，如 CPU核数 * 2

IO密集型，即该任务需要大量的IO操作，即大量的阻塞

在单线程上运行IO密集型的任务会导致浪费大量的CPU运算能力花费在等待上

所以IO密集型任务中使用多线程可以大大的加速程序的运行，即使在单核CPU上，这种加速主要就是利用了被浪费掉的阻塞时间。

IO密集时，大部分线程都被阻塞，故需要多配置线程数：

参考公式：CPU核数 / (1 - 阻塞系数) 阻塞系数在0.8 ~ 0.9左右

例如：8核CPU：8/ (1 - 0.9) = 80个线程数



# 操作系统

## **一**、内核态和用户态

 	用户态：当一个进程在执行自己的用户空间代码块时，处于用户态
	内核态：当一个进程因为某些原因陷入内核空间，执行内核代码块时，处于内核态.  此时处理器处于特权级最高的（0级）内核代码中执行  ，执行的内核代码会使用当前进程的内核栈  即此时处理器在特权级最低的（3级）用户代码中运行。 



## **二**、操作系统的状态：就绪（差cpu）、运行、阻塞。

就绪是等待处理机，阻塞可能是等待输入输出。

## **三**、进程间的通信方式有什么？

- 进程同步：控制多个进程按一定顺序执行；

- 进程通信：进程间传输信息。

  1.管道：

  ​		 半双工，即不能同时在两个方向上传输数据。有的系统可能		支持全双工。 只能在父子进程间 

  2. FIFO：命名管道

      去除了管道只能在父子进程中使用的限制。 

     3.消息队列

     ​	 消息队列可以独立于读写进程存在，从而避免了 FIFO 中同步管道的打开和关闭时可能产生的困难 

     ​	 避免FIFO同步阻塞问题，不需要进程提供同步方法

     ​	可以有选择性接受消息。

     4.信号量

      它是一个计数器，用于为多个进程提供对共享数据对象的访问  **提供了一个不同进程或者进程的不同线程之间访问同步的手段**。 

     5.共享存储

      允许多个进程共享一个给定的存储区 需要使用信号量用来同步对共享存储的访问。

     6.套接字

      与其它通信机制不同的是，它可用于不同机器间的进程通信。 



## 五：进程同步的几种方式

临界区： 对临界资源进行访问的那段代码称为临界区。 

同步与互斥： 多个进程按一定顺序执行；  多个进程在同一时刻只有一个进程能进入临界区。 

信号量： 如果信号量的取值只能为 0 或者 1，那么就成为了 **互斥量（Mutex）** ，0 表示临界区已经加锁，1 表示临界区解锁。 

事件：用来通知线程有一些事件发生，推动后续任务。

### 线程进程

 **对操作系统来说，线程是最小的执行单元，进程是最小的资源管理单元。** 

四：**线程的状态：

new（新建）

runnable（就绪状态）

running（运行状态）

blocked（阻塞状态）

waiting（等待阻塞）

Dead（死亡状态）

进程的状态：

​	创建态、活动就绪、执行态、终止态、静止就绪、静止阻塞、活动阻塞。

#### 进程调度算法

**先来先服务调度算法 FCFS **：根据进程就绪状态进行排序。完成后再处理下一个。

**（短作业）进程优先调度算法**：就是选进程运行时间短的先进行。

**优先权调度算法**：非抢占式的就是一个进去调度了 ，出现一个优先权更高的 ，也得先执行玩这个；而抢占式就是立即停止这个，去执行优先权更高的。

**时间片轮转法**： 系统将所有的就绪进程按先来先服务的原则排成一个队列，每次调度时，把CPU 分配给队首进程，并令其执行一个时间片。 执行结束后，将进程送入队列的末尾； 可以保证就绪队列中的所有进程在一给定的时间内均能获得一时间片的处理机执行时间。换言之，系统能在给定的时间内响应所有用户的请求。 

### 协程

比线程更轻量级的存在。

协程由程序控制。

 协程的暂停完全由程序控制，线程的阻塞状态是由操作系统内核来进行切换



### 内存泄漏

用profiler， 我们一般查找相同对象多的，如果同一个对象在内存中多次新建，而内存垃圾回收器回收之后也同样存在，说明该对象存在内存泄漏，  

# 计算机网络

1.基于TCP的应用层协议有：SMTP、TELNET、HTTP、FTP
   基于UDP的应用层协议：DNS、TFTP（简单文件传输协议）、
	RIP（路由选择协议）、DHCP、BOOTP（是DHCP的前身）、IGMP（Internet组管理协议）

2.53号端口为DNS，22号为ssh



![1586789663923](D:\java-\java基础.assets\1586789663923.png)



## 层

 

**五**、物理层（网卡、中继器）、数据链路层（交换机、网桥）、网络层（路由器）、传输层（TCP和UDP）、会话层、表示层、应用层（http）

​	TCP\IP  链路层、网络层、传输层、应用层

​	**链路层**：以太网帧的格式

​					MTU的概念

​					ARP\RARP协议

​							ARP 为 IP 地址到对应的硬件地址提供动态映射。

​					ARP的报文格式

​						![1584158110177](D:\java-\java基础.assets\1584158110177.png)

​				![1584158134251](D:\java-\java基础.assets\1584158134251.png)

​					ARP查询原理

​					ARP缓存

​	**网络层**：IP首部格式

​					IP分片

​					IP选路

​					ICMP协议

​							报文格式	

​							报文分类

​	**传输层**：UDP协议（特点+各个字段）

​					TCP协议（特点、首部、）

​					TCP连接控制（三、四、同时打开、同时关闭、半关闭）

​					TCP流量控制机制（滑动窗口、慢启动、拥塞避免、快速重							传、快速恢复）

​					TCP超时重传机制：定时器

​					TCP\UDP伪包头

​	**应用层**：DNS协议

​							DNS名字空间、指针查询基本原理、DNS缓存

​					FTP协议（两条连接、两种工作模式、各种响应码）

​					FTP断点续传、匿名FTP

​					**HTTP协议**

​							报文格式：请求报文、响应报文、请求头各种字 段、								响应头各种字段、http状态码			

​					HTTPS协议：详细握手过程



​				![1584102447568](D:\java-\java基础.assets\1584102447568.png)	



## 从输入一个url到页面都经历了什么

①缓存解析：先去缓存看看有没有： 浏览器缓存-系统缓存-路由器缓存 

②DNS解析： 网址到IP地址的转换，这个过程就是DNS解析 

③TCP连接

![1583772597118](C:\Users\56495\AppData\Roaming\Typora\typora-user-images\1583772597118.png)

④发送HTTP请求： 浏览器开始向服务器发送http请求，请求数据包。请求信息包含一个头部和一个请求体 

⑤服务器处理请求并返回HTTP报文： 服务器收到浏览器发送的请求信息，返回一个响应头和一个响应体。 

⑥浏览器解析渲染页面： 浏览器收到服务器发送的响应头和响应体，进行客户端渲染，生成Dom树、解析css样式、js交互。 

连接结束





## 服务器的501、502、503、504

**500**：服务器内部错误。 你的用户权限的问题导致，或者是数据库连接出现了错误 

**501**： 服务器还是不具有请求功能的 

**502**： WEB服务器故障，  可能是由于程序进程不够 ， 请求的php-fpm已经执行 ，但没有执行完成

可能原因：

1、Nginx服务器，php-cgi进程数不够用；

2、PHP执行时间过长；

3、php-cgi进程死掉；

**503**： 服务器目前无法使用，系统维护服务器暂时的无法处理客户端的请求，这只是暂时状态 

**504**： 错误表示超时，是指客户端所发出的请求没有到达网关， 

**505**： 服务器不支持请求中所用的HTTP协议版本。



## http和https的区别

1. HTTPS协议需要到ca申请证书，一般免费证书较少，因而需要一定费用。
2. HTTP是超文本传输协议，信息是明文传输，HTTPS则是具有安全性的SSL加密传输协议。
3. HTTP的连接很简单，是无状态的；HTTPS协议是由SSL+HTTP协议构建的可进行加密传输，身份认证的网络协议，比HTTP协议安全。



## 什么是Nginx？



 是一个[http服务器](http://baike.baidu.com/link?url=a2dLY11NbWgcCzbX1s7JDyWLOh_QFjVlC2wys--TLKbZybTCA8oEP7c-5gEDCK35jFmQHG0YFRoAVEI8M7cbFcp74nDVgz1ckZiWAuntvCF_oxMSMDlDIWEGGN-63mTb)  及反向代理服务器

location？

 作用：基于Nginx服务器接收到的请求字符串，虚拟主机名称（ip，域名）、url匹配，对特定请求进行处理。

**Nginx的负载均衡**：

轮询：每个请求按照顺序逐一分配到不同后端服务器；

指定权重：指定轮训几率，weight和访问几率成正比；

ip_hash：每个请求按照ip的hash结果分配，每个访客固定到一个后端服务器，解决session问题，可以使用一致性哈希算法或者加权哈希（有的性能比较好，让更多流量打在他身上）；

fair：按后端服务器的响应时间来分配请求，响应时间短的优先分配。

url_hash： 按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器，后端服务器为缓存时比较有效。  



## 负载均衡



## TCP（ Transmission Control Protocol，传输控制协议 ）

 TCP是一种面向连接（连接导向）的、可靠的基于字节流的传输层通信协议。TCP将用户数据打包成报文段，它发送后启动一个定时器，另一端收到的数据进行确认、对失序的数据重新排序、丢弃重复数据。

### 特点

- TCP是面向连接的运输层协议
- 每一条TCP连接只能有两个端点，每一条TCP连接只能是点对点的
- TCP提供可靠交付的服务
- TCP提供全双工通信。数据在两个方向上独立的进行传输。因此，连接的每一端必须保持每个方向上的传输数据序号。
- 面向字节流。面向字节流的含义：虽然应用程序和TCP交互是一次一个数据块，但TCP把应用程序交下来的数据仅仅是一连串的无结构的字节流。

 

### tcp三次握手

![1584107628688](D:\java-\java基础.assets\1584107628688.png)

seq是报文序列号， SYN=1是同步，ACK是确认位=1是有效的，ack是确认位字段的值。告诉服务器我要连接，并告诉他我的发送能力。然后返回一个接收能力，

#### 为什么是三次呢？

​	客户端发送请求报文，但是网络延迟滞留了，有个定时器发现没有接收到确认报文，故重新发，这次接收到了。但如果这个滞留的又恢复了，这样第三次的意义就是告诉服务器我不需要了。

### tcp四次挥手

![1584108974396](D:\java-\java基础.assets\1584108974396.png)

FIN=1表示要关闭 ，seq序列号

如果有数据还要传送，则需要再来一次。

最后加2秒是为了防止服务器没有发送成功。

## TCP和UDP

UDP：面向非连接。随时可以发。不可靠。报文模式

TCP：连接。可靠。（顺序是一致的。）流量控制和拥塞控制。流模式。

## TCP 可靠性保证

1.确认应答机制。（三次握手）

2.检验和。

3.序列号。每个字节的数据进行编号，可以去重。

4.超时重传。

5.连接管理机制。

6.流量控制和拥塞控制。



## TCP 流量控制机制和拥塞控制机制

**流量控制**：

一种固定大小的窗口，一种滑动窗口。 流量控制根本目的是防止分组丢失，它是构成TCP可靠性的一方面。 

发送方在发送过程中始终保持着一个发送窗口，只有落在发送窗口内的帧才允许被发送；同时接收方也维持着一个接收窗口，只有落在接收窗口内的帧才允许接收。这样通过调整发送方窗口和接收方窗口的大小可以实现流量控制。 每个TCP/IP主机支持全双工数据传输，因此TCP有两个滑动窗口：一个用于接收数据，另一个用于发送数据。 

**流量控制引发的死锁？怎么避免死锁的发生？**

当发送者收到了一个窗口为0的应答，发送者便停止发送，等待接收者的下一个应答。但是如果这个窗口不为0的应答在传输过程丢失，发送者一直等待下去，而接收者以为发送者已经收到该应答，等待接收新数据，这样双方就相互等待，从而产生死锁。
为了避免流量控制引发的死锁，TCP使用了持续计时器。每当发送者收到一个零窗口的应答后就启动该计时器。时间一到便主动发送报文询问接收者的窗口大小。若接收者仍然返回零窗口，则重置该计时器继续等待；若窗口不为0，则表示应答报文丢失了，此时重置发送窗口后开始发送，这样就避免了死锁的产生。

**拥塞控制：** 拥塞控制是作用于网络的，它是防止过多的数据注入到网络中，避免出现网络负载过大的情况 

 **慢开始和拥塞避免** 



 发送方维持一个拥塞窗口 cwnd ( congestion window )的状态变量。拥塞窗口的大小取决于网络的拥塞程度，并且动态地在变化。发送方让自己的发送窗口等于拥塞。 

**慢开始：**先探测一下，即由小到大逐渐增大发送窗口。每经过一个传输轮次，拥塞窗口 cwnd 就加倍。慢开始的“慢”并不是指cwnd的增长速率慢，而是指在TCP开始发送报文段时先设置cwnd=1，使得发送方在开始时只发送一个报文段（目的是试探一下网络的拥塞情况），然后再逐渐增大cwnd。慢开始算法只是在TCP连接建立时和网络出现超时时才使用。

 **拥塞避免算法：**让拥塞窗口cwnd缓慢地增大，即每经过一个往返时间RTT就把发送方的拥塞窗口cwnd加1，而不是加倍。这样拥塞窗口cwnd按线性规律缓慢增长，比慢开始算法的拥塞窗口增长速率缓慢得多。 

 **快重传和快恢复**
**快重传算法**：首先要求接收方每收到一个失序的报文段后就立即发出重复确认（为的是使发送方及早知道有报文段没有到达对方）而不要等到自己发送数据时才进行捎带确认。 然后接收到三个确认以后就重传没成功的报文。

**快恢复算法**： 当发送方连续收到三个重复确认时，就执行“乘法减小”算法，把ssthresh门限减半（为了预防网络发生拥塞）。但是接下去并不执行慢开始算法 

##  **HTTP**（HyperText Transfer Protocol，超文本传输协议） 

应用层协议。

### 特点

- 支持客户/服务器模式。
- 简单快速：客户向服务器请求服务时，只需传送请求方法和路径。由于HTTP协议简单，使得HTTP服务器的程序规模小，因而通信速度很快。
- 灵活：HTTP允许传输任意类型的数据对象。正在传输的类型由Content-Type加以标记。
- 无连接：无连接的含义是限制每次连接只处理一个请求。服务器处理完客户的请求，并收到客户的应答后，即断开连接。采用这种方式可以节省传输时间。
- 无状态：HTTP协议是无状态协议。无状态是指协议对于事务处理没有记忆能力。缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可能导致每次连接传送的数据量增大。另一方面，在服务器不需要先前信息时它的应答就较快。为了解决这个问题， Web程序引入了Cookie机制来维护状态。

### 工作原理

 HTTP协议定义Web客户端如何从Web服务器请求Web页面，以及服务器如何把Web页面传送给客户端。HTTP协议采用了请求/响应模型。客户端向服务器发送一个请求报文，请求报文包含请求的方法、URL、协议版本、请求头部和请求数据。服务器以一个状态行作为响应，响应的内容包括协议的版本、成功或者错误代码、服务器信息、响应头部和响应数据。 

### 状态码

 服务器的回应被定义在几个**状态码**之间：5开头表示服务器错误，4开头表示客户端错误，3开头表示需要做进一步处理，2开头表示成功，1开头表示在请求被接受处理的同时提供的额外信息。 

### get和post的区别

【1】GET请求可被缓存，POST请求不能被缓存。
【2】GET请求被保留着浏览器历史记录中，POST请求不会被保留。
【3】GET请求能被收藏至书签中，POST请求不能被收藏至书签。
【4】GET请求不应在处理敏感数据时使用，POST可以用户处理敏感数据。
【5】 GET请求有长度限制，POST请求没有长度限制。
【6】POST不限制提交的数据类型，所以POST可以提交文件到服务器。

## Cookies和Session

Cookies是一小段文本信息，客户端请求服务器后，给客户端颁发一个cookies然后保存下来。保存在客户端。

session也是记录客户状态的机制，保存在服务器上。

区别：

1.存储位置不同。

2. session更安全。
3. 单个cookies不能超过4k，一个网站最多20个。
4. session会占用大量服务器性能。

## https加密

- **对称加密：加密解密用的是同样的“钥匙”**

   对称加密可以分为两种：一种是一个一个加密信息，另一种是分块加密信息， 

- **非对称加密：加密解密用的是不同的“钥匙”**

- 公匙是给信息发送者用来加密的，私匙是自己用来解密的 

### 过程

1.客户端往服务器发送https请求，端口号是443。

2.服务器有一个密钥对，公钥和秘钥，用来进行非对称加密使用的。服务器保存着私钥，公钥给任何人都行。

3.服务器把公钥发给客户端。

4.客户端收到服务器端的证书以后，对证书进行检查。就是验证服务器发送的数字证书的合法性。如果公钥合格，客户端就生成一个随机值，用于进行对称加密的秘钥，就是客户端秘钥。然后用服务器的公钥对客户端的秘钥进行非对称加密，这样客户端的秘钥就变成了密文，这是https的第一次http请求。

5.客户端会发送https的第二次http请求，将加密后的客户端秘钥发送给服务器。

6.服务器接收到客户端发送过来的密文之后，就用自己的私钥进行非对称解密。解密之后的就是客户端的秘钥，然后用这个秘钥对数据进行对称加密，数据就成了密文了。

7.然后服务器将加密后的密文发送给客户端。

8.客户端收到服务器发送过来的密文，用客户端的秘钥进行对称解密，就得到了数据。

## 缓存（HTTP）

**强制缓存**： 向浏览器缓存查找该请求结果，并根据该结果的缓存规则来决定是否使用该缓存结果的过程，强制缓存的情况主要有三种(暂不分析协商缓存过程) 

![1584412404514](D:\java-\java基础.assets\1584412404514.png)

**协商缓存**：

协商缓存就是强制缓存失效后，浏览器携带缓存标识向服务器发起请求，由服务器根据缓存标识决定是否使用缓存的过程，主要有以下两种情况：

协商缓存生效，返回304，如下

![1584412487839](D:\java-\java基础.assets\1584412487839.png)

![1584412497426](D:\java-\java基础.assets\1584412497426.png)



# Mybatis

## 概念



三层架构：

​	表现层：展示数据的

​	业务层：处理业务的

​	持久层：和数据库进行交互

![1585824334598](D:\java-\java基础.assets\1585824334598.png)

持久层技术解决方案

​	JDBC技术：

​		Connection

​		PreparedStatement

​		ResultSet

Spring中的jdbctemplate

​			对jdbc的简单封装

Apache中的DBUtils：

​	简单封装

以上不是框架。上述两个都是工具类。



持久层框架，通过xml或者注解方式配置，屏蔽jdbc api底层，使用ORM思想（对象关系映射），结果集封装。



## 环境搭建

![1585831077072](D:\java-\java基础.assets\1585831077072.png)

**配置的文件，主配置文件**

![1585831155405](D:\java-\java基础.assets\1585831155405.png)

![1585831254047](D:\java-\java基础.assets\1585831254047.png)

映射文件的路径和dao包结构相同。

![1585832978792](D:\java-\java基础.assets\1585832978792.png)

实现了三四五，就不用写dao的实现类。

 **测试过程**

1.读取配置文件。

2.创建sqlSessionFactory工厂

3.使用工厂生产SqlSession对象

4.使用SqlSession创建Dao接口的代理对象。

5.使用代理对象执行方法。

6.释放资源。

![1585833907209](D:\java-\java基础.assets\1585833907209.png)

1、使用类加载器，只能读取类路径的配置文件。

使用ServletContext对象的getRealPath（）；

2、创建工厂Mybatis使用了构建者模式（把对象的创建细节隐藏）

3、生产SqlSession使用了工厂模式（降低类之间的依赖关系）

4、getMapper：创建Dao接口实现类使用了代理模式。（不修改源码，对原有方法进行增强）

## 自定义Mybatis的分析（mybatis在使用代理Dao的方式实现增删改查时做什么事呢？）

第一：创建代理对象

第二：代理对象中调用selectList

![1585919417088](D:\java-\java基础.assets\1585919417088.png)

### 执行查询的分析

![1585922047833](D:\java-\java基础.assets\1585922047833.png)

![1585922225414](D:\java-\java基础.assets\1585922225414.png)

### 创建代理对象的分析

![1585998310452](D:\java-\java基础.assets\1585998310452.png)

## 手写框架

**手写Resources类，写getResourcesAsStream方法**

Resources.class.getClassLoader().getResourcesAsStream(filePath)

**手写SqlSessionFactoryBuilder类**

build方法（InputStream config）：根据数据的的字节输入流构建一个SqlSessionFactory工厂

然后写一个SqlSessionFactory接口，有一个方法SqlSession openSession（用于打开一个新的sqlSession对象）；

然后写一个接口SqlSession（自定义mybatis中和数据库交互的核心类，可以创建dao接口的代理对象）；

里面加getMapper（daoInterfaceClass）方法，根据参数创建代理对象。。

最后close方法。







# Spring

web应用入口在tomcat。。初始化web.xml

解耦思路：

1.用反射创建对象，而不是使用new关键字

Class.forname()

2.通过读取配置文件来获取要创建的对象全限定类名

## 事务隔离级别

**1) DEFAULT （默认）** 
这是一个PlatfromTransactionManager默认的隔离级别，使用数据库默认的事务隔离级别。另外四个与JDBC的隔离级别相对应。

**2) READ_UNCOMMITTED （读未提交）** 
这是事务最低的隔离级别，它允许另外一个事务可以看到这个事务未提交的数据。这种隔离级别会产生脏读，不可重复读和幻像读。 

**3) READ_COMMITTED （读已提交）** 
保证一个事务修改的数据提交后才能被另外一个事务读取，另外一个事务不能读取该事务未提交的数据。这种事务隔离级别可以避免脏读出现，但是可能会出现不可重复读和幻像读。 

**4) REPEATABLE_READ （可重复读）** 
这种事务隔离级别可以防止脏读、不可重复读，但是可能出现幻像读。它除了保证一个事务不能读取另一个事务未提交的数据外，还保证了不可重复读。

**5) SERIALIZABLE（串行化）** 
这是花费最高代价但是最可靠的事务隔离级别，事务被处理为顺序执行。除了防止脏读、不可重复读外，还避免了幻像读。 

 

## 事务传播机制

**1) REQUIRED（默认属性）**
如果存在一个事务，则支持当前事务。如果没有事务则开启一个新的事务。 
被设置成这个级别时，会为每一个被调用的方法创建一个逻辑事务域。如果前面的方法已经创建了事务，那么后面的方法支持当前的事务，如果当前没有事务会重新建立事务。 

**2) MANDATORY** 
支持当前事务，如果当前没有事务，就抛出异常。 

**3) NEVER** 
以非事务方式执行，如果当前存在事务，则抛出异常。 

**4) NOT_SUPPORTED** 
以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。 

**5) REQUIRES_NEW** 
新建事务，如果当前存在事务，把当前事务挂起。 

**6) SUPPORTS** 
支持当前事务，如果当前没有事务，就以非事务方式执行。 

**7) NESTED** 
支持当前事务，新增Savepoint点，与当前事务同步提交或回滚。 
嵌套事务一个非常重要的概念就是内层事务依赖于外层事务。外层事务失败时，会回滚内层事务所做的动作。而内层事务操作失败并不会引起外层事务的回滚。 

**PROPAGATION_NESTED 与PROPAGATION_REQUIRES_NEW的区别：**
它们非常类似，都像一个嵌套事务，如果不存在一个活动的事务，都会开启一个新的事务。

使用PROPAGATION_REQUIRES_NEW时，内层事务与外层事务就像两个独立的事务一样，一旦内层事务进行了提交后，外层事务不能对其进行回滚。两个事务互不影响。两个事务不是一个真正的嵌套事务。同时它需要JTA 事务管理器的支持。 
使用PROPAGATION_NESTED时，外层事务的回滚可以引起内层事务的回滚。而内层事务的异常并不会导致外层事务的回滚，它是一个真正的嵌套事务。

## IOC

applicationcontext：

​	他在构建核心容器时，创建对象采取的是立即加载的方式。

​	单例对象适用。

beanfactory：

​	他在构建核心容器时，创建对象采取的是延迟加载的方式，什么时候获取对象了，什么时候创建对象。

​	多例对象适用。



### bean

#### bean的加载过程

##### javabean 和springbean区别

**java对象**：使用new或其他方式直接创建，由JVM统一管理，一个java对象就只有生成这个对象的类中申明的所有属性和性能。

**springbean**：通过反射创建，由spring容器统一管理，拥有自己类中的申明的方法和属性还有spring给其增加、修改的属性。

**java对象加载流程：**

![1584360135967](D:\java-\java基础.assets\1584360135967.png)

**spring流程：**

Class类型的对象---BeanDefinition-----beanDefinitionmap（“@Component（）   springbean对象”）----BeanFactoryPostProcessor----检查检验（类名是否合法，是否单例）-----beanpostprocessor---singletonObjects

#### 创建bean的三种方式

​	1.使用bean标签，配以id和class，切没有其他属性和标签，

​	采用的是默认构造函数创建bean对象，此时如果类中没有默认构造函数，则对象无法创建。

​	2.使用普通工厂中的方法创造对象。（使用某个类的方法创建对象，并存入spring容器）

​	3.使用静态工厂中的静态方法创造对象，（使用某个类中静态方法创建对象）

#### bean的作用范围

bean标签的scope属性：

​	作用：指定bean的作用范围。

​	取值：

​	singleton：单例的（默认值）

​	prototype：多例的

​	request：作用于web应用的请求范围

​	session：作用于web应用的会话范围

​	global-session：作用于集群环境的会话范围（全局会话范围），当不是集群环境时，它就是session



#### bean对象的生命周期

单例对象  

  出生：当容器创建时对象出生  

  活着：只要容器还在，对象一直活着  

  死亡：容器销毁，对象消亡 

   总结：单例对象的生命周期和容器相同



多例对象  

  出生：当我们使用对象时spring框架为我们创建  

  活着：对象只要是在使用过程中就一直活着。  

  死亡：当对象长时间不用，且没有别的对象引用时，由Java的垃圾回收器回收

#### beanfactory和applicationContext的区别

applicationContext：（单例模式适用）

​	他在构建核心容器时，创建对象采取的策略是采取立即加载的方式。

beanfactory：（多例对象适用）

​	构建核心容器是，创建对象采取的策略是延迟加载的方式也就是说什么时候根据id获取对象了，什么时候创建对象。

#### beanfactory和factorybean

factorybean在IOC容器的基础上给Bean的实现加上了一个简单工厂模式和装饰者模式

 BeanFactory是个Factory，也就是IOC容器或对象工厂，FactoryBean是个Bean 

 **所有的Bean都是由BeanFactory(也就是IOC容器)来进行管理的**。但对FactoryBean而言，这个Bean不是简单的Bean，而是一个能生产或者修饰对象生成的工厂Bean,它的实现与设计模式中的工厂模式和修饰器模式类似  

不是用反射创建，  getobject来获取对象，然后放入spring容器中。

#### BeanPostProcessor和BeanFactoryPostProcessor的区别

![1584357313786](D:\java-\java基础.assets\1584357313786.png)

初始化方法之前和之后执行，两个方法。增强bean。

增强工厂，改变功能。

创建对象之前，可以将类修改。

## DI

```
spring中的依赖注入    
依赖注入：        Dependency Injection    
IOC的作用：        降低程序间的耦合（依赖关系）    
依赖关系的管理：        以后都交给spring来维护    在当前类需要用到其他类的对象，由spring为我们提供，我们只需要在配置文件中说明    依赖关系的维护：        就称之为依赖注入。
依赖注入：       
能注入的数据：有三类            
	基本类型和String            
	其他bean类型（在配置文件中或者注解配置过的bean）            复杂类型/集合类型         
注入的方式：有三种          
	第一种：使用构造函数提供            
	第二种：使用set方法提供            
	第三种：使用注解提供（明天的内容）
```

```
使用的标签:constructor-arg
标签出现的位置：bean标签的内部标签中的属性    
	type：用于指定要注入的数据的数据类型，该数据类型也是构造函数中某个或某些参数的类型    
	index：用于指定要注入的数据给构造函数中指定索引位置的参数赋值。索引的位置是从0开始    
	name：用于指定给构造函数中指定名称的参数赋值                                        常用的    =============以上三个用于指定给构造函数中哪个参数赋值===============================    
	value：用于提供基本类型和String类型的数据    
	ref：用于指定其他的bean类型数据。它指的就是在spring的Ioc核心容器中出现过的bean对象
优势：    在获取bean对象时，注入数据是必须的操作，否则对象无法创建成功。
弊端：    改变了bean对象的实例化方式，使我们在创建对象时，如果用不到这些数据，也必须提供。
```

```
set方法注入               
更常用的方式    
涉及的标签：property   
出现的位置：bean标签的内部    
标签的属性        
	name：用于指定注入时所调用的set方法名称        
	value：用于提供基本类型和String类型的数据        
	ref：用于指定其他的bean类型数据。它指的就是在spring的Ioc核心容器中出现过的bean对象    
优势：        创建对象时没有明确的限制，可以直接使用默认构造函数    
弊端：        如果有某个成员必须有值，则获取对象是有可能set方法没有执行。
```

## 注解

### 用于创建对象的

![1583290617764](D:\java-\java基础.assets\1583290617764.png)

![1583290631857](D:\java-\java基础.assets\1583290631857.png)

### 用于注入数据的

![1583290679144](D:\java-\java基础.assets\1583290679144.png)

![1583290690122](D:\java-\java基础.assets\1583290690122.png)

### 用于改变作用范围的、生命周期

![1583290698929](D:\java-\java基础.assets\1583290698929.png)

### 配置xml

![1583290830550](D:\java-\java基础.assets\1583290830550.png)

![1583290841150](D:\java-\java基础.assets\1583290841150.png)

## 配置类

![1583290921599](D:\java-\java基础.assets\1583290921599.png)

![1583290928172](D:\java-\java基础.assets\1583290928172.png)

![1583291206135](D:\java-\java基础.assets\1583291206135.png)

## 动态代理

![1583503759086](D:\java-\java基础.assets\1583503759086.png)

![1583503776104](D:\java-\java基础.assets\1583503776104.png)

## AOP

### 实现方式

代理（JDK的proxy）和cglib

JDK.proxy可以生成语言接口。如果原来类没有实现接口就不合适。

cglib使用自解码的编辑器，ASM的一个编辑器，可以生成一个目标类的子类，去实现类似的代理功能。创建对象过程中做的慢，运行时候效率更高。

面向切面编程，是OOP的延伸![1583897134261](D:\java-\java基础.assets\1583897134261.png)



使用动态代理

通过配置的方式实现

连接点：业务层中所有方法都是连接点。

切入点：业务层中支持事务的方法是切入点。

![1584094595029](D:\java-\java基础.assets\1584094595029.png)



![1584094630020](D:\java-\java基础.assets\1584094630020.png)



### spring基于xml配置AOP的步骤

![1584155845370](D:\java-\java基础.assets\1584155845370.png)

### 切入点表达式写法

![1584156075954](D:\java-\java基础.assets\1584156075954.png)

![1584156090487](D:\java-\java基础.assets\1584156090487.png)

### 四种常用的通知类型

![1584156721555](D:\java-\java基础.assets\1584156721555.png)

![1584156772465](D:\java-\java基础.assets\1584156772465.png)

![1584156735399](D:\java-\java基础.assets\1584156735399.png)

![1584156754511](D:\java-\java基础.assets\1584156754511-1594128665259.png)

### 环绕通知

![1584157095888](D:\java-\java基础.assets\1584157095888.png)



![1584157384619](D:\java-\java基础.assets\1584157384619.png)

# SpringMVC

## servlet的生命周期

​	init（）、service（）、destroy（）

1、浏览器像servlet发送请求
2、tomcat收到请求后，创建Request和Response两个对象的生命周期，并且将浏览器请求的参数传递给Servlet
3、Servlet接收到请求后，调用doget或者dopost方法。处理浏览器的请求信息，然后通过Response返回信息
4、tomcat接收到返回的信息，返回给浏览器。
5、浏览器接收到返回消息后，tomcat销毁Request和Response两个对象，同时销毁这两个对象所获得的信息。

![1584500125235](D:\java-\java基础.assets\1584500125235.png)



# Mysql

## 事务的特性（ACID）

原子性：要同步成功或失败。

一致性：事务从一致状态到另一个一致状态，执行前和执行后都必须处于一致状态。 事务的执行的前后数据的完整性保持一致. 

隔离性：事物之间是隔离的。

持久性：事务提交以后是持久的。

## 事务的隔离等级

脏读： 在处理数据的过程中，读取到另一个未提交事务的数据。

不可重复读：  不可重复读读取到的是前一个事务提交的数据 。（内容不一样）

幻读：和不可重复读类似（数量不一样）

 解决不可重复读的方法是 **锁行**，解决幻读的方式是 ***锁表***。 



**读未提交**：会脏读。

**读已提交**：避免脏读。

**可重复读**：避免脏读、不可重复读。

**串行化**：避免脏读、不可重复读、幻读。



## MVCC

Multi-Version Concurrency Control，多版本并发控制。

 MVCC的实现，通过保存数据在某个时间点的快照来实现的。这意味着一个事务无论运行多长时间，在同一个事务里能够看到数据一致的视图。根据事务开始的时间不同，同时也意味着在同一个时刻不同事务看到的相同表里的数据可能是不同的。 

实现的是读-写，写-读的并发进行。

就是操作时不能修改新增时间之后的操作，查找时过期时间只要在事务id之后就可以查找到。

## 行锁和表锁

行锁：共享锁。 lock in share mode，无法修改（读锁）

![1586436670064](D:\java-\java基础.assets\1586436670064.png)

排它锁：其他人无法操作。

![1586436869515](D:\java-\java基础.assets\1586436869515.png)

## 意向锁

表锁、无法手动创建。![1586437113140](D:\java-\java基础.assets\1586437113140.png)

## 间隙锁和临键锁

插入是1、5、9、11 区间为（-，1】（1,5】（5,9】（9,11】（11，+）

就是锁一个区间，然后不能修改啦。

## 自增锁

级别0、1、2 （binlog） 严格递减。



## 乐观锁、悲观锁

**乐观锁**：数据库进行操作时，认为这次操作不会有冲突，操作时不加锁，更新以后再去判断是否冲突。

**悲观锁**：操作数据时认为会冲突，每次操作都要加锁。

保持高并发一致性：添加版本号，A操作完 version从1到2，B操作完发现version=2，则操作回滚。

### 实现乐观锁：

1.使用数据版本（Version）记录机制实现， 当读取数据时，将version字段的值一同读出，数据每更新一次，对此version值加一。当我们提交更新的时候，判断数据库表对应记录的当前版本信息与第一次取出来的version值进行比对，如果数据库表当前版本号与第一次取出来的version值相等，则予以更新，否则认为是过期数据。 

2. 同样是在需要乐观锁控制的table中增加一个字段，名称无所谓，字段类型使用时间戳（timestamp ） 也是在更新提交的时候检查当前数据库中数据的时间戳和自己更新前取到的时间戳进行对比，如果一致则OK，否则就是版本冲突。 

### 乐观锁适用的场景

 像乐观锁适用于写比较少的情况下（多读场景）。

### 乐观锁实现

```java
package priv.nanjing.testCasClass;

import java.util.concurrent.atomic.AtomicInteger;

/*
 * @Author : darrenqiao
 * */


//多线程争用的数据类
class Counter {
    //int count = 0;
    //使用AtomicInteger代替基本数据类型
    AtomicInteger count = new AtomicInteger(0);

    public int getCount() {
        //return count;
        return count.get();
    }


    public void add() {
        //count += 1;
        count.addAndGet(1);
    }

    public void dec() {
        //count -= 1;
        count.decrementAndGet();
    }
}

//争用数据做加操作的线程
class AddDataThread extends Thread {

    Counter counter;

    public AddDataThread(Counter counter) {
        this.counter = counter;
    }

    @Override
    public void run() {
        for (int i = 0; i < CasClass.LOOP; ++i) {
            counter.add();
        }
    }
}

//争用数据做减法操作的线程
class DecDataThread extends Thread {

    Counter counter;

    public DecDataThread(Counter counter) {
        this.counter = counter;
    }

    @Override
    public void run() {
        for (int j = 0; j < CasClass.LOOP; j++) {
            counter.dec();
        }
    }
}

public class CasClass {
    final static int LOOP = 10000;

    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();
        Thread addThread = new AddDataThread(counter);
        Thread decThread = new DecDataThread(counter);
        addThread.start();
        decThread.start();
        addThread.join();
        decThread.join();
        System.out.println(counter.getCount());
    }

}
```



## 索引

索引是数据结构，空间换时间。索引是在磁盘中。建立索引要有离散型原则。

### 联合索引

满足最左前缀原则，比如建立id，name索引，其实建立了id索引和id，name索引。如果查找的少了一列，则只使用前面列的索引。

### 覆盖索引+explain

通过索引项的信息直接返回所查询的列，该索引就叫查询sql的覆盖索引，支持索引下推的过程。可以防止回表操作。

这个可以在explain中看它执行计划的时候，extra字段里面有using index condition可以看到。 

![1587299628852](D:\java-\java基础.assets\1587299628852.png)

**id**：执行顺序，sql从大到小的执行。如果是 explain select * from (select * from ( select * from t3 where id=3952602) a) b; 则id=3；

**select_type**：就是select的类型。

​	**SIMPLE**：简单select查询；

​	**PRIMARY**：最外层的select查询。就是如果有嵌套的话 最外层的执行就是primary；

​	**.UNION**： UNION中的第二个或后面的SELECT语句 ；

 	**.UNION RESULT** ：UNION的结果；

​    **.SUBQUERY **:子查询的第一个select；

​	**.DERIVED **：派生表的select。

**table**：是哪张表的数据；

**type**：显示连接使用了哪种类别，有没有使用索引。

**possible_keys**： 指出MySQL能使用哪个索引在该表中找到行 。

**key：** 显示MySQL实际决定使用的键（索引） 。

**key_len：** 显示MySQL实际决定使用的键（索引） 的长度。

**ref**：使用哪个列或者常数和key一起从表中选择行。

**row**：执行查询时必须用到多少行。

**extra**：包含mysql解决查询的详细信息：

​		 (1).Distinct
​		一旦MYSQL找到了与行相联合匹配的行，就不再搜索了

​		(2).Not exists
​		MYSQL优化了LEFT JOIN，一旦它找到了匹配LEFT JOIN标准的行，

​		就不再搜索了

​		(3).Range checked for each

​		Record（index map:#）
​		没有找到理想的索引，因此对于从前面表中来的每一个行组合，MYSQL检查使用哪个索引，并用它来从表		中返回行。这是使用索引的最慢的连接之一

​		(4).Using filesort
​		看到这个的时候，查询就需要优化了。MYSQL需要进行额外的步骤来发现如何对返回的行排序。它根据连		接类型以及存储排序键值和匹配条件的全部行的行指针来排序全部行

​		(5).Using index
​		列数据是从仅仅使用了索引中的信息而没有读取实际的行动的表返回的，这发生在对表的全部的请求列都是		同一个索引的部分的时候

​		(6).Using temporary
​		看到这个的时候，查询需要优化了。这里，MYSQL需要创建一个临时表来存储结果，这通常发生在对不同		的列集进行ORDER BY上，而不是GROUP BY上

​		(7).Using where
​		使用了WHERE从句来限制哪些行将与下一张表匹配或者是返回给用户。如果不想返回表中的全部行，并且		连接类型ALL或index，这就会发生，或者是查询有问题 

### 优化

#### 索引还在

联合索引；

覆盖索引；也可以避免排序用到的一些临时文件；利用最左原则匹配和覆盖，减少一些索引的维护

硬盘怕随机读写，有查找的开销；我们可以把MRR打开，就是multi-range-read，他可以在回表之前把我们的id读到buffer里面，进行一个排序，

、个服务的唯一性要求没有那么高，或者我们的业务代码可以保证唯一性的时候，可以用普通索引，因为普通索引可以用到change buffer的，它可以把一些写操作给缓存下来，在我们读的时候进行merge操作，可以提高写入的速度和内存的命中率。

#### 索引走不上

1.可能sql写的问题。对索引字段进行了一些函数操作，连接查询时候两个表的编码不一样，或者两个字段的类型不一样；

2.索引统计信息是不是有问题？可以analyze table重新统计所有的信息。因为索引信息不是准确值，是一个随机采样的一个过程，所以可能出现问题。

3.因为业务表分太多，内存的空洞也比较多。

#### explain分析的索引问题

因为它可能会选错索引，可能会涉及到回表操作，还有一些排序操作，可能会报错。

#### 如果索引建的不好

1.采用force index进行强制索引。不太好的是迁移到别的数据库就不支持了。而且还需要代码的重新发布；

2.考虑用覆盖索引和最左原则

![1586860917725](D:\java-\java基础.assets\1586860917725.png)



## B+树 

### 为什么使用B+树

**不使用二叉树**：深度可能会很深 比如插入1、2、3、4、5、。。。一直向右排。

**不使用红黑树**：深度深。（弱平衡二叉树）

mysql的每个节点16k的限制。

B树叶子节点没有指针穿起来，同时每个节点上都有data

**1.B+树只有叶子节点上有data，可以减少索引占用的内存，同时索引会存储在磁盘上。B树都有data 磁盘IO次数就多啦。**

**2.B+树所有叶子结点有指针穿起来，可以遍历起来方便。**

**3.不用红黑树是因为红黑树深度比较大，**

哈希索引可以精确查找，但是不能范围查找。

## myisam和innodb区别

1.innodb支持事务，myisam不支持。

2.InnoDB支持外键，而MyISAM不支持。

3.InnoDB是聚集索引（只有主键索引是聚集索引，非主键索引是非聚集索引），使用B+Tree作为索引结构，数据文件是和（主键）索引绑在一起的（表数据文件本身就是按B+Tree组织的一个索引结构），必须要有主键，通过主键索引效率很高。但是辅助索引需要两次查询，先查询到主键，然后再通过主键查询到数据。因此，主键不应该过大，因为主键太大，其他索引也都会很大。

 MyISAM是非聚集索引，也是使用B+Tree作为索引结构，索引和数据文件是分离的，索引保存的是数据文件的指针。主键索引和辅助索引是独立的。所以通过B+树，先查出来索引对应的数据文件的指针，然后再去MYD文件里面找数据。

4.**InnoDB不保存表的具体行数，执行select count(\*) from table时需要全表扫描。而MyISAM用一个变量保存了整个表的行数**

5.**InnoDB表必须有主键** 





### 如何选择

1. 是否要支持事务，如果要请选择innodb，如果不需要可以考虑MyISAM；

    2. 如果表中绝大多数都只是读查询，可以考虑MyISAM，如果既有读也有写，请使用InnoDB。
3. 系统奔溃后，MyISAM恢复起来更困难，能否接受；
    4. MySQL5.5版本开始Innodb已经成为Mysql的默认引擎(之前是MyISAM)，
5. 说明其优势是有目共睹的，如果你不知道用什么，那就用InnoDB，至少不会差。
   

## 什么情况下设置了索引但无法使用 

① 以“%”开头的LIKE语句，模糊匹配

② OR语句前后没有同时使用索引

③ 数据类型出现隐式转化（如varchar不加单引号的话可能会自动转换为int型）

## 主键索引和非主键索引

主键索引叶子结点存储一行值，非主键索引叶子结点存储主键的值。

## 大量热点数据更新问题

可以把它写进一个内存的临时表中，innodb会维护一个bufferpool的，如果把大量数据全部读进去，会造成flush的操作（把脏页刷回mysql，造成阻塞）。

如果redis带宽打满了，可以使用本地缓存。有三种session、localStorage、sessionStorage。

​				 Cookie：一般用来存储用户信息，每次请求的时候内容都会自动被传递给服务器。 

​				 LocalStorage：localstorage会把内容一直存在浏览器，直到清除浏览器的缓存 。

​				sessionStorage：跟localStorage一样，只不过sessionStorage的生命周期跟同源窗口有关，就是说当									前同一个源下面的只要有一个窗口没关或者跳到另外的窗口，sessionStorage都会存在。

## 分库分表



# Redis

键值对、缓存数据库、基于内存、单线程

## 特性

![1584188486261](C:\Users\56495\AppData\Roaming\Typora\typora-user-images\1584188486261.png)

## 使用场景

![1584189197816](D:\java-\java基础.assets\1584189197816.png)

## String

![1584190529768](D:\java-\java基础.assets\1584190529768.png)

![1584190595070](D:\java-\java基础.assets\1584190595070.png)

setnx  （not exist）不存在才可以设置



### 应用场景

![1584192655874](D:\java-\java基础.assets\1584192655874.png)



## Hash

![1584191017260](D:\java-\java基础.assets\1584191017260.png)

### 应用场景

![1584193249925](D:\java-\java基础.assets\1584193249925.png)

先找到key应该怎么设计？1.

### 优缺点

![1584191030089](D:\java-\java基础.assets\1584191030089.png)

## List

![1586178722139](D:\java-\java基础.assets\1586178722139.png)

阻塞队列Blocking-queue-brpop（平时和rpop一样，但是没有数据时会一直等待）

List ：lpush（rpush） key xxx

栈：lpush和lpop   队列lpush和rpop

![1586178935307](D:\java-\java基础.assets\1586178935307.png)

**重要：如何处理一对多关系的设计。**

![1586179025896](D:\java-\java基础.assets\1586179025896.png)

## Set

​	 Redis 的 Set 是 String 类型的无序集合。集合成员是唯一的，这就意味着集合中不能出现重复的数据。 

 Redis 中集合是通过hashtable实现的，所以添加，删除，查找的复杂度都是 O(1)。 

![1584268084219](D:\java-\java基础.assets\1584268084219.png)

![1584268096703](D:\java-\java基础.assets\1584268096703.png)

![1584268185945](D:\java-\java基础.assets\1584268185945.png)

![1586180455688](D:\java-\java基础.assets\1586180455688.png)



## Zset

有序集合

为每个元素增加一个double类型的分数。

### 应用场景

排行榜、

### 底层实现

跳跃表

为什么不使用红黑树：因为是单线程，跳跃表更简洁。

![1584353766643](D:\java-\java基础.assets\1584353766643.png)

其中每一个数据的层数是随机的。

![1584355772566](D:\java-\java基础.assets\1584355772566.png)

用score来寻找数据，

## 持久化

持久化功能有效地避免因进程退出造成的数据丢失问题，当下次重启时，利用之前持久化的文件即可实现数据恢复。 

RDB和AOF

### RDB

​	把当前进程数据生成快照保存到硬盘的过程，手动触发和自动触发。

![1585734649258](D:\java-\java基础.assets\1585734649258.png)

1. Redis父进程首先判断：当前是否在执行save，或bgsave/bgrewriteaof（后面会详细介绍该命令）的子进程，如果在执行则bgsave命令直接返回。bgsave/bgrewriteaof 的子进程不能同时执行，主要是基于性能方面的考虑：两个并发的子进程同时执行大量的磁盘写操作，可能引起严重的性能问题。
2. 父进程执行fork操作创建子进程，这个过程中父进程是阻塞的，Redis不能执行来自客户端的任何命令
3. 父进程fork后，bgsave命令返回”Background saving started”信息并不再阻塞父进程，并可以响应其他命令
4. 子进程创建RDB文件，根据父进程内存快照生成临时快照文件，完成后对原有文件进行原子替换
5. 子进程发送信号给父进程表示完成，父进程更新统计信息

**保存**：RDB文件保存在dir配置指定的目录下，文件名通过dbfilename配置
**指定**。可以通过执行conf set dir{newDir}和config set dbfilename {newFileName}运行期动态执行，下次运行时RDB文件会保存在新目录
**压缩**：Redis默认采用LZF算法对生成的RDB文件做压缩处理，压缩后的文件远远小于内存大小，默认开启，可以通过参数config set rdbcompression {yes|no}动态修改
**校验**：如果Redis加载损坏的RDB文件时拒绝启动

#### 优缺点

**优点：**

RDB恢复速度远大于AOF。

他代表了Redis在某个时间点的数据快照，适合用于备份和全量复制。

**缺点；**

不能实时持久化，因为我得fork父进程，重量级操作，需要时间。

RDB文件是特定的二进制文件格式，存在兼容问题。

### AOF*（主流）

独立日志形式记录每次写命令，重启后再执行AOF文件中的命令，作用是解决实时性。

![1585735591430](D:\java-\java基础.assets\1585735591430.png)

 开启AOF功能需要设置配置：appendonly yes，默认不开启。AOF文件名通过appendfilename配置设置，默认文件名是appendonly.aof。 

 1.所有的写入命令追加到aof_buf(缓存区)中
2.AOF缓存区根据对应的策略向磁盘做同步操作
3.随着AOF文件越来越大，需要定期对AOF文件进行重写，达到压缩的目的
4.当Redis服务器重启时，可以加载AOF文件进行数据恢复。



命令写入是文本协议格式。

- 文本协议格式具有很好的兼容性
- 开启AOF后，所有写入命令都包含追加操作，直接采用协议格式，避免了二次处理开销
- 文本协议具有可读性，方便直接修改和处理

为什么要追加到aof_buf中？

- Redis是单线程响应命令，如果每次写AOF文件命令都直接追加到磁盘，那么性能完全取决于当前硬盘负载。
- 先写入缓冲区aof_buf中，还有一个好处，Redis可以提供多种缓冲区同步硬盘策略，在性能和安全性上做出平衡

#### AOF文件同步

![1585748737999](D:\java-\java基础.assets\1585748737999.png)

no：快，持久化没有保障。

always：慢，安全。 fsync针对单个文件操作，做强制硬盘操作，fsync将阻塞直到写入磁盘完成后返回，保证了数据持久化 

eyerysec：可能丢失一秒内的数据。 write操作会触发延迟写机制。  当缓冲区被填满或超过了指定时限后，才真正将缓冲区的数据写入到硬盘里 

#### 重写机制（手动和自动）

 首先从数据库中读取键现在的值，然后用一条命令去记录键值对，代替之前记录该键值对的多个命令; 

 AOF文件重写是把Redis进程内的数据转化为写命令同步到新的AOF文件的过程 。

重写可以降低文件占用空间，可以更快地被Redis加载。

![1585749241976](D:\java-\java基础.assets\1585749241976.png)



1. 执行AOF重写请求
   如果当前进程正在执行AOF重写，请求不执行
   如果当前进程正在执行bgsave操作，重写命令延迟到bgsave完成后再执行

2. 父进程执行fork创建子进程，开销等同于bgsave过程

3. 父进程fork操作完成后，继续响应其他命令。所有修改命令依然写到AOF缓冲区并根据appendfsync策略同步到磁盘上，保证原有AOF机制正确性.

   由于fork操作运用写时复制技术，子进程只能共享fork操作时的内存数据。由于父进程依然响应命令，Redis使用"AOF重写缓冲区"保存这部分新数据，防止新AOF文件生成期间丢失这部分数据

   4. 子进程根据内存快照，按照命令合并规则写入到新的AOF文件。

   5.  新AOF文件写入完成后，子进程发送信号给父进程，父进程更新统计信息
      父进程把AOF重写缓冲区中的数据写入到新的AOF文件



## 分布式锁的实现

![1585843002651](D:\java-\java基础.assets\1585843002651.png)

这样写，第一个锁释放的可能是第二个锁。释放了不属于自己的锁。

![1585843341916](D:\java-\java基础.assets\1585843341916.png)

![1585843331352](D:\java-\java基础.assets\1585843331352.png)

![1585844273289](D:\java-\java基础.assets\1585844273289.png)

![1586007897237](D:\java-\java基础.assets\1586007897237.png)

## 哨兵模式

![1585845232687](D:\java-\java基础.assets\1585845232687.png)

 哨兵模式是一种特殊的模式，首先Redis提供了哨兵的命令，哨兵是一个独立的进程，作为进程，它会独立运行。其原理是**哨兵通过发送命令，等待Redis服务器响应，从而监控运行的多个Redis实例。** 

通过发送命令，让Redis服务器返回监控其运行状态，包括主服务器和从服务器。

当哨兵监测到master宕机，会自动将slave切换成master，然后通过**发布订阅模式**通知其他的从服务器，修改配置文件，让它们切换主机。



用文字描述一下**故障切换（failover）**的过程。假设主服务器宕机，哨兵1先检测到这个结果，系统并不会马上进行failover过程，仅仅是哨兵1主观的认为主服务器不可用，这个现象成为**主观下线**。当后面的哨兵也检测到主服务器不可用，并且数量达到一定值时，那么哨兵之间就会进行一次投票，投票的结果由一个哨兵发起，进行failover操作。切换成功后，就会通过发布订阅模式，让各个哨兵把自己监控的从服务器实现切换主机，这个过程称为**客观下线**。这样对于客户端而言，一切都是透明的。

![1585845316515](D:\java-\java基础.assets\1585845316515.png)

## 缓存

### 缓存穿透

 是指查询一个数据库一定不存在的数据。正常的使用缓存流程大致是，数据查询先进行缓存查询，如果key不存在或者key已经过期，再对数据库进行查询，并把查询到的对象，放进缓存。如果数据库查询对象为空，则不放进缓存。 

### 缓存雪崩

 缓存雪崩，是指在某一个时间段，缓存集中过期失效。  而对这批商品的访问查询，都落到了数据库上，对于数据库而言，就会产生周期性的压力波峰。 

 比较致命的缓存雪崩，是缓存服务器某个节点宕机或断网 。

### 缓存击穿

 缓存击穿，是指一个key非常热点，在不停的扛着大并发，大并发集中对这一个点进行访问，当这个key在失效的瞬间，持续的大并发就穿破缓存，直接请求数据库，就像在一个屏障上凿开了一个洞。 类似爆款，让其永不过期就好。

# JVM

## 谈谈对jvm的理解

   JVM就是java虚拟机，JVM 内存共分为虚拟机栈、堆、方法区、程序计数器、本地方法栈五个部分。 

**1.类加载器**

启动类加载器、扩展类加载器、应用类加载器、自定义类加载器

 **2.在类加载检查通过后，接下来虚拟机将为新生对象分配内存。** 



## JVM的位置

![1583635522502](D:\java-\java基础.assets\1583635522502.png)

## JVM的体系结构

![1583635535763](D:\java-\java基础.assets\1583635535763.png)



## 类加载器

![1583635608383](D:\java-\java基础.assets\1583635608383.png)

![1583635633837](D:\java-\java基础.assets\1583635633837.png)

![1583635654059](D:\java-\java基础.assets\1583635654059.png)

### 类加载器作用

加载Class文件~ 

1.虚拟机自带的加载器

2.启动类（根）加载器

3.扩展类加载器

4.应用程序加载器

5.用户自定义加载器

## 双亲委派机制

APP-->EXC--BOOT(最终执行)

1.类加载器收到类加载的请求

2.将这个请求向上委托给父类加载器去完成，一直向上委托，知道启动类加载器

3.启动类加载器检查是否能够加载当前这个类，能加载就结束，使用当前的加载器，否则跑出异常，通知子类进行加载。

4.重复步骤3

Class Not Found

null：java调用不到~ C\C++

### 双亲委派机制的作用

1、防止重复加载同一个`.class`。通过委托去向上面问一问，加载过了，就不用再加载一遍。保证数据安全。
 2、保证核心`.class`不能被篡改。通过委托方式，不会去篡改核心`.class`，即使篡改也不会去加载，即使加载也不会是同一个`.class`对象了。不同的加载器加载同一个`.class`也不是同一个`Class`对象。这样保证了`Class`执行安全。

### 打破双亲委派机制

1. 自定义类加载器，重写loadClass方法；
2. 使用线程上下文类加载器；

## 沙箱安全机制

![1583654781755](D:\java-\java基础.assets\1583654781755.png)

### 包括：

字节码校验器：确保遵守java语言规范

类装载器：对java沙箱起作用

​	它防止恶意代码干涉善意代码；双亲委派记至

​	它守护了被信任的类库边界

​	它将代码归入保护域，确定了代码可以进行哪些操作

存取控制器：控制核心API对操作系统的存取权限

安全管理器：核心API和操作系统之间的主要接口。比存取控制器优先级更高。

安全软件包：java.security下的类和扩展包下的类。允许用户新增	

​		

## Native

private native void start0（）

带了native，java作用范围达不到了，回去调用底层c语言的

调用本地方法接口JNI

JNI作用：扩展java的使用，融合不同的编程语言为java所用

他在内存区域中，专门开辟了一块表及区域：本地方法栈

执行中，加载本地方法库中的方法通过JNI



调用其他接口：Socker，WebService  http

## PC寄存器

![1583656774434](D:\java-\java基础.assets\1583656774434.png)

## 方法区

![1583656791839](D:\java-\java基础.assets\1583656791839.png)

static final  Class  常量池在方法区



## 栈

数据结构

先进后出、后进先出：桶

队列：先进先出（FIFO：First Input First Output）



栈：栈内存，主管程序的运行，生命周期和线程同步；

线程结束，栈内存也就是释放，对于栈来说，不存在垃圾回收问题

一旦线程结束，栈就over



栈：8大基本类型+对象引用+实例的方法

栈运行原理：栈帧



![1583722177556](D:\java-\java基础.assets\1583722177556.png)

栈满了：StackOverflowError![1583722354752](D:\java-\java基础.assets\1583722354752.png)

栈+堆+方法区：交互关系



## 三种JVM

![1583722767327](D:\java-\java基础.assets\1583722767327.png)

## 堆

Heap，一个JVM只有一个堆内存，堆内存大小是可以调节的。

类加载器读取了类文件后，一般把什么东西放到堆中？类，方法，常量，变量，保存我们所有引用类型的真实对象

堆内存中还要细分为三个区域：

​	新生区（伊甸园区）、幸存者区

​	养老区 old

​	永久区 Perm

GC垃圾回收主要在伊甸园区和养老区~

假设内存满了，OOM，堆内存不够

![1583724002780](D:\java-\java基础.assets\1583724002780.png)

在JDK8以后，永久存储区改了名字（元空间）

真理：有99%的对象都是临时对象！new

### 新生区

​	类：诞生和成长的地方，甚至死亡；

伊甸园区，所有的对象都是在伊甸园区new出来的

幸存者区：0、1

### 老年区



### 永久区

这个区域常驻内存的。用来存放JDK自身携带的Class对象，Interface元数据，存储的是java运行时的一些环境或类信息~，这个区域不存在垃圾回收！关闭虚拟机就会释放这个区域的内存

一个启动类，加载了大量的第三方jar包。Tomcat部署了大量应用，大量动态生成的反射类。不断地被加载。直到内存满，就会出现OOM；

jdk1.6之前：永久代，常量池是在方法区中；

jdk1.7：永久代，但是慢慢退化了，去永久代，常量池在堆中

jdk1.8之后：无永久代，

常量池在元空间。

元空间：逻辑上存在，物理上不存在。

![1583746374952](D:\java-\java基础.assets\1583746374952.png)

![1583746489507](D:\java-\java基础.assets\1583746489507.png)

### 堆内存调优

使用jps和jinfo进行查看

```
-Xms：初始堆空间
-Xmx：堆最大值
-Xss：栈空间
```

-Xms 和 -Xmx最好调整一致，防止JVM频繁进行收集和回收

####  JVM参数类型

- 标配参数（从JDK1.0 - Java12都在，很稳定）
  - -version
  - -help
  - java -showversion
- X参数（了解）
  - -Xint：解释执行
  - -Xcomp：第一次使用就编译成本地代码
  - -Xmixed：混合模式
- XX参数（重点）
  - Boolean类型
    - 公式：-XX:+ 或者-某个属性 + 表示开启，-表示关闭
    - Case：-XX:-PrintGCDetails：表示关闭了GC详情输出
  - key-value类型
    - 公式：-XX:属性key=属性value
    - 不满意初始值，可以通过下列命令调整
    - case：如何：-XX:MetaspaceSize=21807104：查看Java元空间的值

#### 查看运行的Java程序，JVM参数是否开启，具体值为多少？

首先我们运行一个HelloGC的java程序

```java
/**
 * @author: 陌溪
 * @create: 2020-03-19-12:14
 */
public class HelloGC {

    public static void main(String[] args) throws InterruptedException {
        System.out.println("hello GC");
        Thread.sleep(Integer.MAX_VALUE);
    }
}
```

然后使用下列命令查看它的默认参数

```
jps：查看java的后台进程
jinfo：查看正在运行的java程序
```

具体使用：

```
jps -l得到进程号
12608 com.moxi.interview.study.GC.HelloGC
15200 sun.tools.jps.Jps
15296 org.jetbrains.idea.maven.server.RemoteMavenServer36
4528
12216 org.jetbrains.jps.cmdline.Launcher
9772 org.jetbrains.kotlin.daemon.KotlinCompileDaemon
```

查看到HelloGC的进程号为：12608

我们使用jinfo -flag 然后查看是否开启PrintGCDetails这个参数

```
jinfo -flag PrintGCDetails 12608
```

得到的内容为

```
-XX:-PrintGCDetails
```

上面提到了，-号表示关闭，即没有开启PrintGCDetails这个参数

下面我们需要在启动HelloGC的时候，增加 PrintGCDetails这个参数，需要在运行程序的时候配置JVM参数

![image-20200319122922264](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/2_JVM%E5%8F%82%E6%95%B0%E8%B0%83%E4%BC%98/images/image-20200319122922264.png)

然后在VM Options中加入下面的代码，现在+号表示开启

```
-XX:+PrintGCDetails
```

然后在使用jinfo查看我们的配置

```
jps -l
jinfo -flag PrintGCDetails 13540
```

得到的结果为

```
-XX:+PrintGCDetails
```

我们看到原来的-号变成了+号，说明我们通过 VM Options配置的JVM参数已经生效了

使用下列命令，会把jvm的全部默认参数输出

```
jinfo -flags ***
```

####  题外话（坑题）

两个经典参数：-Xms 和 -Xmx，这两个参数 如何解释

这两个参数，还是属于XX参数，因为取了别名

- -Xms 等价于 -XX:InitialHeapSize ：初始化堆内存（默认只会用最大物理内存的64分1）
- -Xmx 等价于 -XX:MaxHeapSize ：最大堆内存（默认只会用最大物理内存的4分1）

####  查看JVM默认参数

- -XX:+PrintFlagsInitial
  - 主要是查看初始默认值
  - 公式
    - java -XX:+PrintFlagsInitial -version
    - java -XX:+PrintFlagsInitial（重要参数）

![image-20200320212256284](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/2_JVM%E5%8F%82%E6%95%B0%E8%B0%83%E4%BC%98/images/image-20200320212256284.png)

- -XX:+PrintFlagsFinal：表示修改以后，最终的值

 会将JVM的各个结果都进行打印

 如果有 := 表示修改过的， = 表示没有修改过的

####  工作中常用的JVM基本配置参数

![image-20200322163252777](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/2_JVM%E5%8F%82%E6%95%B0%E8%B0%83%E4%BC%98/images/image-20200322163252777.png)

##### 查看堆内存

查看JVM的初始化堆内存 -Xms 和最大堆内存 Xmx

```java
/**
 * @author: 陌溪
 * @create: 2020-03-19-12:14
 */
public class HelloGC {

    public static void main(String[] args) throws InterruptedException {
        // 返回Java虚拟机中内存的总量
        long totalMemory = Runtime.getRuntime().totalMemory();

        // 返回Java虚拟机中试图使用的最大内存量
        long maxMemory = Runtime.getRuntime().maxMemory();

        System.out.println("TOTAL_MEMORY(-Xms) = " + totalMemory + "(字节)、" + (totalMemory / (double)1024 / 1024) + "MB");
        System.out.println("MAX_MEMORY(-Xmx) = " + maxMemory + "(字节)、" + (maxMemory / (double)1024 / 1024) + "MB");

    }
}
```

运行结果为：

```
TOTAL_MEMORY(-Xms) = 257425408(字节)、245.5MB
MAX_MEMORY(-Xmx) = 3790077952(字节)、3614.5MB
```

-Xms 初始堆内存为：物理内存的1/64 -Xmx 最大堆内存为：系统物理内存的 1/4

####  打印JVM默认参数

使用 `-XX:+PrintCommandLineFlags` 打印出JVM的默认的简单初始化参数

比如我的机器输出为：

```
-XX:InitialHeapSize=266376000 -XX:MaxHeapSize=4262016000 -XX:+PrintCommandLineFlags -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:-UseLargePagesIndividualAllocation -XX:+UseParallelGC 
```

####   生活常用调优参数

- -Xms：初始化堆内存，默认为物理内存的1/64，等价于 -XX:initialHeapSize
- -Xmx：最大堆内存，默认为物理内存的1/4，等价于-XX:MaxHeapSize
- -Xss：设计单个线程栈的大小，一般默认为512K~1024K，等价于 -XX:ThreadStackSize
  - 使用 jinfo -flag ThreadStackSize 会发现 -XX:ThreadStackSize = 0
  - 这个值的大小是取决于平台的
  - Linux/x64:1024KB
  - OS X：1024KB
  - Oracle Solaris：1024KB
  - Windows：取决于虚拟内存的大小
- -Xmn：设置年轻代大小
- -XX:MetaspaceSize：设置元空间大小
  - 元空间的本质和永久代类似，都是对JVM规范中方法区的实现，不过元空间与永久代之间最大的区别在于：元空间并不在虚拟机中，而是使用本地内存，因此，默认情况下，元空间的大小仅受本地内存限制。
  - -Xms10m -Xmx10m -XX:MetaspaceSize=1024m -XX:+PrintFlagsFinal
  - 但是默认的元空间大小：只有20多M
  - 为了防止在频繁的实例化对象的时候，让元空间出现OOM，因此可以把元空间设置的大一些
- -XX:PrintGCDetails：输出详细GC收集日志信息
  - GC
  - Full GC

#### GC日志收集流程图

![image-20200322185639902](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/2_JVM%E5%8F%82%E6%95%B0%E8%B0%83%E4%BC%98/images/image-20200322185639902.png)

我们使用一段代码，制造出垃圾回收的过程

首先我们设置一下程序的启动配置: 设置初始堆内存为10M，最大堆内存为10M

```java
-Xms10m -Xmx10m -XX:+PrintGCDetails
```

然后用下列代码，创建一个 非常大空间的byte类型数组

```java
byte [] byteArray = new byte[50 * 1024 * 1024];
```

运行后，发现会出现下列错误，这就是OOM：java内存溢出，也就是堆空间不足

```
Exception in thread "main" java.lang.OutOfMemoryError: Java heap space
	at com.moxi.interview.study.GC.HelloGC.main(HelloGC.java:22)
```

同时还打印出了GC垃圾回收时候的详情

```
[GC (Allocation Failure) [PSYoungGen: 1972K->504K(2560K)] 1972K->740K(9728K), 0.0156109 secs] [Times: user=0.00 sys=0.00, real=0.03 secs] 
[GC (Allocation Failure) [PSYoungGen: 504K->480K(2560K)] 740K->772K(9728K), 0.0007815 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[Full GC (Allocation Failure) [PSYoungGen: 480K->0K(2560K)] [ParOldGen: 292K->648K(7168K)] 772K->648K(9728K), [Metaspace: 3467K->3467K(1056768K)], 0.0080505 secs] [Times: user=0.00 sys=0.00, real=0.01 secs] 
[GC (Allocation Failure) [PSYoungGen: 0K->0K(2560K)] 648K->648K(9728K), 0.0003035 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[Full GC (Allocation Failure) [PSYoungGen: 0K->0K(2560K)] [ParOldGen: 648K->630K(7168K)] 648K->630K(9728K), [Metaspace: 3467K->3467K(1056768K)], 0.0058502 secs] [Times: user=0.00 sys=0.00, real=0.01 secs] 
Heap
 PSYoungGen      total 2560K, used 80K [0x00000000ffd00000, 0x0000000100000000, 0x0000000100000000)
  eden space 2048K, 3% used [0x00000000ffd00000,0x00000000ffd143d8,0x00000000fff00000)
  from space 512K, 0% used [0x00000000fff00000,0x00000000fff00000,0x00000000fff80000)
  to   space 512K, 0% used [0x00000000fff80000,0x00000000fff80000,0x0000000100000000)
 ParOldGen       total 7168K, used 630K [0x00000000ff600000, 0x00000000ffd00000, 0x00000000ffd00000)
  object space 7168K, 8% used [0x00000000ff600000,0x00000000ff69dbd0,0x00000000ffd00000)
 Metaspace       used 3510K, capacity 4500K, committed 4864K, reserved 1056768K
  class space    used 389K, capacity 392K, committed 512K, reserved 1048576K
```

问题发生的原因：

因为们通过 -Xms10m 和 -Xmx10m 只给Java堆栈设置了10M的空间，但是创建了50M的对象，因此就会出现空间不足，而导致出错

同时在垃圾收集的时候，我们看到有两个对象：GC 和 Full GC

####  GC垃圾收集

GC在新生区

```
[GC (Allocation Failure) [PSYoungGen: 1972K->504K(2560K)] 1972K->740K(9728K), 0.0156109 secs] [Times: user=0.00 sys=0.00, real=0.03 secs]
```

GC (Allocation Failure)：表示分配失败，那么就需要触发年轻代空间中的内容被回收

```
[PSYoungGen: 1972K->504K(2560K)] 1972K->740K(9728K), 0.0156109 secs]
```

参数对应的图为：

![image-20200323124000865](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/2_JVM%E5%8F%82%E6%95%B0%E8%B0%83%E4%BC%98/images/image-20200323124000865.png)

##### Full GC垃圾回收

Full GC大部分发生在养老区

```
[Full GC (Allocation Failure) [PSYoungGen: 0K->0K(2560K)] [ParOldGen: 648K->630K(7168K)] 648K->630K(9728K), [Metaspace: 3467K->3467K(1056768K)], 0.0058502 secs] [Times: user=0.00 sys=0.00, real=0.01 secs] 
```

![image-20200323125839653](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/2_JVM%E5%8F%82%E6%95%B0%E8%B0%83%E4%BC%98/images/image-20200323125839653.png)

规律：

```
[名称： GC前内存占用 -> GC后内存占用 (该区内存总大小)]
```

当我们出现了老年代都扛不住的时候，就会出现OOM异常

#### -XX:SurvivorRatio

调节新生代中 eden 和 S0、S1的空间比例，默认为 -XX:SuriviorRatio=8，Eden:S0:S1 = 8:1:1

加入设置成 -XX:SurvivorRatio=4，则为 Eden:S0:S1 = 4:1:1

SurvivorRatio值就是设置eden区的比例占多少，S0和S1相同

Java堆从GC的角度还可以细分为：新生代（Eden区，From Survivor区合To Survivor区）和老年代

![image-20200323130442088](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/2_JVM%E5%8F%82%E6%95%B0%E8%B0%83%E4%BC%98/images/image-20200323130442088.png)

- eden、SurvivorFrom复制到SurvivorTo，年龄 + 1

首先，当Eden区满的时候会触发第一次GC，把还活着的对象拷贝到SurvivorFrom去，当Eden区再次触发GC的时候会扫描Eden区合From区域，对这两个区域进行垃圾回收，经过这次回收后还存活的对象，则直接复制到To区域（如果对象的年龄已经到达老年的标准，则赋值到老年代区），通知把这些对象的年龄 + 1

- 清空eden、SurvivorFrom

然后，清空eden，SurvivorFrom中的对象，也即复制之后有交换，谁空谁是to

- SurvivorTo和SurvivorFrom互换

最后，SurvivorTo和SurvivorFrom互换，原SurvivorTo成为下一次GC时的SurvivorFrom区，部分对象会在From和To区域中复制来复制去，如此交换15次（由JVM参数MaxTenuringThreshold决定，这个参数默认为15），最终如果还是存活，就存入老年代

![image-20200323150946414](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/2_JVM%E5%8F%82%E6%95%B0%E8%B0%83%E4%BC%98/images/image-20200323150946414.png)

#### -XX:NewRatio（了解）

配置年轻代new 和老年代old 在堆结构的占比

默认： -XX:NewRatio=2 新生代占1，老年代2，年轻代占整个堆的1/3

-XX:NewRatio=4：新生代占1，老年代占4，年轻代占整个堆的1/5，NewRadio值就是设置老年代的占比，剩下的1个新生代

新生代特别小，会造成频繁的进行GC收集

#### -XX:MaxTenuringThreshold

设置垃圾最大年龄，SurvivorTo和SurvivorFrom互换，原SurvivorTo成为下一次GC时的SurvivorFrom区，部分对象会在From和To区域中复制来复制去，如此交换15次（由JVM参数MaxTenuringThreshold决定，这个参数默认为15），最终如果还是存活，就存入老年代

这里就是调整这个次数的，默认是15，并且设置的值 在 0~15之间

查看默认进入老年代年龄：jinfo -flag MaxTenuringThreshold 17344

-XX:MaxTenuringThreshold=0：设置垃圾最大年龄。如果设置为0的话，则年轻对象不经过Survivor区，直接进入老年代。对于年老代比较多的应用，可以提高效率。如果将此值设置为一个较大的值，则年轻代对象会在Survivor区进行多次复制，这样可以增加对象再年轻代的存活时间，增加在年轻代即被回收的概念



## GCRoots

### 什么是垃圾

简单来说就是内存中已经不再被使用的空间就是垃圾.



### GC垃圾回收

JVM在进行GC时，并不是对三个区域统一回收，大部分时候，回收都是新生代~

​	新生代 

​	幸存区（from，to）

​	老年区

GC两种类型：轻GC，重GC（全局GC）



题目：

​	JVM的内存模型 分区~详细到每个区放什么？

​	堆里面的分区有哪些？Eden，from，to，老年区，特点！

#### 常用算法

​	标记清除法、标记压缩、复制算法、引用计数器

​	轻GC和重GC分别在什么时候发生？

##### 引用计数法

Java中，引用和对象是有关联的。如果要操作对象则必须用引用进行。

因此，很显然一个简单的办法就是通过引用计数来判断一个对象是否可以回收。简单说，给对象中添加一个引用计数器

每当有一个地方引用它，计数器值加1

每当有一个引用失效，计数器值减1

任何时刻计数器值为零的对象就是不可能再被使用的，那么这个对象就是可回收对象。

那么为什么主流的Java虚拟机里面都没有选用这个方法呢？其中最主要的原因是它很难解决对象之间相互循环引用的问题。

该算法存在但目前无人用了，解决不了循环引用的问题，了解即可。

![image-20200318213301603](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/1_%E4%BB%80%E4%B9%88%E6%98%AFGCRoots%E8%83%BD%E5%81%9A%E4%BB%80%E4%B9%88/images/image-20200318213301603.png)

##### 枚举根节点做可达性分析

根搜索路径算法

为了解决引用计数法的循环引用个问题，Java使用了可达性分析的方法：

![image-20200319113611244](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/1_%E4%BB%80%E4%B9%88%E6%98%AFGCRoots%E8%83%BD%E5%81%9A%E4%BB%80%E4%B9%88/images/image-20200319113611244.png)

所谓 GC Roots 或者说 Tracing Roots的“根集合” 就是一组必须活跃的引用

基本思路就是通过一系列名为 GC Roots的对象作为起始点，从这个被称为GC Roots的对象开始向下搜索，如果一个对象到GC Roots没有任何引用链相连，则说明此对象不可用。也即给定一个集合的引用作为根出发，通过引用关系遍历对象图，能被遍历到的（可到达的）对象就被判定为存活，没有被遍历到的对象就被判定为死亡

![image-20200319114526625](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/1_%E4%BB%80%E4%B9%88%E6%98%AFGCRoots%E8%83%BD%E5%81%9A%E4%BB%80%E4%B9%88/images/image-20200319114526625.png)

必须从GC Roots对象开始，这个类似于linux的 / 也就是根目录

蓝色部分是从GC Roots出发，能够循环可达

而白色部分，从GC Roots出发，无法到达

###### 一句话理解GC Roots

假设我们现在有三个实体，分别是 人，狗，毛衣

然后他们之间的关系是：人 牵着 狗，狗穿着毛衣，他们之间是强连接的关系

有一天人消失了，只剩下狗狗 和 毛衣，这个时候，把人想象成 GC Roots，因为 人 和 狗之间失去了绳子连接，

那么狗可能被回收，也就是被警察抓起来，被送到流浪狗寄养所

假设狗和人有强连接的时候，狗狗就不会被当成是流浪狗

###### 哪些对象可以当做GC Roots

- 虚拟机栈（栈帧中的局部变量区，也叫做局部变量表）中的引用对象
- 方法区中的类静态属性引用的对象
- 方法区中常量引用的对象
- 本地方法栈中的JNI（Native方法）的引用对象

######  代码说明

```java
/**
 * 在Java中，可以作为GC Roots的对象有：
 * - 虚拟机栈（栈帧中的局部变量区，也叫做局部变量表）中的引用对象
 * - 方法区中的类静态属性引用的对象
 * - 方法区中常量引用的对象
 * - 本地方法栈中的JNI（Native方法）的引用对象
 * @author: 陌溪
 * @create: 2020-03-19-11:57
 */
public class GCRootDemo {


    // 方法区中的类静态属性引用的对象
    // private static GCRootDemo2 t2;

    // 方法区中的常量引用，GC Roots 也会以这个为起点，进行遍历
    // private static final GCRootDemo3 t3 = new GCRootDemo3(8);

    public static void m1() {
        // 第一种，虚拟机栈中的引用对象
        GCRootDemo t1 = new GCRootDemo();
        System.gc();
        System.out.println("第一次GC完成");
    }
    public static void main(String[] args) {
        m1();
    }
}
```







##### 复制算法（新生代）

谁空谁是to

一旦Eden被GC，就空了

经历15次GC还没死，就进养老区

好处：没有内存的碎片

坏处：浪费了内存空间，多了一半空间永远是空to，



复制算法最佳使用场景：

​	对象存活度较低，新生区~

##### 标记压缩清除法（老年代）

![1583763449697](D:\java-\java基础.assets\1583763449697.png)

优点：不需要额外的空间！

缺点：两次扫描严重浪费时间，会产生内存碎片。，会产生STW问题，必须暂停整个程序。

##### 标记压缩

再优化

![1583763621203](D:\java-\java基础.assets\1583763621203.png)



#### 总结

内存效率：复制》标记清除》标记压缩（时间复杂度）

内存整齐度：复制算法=标记压缩》标记清除

内存利用率：标记压缩=标记清除》复制算法

GC：分代收集算法



年轻代：

​	存活率低

​	复制算法！

老年代：

​	区域大：存活率

​	标记清除（内存碎片不是太多 ）+标记压缩混合  实现



### 引用

####  整体架构

![image-20200323155120778](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/3_Java%E4%B8%AD%E7%9A%84%E5%BC%BA%E5%BC%95%E7%94%A8_%E8%BD%AF%E5%BC%95%E7%94%A8_%E5%BC%B1%E5%BC%95%E7%94%A8_%E8%99%9A%E5%BC%95%E7%94%A8%E5%88%86%E5%88%AB%E6%98%AF%E4%BB%80%E4%B9%88/images/image-20200323155120778.png)

#### 强引用

当内存不足的时候，JVM开始垃圾回收，对于强引用的对象，就算是出现了OOM也不会对该对象进行回收，打死也不回收~！

强引用是我们最常见的普通对象引用，只要还有一个强引用指向一个对象，就能表明对象还“活着”，垃圾收集器不会碰这种对象。在Java中最常见的就是强引用，把一个对象赋给一个引用变量，这个引用变量就是一个强引用。当一个对象被强引用变量引用时，它处于可达状态，它是不可能被垃圾回收机制回收的，即使该对象以后永远都不会被用到，JVM也不会回收，因此强引用是造成Java内存泄漏的主要原因之一。

对于一个普通的对象，如果没有其它的引用关系，只要超过了引用的作用于或者显示地将相应（强）引用赋值为null，一般可以认为就是可以被垃圾收集的了（当然具体回收时机还是要看垃圾回收策略）

强引用小例子：

```java
/**
 * 强引用
 * @author: 陌溪
 * @create: 2020-03-23-16:25
 */
public class StrongReferenceDemo {

    public static void main(String[] args) {
        // 这样定义的默认就是强应用
        Object obj1 = new Object();

        // 使用第二个引用，指向刚刚创建的Object对象
        Object obj2 = obj1;

        // 置空
        obj1 = null;

        // 垃圾回收
        System.gc();

        System.out.println(obj1);

        System.out.println(obj2);
    }
}
```

输出结果我们能够发现，即使 obj1 被设置成了null，然后调用gc进行回收，但是也没有回收实例出来的对象，obj2还是能够指向该地址，也就是说垃圾回收器，并没有将该对象进行垃圾回收

```java
null
java.lang.Object@14ae5a5
```

#### 软引用

软引用是一种相对弱化了一些的引用，需要用Java.lang.ref.SoftReference类来实现，可以让对象豁免一些垃圾收集，对于只有软引用的对象来讲：

- 当系统内存充足时，它不会被回收
- 当系统内存不足时，它会被回收

软引用通常在对内存敏感的程序中，比如高速缓存就用到了软引用，内存够用 的时候就保留，不够用就回收

具体使用

```java
/**
 * 软引用
 *
 * @author: 陌溪
 * @create: 2020-03-23-16:39
 */
public class SoftReferenceDemo {

    /**
     * 内存够用的时候
     */
    public static void softRefMemoryEnough() {
        // 创建一个强应用
        Object o1 = new Object();
        // 创建一个软引用
        SoftReference<Object> softReference = new SoftReference<>(o1);
        System.out.println(o1);
        System.out.println(softReference.get());

        o1 = null;
        // 手动GC
        System.gc();

        System.out.println(o1);
        System.out.println(softReference.get());
    }

    /**
     * JVM配置，故意产生大对象并配置小的内存，让它的内存不够用了导致OOM，看软引用的回收情况
     * -Xms5m -Xmx5m -XX:+PrintGCDetails
     */
    public static void softRefMemoryNoEnough() {

        System.out.println("========================");
        // 创建一个强应用
        Object o1 = new Object();
        // 创建一个软引用
        SoftReference<Object> softReference = new SoftReference<>(o1);
        System.out.println(o1);
        System.out.println(softReference.get());

        o1 = null;

        // 模拟OOM自动GC
        try {
            // 创建30M的大对象
            byte[] bytes = new byte[30 * 1024 * 1024];
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            System.out.println(o1);
            System.out.println(softReference.get());
        }

    }

    public static void main(String[] args) {

        softRefMemoryEnough();

        softRefMemoryNoEnough();
    }
}
```

我们写了两个方法，一个是内存够用的时候，一个是内存不够用的时候

我们首先查看内存够用的时候，首先输出的是 o1 和 软引用的 softReference，我们都能够看到值

然后我们把o1设置为null，执行手动GC后，我们发现softReference的值还存在，说明内存充足的时候，软引用的对象不会被回收

```java
java.lang.Object@14ae5a5
java.lang.Object@14ae5a5

[GC (System.gc()) [PSYoungGen: 1396K->504K(1536K)] 1504K->732K(5632K), 0.0007842 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[Full GC (System.gc()) [PSYoungGen: 504K->0K(1536K)] [ParOldGen: 228K->651K(4096K)] 732K->651K(5632K), [Metaspace: 3480K->3480K(1056768K)], 0.0058450 secs] [Times: user=0.00 sys=0.00, real=0.01 secs] 

null
java.lang.Object@14ae5a5
```

下面我们看当内存不够的时候，我们使用了JVM启动参数配置，给初始化堆内存为5M

```
-Xms5m -Xmx5m -XX:+PrintGCDetails
```

但是在创建对象的时候，我们创建了一个30M的大对象

```
// 创建30M的大对象
byte[] bytes = new byte[30 * 1024 * 1024];
```

这就必然会触发垃圾回收机制，这也是中间出现的垃圾回收过程，最后看结果我们发现，o1 和 softReference都被回收了，因此说明，软引用在内存不足的时候，会自动回收

```java
java.lang.Object@7f31245a
java.lang.Object@7f31245a

[GC (Allocation Failure) [PSYoungGen: 31K->160K(1536K)] 682K->811K(5632K), 0.0003603 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[GC (Allocation Failure) [PSYoungGen: 160K->96K(1536K)] 811K->747K(5632K), 0.0006385 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[Full GC (Allocation Failure) [PSYoungGen: 96K->0K(1536K)] [ParOldGen: 651K->646K(4096K)] 747K->646K(5632K), [Metaspace: 3488K->3488K(1056768K)], 0.0067976 secs] [Times: user=0.02 sys=0.00, real=0.01 secs] 
[GC (Allocation Failure) [PSYoungGen: 0K->0K(1536K)] 646K->646K(5632K), 0.0004024 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[Full GC (Allocation Failure) [PSYoungGen: 0K->0K(1536K)] [ParOldGen: 646K->627K(4096K)] 646K->627K(5632K), [Metaspace: 3488K->3488K(1056768K)], 0.0065506 secs] [Times: user=0.00 sys=0.00, real=0.01 secs] 

null
null
```

####  弱引用

不管内存是否够，只要有GC操作就会进行回收

弱引用需要用 `java.lang.ref.WeakReference` 类来实现，它比软引用生存期更短

对于只有弱引用的对象来说，只要垃圾回收机制一运行，不管JVM的内存空间是否足够，都会回收该对象占用的空间。

```java
/**
 * 弱引用
 *
 * @author: 陌溪
 * @create: 2020-03-24-10:18
 */
public class WeakReferenceDemo {
    public static void main(String[] args) {
        Object o1 = new Object();
        WeakReference<Object> weakReference = new WeakReference<>(o1);
        System.out.println(o1);
        System.out.println(weakReference.get());
        o1 = null;
        System.gc();
        System.out.println(o1);
        System.out.println(weakReference.get());
    }
}
```

我们看结果，能够发现，我们并没有制造出OOM内存溢出，而只是调用了一下GC操作，垃圾回收就把它给收集了

```java
java.lang.Object@14ae5a5
java.lang.Object@14ae5a5

[GC (System.gc()) [PSYoungGen: 5246K->808K(76288K)] 5246K->816K(251392K), 0.0008236 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[Full GC (System.gc()) [PSYoungGen: 808K->0K(76288K)] [ParOldGen: 8K->675K(175104K)] 816K->675K(251392K), [Metaspace: 3494K->3494K(1056768K)], 0.0035953 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 

null
null
```



#### 软引用和弱引用的使用场景

场景：假如有一个应用需要读取大量的本地图片

- 如果每次读取图片都从硬盘读取则会严重影响性能
- 如果一次性全部加载到内存中，又可能造成内存溢出

此时使用软引用可以解决这个问题

设计思路：使用HashMap来保存图片的路径和相应图片对象关联的软引用之间的映射关系，在内存不足时，JVM会自动回收这些缓存图片对象所占的空间，从而有效地避免了OOM的问题

```java
Map<String, SoftReference<String>> imageCache = new HashMap<String, SoftReference<Bitmap>>();
```

####  WeakHashMap是什么？

比如一些常常和底层打交道的，mybatis等，底层都应用到了WeakHashMap

WeakHashMap和HashMap类似，只不过它的Key是使用了弱引用的，也就是说，当执行GC的时候，HashMap中的key会进行回收，下面我们使用例子来测试一下

我们使用了两个方法，一个是普通的HashMap方法

我们输入一个Key-Value键值对，然后让它的key置空，然后在查看结果

```java
    private static void myHashMap() {
        Map<Integer, String> map = new HashMap<>();
        Integer key = new Integer(1);
        String value = "HashMap";

        map.put(key, value);
        System.out.println(map);

        key = null;

        System.gc();

        System.out.println(map);
    }
```

第二个是使用了WeakHashMap，完整代码如下

```java
/**
 * WeakHashMap
 * @author: 陌溪
 * @create: 2020-03-24-11:33
 */
public class WeakHashMapDemo {
    public static void main(String[] args) {
        myHashMap();
        System.out.println("==========");
        myWeakHashMap();
    }

    private static void myHashMap() {
        Map<Integer, String> map = new HashMap<>();
        Integer key = new Integer(1);
        String value = "HashMap";

        map.put(key, value);
        System.out.println(map);

        key = null;

        System.gc();

        System.out.println(map);
    }

    private static void myWeakHashMap() {
        Map<Integer, String> map = new WeakHashMap<>();
        Integer key = new Integer(1);
        String value = "WeakHashMap";

        map.put(key, value);
        System.out.println(map);

        key = null;

        System.gc();

        System.out.println(map);
    }
}
```

最后输出结果为：

```
{1=HashMap}
{1=HashMap}
==========
{1=WeakHashMap}
{}
```

从这里我们看到，对于普通的HashMap来说，key置空并不会影响，HashMap的键值对，因为这个属于强引用，不会被垃圾回收。

但是WeakHashMap，在进行GC操作后，弱引用的就会被回收

#### 虚引用

**概念**

虚引用又称为幽灵引用，需要`java.lang.ref.PhantomReference` 类来实现

顾名思义，就是形同虚设，与其他几种引用都不同，虚引用并不会决定对象的生命周期。

如果一个对象持有虚引用，那么它就和没有任何引用一样，在任何时候都可能被垃圾回收器回收，它不能单独使用也不能通过它访问对象，虚引用必须和引用队列ReferenceQueue联合使用。

虚引用的主要作用和跟踪对象被垃圾回收的状态，仅仅是提供一种确保对象被finalize以后，做某些事情的机制。

PhantomReference的get方法总是返回null，因此无法访问对象的引用对象。其意义在于说明一个对象已经进入finalization阶段，可以被gc回收，用来实现比finalization机制更灵活的回收操作

换句话说，设置虚引用关联的唯一目的，就是在这个对象被收集器回收的时候，收到一个系统通知或者后续添加进一步的处理，Java技术允许使用finalize()方法在垃圾收集器将对象从内存中清除出去之前，做必要的清理工作

这个就相当于Spring AOP里面的后置通知

 **场景**

一般用于在回收时候做通知相关操作

#### 引用队列 ReferenceQueue

软引用，弱引用，虚引用在回收之前，需要在引用队列保存一下

我们在初始化的弱引用或者虚引用的时候，可以传入一个引用队列

```
Object o1 = new Object();

// 创建引用队列
ReferenceQueue<Object> referenceQueue = new ReferenceQueue<>();

// 创建一个弱引用
WeakReference<Object> weakReference = new WeakReference<>(o1, referenceQueue);
```

那么在进行GC回收的时候，弱引用和虚引用的对象都会被回收，但是在回收之前，它会被送至引用队列中

完整代码如下：

```java
/**
 * 虚引用
 *
 * @author: 陌溪
 * @create: 2020-03-24-12:09
 */
public class PhantomReferenceDemo {

    public static void main(String[] args) {

        Object o1 = new Object();

        // 创建引用队列
        ReferenceQueue<Object> referenceQueue = new ReferenceQueue<>();

        // 创建一个弱引用
        WeakReference<Object> weakReference = new WeakReference<>(o1, referenceQueue);

        // 创建一个弱引用
//        PhantomReference<Object> weakReference = new PhantomReference<>(o1, referenceQueue);

        System.out.println(o1);
        System.out.println(weakReference.get());
        // 取队列中的内容
        System.out.println(referenceQueue.poll());

        o1 = null;
        System.gc();
        System.out.println("执行GC操作");

        try {
            TimeUnit.SECONDS.sleep(2);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println(o1);
        System.out.println(weakReference.get());
        // 取队列中的内容
        System.out.println(referenceQueue.poll());

    }
}
```

运行结果

```java
java.lang.Object@14ae5a5
java.lang.Object@14ae5a5
null
执行GC操作
null
null
java.lang.ref.WeakReference@7f3124
```

从这里我们能看到，在进行垃圾回收后，我们弱引用对象，也被设置成null，但是在队列中还能够导出该引用的实例，这就说明在回收之前，该弱引用的实例被放置引用队列中了，我们可以通过引用队列进行一些后置操作

## OOM

###  经典错误

JVM中常见的两个错误

StackoverFlowError ：栈溢出

OutofMemoryError: java heap space：堆溢出

除此之外，还有以下的错误

- java.lang.StackOverflowError
- java.lang.OutOfMemoryError：java heap space
- java.lang.OutOfMemoryError：GC overhead limit exceeeded
- java.lang.OutOfMemoryError：Direct buffer memory
- java.lang.OutOfMemoryError：unable to create new native thread
- java.lang.OutOfMemoryError：Metaspace

### 架构

OutOfMemoryError和StackOverflowError是属于Error，不是Exception

![image-20200324144802828](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/4_Java%E5%86%85%E5%AD%98%E6%BA%A2%E5%87%BAOOM/images/image-20200324144802828.png)



### StackoverFlowError

堆栈溢出，我们有最简单的一个递归调用，就会造成堆栈溢出，也就是深度的方法调用

栈一般是512K，不断的深度调用，直到栈被撑破

```java
/**
 * @author: 陌溪
 * @create: 2020-03-24-14:42
 */
public class StackOverflowErrorDemo {

    public static void main(String[] args) {
        stackOverflowError();
    }
    /**
     * 栈一般是512K，不断的深度调用，直到栈被撑破
     * Exception in thread "main" java.lang.StackOverflowError
     */
    private static void stackOverflowError() {
        stackOverflowError();
    }
}
```

运行结果

```
Exception in thread "main" java.lang.StackOverflowError
	at com.moxi.interview.study.oom.StackOverflowErrorDemo.stackOverflowError(StackOverflowErrorDemo.java:17)
```

###  OutOfMemoryError

#### java heap space

创建了很多对象，导致堆空间不够存储

```java
/**
 * Java堆内存不足
 * @author: 陌溪
 * @create: 2020-03-24-14:50
 */
public class JavaHeapSpaceDemo {
    public static void main(String[] args) {

        // 堆空间的大小 -Xms10m -Xmx10m
        // 创建一个 80M的字节数组
        byte [] bytes = new byte[80 * 1024 * 1024];
    }
}
```

我们创建一个80M的数组，会直接出现Java heap space

```
Exception in thread "main" java.lang.OutOfMemoryError: Java heap space
```

####  GC overhead limit exceeded

GC回收时间过长时会抛出OutOfMemoryError，过长的定义是，超过了98%的时间用来做GC，并且回收了不到2%的堆内存

连续多次GC都只回收了不到2%的极端情况下，才会抛出。假设不抛出GC overhead limit 错误会造成什么情况呢？

那就是GC清理的这点内存很快会再次被填满，迫使GC再次执行，这样就形成了恶性循环，CPU的使用率一直都是100%，而GC却没有任何成果。

![image-20200324150646260](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/4_Java%E5%86%85%E5%AD%98%E6%BA%A2%E5%87%BAOOM/images/image-20200324150646260.png)

代码演示：

为了更快的达到效果，我们首先需要设置JVM启动参数

```
-Xms10m -Xmx10m -XX:+PrintGCDetails -XX:MaxDirectMemorySize=5m
```

这个异常出现的步骤就是，我们不断的像list中插入String对象，直到启动GC回收

```java
/**
 * GC 回收超时
 * JVM参数配置: -Xms10m -Xmx10m -XX:+PrintGCDetails -XX:MaxDirectMemorySize=5m
 * @author: 陌溪
 * @create: 2020-03-24-15:14
 */
public class GCOverheadLimitDemo {
    public static void main(String[] args) {
        int i = 0;
        List<String> list = new ArrayList<>();
        try {
            while(true) {
                list.add(String.valueOf(++i).intern());
            }
        } catch (Exception e) {
            System.out.println("***************i:" + i);
            e.printStackTrace();
            throw e;
        } finally {

        }

    }
}
```

运行结果

```
[Full GC (Ergonomics) [PSYoungGen: 2047K->2047K(2560K)] [ParOldGen: 7106K->7106K(7168K)] 9154K->9154K(9728K), [Metaspace: 3504K->3504K(1056768K)], 0.0311093 secs] [Times: user=0.13 sys=0.00, real=0.03 secs] 
[Full GC (Ergonomics) [PSYoungGen: 2047K->0K(2560K)] [ParOldGen: 7136K->667K(7168K)] 9184K->667K(9728K), [Metaspace: 3540K->3540K(1056768K)], 0.0058093 secs] [Times: user=0.00 sys=0.00, real=0.01 secs] 
Heap
 PSYoungGen      total 2560K, used 114K [0x00000000ffd00000, 0x0000000100000000, 0x0000000100000000)
  eden space 2048K, 5% used [0x00000000ffd00000,0x00000000ffd1c878,0x00000000fff00000)
  from space 512K, 0% used [0x00000000fff80000,0x00000000fff80000,0x0000000100000000)
  to   space 512K, 0% used [0x00000000fff00000,0x00000000fff00000,0x00000000fff80000)
 ParOldGen       total 7168K, used 667K [0x00000000ff600000, 0x00000000ffd00000, 0x00000000ffd00000)
  object space 7168K, 9% used [0x00000000ff600000,0x00000000ff6a6ff8,0x00000000ffd00000)
 Metaspace       used 3605K, capacity 4540K, committed 4864K, reserved 1056768K
  class space    used 399K, capacity 428K, committed 512K, reserved 1048576K
  
 
Exception in thread "main" java.lang.OutOfMemoryError: GC overhead limit exceeded
	at java.lang.Integer.toString(Integer.java:403)
	at java.lang.String.valueOf(String.java:3099)
	at com.moxi.interview.study.oom.GCOverheadLimitDemo.main(GCOverheadLimitDemo.java:18)
```

我们能够看到 多次Full GC，并没有清理出空间，在多次执行GC操作后，就抛出异常 GC overhead limit

#### Direct buffer memory

Netty + NIO：这是由于NIO引起的

写NIO程序的时候经常会使用ByteBuffer来读取或写入数据，这是一种基于通道(Channel) 与 缓冲区(Buffer)的I/O方式，它可以使用Native 函数库直接分配堆外内存，然后通过一个存储在Java堆里面的DirectByteBuffer对象作为这块内存的引用进行操作。这样能在一些场景中显著提高性能，因为避免了在Java堆和Native堆中来回复制数据。

ByteBuffer.allocate(capability)：第一种方式是分配JVM堆内存，属于GC管辖范围，由于需要拷贝所以速度相对较慢

ByteBuffer.allocteDirect(capability)：第二种方式是分配OS本地内存，不属于GC管辖范围，由于不需要内存的拷贝，所以速度相对较快

但如果不断分配本地内存，堆内存很少使用，那么JVM就不需要执行GC，DirectByteBuffer对象就不会被回收，这时候怼内存充足，但本地内存可能已经使用光了，再次尝试分配本地内存就会出现OutOfMemoryError，那么程序就奔溃了。

一句话说：本地内存不足，但是堆内存充足的时候，就会出现这个问题

我们使用 -XX:MaxDirectMemorySize=5m 配置能使用的堆外物理内存为5M

```
-Xms10m -Xmx10m -XX:+PrintGCDetails -XX:MaxDirectMemorySize=5m
```

然后我们申请一个6M的空间

```
// 只设置了5M的物理内存使用，但是却分配 6M的空间
ByteBuffer bb = ByteBuffer.allocateDirect(6 * 1024 * 1024);
```

这个时候，运行就会出现问题了

```java
配置的maxDirectMemory：5.0MB
[GC (System.gc()) [PSYoungGen: 2030K->488K(2560K)] 2030K->796K(9728K), 0.0008326 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[Full GC (System.gc()) [PSYoungGen: 488K->0K(2560K)] [ParOldGen: 308K->712K(7168K)] 796K->712K(9728K), [Metaspace: 3512K->3512K(1056768K)], 0.0052052 secs] [Times: user=0.09 sys=0.00, real=0.00 secs] 
Exception in thread "main" java.lang.OutOfMemoryError: Direct buffer memory
	at java.nio.Bits.reserveMemory(Bits.java:693)
	at java.nio.DirectByteBuffer.<init>(DirectByteBuffer.java:123)
	at java.nio.ByteBuffer.allocateDirect(ByteBuffer.java:311)
	at com.moxi.interview.study.oom.DIrectBufferMemoryDemo.main(DIrectBufferMemoryDemo.java:19)
```

#### unable to create new native thread

不能够创建更多的新的线程了，也就是说创建线程的上限达到了

在高并发场景的时候，会应用到

高并发请求服务器时，经常会出现如下异常`java.lang.OutOfMemoryError:unable to create new native thread`，准确说该native thread异常与对应的平台有关

导致原因：

- 应用创建了太多线程，一个应用进程创建多个线程，超过系统承载极限
- 服务器并不允许你的应用程序创建这么多线程，linux系统默认运行单个进程可以创建的线程为1024个，如果应用创建超过这个数量，就会报 `java.lang.OutOfMemoryError:unable to create new native thread`

解决方法：

1. 想办法降低你应用程序创建线程的数量，分析应用是否真的需要创建这么多线程，如果不是，改代码将线程数降到最低
2. 对于有的应用，确实需要创建很多线程，远超过linux系统默认1024个线程限制，可以通过修改linux服务器配置，扩大linux默认限制

```java
/**
 * 无法创建更多的线程
 *
 * @author: 陌溪
 * @create: 2020-03-24-17:02
 */
public class UnableCreateNewThreadDemo {
    public static void main(String[] args) {
        for (int i = 0; ; i++) {
            System.out.println("************** i = " + i);
            new Thread(() -> {
                try {
                    TimeUnit.SECONDS.sleep(Integer.MAX_VALUE);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }, String.valueOf(i)).start();
        }
    }
}
```

这个时候，就会出现下列的错误，线程数大概在 900多个

```
Exception in thread "main" java.lang.OutOfMemoryError: unable to cerate new native thread
```

如何查看线程数

```
ulimit -u
```

#### Metaspace

元空间内存不足，Matespace元空间应用的是本地内存

`-XX:MetaspaceSize` 的处理化大小为20M

##### 元空间是什么

元空间就是我们的方法区，存放的是类模板，类信息，常量池等

Metaspace是方法区HotSpot中的实现，它与持久代最大的区别在于：Metaspace并不在虚拟内存中，而是使用本地内存，也即在java8中，class metadata（the virtual machines internal presentation of Java class），被存储在叫做Matespace的native memory

永久代（java8后背元空间Metaspace取代了）存放了以下信息：

- 虚拟机加载的类信息
- 常量池
- 静态变量
- 即时编译后的代码

模拟Metaspace空间溢出，我们不断生成类 往元空间里灌输，类占据的空间总会超过Metaspace指定的空间大小

##### 代码

在模拟异常生成时候，因为初始化的元空间为20M，因此我们使用JVM参数调整元空间的大小，为了更好的效果

```
-XX:MetaspaceSize=8m -XX:MaxMetaspaceSize=8m
```

代码如下：

```java
/**
 * 元空间溢出
 *
 * @author: 陌溪
 * @create: 2020-03-24-17:32
 */
public class MetaspaceOutOfMemoryDemo {

    // 静态类
    static class OOMTest {

    }

    public static void main(final String[] args) {
        // 模拟计数多少次以后发生异常
        int i =0;
        try {
            while (true) {
                i++;
                // 使用Spring的动态字节码技术
                Enhancer enhancer = new Enhancer();
                enhancer.setSuperclass(OOMTest.class);
                enhancer.setUseCache(false);
                enhancer.setCallback(new MethodInterceptor() {
                    @Override
                    public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
                        return methodProxy.invokeSuper(o, args);
                    }
                });
            }
        } catch (Exception e) {
            System.out.println("发生异常的次数:" + i);
            e.printStackTrace();
        } finally {

        }

    }
}
```

会出现以下错误：

```java
发生异常的次数: 201
java.lang.OutOfMemoryError:Metaspace
```



## 垃圾收集器

### GC垃圾回收算法和垃圾收集器关系

> 天上飞的理念，要有落地的实现（垃圾收集器就是GC垃圾回收算法的实现）
>
> GC算法是内存回收的方法论，垃圾收集器就是算法的落地实现

GC算法主要有以下几种

- 引用计数（几乎不用，无法解决循环引用的问题）
- 复制拷贝（用于新生代）
- 标记清除（用于老年代）
- 标记整理（用于老年代）

因为目前为止还没有完美的收集器出现，更没有万能的收集器，只是针对具体应用最合适的收集器，进行分代收集（那个代用什么收集器）

###  四种主要的垃圾收集器

- Serial：串行回收 `-XX:+UseSeriallGC`
- Parallel：并行回收 `-XX:+UseParallelGC`
- CMS：并发标记清除
- G1
- ZGC：（java 11 出现的）

![image-20200325084453631](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/5_%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8/images/image-20200325084453631.png)

####  Serial

串行垃圾回收器，它为单线程环境设计且值使用一个线程进行垃圾收集，会暂停所有的用户线程，只有当垃圾回收完成时，才会重新唤醒主线程继续执行。所以不适合服务器环境

![image-20200325085320683](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/5_%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8/images/image-20200325085320683.png)

#### Parallel

并行垃圾收集器，多个垃圾收集线程并行工作，此时用户线程也是阻塞的，适用于科学计算 / 大数据处理等弱交互场景，也就是说Serial 和 Parallel其实是类似的，不过是多了几个线程进行垃圾收集，但是主线程都会被暂停，但是并行垃圾收集器处理时间，肯定比串行的垃圾收集器要更短

![image-20200325085729428](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/5_%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8/images/image-20200325085729428.png)

#### CMS

并发标记清除，用户线程和垃圾收集线程同时执行（不一定是并行，可能是交替执行），不需要停顿用户线程，互联网公司都在使用，适用于响应时间有要求的场景。并发是可以有交互的，也就是说可以一边进行收集，一边执行应用程序。

![image-20200325090858921](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/5_%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8/images/image-20200325090858921.png)

#### G1

G1垃圾回收器将堆内存分割成不同区域，然后并发的进行垃圾回收

![image-20200325093222711](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/5_%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8/images/image-20200325093222711.png)

### 垃圾收集器总结

注意：并行垃圾回收在单核CPU下可能会更慢

![image-20200325091619082](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/5_%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8/images/image-20200325091619082.png)



### 查看默认垃圾收集器

使用下面JVM命令，查看配置的初始参数

```
-XX:+PrintCommandLineFlags
```

然后运行一个程序后，能够看到它的一些初始配置信息

```java
-XX:InitialHeapSize=266376000 -XX:MaxHeapSize=4262016000 -XX:+PrintCommandLineFlags -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:-UseLargePagesIndividualAllocation -XX:+UseParallelGC
```

移动到最后一句，就能看到 `-XX:+UseParallelGC` 说明使用的是并行垃圾回收

```java
-XX:+UseParallelGC
```

###  默认垃圾收集器有哪些

Java中一共有7大垃圾收集器

- UserSerialGC：串行垃圾收集器
- UserParallelGC：并行垃圾收集器
- UseConcMarkSweepGC：（CMS）并发标记清除
- UseParNewGC：年轻代的并行垃圾回收器
- UseParallelOldGC：老年代的并行垃圾回收器
- UseG1GC：G1垃圾收集器
- UserSerialOldGC：串行老年代垃圾收集器（已经被移除）

底层源码

![image-20200325100653829](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/5_%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8/images/image-20200325100653829.png)



###  各垃圾收集器的使用范围

![image-20200325101451849](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/5_%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8/images/image-20200325101451849.png)

新生代使用的：

- Serial Copying： UserSerialGC，串行垃圾回收器
- Parallel Scavenge：UserParallelGC，并行垃圾收集器
- ParNew：UserParNewGC，新生代并行垃圾收集器

老年区使用的：

- Serial Old：UseSerialOldGC，老年代串行垃圾收集器
- Parallel Compacting（Parallel Old）：UseParallelOldGC，老年代并行垃圾收集器
- CMS：UseConcMarkSwepp，并行标记清除垃圾收集器

各区都能使用的：

G1：UseG1GC，G1垃圾收集器

垃圾收集器就来具体实现这些GC算法并实现内存回收，不同厂商，不同版本的虚拟机实现差别很大，HotSpot中包含的收集器如下图所示：

![image-20200325102329216](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/5_%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8/images/image-20200325102329216.png)

### 部分参数说明

- DefNew：Default New Generation
- Tenured：Old
- ParNew：Parallel New Generation
- PSYoungGen：Parallel Scavenge
- ParOldGen：Parallel Old Generation

### Java中的Server和Client模式

使用范围：一般使用Server模式，Client模式基本不会使用

操作系统

- 32位的Window操作系统，不论硬件如何都默认使用Client的JVM模式
- 32位的其它操作系统，2G内存同时有2个cpu以上用Server模式，低于该配置还是Client模式
- 64位只有Server模式

![image-20200325175208231](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/5_%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8/images/image-20200325175208231.png)



### 新生代下的垃圾收集器

#### 串行GC(Serial)

串行GC（Serial）（Serial Copying）

是一个单线程单线程的收集器，在进行垃圾收集时候，必须暂停其他所有的工作线程直到它收集结束。

![image-20200325175704604](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/5_%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8/images/image-20200325175704604.png)

串行收集器是最古老，最稳定以及效率高的收集器，只使用一个线程去回收但其在垃圾收集过程中可能会产生较长的停顿(Stop-The-World 状态)。 虽然在收集垃圾过程中需要暂停所有其它的工作线程，但是它简单高效，对于限定单个CPU环境来说，没有线程交互的开销可以获得最高的单线程垃圾收集效率，因此Serial垃圾收集器依然是Java虚拟机运行在Client模式下默认的新生代垃圾收集器

对应JVM参数是：-XX:+UseSerialGC

开启后会使用：Serial(Young区用) + Serial Old(Old区用) 的收集器组合

表示：新生代、老年代都会使用串行回收收集器，新生代使用复制算法，老年代使用标记-整理算法

```
-Xms10m -Xmx10m -XX:PrintGCDetails -XX:+PrintConmandLineFlags -XX:+UseSerialGC
```

#### 并行GC(ParNew)

并行收集器，使用多线程进行垃圾回收，在垃圾收集，会Stop-the-World暂停其他所有的工作线程直到它收集结束

![image-20200325191328733](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/5_%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8/images/image-20200325191328733.png)

ParNew收集器其实就是Serial收集器新生代的并行多线程版本，最常见的应用场景时配合老年代的CMS GC工作，其余的行为和Serial收集器完全一样，ParNew垃圾收集器在垃圾收集过程中同样也要暂停所有其他的工作线程。它是很多Java虚拟机运行在Server模式下新生代的默认垃圾收集器。

常见对应JVM参数：-XX:+UseParNewGC 启动ParNew收集器，只影响新生代的收集，不影响老年代

开启上述参数后，会使用：ParNew（Young区用） + Serial Old的收集器组合，新生代使用复制算法，老年代采用标记-整理算法

```
-Xms10m -Xmx10m -XX:PrintGCDetails -XX:+PrintConmandLineFlags -XX:+UseParNewGC
```

但是会出现警告，即 ParNew 和 Serial Old 这样搭配，Java8已经不再被推荐

![image-20200325194316660](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/5_%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8/images/image-20200325194316660.png)

备注： -XX:ParallelGCThreads 限制线程数量，默认开启和CPU数目相同的线程数

#### 并行回收GC（Parallel）/ （Parallel Scavenge）

因为Serial 和 ParNew都不推荐使用了，因此现在新生代默认使用的是Parallel Scavenge，也就是新生代和老年代都是使用并行

![image-20200325204437678](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/5_%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8/images/image-20200325204437678.png)

Parallel Scavenge收集器类似ParNew也是一个新生代垃圾收集器，使用复制算法，也是一个并行的多线程的垃圾收集器，俗称吞吐量优先收集器。一句话：串行收集器在新生代和老年代的并行化

它关注的重点是：

可控制的吞吐量（Thoughput = 运行用户代码时间 / (运行用户代码时间 + 垃圾收集时间) ），也即比如程序运行100分钟，垃圾收集时间1分钟，吞吐量就是99%。高吞吐量意味着高效利用CPU时间，它多用于在后台运算而不需要太多交互的任务。

自适应调节策略也是ParallelScavenge收集器与ParNew收集器的一个重要区别。（自适应调节策略：虚拟机会根据当前系统的运行情况收集性能监控信息，动态调整这些参数以提供最合适的停顿时间( -XX:MaxGCPauseMills)）或最大的吞吐量。

常用JVM参数：-XX:+UseParallelGC 或 -XX:+UseParallelOldGC（可互相激活）使用Parallel Scanvenge收集器

开启该参数后：新生代使用复制算法，老年代使用标记-整理算法

```java
-Xms10m -Xmx10m -XX:PrintGCDetails -XX:+PrintConmandLineFlags -XX:+UseParallelGC
```

###  老年代下的垃圾收集器

#### 串行GC（Serial Old） / (Serial MSC)

Serial Old是Serial垃圾收集器老年代版本，它同样是一个单线程的收集器，使用标记-整理算法，这个收集器也主要是运行在Client默认的Java虚拟机中默认的老年代垃圾收集器

在Server模式下，主要有两个用途（了解，版本已经到8及以后）

- 在JDK1.5之前版本中与新生代的Parallel Scavenge收集器搭配使用（Parallel Scavenge + Serial Old）
- 作为老年代版中使用CMS收集器的后备垃圾收集方案。

配置方法：

```
-Xms10m -Xmx10m -XX:PrintGCDetails -XX:+PrintConmandLineFlags -XX:+UseSerialOldlGC
```

该垃圾收集器，目前已经不推荐使用了

#### 并行GC（Parallel Old）/ （Parallel MSC）

Parallel Old收集器是Parallel Scavenge的老年代版本，使用多线程的标记-整理算法，Parallel Old收集器在JDK1.6才开始提供。

在JDK1.6之前，新生代使用ParallelScavenge收集器只能搭配老年代的Serial Old收集器，只能保证新生代的吞吐量优先，无法保证整体的吞吐量。在JDK1.6以前(Parallel Scavenge + Serial Old)

Parallel Old正是为了在老年代同样提供吞吐量优先的垃圾收集器，如果系统对吞吐量要求比较高，JDK1.8后可以考虑新生代Parallel Scavenge和老年代Parallel Old 收集器的搭配策略。在JDK1.8及后（Parallel Scavenge + Parallel Old）

JVM常用参数：

```
-XX +UseParallelOldGC：使用Parallel Old收集器，设置该参数后，新生代Parallel+老年代 Parallel Old
```

![image-20200325211525152](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/5_%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8/images/image-20200325211525152.png)



使用老年代并行收集器：

```
-Xms10m -Xmx10m -XX:PrintGCDetails -XX:+PrintConmandLineFlags -XX:+UseParallelOldlGC

```

#### 并发标记清除GC（CMS）

CMS收集器（Concurrent Mark Sweep：并发标记清除）是一种以最短回收停顿时间为目标的收集器

适合应用在互联网或者B/S系统的服务器上，这类应用尤其重视服务器的响应速度，希望系统停顿时间最短。

CMS非常适合堆内存大，CPU核数多的服务器端应用，也是G1出现之前大型应用的首选收集器。

![image-20200325212836441](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/5_%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8/images/image-20200325212836441.png)

Concurrent Mark Sweep：并发标记清除，并发收集低停顿，并发指的是与用户线程一起执行

开启该收集器的JVM参数： -XX:+UseConcMarkSweepGC 开启该参数后，会自动将 -XX:+UseParNewGC打开，开启该参数后，使用ParNew(young 区用）+ CMS（Old 区用） + Serial Old 的收集器组合，Serial Old将作为CMS出错的后备收集器

```
-Xms10m -Xmx10m -XX:+PrintGCDetails -XX:+UseConcMarkSweepGC
```

##### 四个步骤

- 初始标记（CMS initial mark）
  - 只是标记一个GC Roots 能直接关联的对象，速度很快，仍然需要暂停所有的工作线程
- 并发标记（CMS concurrent mark）和用户线程一起
  - 进行GC Roots跟踪过程，和用户线程一起工作，不需要暂停工作线程。主要标记过程，标记全部对象
- 重新标记（CMS remark）
  - 为了修正在并发标记期间，因用户程序继续运行而导致标记产生变动的那一部分对象的标记记录，仍然需要暂停所有的工作线程，由于并发标记时，用户线程依然运行，因此在正式清理前，在做修正
- 并发清除（CMS concurrent sweep）和用户线程一起
  - 清除GC Roots不可达对象，和用户线程一起工作，不需要暂停工作线程。基于标记结果，直接清理对象，由于耗时最长的并发标记和并发清除过程中，垃圾收集线程可以和用户现在一起并发工作，所以总体上来看CMS收集器的内存回收和用户线程是一起并发地执行。

![image-20200710182922046](D:\java-\java基础.assets\image-20200710182922046.png)





优点：并发收集低停顿

缺点：并发执行，对CPU资源压力大，采用的标记清除算法会导致大量碎片

由于并发进行，CMS在收集与应用线程会同时增加对堆内存的占用，也就是说，CMS必须在老年代堆内存用尽之前完成垃圾回收，否则CMS回收失败时，将触发担保机制，串行老年代收集器将会以STW方式进行一次GC，从而造成较大的停顿时间

标记清除算法无法整理空间碎片，老年代空间会随着应用时长被逐步耗尽，最后将不得不通过担保机制对堆内存进行压缩，CMS也提供了参数 -XX:CMSFullGCSBeForeCompaction（默认0，即每次都进行内存整理）来指定多少次CMS收集之后，进行一次压缩的Full GC



## 为什么新生代采用复制算法，老年代采用标整算法

### 新生代使用复制算法

因为新生代对象的生存时间比较短，80%的都要回收的对象，采用标记-清除算法则内存碎片化比较严重，采用复制算法可以灵活高效，且便与整理空间。

### 老年代采用标记整理

标记整理算法主要是为了解决标记清除算法存在内存碎片的问题，又解决了复制算法两个Survivor区的问题，因为老年代的空间比较大，不可能采用复制算法，特别占用内存空间

### 垃圾收集器如何选择

#### 组合的选择

- 单CPU或者小内存，单机程序
  - -XX:+UseSerialGC
- 多CPU，需要最大的吞吐量，如后台计算型应用
  - -XX:+UseParallelGC（这两个相互激活）
  - -XX:+UseParallelOldGC
- 多CPU，追求低停顿时间，需要快速响应如互联网应用
  - -XX:+UseConcMarkSweepGC
  - -XX:+ParNewGC



| 参数                    | 新生代垃圾收集器         | 新生代算法 | 老年代垃圾收集器                                             | 老年代算法 |
| ----------------------- | ------------------------ | ---------- | ------------------------------------------------------------ | ---------- |
| -XX:+UseSerialGC        | SerialGC                 | 复制       | SerialOldGC                                                  | 标记整理   |
| -XX:+UseParNewGC        | ParNew                   | 复制       | SerialOldGC                                                  | 标记整理   |
| -XX:+UseParallelGC      | Parallel [Scavenge]      | 复制       | Parallel Old                                                 | 标记整理   |
| -XX:+UseConcMarkSweepGC | ParNew                   | 复制       | CMS + Serial Old的收集器组合，Serial Old作为CMS出错的后备收集器 | 标记清除   |
| -XX:+UseG1GC            | G1整体上采用标记整理算法 | 局部复制   |                                                              |            |

###  G1垃圾收集器

![image-20200326115120405](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/5_%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8/images/image-20200326115120405.png)

#### 开启G1垃圾收集器

```
-XX:+UseG1GC
```

#### 以前收集器的特点

- 年轻代和老年代是各自独立且连续的内存块
- 年轻代收集使用单eden + S0 + S1 进行复制算法
- 老年代收集必须扫描珍整个老年代区域
- 都是以尽可能少而快速地执行GC为设计原则

#### G1是什么

G1：Garbage-First 收集器，是一款面向服务端应用的收集器，应用在多处理器和大容量内存环境中，在实现高吞吐量的同时，尽可能满足垃圾收集暂停时间的要求。另外，它还具有一下特征：

- 像CMS收集器一样，能与应用程序并发执行
- 整理空闲空间更快
- 需要更多的时间来预测GC停顿时间
- 不希望牺牲大量的吞吐量性能
- 不需要更大的Java Heap

G1收集器设计目标是取代CMS收集器，它同CMS相比，在以下方面表现的更出色

- G1是一个有整理内存过程的垃圾收集器，不会产生很多内存碎片。
- G1的Stop The World（STW）更可控，G1在停顿时间上添加了预测机制，用户可以指定期望停顿时间。

CMS垃圾收集器虽然减少了暂停应用程序的运行时间，但是它还存在着内存碎片问题。于是，为了去除内存碎片问题，同时又保留CMS垃圾收集器低暂停时间的优点，JAVA7发布了一个新的垃圾收集器-G1垃圾收集器

G1是在2012奶奶才在JDK1.7中可用，Oracle官方计划在JDK9中将G1变成默认的垃圾收集器以替代CMS，它是一款面向服务端应用的收集器，主要应用在多CPU和大内存服务器环境下，极大减少垃圾收集的停顿时间，全面提升服务器的性能，逐步替换Java8以前的CMS收集器

主要改变时：Eden，Survivor和Tenured等内存区域不再是连续了，而是变成一个个大小一样的region，每个region从1M到32M不等。一个region有可能属于Eden，Survivor或者Tenured内存区域。

#### 特点

- G1能充分利用多CPU，多核环境硬件优势，尽量缩短STW
- G1整体上采用标记-整理算法，局部是通过复制算法，不会产生内存碎片
- 宏观上看G1之中不再区分年轻代和老年代。把内存划分成多个独立的子区域（Region），可以近似理解为一个围棋的棋盘
- G1收集器里面将整个内存区域都混合在一起了，但其本身依然在小范围内要进行年轻代和老年代的区分，保留了新生代和老年代，但他们不再是物理隔离的，而是通过一部分Region的集合且不需要Region是连续的，也就是说依然会采取不同的GC方式来处理不同的区域
- G1虽然也是分代收集器，但整个内存分区不存在物理上的年轻代和老年代的区别，也不需要完全独立的Survivor（to space）堆做复制准备，G1只有逻辑上的分代概念，或者说每个分区都可能随G1的运行在不同代之间前后切换。

#### 底层原理

Region区域化垃圾收集器，化整为零，打破了原来新生区和老年区的壁垒，避免了全内存扫描，只需要按照区域来进行扫描即可。

区域化内存划片Region，整体遍为了一些列不连续的内存区域，避免了全内存区的GC操作。

核心思想是将整个堆内存区域分成大小相同的子区域（Region），在JVM启动时会自动设置子区域大小

在堆的使用上，G1并不要求对象的存储一定是物理上连续的，只要逻辑上连续即可，每个分区也不会固定地为某个代服务，可以按需在年轻代和老年代之间切换。启动时可以通过参数`-XX:G1HeapRegionSize=n` 可指定分区大小（1MB~32MB，且必须是2的幂），默认将整堆划分为2048个分区。

大小范围在1MB~32MB，最多能设置2048个区域，也即能够支持的最大内存为：32MB*2048 = 64G内存

Region区域化垃圾收集器

####  Region区域化垃圾收集器

G1将新生代、老年代的物理空间划分取消了

![image-20200326120105859](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/5_%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8/images/image-20200326120105859.png)

同时对内存进行了区域划分

![image-20200326120130427](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/5_%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8/images/image-20200326120130427.png)

G1算法将堆划分为若干个区域（Reign），它仍然属于分代收集器，这些Region的一部分包含新生代，新生代的垃圾收集依然采用暂停所有应用线程的方式，将存活对象拷贝到老年代或者Survivor空间

这些Region的一部分包含老年代，G1收集器通过将对象从一个区域复制到另外一个区域，完成了清理工作。这就意味着，在正常的处理过程中，G1完成了堆的压缩（至少是部分堆的压缩），这样也就不会有CMS内存碎片的问题存在了。

在G1中，还有一种特殊的区域，叫做Humongous（巨大的）区域，如果一个对象占用了空间超过了分区容量50%以上，G1收集器就认为这是一个巨型对象，这些巨型对象默认直接分配在老年代，但是如果他是一个短期存在的巨型对象，就会对垃圾收集器造成负面影响，为了解决这个问题，G1划分了一个Humongous区，它用来专门存放巨型对象。如果一个H区装不下一个巨型对象，那么G1会寻找连续的H区来存储，为了能找到连续的H区，有时候不得不启动Full GC。

####  回收步骤

针对Eden区进行收集，Eden区耗尽后会被触发，主要是小区域收集 + 形成连续的内存块，避免内碎片

- Eden区的数据移动到Survivor区，加入出现Survivor区空间不够，Eden区数据会晋升到Old区
- Survivor区的数据移动到新的Survivor区，部分数据晋升到Old区
- 最后Eden区收拾干净了，GC结束，用户的应用程序继续执行

![image-20200326121409237](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/5_%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8/images/image-20200326121409237.png)

回收完成后

![image-20200326121622208](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/5_%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8/images/image-20200326121622208.png)

小区域收集 + 形成连续的内存块，最后在收集完成后，就会形成连续的内存空间，这样就解决了内存碎片的问题

#### 四步过程

- 初始标记：只标记GC Roots能直接关联到的对象
- 并发标记：进行GC Roots Tracing（链路扫描）的过程
- 最终标记：修正并发标记期间，因为程序运行导致标记发生变化的那一部分对象
- 筛选回收：根据时间来进行价值最大化回收

![image-20200326121914326](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/12_JVM/JVM%E9%9D%A2%E8%AF%95%E9%A2%98%E6%B1%87%E6%80%BB/5_%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8/images/image-20200326121914326.png)

#### 参数配置

开发人员仅仅需要申明以下参数即可

三步归纳：`-XX:+UseG1GC -Xmx32G -XX:MaxGCPauseMillis=100`

-XX:MaxGCPauseMillis=n：最大GC停顿时间单位毫秒，这是个软目标，JVM尽可能停顿小于这个时间

#### G1和CMS比较

- G1不会产生内碎片
- 是可以精准控制停顿。该收集器是把整个堆（新生代、老年代）划分成多个固定大小的区域，每次根据允许停顿的时间去收集垃圾最多的区域。

###  SpringBoot结合JVMGC

启动微服务时候，就可以带上JVM和GC的参数

- IDEA开发完微服务工程
- maven进行clean package
- 要求微服务启动的时候，同时配置我们的JVM/GC的调优参数
  - 我们就可以根据具体的业务配置我们启动的JVM参数

例如：

```
java -Xms1024m -Xmx1024 -XX:UseG1GC -jar   xxx.jar
```

# Linux

## 命令集合

###  整机：top，查看整机系统新能

![image-20200326162329550](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/13_Linux%E7%9B%B8%E5%85%B3%E5%91%BD%E4%BB%A4/images/image-20200326162329550.png)

使用top命令的话，重点关注的是 %CPU、%MEM 、load average 三个指标

在这个命令下，按1的话，可以看到每个CPU的占用情况

uptime：系统性能命令的精简版

### CPU：vmstat

- 查看CPU（包含但是不限于）
- 查看额外
  - 查看所有CPU核信息：mpstat -p ALL 2
  - 每个进程使用CPU的用量分解信息：pidstat -u 1 -p 进程编号

命令格式：`vmstat -n 2 3`

![image-20200326162803165](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/13_Linux%E7%9B%B8%E5%85%B3%E5%91%BD%E4%BB%A4/images/image-20200326162803165.png)

一般vmstat工具的使用是通过两个数字参数来完成的，第一个参数是残阳的时间间隔数（单位秒），第二个参数是采样的次数

**procs**

 r：运行和等待的CPU时间片的进程数，原则上1核的CPU的运行队列不要超过2，整个系统的运行队列不超过总核数的2倍，否则代表系统压力过大，我们看蘑菇博客测试服务器，能发现都超过了2，说明现在压力过大

 b：等待资源的进程数，比如正在等待磁盘I/O、网络I/O等

**cpu**

 us：用户进程消耗CPU时间百分比，us值高，用户进程消耗CPU时间多，如果长期大于50%，优化程序

 sy：内核进程消耗的CPU时间百分比

![image-20200326164521263](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/13_Linux%E7%9B%B8%E5%85%B3%E5%91%BD%E4%BB%A4/images/image-20200326164521263.png)

 us + sy 参考值为80%，如果us + sy 大于80%，说明可能存在CPU不足，从上面的图片可以看出，us + sy还没有超过百分80，因此说明蘑菇博客的CPU消耗不是很高

 id：处于空闲的CPU百分比

 wa：系统等待IO的CPU时间百分比

 st：来自于一个虚拟机偷取的CPU时间比

## CPU：mpstat -P ALL 2  和 pidstat -u 1 -p 进程编号

mpstat -P ALL 2 查看所有cpu核信息

pidstat -u 1 -p 进程编号    每个进程使用cpu的用量分解信息

### 内存：free

- 应用程序可用内存数：free -m
- 应用程序可用内存/系统物理内存 > 70% 内存充足
- 应用程序可用内存/系统物理内存 < 20% 内存不足，需要增加内存
- 20% < 应用程序可用内存/系统物理内存 < 70%，表示内存基本够用

free -h：以人类能看懂的方式查看物理内存

![image-20200326170217637](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/13_Linux%E7%9B%B8%E5%85%B3%E5%91%BD%E4%BB%A4/images/image-20200326170217637.png)

free -m：以MB为单位，查看物理内存

![image-20200326165815071](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/13_Linux%E7%9B%B8%E5%85%B3%E5%91%BD%E4%BB%A4/images/image-20200326165815071.png)

free -g：以GB为单位，查看物理内存

### 硬盘：df

格式：`df -h /` (-h：human，表示以人类能看到的方式换算)

![image-20200326170318733](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/13_Linux%E7%9B%B8%E5%85%B3%E5%91%BD%E4%BB%A4/images/image-20200326170318733.png)

- 硬盘IO：iostat

系统慢有两种原因引起的，一个是CPU高，一个是大量IO操作

格式：`iostat -xdk 2 3`

![image-20200326170522559](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/13_Linux%E7%9B%B8%E5%85%B3%E5%91%BD%E4%BB%A4/images/image-20200326170522559.png)



磁盘块设备分布：

rkB /s：每秒读取数据量kB；

wkB/s：每秒写入数据量kB；

svctm I/O：请求的平均服务时间，单位毫秒

await I/O：请求的平均等待时间，单位毫秒，值越小，性能越好

util：一秒钟有百分几的时间用于I/O操作。接近100%时，表示磁盘带宽跑满，需要优化程序或者增加磁盘；

rkB/s，wkB/s根据系统应用不同会有不同的值，但有规律遵循：长期、超大数据读写，肯定不正常，需要优化程序读取。

svctm的值与await的值很接近，表示几乎没有I/O等待，磁盘性能好，如果await的值远高于svctm的值，则表示I/O队列等待太长，需要优化程序或更换更快磁盘



### 网络IO：ifstat

- 默认本地没有，下载ifstat

![image-20200326171559406](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/13_Linux%E7%9B%B8%E5%85%B3%E5%91%BD%E4%BB%A4/images/image-20200326171559406.png)



## 生产环境服务器变慢，诊断思路和性能评估

记一次印象深刻的故障？

结合Linux 和 JDK命令一起分析，步骤如下

- 使用top命令找出CPU占比最高的

- ps -ef 或者 jps 进一步定位，得知是一个怎么样的后台程序出的问题

- 定位到具体线程或者代码

  - ps -mp 进程 -o THREAD，tid，time
  - 参数解释
    - -m：显示所有的线程
    - -p：pid进程使用CPU的时间
    - -o：该参数后是用户自定义格式

  ![image-20200326173656164](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/13_Linux%E7%9B%B8%E5%85%B3%E5%91%BD%E4%BB%A4/images/image-20200326173656164.png)

  

- 将需要的线程ID转换为16进制格式（英文小写格式）

  - printf “%x\n” 有问题的线程ID

- jstack 进程ID | grep tid（16进制线程ID小写英文） -A60

  精准定位到错误的地方

![image-20200326174107444](https://gitee.com/moxi159753/LearningNotes/raw/master/%E6%A0%A1%E6%8B%9B%E9%9D%A2%E8%AF%95/JUC/13_Linux%E7%9B%B8%E5%85%B3%E5%91%BD%E4%BB%A4/images/image-20200326174107444.png)



# Spring boot



## boot内嵌容器原理

tomcat、jetty

![1585916297171](D:\java-\java基础.assets\1585916297171.png)

可以去掉web。xml

DispatcherServlet注入给web容器。

## 零配置原理

## 自动配置原理


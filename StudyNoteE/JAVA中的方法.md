# JAVA中的方法

- 替换字符串种的东西

```java
String str1 = "硅硅谷 尚硅谷你尚硅 尚硅谷你尚硅谷你尚硅你好";
String newStr = str1.replaceAll("尚硅谷","java");
System.out.println(newStr);
```

- 二维数组种的稀疏数组可以完成压缩和解压的功能

- 二维数组创建以及格式化输出

- ```java
  public static void main(String[] args){
      //创建一个原始的二维数组
      //0: 表示没有棋子， 1：表示黑子， 2：表示蓝子
      int chessArr[][] = new int[11][11];
      chessArr[1][2] = 1;
      chessArr[2][3] = 2;
      for(int[] row: chessArr){
          for(int data: row){
              System.out.printf("%d\t",data); //格式化输出
          }
          System.out.println();
      }
  }
  ```

- 输出二维数组的长度，得到的时它有多少行 



-----------------------

## java中native方法的适用情况

- 1.为了使用底层的主机平台的某个特性，而这个特性不能通过java api访问
- 为了访问一个老的系统或者使用一个已有的库，而这个系统或者这个库不是用java编写的
- 为了加快程序的性能，而将一段时间敏感的代码作为本地方法实现

------------------------------

## 静态代码块

- 随着类的加载而执行，而且只执行一次

----------------

## **String中的equil源码分析**

```java
 public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }
        if (anObject instanceof String) {
            String anotherString = (String)anObject;
            int n = value.length;
            if (n == anotherString.value.length) {
                char v1[] = value;
                char v2[] = anotherString.value;
                int i = 0;
                while (n-- != 0) {
                    if (v1[i] != v2[i])
                        return false;
                    i++;
                }
                return true;
            }
        }
        return false;
    }
```

在Object中  就是 ==  （两个对象的应用是否相等）  

然后这个String中重写后，先比较是否  ==  

不是 ==  的话，就比较自己和比较对象的字符串长度，然后逐个字符的进行比较。

--------------------------------

## java 中允许在运行的时候在确定数组的大小

ArrayList类类似于数组，但在添加或删除元素时，它能够自动的调整数组容量，而不需要为此编写任何代码

------------------

## **class类**

java运行时，系统始终为所有对象维护一个运行时标识，

-----

## map集合

常用方法

- void clear()
  - 删除map对象中所有键值对
- boolean containsKey(Object key)
  - 查询map中是否包含指定的key值
- boolean containsvalue(Object value)
  - 查询map中是否包含一个或多个value
- Set enetrySet()
  - 返回map中包含的键值对所组成的Set集合，每个集合都是Map,Entry对象
- Object get() 
  - 返回指定key对应的value ,如果不包含key，则返回null
- boolean isEmpty()
  - 查询map是否为空
- Set keySet()
  - 返回Map中所有key组成的集合
- Collection values() 
  -  返回该map里所有value组成的Collection
- Object put (Object key , Object value)
  - 添加一个键值对，如果集合中的key重复，则覆盖原来的键值对
- void putAll(Map m) 
  - 将Map中的键值对复制到本Map中
- Object remove(object key )
  - 删除指定的key对应的键值对，并返回被删除键值对的value,如果不存在，则返回null
- boolean remove(Object key, Object value)
  - 删除指定键值对，删除成功返回true
- in Size() 
  - 返回map里键值对的个数





HashMap和HashSet是Java Collection Frameword的两个重要成员，其中HashMap是Map接口的常用实现类，HashSet是Set接口的常用实现类。	





-----

## 泛型

泛型的主要目的之一就是，用来指定容器类（需要持有对象的类）要持有什么类型的对象，并且由编辑器来保证类型的正确性。

---------------------------

## String类

- length()
  - 返回字符串的长度
- isEmpty()
  - length 为0的时候返回true, 	length为0,对应的是“”这种字符串，而不是null
  - 底层实现就是判断length==0
- charAt(int index)
  - 根据index返回字符，这个方法会抛出一个经典的异常
  - StringIndexOutOfBoundsException   如果index参数为负，或大于此字符串长度
- char[]  toCharArray()
  - 将字符串变成一个字符数组
- int indexOf("字符“)
  - 查找一个指定的字符串是否存在，返回字符串的位置，如果不存在，则返回-1
- toUpperCase()
  - 将字符串里面的小写字母变成大写字母
- toLowerCase()
  - 将字符串里面的大写字母变成小写字母
- String[]  split("字符")
  - 根据给定的正则表达式的匹配来拆分此字符串，形成一个新的String 数组
- String trim()
  - 去掉字符串左右空格
- String replace(char oldChar,char newChar)
  - 新字符替换旧字符
- String substring(int beginlndex,int endIndex)
  - 截取字符串，包括beginIndex位置的，不包括endIndex位置的
- boolean equalsIgnoreCase(String str2)
  - 忽略大小写的比较两个字符串的值是否一摸一样，返回一个布尔值
- boolean equals(String str2)
  - 比较两个字符串的值是否一摸一样
- boolean contains(String str2)
  - 判断一个字符串里面是否包含指定的内容，返回一个布尔值
- boolean startsWith(String str)
  - 测试此字符串是否以指定的前缀开始，返回一个布尔值
- boolean endsWith(String str)
  - 测试此字符串是否以指定的后缀结束，返回一个布尔值
- String replaceAll(String st1, String st2)
  - 将某个内容全部替换成指定内容
- String replaceFirst(String str1,String str2)
  - 将第一次出现的某个内容替换成指定的内容
- 比较字符串内容是否相同时，一般将常量写在前面，这样避免equals前面的字符串内容为null，equals方法本身具有null判断的功能。
- https://blog.csdn.net/w464960660/article/details/105162900
  - 赋值后对象的比较，
- 



abcadefghijk



-------------------------

## ArrayList类

ArrayList作为list接口的主要实现类，是线程不安全的，但是效率高，底层使用Object[]  elementData数组存储.
扩容问题，底层
jdk7 和jdk8 略有不同，为什么要改呢。
jdk7 
当调用空参构造器的时候，底层默认创建一个长度为10的Object类型的数组。
boolean add(E e)方法，往里面添加值，

有一个全局变量Size方法，当添加值的时候，size+1,数组的长度

+1之前，会判断size+1和数组.length的长度大小，如果要溢出了。就需要去扩容

扩容步骤：

1.记录下底层数组的长度

2.设置一个新的长度，为原来长度的1.5倍

3.如果扩容之后的大小和需要的大小相比，还是小了，就直接拿所需要的长度

4.如果需要的容量，比底层设置的阈值还要大，就会把新的长度设置为整形的最大值，如果还超过的话，就报error了

5.更具新的长度创建一个数组，然后把旧的数组中的数据copy入新的数组

如果在开发中确定数据的长度，可以直接使用带参的构造器，ArrayList (int  initialCapacity)，就不需要扩容操作，效率就要高一些

jdk8中的变化

调用空参构造器，

```
public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}

  private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
```

就不会像7一样在创建对象的时候，就把底层的数组实例化了，从一开始占用很大资源的情况上来讲，这样子好一些bi

  第一次调用add时，底层才创建了长度10的数组，并将数据添加到elementData 中后续的扩容操作和jdk1.8无异。



从数组的创建，到数组的使用这之间很大可能需要时间，比如说，创建了一个ArrayList数组，需要数据，数据呢需要到数据库中去请求一下，请求需要时间。 在这些个时间内的内存就节省下来了。

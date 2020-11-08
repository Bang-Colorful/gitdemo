> 导读：很多优秀的高级框架都是通过反射完成的，反射的重要性，由此可见一斑。反射机制可以使得程序更加灵活，只有学习好反射的基础语法，这样才能自己写出优秀的框架。好了一起打卡学习吧，别忘记了素质三连哦！

往期精彩回放:[一文搞定Java的输入输出流等常见流](https://blog.csdn.net/jiahuan_/article/details/105310304)

[java多线程编程从入门到卓越(超详细总结）](https://blog.csdn.net/jiahuan_/article/details/105392125)

[一文搞定Java集合类，你还在为Java集合类而烦恼吗？](https://blog.csdn.net/jiahuan_/article/details/105281521)

### 文章目录

- [1.反射机制概述](https://editor.csdn.net/md/?articleId=105452884#1_8)

- [2.通过反射机制实例化对象](https://editor.csdn.net/md/?articleId=105452884#2_26)

- [3.反射Field(属性）获取字节码](https://editor.csdn.net/md/?articleId=105452884#3Field_164)

- [4.反射Mothod（方法）](https://editor.csdn.net/md/?articleId=105452884#4Mothod_265)

- [5.反射Constructor(构造方法）](https://editor.csdn.net/md/?articleId=105452884#5Constructor_336)

- [6.通过反射获取父类和父接口](https://editor.csdn.net/md/?articleId=105452884#6_390)

- [7.扩展知识](https://editor.csdn.net/md/?articleId=105452884#7_455)

- - [1.类加载器](https://editor.csdn.net/md/?articleId=105452884#1_456)

  - [2.可变长度参数](https://editor.csdn.net/md/?articleId=105452884#2_475)

  - [3.注解](https://editor.csdn.net/md/?articleId=105452884#3_479)

  - - [反射注解](https://editor.csdn.net/md/?articleId=105452884#_506)

 

# 1.反射机制概述

- 反射机制有什么作用？
  - java语言中反射机制可以操作字节码文件。
  - 优点:可以直接读和修改字节码文件。通过反射机制可以操作class文件。
  - 反射在框架中使用很多，掌握它，你注定不凡。
- 在java.lang.reflect.*;包下。
- 反射机制相关重要的类：
  - java.lang.Class：代表字节码，代表一个类型（整个类）。
  - java.lang.reflect.Method：代表字节码中的方法字节码（类中的方法）
  - java.lang.reflect.Constructor：代表字节码中的构造方法字节码（类中的构造方法）
  - java.lang.reflect.Field:代表字节码中的属性字节码。（类中的静态变量和成员变量）
- 要操作一个类的字节码，需要首先获取到这个类的字节码，那如何获取java.lang.Class实例呢？
  - 有三种方式：
  - 第一种：class.forName（“ ”）方法：静态方法，方法的参数是一个字符串（完整的类名），不能省略包。返回一个Class类型.（lang.class字节码文件）。使用这个方法会加载类到JVM,使static静态代码块执行（只执行一次），如果你只想让程序执行static静态代码块，只用这个方法就好了。（重点，JDBC中会用）
  - 第二种：getClass()方法，任何一个对象都有这个方法。(返回字节码文件）同一个类的对象返回的java.lang.Class字节码相同（所指的地址相同）。
  - 第三种：java语言中任何一中类型，包括基本数据类型，他都有.class属性。例如：
    - Class x = String.class;
    - Class a = int.class;

# 2.通过反射机制实例化对象

- 通过反射机制，获取Class,通过Class来实例化对象.
  - newInstance()方法：创建一个和之前反射相同类型的对象，会自动调用类的无参构造方法。没有无参构造方法，会报异常。（JDK8之后过时了，不过现在主要用的就是JDK8,sun公司长期支持JDK8和JDK11）
  - 举个栗子：

```go
package Day2;


public class ReflectTest1 {
    public static void main(String[] args) {
        Class s1 = null;
        try {
            //通过反射机制获取Class
             s1= Class.forName("Day2.User");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
        Object a=null;
        try {
           a =s1.newInstance();//用反射获得的Class,创建一个对象
            System.out.println(a);
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }

    }
}
class User{

    public User() {
        System.out.println("无参构造方法！");
    }
}
```

- 使用反射机制创建对象有什么作用呢？
  - 反射机制让程序更加灵活。
  - java代码写一遍，在不改变java源代码的基础上，可以做到不同对象的实例化。（符合OCP原则:对外开放，修改关闭。)
  - 高级框架：底层实现原理：采用了反射机制。
  - 举个栗子：（使用IO流，properties集合，通过属性文件来实例化对象）（重点）

```go
package Day2;

import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;
import java.util.Properties;

public class ReflectTest2 {
    public static void main(String[] args) throws Exception {
        //通过IO流读取flie.properties文件
        FileReader reader = new FileReader("file.properties");
        //创建属性类对象(Properties对象，Map集合，key和value都是字符串类型
        Properties pro = new Properties();
        //将文件中的数据加载到Map集合中加载
        pro.load(reader);
        reader.close();//这个流不能在没加载数据之前就关闭了
        //通过key获取value
        String a = pro.getProperty("ClassName");
        Class bb = Class.forName(a);//反射获得Class
        Object c = bb.newInstance();//通过Class,新建一个对象
        System.out.println(c);
    }
}
class User1 {
    public User1() {
        System.out.println("无参构造方法！");
    }
}
```

- 如何获取类路径下文件的绝对路径？
  - 为什么聊这个？因为上述代码可能换一个位置，当前路径名就失效了，（即移植性差)所以我们的聊聊这个。
  - 使用以下通用方式，这个文件必须保存在类路径下，（即src目录下)。
  - String path = Thread.currentThread().getContextClassLoader().getResource(" ").getPath();(重点）使用这个方法可以获取文件的绝对路径。（适用于各种操作系统）
  - 哈哈，看不懂?别急解释一下上述程序：
  - Thread.currentThread()当前线程对象
  - getContextClassLoader()使对象的方法，可以获取当前线程的类加载器对象。
  - getResource()这是类加载器对象的方法，当前线程的类加载器默认从类的根路径下加载资源。
  - 示例：

```go
public class Path1 {
    public static void main(String[] args) {
        String path = Thread.currentThread().getContextClassLoader().getResource("File2").getPath();
        System.out.println(path);
    }
}
```

- 从执行结果可以看出：源文件和字节码文件存放在不同的位置，执行后与之对应的文件保存在out/production目录下（IDEA中）。
- 直接以流的方式读取文件，省去获取绝对路径的环节。
  - InputStream reader = Thread.currentThread().getContextClassLoader().getResoutceAsStream("文件名“（类路径下））
  - 举个栗子：

```java
package Day2;


import java.io.InputStream;
import java.util.Properties;

public class Path2 {
    public static void main(String[] args) throws Exception {
        //创建一个流，读取文件
        InputStream reader = Thread.currentThread().getContextClassLoader().getResourceAsStream("File2");
        //创建一个Properties集合，保存数据
        Properties pro = new Properties();
            pro.load(reader);//将文件数据加载到集合中
            reader.close();
        String f =pro.getProperty("NewName");
        Class bb = Class.forName(f);//反射获得Class
        Object cc = bb.newInstance();//通过Class实例化对象
        System.out.println(cc);

    }
}
class User2 {
    public User2() {
        System.out.println("无参构造方法！");
    }
}
```

- 资源绑定器（重点）
  - 在java.util包下，便于获取属性配置文件的内容。
  - 使用这种方式也必须将属性配置文件放置类路径下。
  - 只能绑定xxx.properties文件。并且不用写路径的扩展名。
  - 此方法简洁简单。所以，以后就用这个方法就好了，集合配合流的方法就不需要用了。
  - 举个栗子：

```go
import java.util.ResourceBundle;

public class Path3 {
    public static void main(String[] args) {
        ResourceBundle bundle = ResourceBundle.getBundle("File2");
        String Name = bundle.getString("NewName");
        System.out.println(Name);
    }
}
```

# 3.反射Field(属性）获取字节码

- 反射属性之前，应该获取整个类。（Class.forName(" ")方法获取。)
- 获取整个类中的所有的Field。
  - **getFields()方法；用之前获取的整个类调用；**其获取所有public修饰的属性，返回到Field[]数组中。
  - **getDeclaredFields()方法；获取所有属性。**不受修饰符现在，返回到Field[]数组中。
  - getName()方法：可以获取属性名字。对于类也有名字，也用这个方法，对于反射获得的类还有simpleName()方法，来获取简单名字。
  - **getType()方法：获取属性的类型，返回值为类，**然后我们再通过这个得到的类用getName方法获取属性类型的名字。
  - getModifiers()方法：获取属性的修饰符列表，返回的是int类型，每个修饰符的数字是，修饰符的代号。我们可以用toString方法（Modifier.toString),Modefier为reflect的子类，返回一个字符串类型。
  - 举个栗子：

```go
package Day1;

import java.lang.reflect.Field;
import java.lang.reflect.Modifier;

public class FieldTest1 {
    public static void main(String[] args) {
        Class First=null;

        try {
            First = Class.forName("Day1.Student");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
        Field[] fields = First.getDeclaredFields();
        System.out.println(fields.length);
        for(Field arg:fields) {
            int a = arg.getModifiers();
            System.out.println(Modifier.toString(a));//Modefier为reflect的子类
            Class ff = arg.getType();
            System.out.println(ff.getName());
            System.out.println(arg.getName());
        }
    }
}
class Student{
    public String name;
    private int age;
    protected double scroe;
    boolean flag;
}
```

- 通过反射机制，反编译一个类的属性Field
- 怎么通过反射机制访问对象属性？（赋值，获取属性的值）
  - 首先获取整个Class。
  - 然后通过Class实例化对象。
  - 获取对象的属性；依据名字。
  - 属性.set（对象,222)方法：以获取的属性调用，给对象的属性赋值。
  - 反射机制代码复杂，但灵活。
  - 读取属性的值：属性.get(对象）。
  - 举个栗子:

```go
package Day1;

import java.lang.reflect.Field;

public class FieldTest2 {
    public static void main(String[] args) {
        Class fio = null;//获取Class
        Object ff = null;
        {
            try {
                fio = Class.forName("Day1.Student");
                ff = fio.newInstance();//实例化对象
            } catch (ClassNotFoundException e) {
                e.printStackTrace();
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            } catch (InstantiationException e) {
                e.printStackTrace();
            }
        }
        //获取属性
        Field fl = null;
        {
            try {
                fl = fio.getDeclaredField("name");//通过类获取属性，无法通过对象获取
            } catch (NoSuchFieldException e) {
                e.printStackTrace();
            }
        }
        //给fio对象的fl属性赋值
        try {
            fl.set(ff,"sfdsa");
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }
        //获取属性的值
        try {
            System.out.println(fl.get(ff));
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }
    }
}
```

- 可以用上述方法访问私有属性吗？
  - 不可以？当时我们可以用：属性.setAccessible(true)方法打破封装。
  - 因此安全性差。

# 4.反射Mothod（方法）

反射Mothod

- 首先获取Class。
- 获取所有的Method方法。
- Method[] methods=类.getDeclaredMethod();方法:获取所有的Method.
- 获取方法的返回值类型：方法.getReturnType().getSimpleName();
- 获取修饰符列表：Modifer.toString(方法.getModifiers()；
- 获取方法参数的类型：方法.getParameterTypes()；返回Class[]数组。再调用getSimpleName()即可。
- Mothod的反编译（不要求掌握，有兴趣的小伙伴可以自行了解一下）
- 通过反射机制怎么调用对象的方法。（重点）
- 1.获取Calss，并用Calss实例化对象。
- 2.获取Method:使用Method logMethod = 类.getDeclaredMethod("方法名“，类型名.class,类型名.class);方法。
- 3.调用方法：（对象，方法名，参数，返回值）
  - Object value = 方法.invoke("对象”，“参数值”，“参数值”）；invoke方法返回一个Object类型。
  - 举个栗子：

```go
package Day1;

import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

public class MethodTest1 {
    public static void main(String[] args) {
        Class fos =null;
        try {
             fos = Class.forName("Day1.Student1");//获取Class
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
        Object fis =null;
        try {
            fis = fos.newInstance();//创建对象
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }
        Method method =null;
        try {
             method = fos.getDeclaredMethod("GetScore",String.class,double.class);//获取方法
        } catch (NoSuchMethodException e) {
            e.printStackTrace();
        }
        Object value  = null;
        try {
            value = method.invoke(fis,"jia",520);//调用方法

        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        }
        System.out.println(value);
    }
}
class Student1{
    private String name;
    private  int age;
    private double score;
    public String   GetScore(String name,double score){
        if(score>500){
            System.out.println("恭喜成功！");
        }
        else {
            System.out.println("继续加油！");
        }
        return  name;
    }
}
```

- 反射机制的优点：让代码具有通用性，可变化的内容都是写到配置文件中，将来修改配置文件之后，创建的对象不一样了，调用的方法也不同了，但是java代码不需要做任何修改。非常的灵活。

# 5.反射Constructor(构造方法）

- 反射机制怎么调用构造方法？
- 使用反射机制创建对象？
  - 1.调用无参构造方法。（newInstance方法)
  - 2.调用有参构造方法（也可以用这种方法调用无参构造方法）。
  - 第一步：先获取这个有参构造方法；Constructor con = 类.getDeclaredConstructor(int.class,String.classs)第二步：调用构造方法new对象。Object aa =con.newInstance(参数列表）。
  - 举个栗子：

```go
package Day1;

import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;

public class ConstructorTest1 {
    public static void main(String[] args){
        Class fio=null;
        Constructor con=null;
        Object cc = null;
        try {
            //获取类Class
            fio = Class.forName("Day1.Student3");
            //获取构造方法，参数很重要
            con =fio.getDeclaredConstructor(int.class,String.class);
            cc= con.newInstance(11,"Jia");//调用构造方法new对象，这种方法没有过期
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (NoSuchMethodException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        }
        //输出对象
        System.out.println(cc);//自动调用toString方法

    }
}
class Student3{
    private  int age;
    private  String name;
//构造方法
    public Student3() {
    }

    public Student3(int age, String name) {
        this.age = age;
        this.name = name;
        System.out.println("调用成功！");
    }
}
```

# 6.通过反射获取父类和父接口

- 1.获取类的Class
- 2.获取Class父类：Class superClass = 子类.getSuperclass();
- 获取子类实现的所有接口：Class[] interfaces = 子类.getInterfaces();方法。
- 举个栗子：

```go
package Day1;

public class SuperClassTest1 {
    public static void main(String[] args){
        Class fos =null;
        Class superClass=null;
        try {
            //获取类的Class
            fos= Class.forName("Day1.Student5");
            //获取父类的Class
            superClass = fos.getSuperclass();
            //实例化父类对象
            Object cc = superClass.newInstance();
            System.out.println(cc);
            //获取子类的所有接口
            Class[] interfaces = superClass.getInterfaces();
            for(Class ff : interfaces){
                //获取接口的简称，即不含包名
                System.out.println(ff.getSimpleName());
            }
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InstantiationException e) {
            e.printStackTrace();
        }
    }
}
//学生类
class Student5 extends Parents{
    private  int age;
    private  String name;
    //构造方法
    public Student5() {
    }

    public Student5(int age, String name) {
        this.age = age;
        this.name = name;
        System.out.println("调用成功！");
    }
}
//家长类
class Parents implements Study,Sport{
    public Parents() {
        super();
    }
}
//接口
interface Study{

}
interface Sport{

}
```

# 7.扩展知识

## 1.类加载器

- 什么是类加载器？
  - 专门负责加载类的命令/工具。
  - ClassLoader
- JDK中自带了3个类加载器
  - 启动类加载器（加载jre\lib\rt.jar)父
  - 扩展类加载器（母）
  - 应用类加载器
- 假设有这样一段代码：
- String s = “abc”;
  - 代码在开始执行之前，会将所需要的类全部加载JVM。看到以上代码，类加载器会找到String.class文件，找到就加载，那么是怎么进行加载的呢？
  - 首先通过：启动类加载器”加载。
  - rt.jar中都是Jdk最基本的核心类库。
  - 如果通过“启动类加载器"加载不到的时候：会通过”扩展类加载器“加载。
  - 注意：扩展类加载器专门加载：jre\lib\ext*.jar
  - 如果扩展类加载器没有加载到，那么会通过“应用类加载器”加载。
  - 应用类加载器:classpath中的jar包的类。（class文件）
- java中为了保证类加载的安全，使用了双亲委派机制。
  - 优先从启动类，（父）然后是扩展类（母），最后是应用类加载器。

## 2.可变长度参数

- int…args:这就是可变长度参数。个数为0到N个。
- 可变长只能在参数的最后一个，且只能有一个。
- 可变成长度参数可以当做数组看待，具有length属性，有下标，调用的时候可以传一个数组。

## 3.注解

- 注解：或者叫做注释（Annotation)
- 注解有什么作用？
  - 对程序的注释。
- 注解是一个引用数据类型。编译之后也是生成xxx.class文件。
- 怎么自定义注解呢？语法格式是怎么样的？
  - [修饰符列表] @interface 注解类型名{
    }
- 注解怎么使用，用在什么地方？
- 注解使用时语法格式时：@注解类型名
- 可以用在构造方法，类，属性，接口，方法，注解等，注解可以修饰注解。
- 自定义注解：
  - 如果注解中有属性，必须给其赋值。可以在注释的时候@注解名(属性=值），属性名为value时并且只有一个属性的时候，注解的时候可以不写value。定义属性的时候后面需要小括号，可以用default给属性赋默认值。
- 注解的属性可以是哪些类型？
  - byte,short,int, long,double,boolean,char,float,String,枚举，Class类型，和其数组类型。如果数组中只有一个元素给其赋值时大括号可以省略。
- 什么是元注解？
  - 用来标注“注解类型”的注解。
- 常见的元注解：
  - Target注解：（用来标注，被注解的注解可以出现在哪个位置上，其由后面的小括号参数决定。）如：@Target({ElementType.Type,ElementType.MEYHOD})只允许该注解标注类，方法
  - Retention注解:用来标注“被注解的注解”最终保存在哪里。
  - @Retention(RetentionPolicy,SOURCE)表示只被保留在java源文件中。
  - @Retention（RetentionPolicy,CLASS)class文件中。
  - @Retention（RetentionPolicy,RUNTIME）class文件中，并能被注解。//RetentionPolicy是一个枚举类型。
- JDK内置的注解。
- java.lang包下：
- 1.Override注解：只能注解方法，只给编译器参考。如果这个方法不是重写父类的方法，编译器报错。（标识性注解）
- 2.Deprecated注解：表示某个类，方法已过时。为了告知别人某个类，方法等已过时。

### 反射注解

如何获取注解类的属性？
1.获取类Class
\* Class dd = Class.forName(“完整类名”);
2.判断类上面是否存在特定的注解。
\* if(dd(类).isAnnotationPresent(注解类名.class)
3.如果存在特定的注解，则获取注解对象。
\* 注解类名 fs = (注解类名）dd.getAnnotation(注解类名.class);
4.获取注解对象的属性。


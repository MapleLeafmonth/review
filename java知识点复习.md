# java知识点复习

## 1、java的数据类型

### 八个基本类型：



|          | boolean | byte | char      | short | int     | float | long | double |
| -------- | ------- | ---- | --------- | ----- | ------- | ----- | ---- | ------ |
| 位数     | 1       | 8    | 16        | 16    | 32      | 32    | 64   | 64     |
| 字节数   | 1/8     | 1    | 2         | 2     | 4       | 4     | 8    | 8      |
| 默认值   | false   | 0    | 0         | 0     | 0       | 0.0f  | 0L   | 0.0d   |
| 包装类型 | Boolean | Byte | Character | Short | Integer | Float | Long | Double |

特别说明：

​       一个字节等于8个二进制位

- char类型占2个字节，可以表示汉字，所以**无论是汉字还是英文字符都占2字节**
- boolean类型理论上占1bit（1/8字节），而实际中按1byte（1字节）处理
- 基本数据类型之间的转换：低级向高级自动转换（隐式），高级向低级强制转换（显式）

```
public class DataTypeDemo {

    byte a;
    short b;
    int c;
    long d;
    float e;
    double f;
    boolean g;
    char h;
    
    public static void main(String[] args) {
        /*
         * 几种基本数据类型的大小(bit)
         */
        System.out.println(Byte.SIZE); //8
        System.out.println(Short.SIZE);//16
        System.out.println(Integer.SIZE);//32
        System.out.println(Long.SIZE);//64
        System.out.println(Float.SIZE);//32
        System.out.println(Double.SIZE);//64
        System.out.println(Character.SIZE);//16
        
        /*
         * 基本数据类型的默认值
         */
        DataTypeDemo dtd = new DataTypeDemo();
        System.out.println(dtd.a);//0
        System.out.println(dtd.b);//0
        System.out.println(dtd.c);//0
        System.out.println(dtd.d);//0
        System.out.println(dtd.e);//0.0
        System.out.println(dtd.f);//0.0
        System.out.println(dtd.g);//false
        System.out.println(dtd.h);//
        
        /*
         * char类型占2字节，可以表示汉字
         */
        char ch = '犇';
        System.out.println(ch);//犇
        
        /*
         * 基本数据类型的转换
         *     低级->高级：自动转换，隐式的
         *     高级->低级：强制转换，显式的
         * 另外，常规的算术运算(+、-、*、/)都要求是int型的
         *     所以对于较int低级的数据类型会进行隐式转换，而较int高级的则要求强转，否则报错
         */
        byte n1 = 10, n2 = 20;
        int n3, n4 = 100;
        long n5 = 200;
        //隐式转换
        n3 = n1;
        //显式转换
//        n3 = n5;    编译报错
        n3 = (int) n5;
        
        //算术运算,"+"两边的操作数会自动转换为int型
        n3 = n1 + n2;
//        n3 = n4 + n5;  编译报错：需要强转
        //修改后
        n3 = n4 + (int)n5;
        n3 = (int) (n4 + n5);
        
//        n3 = n4/(int)n5;
//        n3 = (int) (n4/n5);
    }
}
```

### 包装器

为了满足Java语言面向对象的这一特性，上述基本数据类型中的每一个在java.lang包中都有一个包装类，即将每个基本类型都包装成了一个类。常用的包装类可以分为三类：Character、Number、Boolean，具体见上表所示。

这里总结一下包装类的一些特性：

- 所有包装类都可以将与之对应的基本数据类型作为参数来创建它们的实例对象

- 除了Character类之外，其他包装类都可以将一个字符串作为参数来构造它们的实例

- Boolean类的构造方法参数为String类型时，若该字符串为true（不论大小写），则该对象表示true，否则表示false

- 当包装类Number构造方法的参数为String类型时，字符串不能为null，并且该字符串必须能够解析为基本类型的数据

  **包装类的作用**

  总的来说，包装类有以下一些用途：

  - 集合不允许存放基本数据类型，故常用包装类
  - 包含了每种基本数据类型的相关属性，如最大值、最小值、所占位数等
  - 作为基本数据类型对应的类类型，提供了一系列实用的对象操作，如类型转换、进制转换等等

```
Integer i = 9; //装箱
int j = i; //拆箱
```

### 缓冲池

new Integer(123) 与 Integer.valueOf(123) 的区别在于：

- new Integer(123) 每次都会新建一个对象；
- Integer.valueOf(123) 会使用缓存池中的对象，多次调用会取得同一个对象的引用。

```
Integer x = new Integer(123);
Integer y = new Integer(123);
System.out.println(x == y);    // false
Integer z = Integer.valueOf(123);
Integer k = Integer.valueOf(123);
System.out.println(z == k);   // true
```

valueOf() 方法的实现比较简单，就是先判断值是否在缓存池中，如果在的话就直接返回缓存池的内容。

```
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

在 Java 8 中，Integer 缓存池的大小默认为 -128~127。

```
static final int low = -128;
static final int high;
static final Integer cache[];

static {
    // high value may be configured by property
    int h = 127;
    String integerCacheHighPropValue =
        sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
    if (integerCacheHighPropValue != null) {
        try {
            int i = parseInt(integerCacheHighPropValue);
            i = Math.max(i, 127);
            // Maximum array size is Integer.MAX_VALUE
            h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
        } catch( NumberFormatException nfe) {
            // If the property cannot be parsed into an int, ignore it.
        }
    }
    high = h;

    cache = new Integer[(high - low) + 1];
    int j = low;
    for(int k = 0; k < cache.length; k++)
        cache[k] = new Integer(j++);

    // range [-128, 127] must be interned (JLS7 5.1.7)
    assert IntegerCache.high >= 127;
}
```

**基本类型对应的缓冲池**如下：

- boolean values true and false
- all byte values
- short values between -128 and 127
- int values between -128 and 127
- char in the range \u0000 to \u007F

在使用这些基本类型对应的包装类型时，就可以直接使用缓冲池中的对象。 



## 2、String

String 被声明为 final，因此它不可被继承。

内部使用 char 数组存储数据，该数组被声明为 final，这意味着 value 数组初始化之后就不能再引用其它数组。并且 String 内部没有改变 value 数组的方法，因此可以保证 String 不可变。

```
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];
```

**`不可变的好处`**

**1. 可以缓存 hash 值**

因为 String 的 hash 值经常被使用，例如 String 用做 HashMap 的 key。不可变的特性可以使得 hash 值也不可变，因此只需要进行一次计算。

**2. String Pool 的需要**

如果一个 String 对象已经被创建过了，那么就会从 String Pool 中取得引用。只有 String 是不可变的，才可能使用 String Pool。

 ![1542712163835](C:\Users\LaoK\AppData\Local\Temp\1542712163835.png)

 

**3. 安全性**

String 经常作为参数，String 不可变性可以保证参数不可变。例如在作为网络连接参数的情况下如果 String 是可变的，那么在网络连接过程中，String 被改变，改变 String 对象的那一方以为现在连接的是其它主机，而实际情况却不一定是。

**4. 线程安全**

String 不可变性天生具备线程安全，可以在多个线程中安全地使用。











## I/O流

创建文件夹：

```
File f = new File("g:"+File.separator +"fileDemo");
f.mkdir();
```

创建文件：

```
File f = new File("g:"+File.separator +"fileDemo.txt");
f.createNewFile();
```

删除文件：

```
File f = new File("g:"+File.separator +"fileDemo.txt");
f.delete();//删除的文件不会放在回收站，不可恢复
```

常见的一些文件操作：

```
System.out.println("是否是文件："+f.isFile());
System.out.println("是否是文件夹："+f.isDirectory());
System.out.println("文件是否存在："+f.exists());
System.out.println("文件名："+f.getName());
System.out.println("文件路径："+f.getAbsolutePath());
System.out.println("文件的大小："+f.length());
```



列出指定文件下的所有文件名   file.list()

```
		File file = new File("g://ICSS");
		String[] list = file.list();
		for (String string : list) {
			System.out.println(string);
		}
/*输出结果：
        pom中关于SSM的配置.txt
        PPT
        TestSpringMVC
        小组信息
        康志伟学员周报表.xlsx
        文档
        最终提交的资料
        课堂共享软件
*/
```

列出指定目录下的所有文件（路径）file.listFies()

```
		File file = new File("g://ICSS");
		String[] list = file.list();
		File[] listFiles = file.listFiles();
		for (File file2 : listFiles) {
			System.out.println(file2);
		}
/*输出结果：
        g:\ICSS\pom中关于SSM的配置.txt
        g:\ICSS\PPT
        g:\ICSS\TestSpringMVC
        g:\ICSS\小组信息
        g:\ICSS\康志伟学员周报表.xlsx
        g:\ICSS\文档
        g:\ICSS\最终提交的资料
        g:\ICSS\课堂共享软件
*/
```

字节输出流：

```
		/**
		 * 1、字节流：可以用于读写任何类型文件
		 * 2、字符流：可以用于读写文本文件，不能操作二进制文件
		 * 
		 * 在java中IO流的操作步骤：
		 * 1、使用File类去打开一个文件
		 * 2、通过字节流或字符流指定输出位置
		 * 3、进行相关的读/写操作
		 * 4、关闭流
		 */
		//1、字节输出流
		File f = new File("e:\\123.txt");//文件如果不存在，会自动创建
		OutputStream out = null;
		try {
			out = new FileOutputStream(f,true);
			String str = "\r\n"+"Java";
			byte []b = str.getBytes();
			out.write(b);
			System.out.println("字节输出流写消息成功...");
		} catch (FileNotFoundException e) {	
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} finally{
			try {
				out.close();
			} catch (IOException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}	
```

字节输入流：（将文件的长度设置为byte数组的长度，一次即可读取全部的内容）

```
		File f = new File("e:\\123.txt");
		InputStream in = null;
		try {
			in = new FileInputStream(f);
			byte []b = new byte[(int)f.length()];
			in.read(b);
			System.out.print("内容为："+new String(b));
		} catch (FileNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} finally{
			try {
				in.close();
			} catch (IOException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
```

将byte的数组设置为指定的长度，每次从中读取指定的长度，直到读到最后一位

```
File f = new File("e:\\123.txt");
		InputStream in = null;
		try {
			in = new FileInputStream(f);
			int len = 0;
			byte []b = new byte[1024];
			int temp = 0;
			//如果文件读到了末尾，read方法会返回一个-1
			while((temp = in.read())!=-1){
				b[len] = (byte)temp;
				len++;
			}
			System.out.println("内容为："+new String(b, 0, len));
		} catch (FileNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}finally{
			try {
				
				in.close();
			} catch (IOException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
```

字符输入流：

```
File f = new File("e:\\123.txt");
		Reader in = null;
		try {
			in = new FileReader(f);
			int len = 0;
			int i = 0;
			char c[] = new char[1024*4];
			while((i=in.read())!=-1){
				c[len] = (char)i;
				len++;
			}
			System.out.println("内容为："+new String(c,0,len));
```

字符输出流：

```
File f = new File("e:\\123.txt");
		Writer out = null;
		try {
			out = new FileWriter(f,true);
			String str = "嘿嘿\r\n";
			out.write(str);
			System.out.println("消息写入成功...");
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}finally{
			try {
				//清空缓冲区
				/*
				 * 字节流和字符流在输出的时候的区别：
				 * 1、字节流在操作时，本身不会用到缓冲区，是直接操作文件
				 * 2、字符流在操作时，使用了缓冲区，通过缓冲区再操作文件，清空缓冲区
				 */
				out.flush();
			} catch (IOException e1) {
				// TODO Auto-generated catch block
				e1.printStackTrace();
			}
			try {
				out.close();
			} catch (IOException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
```

文件复制：（通过字符流复制）

```
File f1 = new File("e:\\123.txt");
		File f2 = new File("f:\\123.txt");
		BufferedReader br = null;
		BufferedWriter bw = null;
		
		try {
			br = new BufferedReader(new FileReader(f1));
			bw = new BufferedWriter(new FileWriter(f2));
			String line = br.readLine();
			while(line!=null){
				bw.write(line+"\r\n");
				line = br.readLine();
				bw.flush();
			}
			System.out.println("文件复制成功...");
```

文件的拷贝（通过字节流）

```
File f1 = new File("e:\\123.txt");
		File f2 = new File("f:\\123.txt");
		FileInputStream in = null;
		FileOutputStream out = null;
		
		try {
			in = new FileInputStream(f1);
			out = new FileOutputStream(f2);
			
			byte []b = new byte[(int)(f1.length())];
			in.read(b);
			out.write(b);
			System.out.println("文件复制成功...");
```









转换流：

字节输出流---》字符输出流

```
//将字节输出流转换成字符输出流：OutputStreamWriter
		File f = new File("e:\\123.txt");
		Writer out = null;
		try {
			out = new OutputStreamWriter(new FileOutputStream(f));
			out.write("hello world");
```

字符输入流-----》字节输入流

```
//字节输入流转换成字符输入流
		File f = new File("e:\\123.txt");
		Reader in = null;
		try {
			in = new InputStreamReader(new FileInputStream(f));
			char []c = new char[(int)f.length()];
			int len = in.read(c);
			System.out.println("内容为："+new String(c,0,len));
```


# Java_Reference
对象引用
<br>
http://www.cnblogs.com/vamei/archive/2013/04/01/2992484.html

我们之前一直在使用“对象”这个概念，但没有探讨对象在内存中的具体存储方式。这方面的讨论将引出“对象引用”(object reference)这一重要概念。 

 

对象引用
我们沿用之前定义的Human类，并有一个Test类:

复制代码
public class Test
{
    public static void main(String[] args)
    {
        Human aPerson = new Human(160);                 
    }
}

class Human
{   
    /**
     * constructor
     */
    public Human(int h)
    {
        this.height = h;
    }

    /**
     * accessor
     */
    public int getHeight()
    {
       return this.height;
    }

    /**
     * mutator
     */
    public void growHeight(int h)
    {
        this.height = this.height + h;
    }

    private int height;
}
复制代码
 

外部可以调用类来创建对象，比如上面在Test类中:

Human aPerson = new Human(160);
创建了一个Human类的对象aPerson。 

上面是一个非常简单的表述，但我们有许多细节需要深入:

首先看等号的右侧。new是在内存中为对象开辟空间。具体来说，new是在内存的堆(heap)上为对象开辟空间。这一空间中，保存有对象的数据和方法。
再看等号的左侧。aPerson指代一个Human对象，被称为对象引用(reference)。实际上，aPerson并不是对象本身，而是类似于一个指向对象的指针。aPerson存在于内存的栈(stack)中。
当我们用等号赋值时，是将右侧new在堆中创建对象的地址赋予给对象引用。
这里的内存，指的是JVM (Java Virtual Machine)虚拟出来的Java进程内存空间。内存的堆和栈概念可参考Linux从程序到进程。

 



对象引用

 

 

栈的读取速度比堆快，但栈上存储的数据受到有效范围的限制。在C语言中，当一次函数调用结束时，相应的栈帧(stack frame)要删除，栈帧上存储的参量和自动变量就消失了。Java的栈也受到同样的限制，当一次方法调用结束，该方法存储在栈上的数据将清空。在 Java中，所有的(普通)对象都储存在堆上。因此，new关键字的完整含义是，在堆上创建对象。

 

基本类型(primitive type)的对象，比如int, double，保存在栈上。当我们声明基本类型时，不需要new。一旦声明，Java将在栈上直接存储基本类型的数据。所以，基本类型的变量名表示的是数据本身，不是引用。

 

 

引用和对象的关系就像风筝和人。我们看天空时(程序里写的)，看到的是风筝(引用)，但风筝下面对应的，是人(对象):



 

引用和对象分离；引用指向对象

 

尽管引用和对象是分离的，但我们所有通往对象的访问必须经过引用这个“大门”，比如以 引用.方法() 的方式访问对象的方法。在Java中，我们不能跳过引用去直接接触对象。再比如，对象a的数据成员如果是一个普通对象b，a的数据成员保存的是指向对象b的引用 (如果是基本类型变量，那么a的数据成员保存的是基本类型变量本身了)。

在Java中，引用起到了指针的作用，但我们不能直接修改指针的值，比如像C语言那样将指针值加1。我们只能通过引用执行对对象的操作。这样的设计避免了许多指针可能引起的错误。

 

引用的赋值
当我们将一个引用赋值给另一个引用时，我们实际上复制的是对象的地址。两个引用将指向同一对象。比如 dummyPerson=aPerson;，将导致:



一个对象可以有多个引用 (一个人可以放多个风筝)。当程序通过某个引用修改对象时，通过其他引用也可以看到该修改。我们可以用以下Test类来测试实际效果:

复制代码
public class Test
{
    public static void main(String[] args)
        {
             Human aPerson = new Human(160);
             Human dummyPerson = aPerson;
             System.out.println(dummyPerson.getHeight());
             aPerson.growHeight(20);
             System.out.println(dummyPerson.getHeight());
        }
}
复制代码
我们对aPerson的修改将影响到dummyPerson。这两个引用实际上指向同一对象。

 

所以，将一个引用赋值给另一个引用，并不能复制对象本身。我们必须寻求其他的机制来复制对象。

 

垃圾回收
随着方法调用的结束，引用和基本类型变量会被清空。由于对象存活于堆，所以对象所占据的内存不会随着方法调用的结束而清空。进程空间可能很快被不断创建的对象占满。Java内建有垃圾回收(garbage collection)机制，用于清空不再使用的对象，以回收内存空间。

垃圾回收的基本原则是，当存在引用指向某个对象时，那么该对象不会被回收; 当没有任何引用指向某个对象时，该对象被清空。它所占据的空间被回收。

上图假设了某个时刻JVM中的内存状态。Human Object有三个引用: 来自栈的aPerson和dummyPerson，以及另一个对象的数据成员president。而Club Object没有引用。如果这个时候垃圾回收启动，那么Club Object将被清空，而Human Object来自Club Object的引用(president)也随之被删除。

 

垃圾回收是Java中重要的机制，它直接影响了Java的运行效率。我将在以后深入其细节。

 

参数传递
当我们分离了引用和对象的概念后，Java方法的参数传递机制实际上非常清晰: Java的参数传递为值传递。也就是说，当我们传递一个参数时，方法将获得该参数的一个拷贝。

实际上，我们传递的参数，一个是基本类型的变量，另一个为对象的引用。

基本类型变量的值传递，意味着变量本身被复制，并传递给Java方法。Java方法对变量的修改不会影响到原变量。

引用的值传递，意味着对象的地址被复制，并传递给Java方法。Java方法根据该引用的访问将会影响对象。

 

在这里有另一个值得一提的情况: 我们在方法内部使用new创建对象，并将该对象的引用返回。如果该返回被一个引用接收，由于对象的引用不为0，对象依然存在，不会被垃圾回收。

 

总结
new

引用，对象

被垃圾回收的条件

参数: 值传递

 

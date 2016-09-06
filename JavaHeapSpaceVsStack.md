# Java Heap Space vs Stack – Распределение памяти в Java
 
## Java Heap Space
Java Heap используется в Java Runtime для выделения памяти для объектов и классов JRE. Всякий раз, когда мы создаем какой-либо объект, он всегда создается в динамической памяти пространства. Сбор мусора выполняется на динамической памяти, чтобы освободить память, используемую объектами, которые не имеют каких-либо ссылок. Любой объект, созданный в простанстве хипа имеет глобальный доступ и можно ссылаться из любого места приложения.
heap - куча


## Java Stack Memory
Stack  используется для выполнения потоков. Они содержат конкретные значения методов, которые недолговечны и ссылки на другие объекты в куче(хипе), которые получают из указанных методов. Стек всегда ссылааеться в LIFO (Last-In-First-Out) порядка. Всякий раз, когда вызывается метод, новый блок создается в памяти стека для метода где храняться  значения локальных примитивов и ссылки на другие объекты, в методе. Как только метод заканчивается, блок становится неиспользованной и становятся доступными для следующего метода.
Размер памяти стека намного меньше по сравнению с динамической памятью.

```java
  public class Memory {
    public static void main(String[] args) { // Line 1
		int i=1; // Line 2
		Object obj = new Object(); // Line 3
		Memory mem = new Memory(); // Line 4
		mem.foo(obj); // Line 5
	} // Line 9

	private void foo(Object param) { // Line 6
		String str = param.toString(); //// Line 7
		System.out.println(str);
	} // Line 8
}
```

Below image shows the Stack and Heap memory with reference to above program and how they are being used to store primitive, Objects and reference variables.

![JavaRuntimeMemory](https://github.com/Oleiva/Java-Interview-Questions/blob/EN/images/JavaHeapSpaceVsStack/JavaRuntimeMemory.png)
 
Let’s go through the steps of execution of the program.

*  As soon as we run the program, it loads all the Runtime classes into the Heap space. When main() method is found at line 1, Java Runtime creates stack memory to be used by main() method thread.
*	We are creating primitive local variable at line 2, so it’s created and stored in the stack memory of main() method.
*	Since we are creating an Object in line 3, it’s created in Heap memory and stack memory contains the reference for it. Similar process occurs when we create Memory object in line 4.
*	Now when we call foo() method in line 5, a block in the top of the stack is created to be used by foo() method. Since Java is pass by value, a new reference to Object is created in the foo() stack block in line 6.
*	A string is created in line 7, it goes in the String Pool in the heap space and a reference is created in the foo() stack space for it.
*	foo() method is terminated in line 8, at this time memory block allocated for foo() in stack becomes free.
*	In line 9, main() method terminates and the stack memory created for main() method is destroyed. Also the program ends at this line, hence Java Runtime frees all the memory and end the execution of the program.

## Difference between Java Heap Space and Stack Memory
Based on the above explanations, we can easily conclude following differences between Heap and Stack memory.

1.  Heap память используется всеми частями приложения, тогда как стек памяти используется только одним потоком исполнения.

2.  Всякий раз, когда создается объект, он всегда хранится в пространстве хипа и стек содержит ссылку на него. Стек памяти содержит только локальные переменные и примитивные ссылочные переменные на объекты в хипе.

3.  Объекты, хранящиеся в хипе доступны везде в то время как стек не может получить доступ к другим потокам.

4.	Memory management in stack is done in LIFO manner whereas it’s more complex in Heap memory because it’s used globally. Heap memory is divided into Young-Generation, Old-Generation etc, more details at Java Garbage Collection.

5.	Stack memory is short-lived whereas heap memory lives from the start till the end of application execution.

6.	We can use -Xms and -Xmx JVM option to define the startup size and maximum size of heap memory. We can use -Xss to define the stack memory size.

7.	When stack memory is full, Java runtime throws java.lang.StackOverFlowError whereas if heap memory is full, it throws java.lang.OutOfMemoryError: Java Heap Space error.

8.	Stack memory size is very less when compared to Heap memory. Because of simplicity in memory allocation (LIFO), stack memory is very fast when compared to heap memory.
	
Я сделал видео-учебник для Java пространство кучи и стека памяти, вы должны смотреть, чтобы уточнить какие-либо сомнения.
https://www.youtube.com/watch?v=_y7k_0edvuY

# Lambda表达式

## 格式

*左边：lambda形参列表（接口中的抽象方法的形参列表）；右边：lambda体（重写的抽象方法的方法体）*

lambda表达式的本质就是接口的实例！

1. 无参、无返回

```java
Runnable r = () -> System.out.println("hello world");
r.run();
```

2. 有参、无返回

```java
Consumer<String> consumer = (String s) -> {
    System.out.println("接收的参数为：" + s);
};
consumer.accept("123");
```

3. 省略数据类型

```java
Consumer<String> consumer1 = (s) -> {
    System.out.println("接收的参数为：" + s);
};
consumer1.accept("你好");
```

4. 省略括号

```java
Consumer<String> consumer2 = s -> {
    System.out.println("接收的参数为：" + s);
};
consumer2.accept("hello");
```

5. 多参、多执行语句、有返回

```java
Comparator<Integer> comparator = (i, j) -> {
    System.out.println(i);
    System.out.println(j);
    return i.compareTo(j);
};
System.out.println(comparator.compare(1, 2));
```

6. 只有一条语句

```java
Comparator<Integer> comparator1 = (i, j) -> i.compareTo(j);
System.out.println(comparator1.compare(2, 3));
```

### 总结

1. 形参类型可以省略
2. 单个参数括号可以省略
3. 只有一条执行语句可以省略大括号及return关键字（同时）
4. 依赖函数式接口

## 函数式接口

一句话：接口只有一个抽象方法的接口

*因为只有一个抽象方法，所以重写的时候没有歧义，本质就是该接口的实例*

**@FunctionalInterface**：通过该注解，可以标识这个类是函数是接口（没有标注不代表不是），编译时会校验该接口是否只有一个抽象方法。

### 内置4大函数式接口

|   函数式接口   | 参数类型 | 返回类型 |                            用途                            |
| :------------: | :------: | :------: | :--------------------------------------------------------: |
|  Consumer\<T>  |    T     |   void   |        对类型为T的对象的应用操作，void accept(T t)         |
|  Supplier\<T>  |    /     |    T     |                 返回类型为T的对象，T get()                 |
| Function<T, R> |    T     |    R     |      对类型为T的对象操作，返回R类型对象，R apply(T t)      |
| Predicate\<T>  |    T     | boolean  | 类型为T的对象是否满足条件，返回布尔类型，boolean test(T t) |

#### Consumer\<T>

```java
shopping(400, money -> System.out.println("购物消费了 " + money + " 元"));
```


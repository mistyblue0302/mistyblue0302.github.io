---
layout: single
title : "**추상클래스와 인터페이스는 어떻게 구분하여 써야할까?**"
excerpt : ""
---

<br>


**추상클래스**

- 추상클래스는 abstract로 선언된 메소드가 하나라도 있을 때 선언하며, 인터페이스와 달리 구현되어 있는 메소드가 있어도 상관 없다.
- static이나 final메소드 선언이 가능하며, 다중상속은 불가능하다.

```java
public abstract class 클래스이름{
    //필드 선언
    //추상메소드
    public abstract void a();
    //일반메소드
    public void b(){

    }
}
```

**언제 사용할까?**

- 여러 하위 클래스의 공통 기능을 캡슐화할 때 사용
- public이외의 접근지정자를 사용하고 싶을 때(protected 또는 private)
- non-static 이나 non-final 필드를 선언하고 싶을 때

**Example**

```java
public class Rectangle{
    public void a(){};
}

public static void main(String[] args){
    Rectangle rec = new Rectangle():
    rec.a();
}
```

main 클래스에서 다음과 같이 객체를 생성했다. 객체는 생성 이후 Rectangle 클래스에 선언된 모든 메소드를 사용할 수 있게 된다. 하지만 이것은 전적으로 Rectangle 객체에 의존하는 코드이다. 만약, 이것과 유사한 다른 클래스를 구현해야 한다면 다음과 같이 만들 수 있다.

```java
public abstract class Shape{
    abstract void draw();
    abstract double area();
}

public class Rectangle extends Shape{
    private double width;
    private double height;

    public void Rectangle(double width, double height){
        this.width = width;
        this.height = height;
    }

    @Override
    public void draw(){
        System.out.println("Draw");
    }

    @Override
    public double area(){
        return this.width * this.height;
    }
}

public class Main{
    public static void main(String[] args){
        Shape shape = new Rectangle(5,3);
        shape.draw(); //Shape 클래스에 의존적임
        System.out.println("area :"+shape.area());
    }
}
```

위의 코드는 동일하게 Rectangle 객체를 생성했지만 상위 타입인 shape로 객체 참조가 된다. 따라서, 메소드 호출 시 Shape 클래스의 메소드만 사용이 가능해진다. Rectangle과 유사한 다른 클래스를 사용하고 싶을 때에는 객체를 생성하도록 변경해주기만 하면 이후의 코드들은 전혀 수정될 필요가 없다.

<br>

**인터페이스**

- 모든 멤버 변수는 public static final 이어야 하며, 생략이 가능하다.
- 모든 메소드는 public abstract 이어야 하며, 생략이 가능하다.
- 다중 상속이 가능하다.

```java
public interface 인터페이스이름{
    //상수 필드
    //메소드
}
```

**언제 사용할까?**

- 관련 없는 클래스들이 인터페이스를 구현할 때
- 특정 데이터 타입의 행동을 명시하고싶은데, 어디서 구현되는지 알 필요 없는 경우
- 다중상속을 활용할 때

**Example**

```java
public class A {
    void auto(I i){
        i.method();
    }
}

public interface I {
    public abstract void method();
}

public class B implements I{
    @Override
    public void method() {
        System.out.println("method in B class");
    }
}

public class C implements I{
    @Override
    public void method() {
        System.out.println("method in C class");
    }
}

public class Main {
    public static void main(String[] args){
        A a = new A();
        a.auto(new B()); //method in B class
        a.auto(new C()); //method in C class
    }
}
```

클래스 A는 클래스 B와 C의 메소드를 호출한다. 하지만 인터페이스를 통해 간접적인 관계를 맺음으로써 서로에게 영향을 주지 않는다.
하나의 클래스가 변경되더라도 다른 클래스에 영향을 미치지 않고 독립적으로 프로그래밍이 가능하다.

<br>

**JDK 8의 인터페이스에 추가된 기능**

- default method 

   JDK 8 이전엔 인터페이스에서 구현을 정의할 수 없었다. 그래서 새로운 메소드를 추가하려면 인터페이스를 구현하는 모든 클래스들이 해당 메소드를 구현해야했다.
   하지만 default method로 추가가 가능해지면서 기존 인터페이스를 구현했던 클래스에서 메소드를 override하지 않아도 된다.
   기존 코드에 영향을 주지 않고 새로운 메소드를 가질 수 있도록 이전 인터페이스에 대한 호환성을 제공한다.

```java
public interface Calculator{
    final int first = 10; //상수 필드
    public int plus(int x, int y); //메소드
    public int minus(int x, int y);
    default int multiply(int x, int y){
        return x * y;
    }
}

public class A implements Calculator{
    @Override
    public int plus(int x, int y){
        return x+y;
    }

    @Override
    public int minus(int x, int y){
        return x-y;
    }
}

public class Main{
    public static void main(String[] args){
        Calculator calc = new A();
        int value = calc.multiply(2,3);
        System.out.println(value); //6
    }
}
```
<br>

- static method 

   객체 생성 여부와 상관없이 사용이 가능하며, override가 불가능하고 상속되지 않는다.
   상수를 선언할 때는 static block에서 초기화할 수 없고, 선언과 동시에 초기화해야한다.
   간단한 기능을 가지는 유틸리티성 인터페이스를 만들 때 사용할 수 있다.

```java
public interface Calculator{
    final int first = 10; //상수 필드
    public int plus(int x, int y); //메소드
    public int minus(int x, int y);
    static int multiply(int x, int y){
        return x * y;
    }
}

public class A implements Calculator{
    @Override
    public int plus(int x, int y){
        return x+y;
    }

    @Override
    public int minus(int x, int y){
        return x-y;
    }
}

public class Main{
    public static void main(String[] args){
        int value = Calculator.multiply(5, 3);
        System.out.println((value));
    }
}
```
<br>


**정리**

추상클래스와 인터페이스는 추상메소드를 사용하고, 객체 생성이 불가능하다.

**추상클래스**는 추상메소드를 포함하는 것을 제외하면 일반 클래스와 다르지 않다. 추상클래스를 상속 받음으로써 자식 클래스들 간의 공통 기능을 구현하고 확장시킨다. 추상클래스는 is ~A(~이다) 이고, 여러 클래스들의 공통점을 찾아 추상화 시켜서 사용하는 것이 개발에서 이득일 때 사용한다.

**인터페이스**는 추상클래스보다 추상화 정도가 강해 몸통이 있는 일반 메소드나 멤버변수를 가질 수 없다. 추상 메소드를 정의해놓고 구현하는 클래스에서 각 기능들을 override하여 여러가지 형태로 구현할 수 있다. 상속을 받아 기능을 확장시키는 것이 목적인 추상클래스와는 달리, 구현 객체가 같은 동작을 한다는 것을 보장하기 위한 목적으로 사용한다.

<br>

참고

[https://docs.oracle.com/javase/tutorial/java/IandI/createinterface.html](https://docs.oracle.com/javase/tutorial/java/IandI/createinterface.html)

[https://docs.oracle.com/javase/tutorial/java/IandI/abstract.html](https://docs.oracle.com/javase/tutorial/java/IandI/abstract.html)

[https://www.geeksforgeeks.org/difference-between-abstract-class-and-interface-in-java/](https://www.geeksforgeeks.org/difference-between-abstract-class-and-interface-in-java/)

[https://www.journaldev.com/1607/difference-between-abstract-class-and-interface-in-java](https://www.journaldev.com/1607/difference-between-abstract-class-and-interface-in-java)


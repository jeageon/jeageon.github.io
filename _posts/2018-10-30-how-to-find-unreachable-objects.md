---
title: Java Object가 살아있는지 죽어있는지를 관리하는 방법
updated: 2018-10-30 14:19
---

> **NOTE**: 이 게시물은 다음 [**Java Article**](http://www.javarticles.com/2016/09/java-garbage-collector-reachable-and-unreachable-objects.html)을 번역하여 정리한 글입니다.

## Java Garbage Collector Reachable and Unreachable Objects

### Root Set of References

Garbage detection은 프로그램에서 항상 접근 가능한 참조변수들(roots)의 집합을 정의하여 수행한다. 이러한 root들로 부터 참조될수있는 방법이 존재한다면(참조 변수들을 따라) 그 object는 reachable하다.(살아 있다)  즉 root가 object를 참조하고 해당 object는 또다른 object를 참조하는 변수를 가지고 있다면 이렇게 연결된 모든 object들은 접근가능한, 즉 살아있는 객체이다. Garbage collector는 collecting을 하기전에 이렇게 root들로 부터 순회를 한번 하여 살아있는 object들을 감지하고, 남은 object들은 자연히 접근 불가능한, 죽은 객체가 된다. Garbage collector는 죽은 객체에 finalized method가 존재한다면 해당 heap memory를 free시키기 전에 반드시 finalize를 실행 시켜야 한다.



### Reachable and Unreachable Objects

![Object_Tree](http://www.javarticles.com/wp-content/uploads/2016/09/GarbageCollectionObjTree.png "Object Tree")

아래의 예제와 위 이미지를 참조하여 예시를 들어보자. 

object A, B, C, F는 main program에서 정의되어 root들로 부터 바로 참조 될 수 있었으나  C와 F를 참조하던 root들은 nullifying되어서 더이상 참조가 불가능하게 되었다. 각각 A는 객체 내에서 B를 참조하고 B는 객체 내에서 E를 참조하여 E는 실제로 직접적으로 참조해주는 root는 존재하지 않지만 살아있음을 알 수 있다. 반면 D의 경우 C에서 정의된 참조변수에 의해 참조되었으나 C가 더이상 root로부터 참조되지 못하므로 같이 죽은 객체가 되었다. F는 객체내부에서 G를 참조하고 G는 내부에서 H를 참조하며 H는 내부에서 F를 참조한다. 이렇게 참조의 사이클이 일어나도 F가 nullifying되는 순간 root로 부터 접근 할 방법이 없어지므로 모두 죽은 객체로 취급된다.

*GarbageCollectionExample:*

```java
package com.javarticles.references;
 
 
public class GarbageCollectionExample {
    public static void main(String[] args) {
        B b = new B();
        A a = new A(b);
        C c = new C(b);
        c = null;
        F f = new F();
        f = null;
         
        System.out.println("Keep creating objects in heap, this should throw OutOfMemoryError");
        boolean gcRun = false;
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i <10000000; i++) {
            try {
                sb.append("one").append(sb.toString());
            } catch (java.lang.OutOfMemoryError e) {
                System.out.println("This should run the gc");
                gcRun = true;
                break;
            }
        }
        if (!gcRun) {
            System.out.println("In case Gc didnt run, run now");
            Runtime.getRuntime().gc();
        }
    }
}
```

*A:*

```java
package com.javarticles.gc;
 
public class A {
    private B b;
     
    public A(B b) {
        this.b = b;
    }
     
    @Override
    public void finalize() {
        System.out.println("A cleaned");
    }
}
```

*B:*

```java
package com.javarticles.gc;
 
public class B {
    private E e = new E();
 
    @Override
    public void finalize() {
        System.out.println("B cleaned");
    }
}
```

*C:*

```java
package com.javarticles.gc;
 
public class C {
    private B b;
    private D d = new D();
 
    public C(B b) {
        this.b = b;
    }
 
    @Override
    public void finalize() {
        System.out.println("C cleaned");
    }
}
```

*D:*

```java
package com.javarticles.gc;
 
public class D {
    @Override
    public void finalize() {
        System.out.println("D cleaned");
    }
}
```

*F:*

```java
package com.javarticles.gc;
 
public class F {
    private G g = new G(this);
    @Override
    public void finalize() {
        System.out.println("F cleaned");
    }
}
```

*G:*

```java
package com.javarticles.gc;
 
public class G {
    private H h;
    public G(F f) {
        h = new H(f);
    }
    @Override
    public void finalize() {
        System.out.println("G cleaned");
    }
}
```

*H:*

```java
package com.javarticles.gc;
 
public class H {
    private F f;
 
    public H(F f) {
        this.f = f;
    }
    @Override
    public void finalize() {
        System.out.println("H cleaned");
    }
}
```

*Output:*

```
Keep creating objects in heap, this should throw OutOfMemoryError
H cleaned
G cleaned
C cleaned
F cleaned
D cleaned
This should run the gc
```


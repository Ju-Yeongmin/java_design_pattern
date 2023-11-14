## Part6. Prototype - 복사해서 인스턴스를 만든다

### Prototype 패턴

<br>

Someting이라는 클래스으 인스턴스를 만드려면? <br>
Java에서는 new 키워드를 이용한다 <br>
`new Something()`

이처럼 new를 사용해 인스턴스를 만든다면 클래스 이름을 반드시 지정해야 한다 <br>
클래스 이름을 지정하지 않고 인스턴스를 만들고 싶을땐 어떻게 해야할까? <br>

<br>

#### 아래의 경우는 클래스로부터 인스턴스를 만드는 대신 복사해서 새 인스턴스를 만든다
1. <b>종류가 너무 많아 클래스가 정리할 수 없는 경우</b>
   - 취급해야 하는 오브젝트의 종류가 너무 많아서 하나한 다른 클래스로 만들면 많은 소스 파일을 만들어야 한다
2. <b>클래스로부터 인스턴스 생성이 어려운경우</b>
   - 생성하고 싶은 인스턴스가 복잡한 과정을 거쳐 만들어진다면 클래스로 만들기 매우 어렵다
   - 이런 경우 사용자의 조작으로 만들어진 인스턴스와 같은 것을 만들고 싶다면 인스턴스를 저장해두고 만들고 싶을때 복사한다
3. <b>프레임워크와 생성하는 인스턴스를 분리하고 싶은 경우</b>
   -  인스턴스를 생성하는 프레임워크에 의존하지 않게 하고싶은 경우
   -  클래스의 이름을 지정해서 인스턴스를 만드는게 아닌 원형이 될 인스턴스를 미리 등록해두고 복사해서 인스턴스를 생성한다
  

<br>

new 생성자를 통해 생성하지 않고, 인스턴스로부터 다른 인스턴스를 생성하는 것은 복사기로 문서를 복사하는 것과 비슷하다 <br>
(원본 서류가 어떻게 만들어졌는지는 중요하지 않고 복사기에 넣으면 같은 서류를 몇 장이든 만들 수 있다)

<br>

#### Prototype 패턴은 클래스에서 인스턴스를 생성하는 대신 인스턴스로부터 다른 인스턴스를 생성하는 패턴
- prototype은 원형이나 모범 이라는 뜻
- 원형이 되는 인스턴스를 바탕으로 새로운 인스턴스를 만든다

<br>

이번 장에서 등장하는 클래스와 인터페이스의 목록
![image](https://github.com/Ju-Yeongmin/java_design_pattern/assets/110506500/a0053b22-db53-4673-91e8-8459940aff1e)
- Product 인터페이스와 Manager 클래스는 framework 패키지에 속하며 인스턴스를 복제
- Manager 클래스는 createCopy를 호출하지만 구체적으로 어느 클래스의 인스턴스를 복제할지 관여하지 않음
- Product 인터페이스를 구현한 클래스이기만 하면 해당 인스턴스의 복제가 가능
- MessageBox, UnderlinePen 클래스는 모두 Product를 구현한 클래스

<br>

### Product 인터페이스
java.lang.Cloneable 인터페이스를 상속한 Product 인터페이스는 복제를 가능하게 한다 <br>
Cloneable 인터페이스를 구현하느 클래스의 인스턴스는 clone 메섣르르 사용해 자동으로 복제할 수 있다

```java
package framework;

public interface Product extends Cloneable {
  public abstract void use(String s); // 사용 하기 위한 것
  public abstract Product createCopy(); // 인스턴스를 복제하기 위한 메서
}
```

<Br>

### Manager 클래스
Manager 클래스는 Product를 이용해 인스턴스를 복제하는 클래스 <br>

```java
package framework;

import java.util.HashMap;
import java.util.Map;

public class Manager {
  private Map<String, Product> showcase = new HashMap<>();
  // showcase 필드에 이름과 인스턴스의 쌍을 등록해두면 지정한 이름에 대응하는 인스턴스를 get할 수 있다

  // 여기서 변수로 주는 Product 타입의 prototype은 그 실제 클래스는 모르지만 어쨌든 Product 인터페이스를 구현한 클래스의 인스턴스
  public void register(String name, Product prototype) {
    showcase.put(name, prototype)
  }

  // 이 Manager 클래스에는 MessageBox나 UnderlinePen 클래스의 이름이 전혀 나오지 않는 것에 주목하자
  // 클래스 이름이 나오지 않는다는 것은 Product와 Manager는 그들과 독립적으로 수정할 수 있다는 것을 의미
  // ** 소스안에 클래스 이름을 기술하면 그 클래스와 밀접한 관계가 생긴다
  // Manager 클래스에서는 구체적인 개별 클래스의 이름을 사용하는 대신 Product라는 인터페이스 이름만 사용한다
  // (다른 클래스와 연결하는 다리 역할)

  public Product create(String prototypeName) {
    Product p = showcase.get(prototypeName);
    return p.createCopy();
  }
}
```

<br>

### MessageBox 클래스
MessageBox는 Product 인터페이스를 구현한 클래스

```java
import framework.Product;

public class MessageBox implements Product {
  private char decochar; // 문자열을 에워 싸는 문자

  // 예를들어 decochar가 * 라면
  //  ********
  //  * 영민 *
  //  ********

  public MessageBox(char decochar) {
    this.decochar = decochar
  }

  @Override
  public void use(String s) {
    int decolen = 1 + s.length() + 1;
    for (int i = 0; i < decolen; i++) {
      System.out.print(decochar);
    }
    System.out.println();
    System.out.println(decochar + s + decochar);
    for (int i = 0; i < decolen; i++) {
      System.out.print(decochar)
    }
    System.out.println();
  }

  // createCopy는 자신을 복제하는 메서드
  // 여기서 호출하는 clone 메서드는 자기 자신의 복제를 생성하는 메서드 (인스턴스가 가진 필드값도 그대로 복사)
  // clone 메서드로 복사할 수 있는 것은 java.clone.Cloneable 인터페이스를 구현한 클래스만
  // 이 인터페이스를 구현하지 않았을 때는 에러가 발생하므로 예외처리를 해야한다


  @Override
  public Product createCopy() {
    Product p = null;
    try {
      p = (Product)clone();
    } catch (CloneNotSupportedException e) {
      e.printStactTrace();
    }
    return p;
  }
}
```

<br>

### UnderlinePen 클래스
MessageBox와 거의 같은 동작을 하는 클래스

```java
import fromawork.Product;

public class UnderlinePen implements Product {
  private char ulchar; // ulchar라는 필드가 밑줄로 사용

  // 예를들어 ulchar가 '-' 라면
  //  영민
  //  ----

  public UnderlinePen(char ulchar) {
    this.ulchar = ulchar
  }

  @Override
  public void use(String s) {
    int ulen = s.length();
    System.out.println(s);
    for (int i = 0; i < ulen; i++) {
      System.out.print(ulchar)
    }
    System.out.println();
  }

  @Override
  public Product createCopy() {
    Product p = null;
    try {
      p = (Product)clone();
    } catch (CloneNotSupportedException e) {
      e.printStactTrace();
    }
    return p;
  }
}
```

<br>

### Main 클래스

```java
import framework.Manager;
import framework.Product;

public class Main {
  public static void main(String[] args) {
    // 준비
    Manager manager = new Manager();
    UnderlinePen upen = new UnderlinePen('-');
    MessageBox mbox = new MessageBox('*');
    MessageBox sbox = new MessageBox('/');

    // 등록
    manager.register("strong message", upen);
    manager.register("warning box", mbox);
    manager.register("slash box", sbox);

    // 생성과 사용
    Product p1 = manager.create("string message");
    p1.use("Hello, world");

    Product p2 = manager.create("warning box");
    p2.use("Hello, world");

    Product p3 = manager.craete("slash box");
    p3.use("Hello, world)";
  }
}
```

<br>

### 실행결과
![image](https://github.com/Ju-Yeongmin/java_design_pattern/assets/110506500/0a319c37-feb5-4194-a9ef-66f3ee1c8171)

<br>
<br>

## 사고력을 넓혀주는 힌트

### 1. 클래스에서 인스턴스를 만들면 되는데 Prototype을 왜써?
인스턴스를 만들고 싶으면 `new Something()` 이라고 하면 되는데 왜 어렵게 Prototpye을 사용할까 <br>
시작할때 설명한 3가지의 이유

- 종류가 너무 많아 클래스르 정리할 수 없을 경우
- 클래스로부터 인스턴스 생성이 어려운 경우
- 프레임워크와 생성하는 인스턴스를 분리하고 싶은 경우

<br>

### 2. 소스코드 안에 클래스의 이름이 들어간게 왜 안좋을까?
객체지향 프로그래밍의 목표중 하나는 "부품으로서의 재사용"

<br>

소스코드 안에 이용할 클래스의 이름이 쓰여 있으면, 그 클래스와 분리해서 재사용할 수가 없다 <br>
물론 소스코드를 수정해서 클래스 이름을 변경할 수 있지만 "부품으로서의 재사용"에 해당하지 않음

<br>

클래스 파일(.class)만 있어도 재사용할 수 있는지가 중요하다 <br>
다시말해 <b> 소스 파일(.java)이 없이도 재사용할 수 있는가?가 포인트 </b>

<br>

밀접하게 결합해야 하는 클래스 이름이 소스 안에서 사용되는 것은 당연히 문제가 없지만, <br>
부품으로 독립시켜야 하는 클래스 이름이 소스 안에서 사용된다면 문제가된다

<br>

### clone 메서드는 얕은 복사를 한다
clone 메서드의 동작은 필드 내용을 그대로 복사하는 것 <br>
즉, 필드가 가리키는 인스턴스의 내용은 고려하지 않는다

<br>

예를들어 필드에 배열이 있고, clone 메서드로 복사한다면 배열에 대한 참조만 복사되고 요소가 복사되진 않는다 <br>
이러한 필드 대 필드의 복사를 얕은 복사라고 부른다

<br>

얕은 복사만으로 충분치 않다면 clone 메서드를 오버라이드 하여 필요로하는 복사를 재정의하여 사용할 수 있다

### clone은 사용하기 어렵다
clone 메서드는 상속관계가 없는 클래스의 clone 메서드를 호출하기 어렵다 <br>
실제 java 프로그램으로 인스턴스를 복제하는 클래스를 설계한다면 clone에 의존하지 않고 <br>
복사 생성자나 복사 팩토리를 사용하는 편이 좋다














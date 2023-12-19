# part 6 : Prototype - 복사해서 인스턴스를 만든다

## Prototype 패턴이란?
- `new`라는 생성자를 이용해 클래스의 인스턴스를 만들며, 이때 클래스 이름을 지정해야함
- 하지만, 클래스 이름을 지정하지 않고 인스턴스를 생성하고 싶을 때, 아래와 같은 경우에서는 클래스로부터 인스턴스를 만드는 대신, 인스턴스를 복사해서 새 인스턴스를 만든다

#### (1) 종류가 너무 많아 클래스로 정리할 수 없는 경우
- 하나하나 다른 클래스로 만들면 소스 파일을 많이 작성해야 하는 경우

#### (2) 클래스로부터 인스턴스 생성이 어려운 경우
- 생성하고 싶은 인스턴스가 복잡한 과정을 거쳐 만들어지는 것으로, 클래스로부터 만들기가 매우 어려운 경우
- 그래픽 에디터 등

#### (3) 프레임워크와 생성하는 인스턴스를 분리하고 싶은 경우
- 인스턴스를 생성하는 프레임워크를 특정 클래스에 의존하지 않게 하고 싶은 경우
- 미리 '원형'이 될 인스턴스를 등록해두고, 등록된 인스턴스를 복사해서 인스턴스를 생성

#### Java에서는 복제하는 조작을 `clone`이라고 부른다. 이 장에서는 `clone`메소드와 `Cloneable`인터페이스 사용법을 학습한다


## 예제 프로그램
|패키지| 이름      |설명|
|----|---------|---|
|framework| Product |추상메소드 use와 createCopy가 선언되어 있는 인터페이스|
|framework| Manager |createCopy를 사용하여 인스턴스를 복제하는 클래스|
|이름 없음|MessageBox|문자열을 테두리로 감싸서 표시하는 클래스로 use와 createCopy를 구현|
|이름 없음|UnderlinePen|문자열에 밑줄을 그어 표시하는 클래스로 use와 createCopy를 구현|
|이름 없음|Main|동작 테스트용 클래스|

### Product 인터페이스

```java
package framework;

public interface Product extends Cloneable{
    public abstract void use(String s);
    public abstract Product createCopy();
}
```

- `java.lang.Cloneable` 인터페이스를 상속한 `Product` 인터페이스는 복제를 가능하게 함

### Manger 클래스
```java
package framework;

import java.util.*;

public class Manger{
    private Map<String,Product> showcase = new HashMap<>();
    
    public void register(String name, Product prototype){
        showcase.put(name, prototype);
    }
    
    public Product create(String prototyppeName){
        Product p = showcase.get(prototyppeName);
        return p.createCopy();
    }
}
```

- `showcase` 필드에 이름과 인스턴스의 쌍을 등록하여 지정한 이름에 대응하는 인스턴스를 취득할 수 있도록 해 둠
- 제품 이름과 `Product` 인터페이슬를 주면, `register` 메소드에서 해당 쌍을 `showcase`에 등록
- `Product` 인터페이스나 `Manager` 클래스이 소스 코드에 `MessageBox` 클래스나 `UnderLinePen` 클래스의 이름이 전혀 등장하지 않음
  - 즉, `Product`와 `Manager`는 그 클래스들과는 독립적으로 수정할 수 있다는 의미
  - 소스 코드 안에 클래스 이름을 기술하면 그 클래스와 밀접한 관계가 생기기 때문

### MessageBox 클래스

```java
import framework.Product;

public class MessageBox implements Product{
    private char decochar;
    
    public MessageBox(char decochar){
        this.decochar = decochar;
    }
    
    @Override
    public void use(String s){
        int decolen = 1 + s.length() + 1;
        for(int i = 0; i < decolen; i++){
            System.out.println(decochar);
        }
        System.out.println();
        System.out.println(decochar + s + decochar);
        for(int i = 0; i < decolen; i++){
            System.out.println(decochar);
        }
        System.out.println();
    }
    
    @Override
    public Product createCopy(){
        Product  p = null;
        try{
            p = (Product) clone();
        } catch (CloneNotSupportedException e){
            e.printStackTrace();
        }
        return p;
    }
}
```
- `createCopy` 는 자기 자신을 복제하는 메소드
  - `clone` 메소드로 복사할 수 있는 것으ㅡㄴ `java.lang.cloneable` 인터페이스를 구현한 클래스 뿐이다
- Java 언어의 `clone` 메소드는 자신의 클래스에서만 호출할 수 있으므로, 다른 클래스의 요청으로 복제할 경우에는 `createeCopy`와 같은 별도의 메소드로 `clone`을 감싸줘야 함


### UnderlinePen 클래스
- `MessageBox`와 거의 같은 동작
```java
import framework.Product;

public class UnderlinePen implements Product{
    private char ulchar;
    
    public UnderlinePen(char ulchar){
        this.ulchar = ulchar;
    }
    
    @Override
    public void use(String s){
        int ulen = s.length();
        System.out.println(s);
        for(int i = 0; i < ulen; i++){
            System.out.println(ulchar);
        }
        System.out.println();
    }
    
    @Override
    public Product createCopy(){
        Product p = null;
        try{
            p = (Product) clone();
        } catch (CloneNotSupportedException e){
            e.printStackTrace();
        }
        return p;
    }
}
```

### Main 클래스
```java
import framework.Manger;
import framework.Product;

public class Main{
    public static void main(String[] args){
        //준비
        Manager manager = new Manager();
        UnderlinePen upen = new UnderlinePen('-');
        MessageBox mbox = new MessageBox('*');
        MessageBox sbox = new MesssageBox('/');
        
        //등록
        manager.register("strong message", upen);
        manager.register("warning box", mbox);
        manager.register("slash box", sbox);
        
        //생성과 사용
        Product p1 = manager.create("strong message");
        p1.use("Hello, world.");
        Product p2 = manager.create("waring box");
        p2.use("Hello, world.");
        Product p3 = manager.create("slash box");
        p1.use("Hello, world.");
    }
}
```

## Prototype의 등장인물
#### Prototype
- 인스턴스를 복사하여 새로운 인스턴스를 만들기 위한 메소드를 결정한다.
- `Product` 인터페이스

#### ConcretePrototype
- 인스턴스를 복사하여 새로운 인스턴스를 만드는 메소드를 구현한다.
- `MessageBox` 클래스와 `UnderlinePen` 클래스

#### Client
- 인스턴스를 복사하는 메소드를 이용해 새로운 인스턴스를 만든다
- `Manager` 클래스

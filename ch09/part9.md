# part 9 : Bridge 패턴 - 기능 계층과 구현 계층을 나눈다

## Bridge 패턴이란?
- `기능의 클래스 계층`과 `구현의 클래스 계층`을 `다리`처럼 연결하는 역할
- 브리지 패턴은 기존 시스템에 부수적인 새로운 기능들을 지속적으로 추가할 때 사용하면 유용
  - 추상화와 구현을 분리하므로, **추상화된 부분이나 구현부분의 변화가 서로 영향을 미치지 않고 독립적으로 확장될 수 있어 시스템이 더 유연하게 대응**
  - 또한, 추상화와 구현의 분리르 통해 **새로운 구현을 추가할 때 기존의 추상화 코드를 재사용**
  - 새로운 구현을 추가하거나 변경할 때 **기존 코드에 최소한의 변화**만 필요


### 클래스 계층의 두 가지 역할
#### 새로운 `기능`을 추가하고 싶다
- `Something`이라는 클래스에 새로운 `기능`을 추가하고 싶을 때(즉, 구체적으로 새로운 메소드를 추가하고자 할때)
- `Something`의 하위 클래스인 `SomethingGood` 클래스를 만들면, 클래스 계층이 생김   
Something   
└─ SomethingGood   
- 이 계층은 기능을 추가하기 위해 만들어짐
  - 상위 클래스는 기본적인 기능을 가지고 있음
  - 하위 클래스에서 새로운 기능을 추가함
- 이 클래스 계층을 `기능의 클래스 계층`이라 한다
- 새로운 기능을 추가하고 싶을 때, 클래스 계층 안에서 자신의 목적과 가까운 클래스를 찾아 그 하위 클래스를 만들고, 원하는 기능을 추가한 새로운 클래스를 만든다
- 일반적으로 클래스 계층을 너무 깊게 하지 않는 편이 좋음

#### 새로운 `구현`을 추가하고 싶다
- 상위 클래스는 추상 메소드로 인터페이스(API)를 규정하는 역할을 하고, 하위 클래스는 추상 메소드를 구현하는 역할을 함
- 이렇게 상위 클래스와 하위 클래스의 역할 분담을 통해 부품으로서의 가치가 높은 클래스를 만들 수 있음   
AbstractClass   
└─ ConcreteClass
- 여기에서 사용되는 클래스 계층은 기능을 추가하기 위해 사용되는 것은 아니며, 새로운 메소드를 늘리기 위해 클래스 계층을 만든 것도 아님
- 이 계층은 다음과 같은 역할 분담을 위해 클래스 계층이 사용됨
  - 상위 클래스는 추상 메소드로 인터페이스(API)를 규정한다
  - 하위 클래스는 구상 메소드로 그 인터페이스(API)를 구현한다
- 이 클래스 계층을 `구현의 클래스 계층`이라고 한다
- 새로운 구현을 만들기 위해서는 `AbstractClass`의 하위 클래스를 만들고 추상 메소드를 구현하게 된다

### 하위 클래스를 만들고자 할때 의도를 확인 해야 한다
- 기능을 추가하려고 하는가? vs 구현하려고 하는가?
- 예를 들어 `모양(Shape)`이라는 기능의 계층이 존재할때, 기능의 계층에서는 다양한 모양(원, 사각형 등)을 나타내는 추상화를 담당하고, 구현 계층에서는 각각의 모양(원, 사각형 등)에 대한 구현을 담당
  - `새로운 모양`을 추가하려면 `기능` 계층에서만 확장을 하면 되고, `구현을 변경`하려면 `구현` 계층에서만 수정할 수 있다
- `기능의 클래스 계층`과 `구현의 클래스 계층`을 **두 개의 독립된 클래스 계층으로 나누고** 두 클래스 계층 사이에 **다리**를 놓자


## 예제 프로그램
| 다리의 어느 쪽?  | 이름                | 설명                         |
|------------|-------------------|----------------------------|
| 기능의 클래스 계층 | Display           | '표시한다' 클래스                 |
| 기능의 클래스 계층 | CountDisplay      | '지정 횟수만큼 표시한다' 기능을 추가한 클래스 |
| 구현의 클래스 계층 | DisplayImpl       | '표시한다' 클래스                 |
| 구현의 클래스 계층 | StringDisplayImpl | '문자열을 사용해서 표시한다' 클래스       |
|            | Main              | 동작 테스트용 클래스                |







### 기능의 클래스 계층 : Display 클래스
- `기능의 클래스 계층`의 최상위 클래스
```java
public class Display{
    private DisplayImpl impl;
    
    public Display(DisplayImpl impl){
        this.impl = impl;
    }
    
    public void open(){
        impl.rawOpen();
    }
    
    public void print(){
        impl.rawPrint();
    }
    
    public void close(){
        impl.rawClose();
    }
    
    public final void display(){
        open();
        print();
        close();
    }
}
```

- `impl` 필드는 `Display` 클래스의 **구현**을 나타내는 인스턴스
  - 생성자에는 구현을 나타내는 클래스의 인스턴스를 전달
  - 이 필드가 두 클래스 계층의 **다리** 역할
- `open`, `print`, `close` 세 메소드는 `Display` 클래스에서 제공하는 인터페이스(API)
  - 이 메소드를 실현하고자 `impl` 필드의 구현 메소드를 이용함
  - 여기서 `Display`의 인터페이스(API)가 `DisplayImpl`의 인터페이스(API)로 변환됨
    - 즉, `Display` 클래스가 구현하지 않고 `DisplayImpl` 클래스에 위임(delegate)한다는 것을 의미

### 기능의 클래스 계층 : CountDisplay 클래스
```java
public class CountDisplay extends Display{
    public CountDisplay(DisplyImpl impl){
        super(impl);
    }
    
    public void multiDisplay(int times){
        open();
        for(int i = 0; i < times; i++){
            print();
        }
        close();
    }
}
```
- `Display` 클래스에 `기능`을 추가한 것
  - `Display`클래스에는 **표시하는** 기능 밖에 없었는데, `CountDisplay` 클래스에는 **지정 횟수만큼 표시하는** 기능을 추가
  - `multiDisplay` 메소드의 역할
    - `Display` 클래스에서 상속받은 `open`, `print`, `close`라는 메소드를 사용하여 새로운 메소드를 추가한 것


### 구현의 클래스 계층 : DisplayImpl 클래스
- `구현의 클래스 계층`의 최상위 클래스
```java
public abstract class DisplayImpl{
    public abstract void rawOpen();
    public abstract void rawPrint();
    public abstract void rawClose();
}
```
- `DisplayImpl` 클래스는 추상 클래스로 `rawOpen`, `rawprint`, `rawClose`라는 세가지 메소드를 가지고 있음

### 구현의 클래스 계층 : StringDisplayImpl 클래스
- 추상메소드를 직접 구현하는 부분

```java
public class StringDisplayImpl extends DisplayImpl{
    private String string;
    private int width;
    
    public StringDisplayImpl(String string){
        this.string = string;
        this.width = string.length();
    }
    
    @Override
    public void rawOpen(){
        printLine();
    }
    
    @Override
    public void rawPrint(){
      System.out.println("|" + stirng + "|");
    }
    
    @Override
    public void rawClose(){
        printLine();
    }
  
    private void printLine(){
      System.out.println("+");
      for(int i = 0; i < width; i++){
        System.out.println("-");
      }
      System.out.println("+");
    }
}
```
- 문자열을 표시하는데, `DisplayImpl` 클래스의 하위 클래스로서 `rawOpen`, `rawprint`, `rawClose`라는 메소드를 사용하여 표시

### Main 클래스
```java
public class Main{
    public static void main(Stirng[] args){
        Display d1 = new Display(new StringDisplayImpl("Hello, Korea."));
        Display d2 = new CountDisplay(new StringDisplayImpl("Hello, World."));
        CountDisplay d3 = new CountDisplay(new StringDisplayImpl("Hello, Universe."));
        d1.display();
        d2.display();
        d3.display();
        d3.multiDisplay(5);
    }
}
```
- `d1`, `d2`, `d3` 모두 `Display` 클래스의 인스턴스이므로 `display` 메소드를 호출할 수 있음
- `d3`에서는 `multiDisplay` 메소드도 호출할 수 있음


## 독자의 사고를 넓혀 주는 힌트
### 상속은 강한 결합이고 위임은 약한 결합이다
```java
class SomethingGood extends Something{
  ...
}
```
- `SomethingGood` 클래스는 `Something` 클래스의 하위 클래스로 **소스 코드를 다시 쓰지 않는 한 바꿀 수 없음**
  - 즉, 매우 강하게 결합된다는 뜻
  - 따라서, 필요에 따라 클래스 간의 관계를 전환하고자 할때 상속을 사용하는 것은 부적절하다

```java
//예제프로그램의 일부
    ...
    public void open(){
            impl.rawOpen();
            }
    
            public void print(){
            impl.rawPrint();
            }
    
            public void close(){
            impl.rawClose();
            }
        ...

```
- `Display` 클래스 안에서 `위임`을 사용
- `위임`은 느슨한 결합
- 만약 `PlusDisplayImpl` 라는 구현 클래스가 있다고 했을 때, 그 인스턴스를 `Display`나 `CountDisplay`에 넘겨주면 해당 객체는 `PlusDisplayImpl` 클래스의 구현을 사용하게 됨

## 브릿지 패턴의 장단점
### 장점
- 추상적인 코드를 구체적인 코드 변경 없이도 **독립적으로** 확장할 수 있다.
  - 유지보수가 간편해지며, 한 쪽을 수정하더라도 다른 쪽에 영향을 미치지 않음
- 추상적인 코드와 구체적인 코드를 분리할 수 있다.
  - 구조적인 틀을 작성해놓고, 자유롭게 구현 코드를 주입할 수 있다.

### 단점
- 계층 구조가 늘어나 복잡도가 증가할 수 있다.
  - 단 하나의 클래스만 만들고 추후에 확장 가능성이 아예 존재하지 않는다면, 오히려 번거로운 작업이 될 수 있다.
  - 처음 보는 개발자는 코드를 파악하는데 어려움을 겪을 수도 있다.

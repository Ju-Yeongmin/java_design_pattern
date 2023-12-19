# part 5 : Singleton - 인스턴스를 단 하나만 만든다

## Singleton 패턴이란?
- 문자열을 나타내는 `java.lang.String` 클래스는 문자열 1개에 인스턴스 1개가 생성됨
  - 문자열이 1000개가 등장하면 인스턴스가 1000개 생성
- 만약, `시스템 안에 인스턴스가 단 하나만 존재한다는 것을 표현`하고 싶다면
  - `new MyClass()`와 같이 클래스를 한 번만 실행하도록 하면, `MyClass` 인스턴스는 하나만 생성됨
  - 하지만, `프로그래머가 주의를 기율여서 인스턴스를 하나만 생성`하는 것이 아니라,
    - 지정한 클래스의 인스턴스가 반드시 1개만 존재한다는 것을 보증하고 싶을 때
    - 인스턴스가 하나만 존재한다는 것을 프로그램 상에서 표현하고 싶을 때
  - 와 같이 인스턴스가 하나만 존재하는 것을 보증하는 패턴을 `Singleton 패턴`이라고 부른다

### Singleton 패턴 사용 이유
#### 메모리 측면
- 최초 한번의 `new` 연산자를 통해서 고정된 메모리 영역을 사용하기 때문에 추후 해당 객체에 접근할 때 메모리 낭비를 방지할 수 있음
- 이미 생성된 인스턴스를 활용하니 속도 측면에서도 이점이 있음

#### 데이터 공유의 용이성
- 싱글톤 인스턴스는 전역으로 사용되는 인스턴스 이기 때문에 다른 클래스의 인스턴스들이 접근하여 사용할 수 있음.
- 하지만, 여러 클래스의 인스턴스에서 싱글톤 인스턴스의 데이터에 동시 접근을 하게 되면 동시성의 문제가 발생할 수 있으니 이 점을 유의해서 설계해야함

### Singleton 패턴의 단점
#### 1. private 생성자를 갖고 있어 상속이 불가능하다.
- 상속을 통해 다형성을 적용하기 위해서는 다른 기본생성자가 필요하므로 객체지향의 장점을 적용할 수 없다
- 또한, static 필드와 static 메소드는 객체지향적이지 못하다

#### 2. 테스트하기 힘들다
- Singleton은 생성 방식이 제한적이기 때문에 Mock 객체로 대체하기가 어려우며, 동적으로 객체를 주입하기 힘들어 테스트 코드 작성에 어려움을 준다

#### 3. 서버 환경에서는 Singleton이 1개만 생성됨을 보장하지 못한다.
- 서버에서 클래스 로더를 어떻게 구성하느냐에 따라 1개 이상의 객체가 만들 수 있다.
- 따라서 Java 언어를 이용한 싱글톤 패턴은 서버 환경에서 꼭 보장된다고는 보기 어렵다
- 또한, 여러개의 JVM에 분산되어 설치되는 경우에도 독립적으로 객체가 생성된다

#### 4. 전역 상태를 만들 수 있기 때문에 바람직하지 못하다
- static 메소드를 이용하면 언제든지 해당 객체를 사용할 수 있고, 전역 상태로 사용되기 쉽다
- 아무 객체나 자유롭게 접근하고 수정하며 공유되는 전역 상태는 객체지향 프로그래밍에서 권장되지 않는다.



## 예제 프로그램
|이름|설명|
|---|---|
|Singleton|인스턴스가 하나만 존재하는 클래스|
|Main|동작 테스트용 클래스|

### Singleton 클래스
```java
public class Singleton{
    private static Singleton singleton = new Singleton();
    
    private Singleton(){
      System.out.println("인스턴스를 생성했습니다.");
    }
    
    public static Singleton getInstance(){
        return singleton;
    }
}
```
#### static
- `Singleton` 클래스에서는 인스턴스를 하나만 만들 수 있는데, `Singleton`은 `static` 필드로 정의되고 `Singleton` 클래스의 인스턴스에서 초기화한다.
  - 이때, 초기화는 `Singleton` 클래스를 로드할 때 한 번만 실행됨

#### private
- `Singleton` 클래스의 생성자는 위부에서 생성자 호출을 금지하기 위해 `private`로 되어 있다.
  - 만약, 외부에서 생성자를 호출하여 객체를 쉽게 생성할 수 있다면, 프로세스 내에 1개의 객체만 생성되도록 관리하기 어려울 수 있음
- 처음부터 프로그래머가 주의해서 `new` 하지 않도록 해주면, 생성자를 `private`로 할 필요는 없음
  - 하지만, `Singleton 패턴`은 프로그래머가 어떤 실수를 하더라도 인스턴스가 하나만 생성되는 것을 보증하는 패턴이므로, 이 보증을 위해 생성자를 `private`로 선언

#### getInstance
- `Singleton` 클래스의 유일한 인스턴스를 얻는 메소드
- 이 메소드에서 한개의 객체만 생성되도록 관리할 수 있음
- p.96 static Factory Method의 일종
- Effective Java의 'item01' : 정적 팩터리 메서드가 생성자보다 좋은 장점의 예시


### Main 클래스
```java
public class Main{
    public static void main(String[] args){
      System.out.println("Start.");
      Singleton obj1 = Singleton.getInstance();
      Singleton obj2 = Singleton.getInstance();
      if(obj1 == obj2){
        System.out.println("obj1과 obj2는 같은 인스턴스입니다.");
      } else{
        System.out.println("obj1과 obj2는 같은 인스턴스가 아닙니다.");
      }
      System.out.println("End.");
    }
}
```
- `getInstance` 메소드는 두 번 호출되며, 반환값은 각각 obj1과 obj2에 대입

## enum을 이용한 Singleton
```java
public enum SingletonEnum {
    INSTANCE;
    int value;
    
    public int getValue() {
        return value;
    }
    public void setValue(int value) {
        this.value = value;
    }
}
public class EnumDemo {
    
    public static void main(String[] args) {
        SingletonEnum singleton = SingletonEnum.INSTANCE;
        
        System.out.println(singleton.getValue());
        singleton.setValue(2);
        System.out.println(singleton.getValue());
    }
}

// 실행결과
// 0
// 2
```
- `enum`의 요소는 상수로서 인스턴스의 유일성을 보증받음
- 요소를 하나만 가지는 `enum`을 이용하여 `Singleton` 패턴을 구현할 수 있다

# part 2 : Adapter - 사이에 끼워 재사용한다

## Adapter 패턴
- 제공된 것과 필요한 것 사이에 들어가서 그 사이를 채우는 것
- `이미 제공된 것`과 `필요한 것` 사이의 `차이`를 메우는 디자인 패턴이 바로 `Adapter 패턴`
- `Wrapper` 패턴이라고도 불림
- Adapter 패턴의 종류
  - 클래스에 의한 Adapter 패턴(상속을 사용한 패턴)
  - 인스턴스에 의한 Adapter 패턴(위임을 사용한 패턴)

## 상속을 사용한 Adapter 패턴
- `Banner` 클래스의 `ShowWithParen` 메소드, `ShowWithAster` 메소드
- `Print` 인터페이스의 `printWeak` 메소드, `printStrong` 메소드
- 목표는 `Banner` 클래스를 사용하여 `Print` 인터페이스를 충족하는 클래스를 만드는 것

### Banner 클래스
```java
public class Banner{
    private String string;
    
    public Banner(String string){
        this.string = string;
    }
    
    public void showWithParen(){
        System.out.println("(" + string + ")");
    }
    
    public void showWithAster(){
        System.out.println("*" + string + "*");
    }
}
```

### Print 인터페이스
```java
public interface Print{
    public abstract void printWeak();
    public abstract void printStrong();
}
```

### PrintBanner 클래스
- **어댑터** 역할을 함
- 준비된 `Banner` 클래스를 확장(extends)하여 `showWithParen` 메소드와 `showWithAster` 메소드를 상속받으며, 필요한 `Print` 인터페이스를 구현(implements)하여 `printWeak` 메소드와 `printStrong` 메소드를 구현
- `@Override`는 `printWeak`와 `printStrong` 메소드에 대한 부가 정보의 일종, `Print` 인터페이스에서 선언된 메소드를 오버라이드하여 구현한 것

```java
public class PrintBanner extends Banner implements Print{
    public PrintBanner(String string){
        super(string);
    }
    
    @Override
    public void printWeak(){
        showWithParen();
    }
    
    @Override
    public void printStrong(){
        showWithAster();
    }
}
```

### Main 클래스
- `PrintBanner` 클래스를 이용해 문자열을 약하게 혹은 강하게 표시함
```java
public class Main{
    public static void main(String[] args){
        Print p = new PrintBanner("Hello");
        p.printWeak();
        p.printStrong();
    }
}
```

## 위임을 사용한 Adapter 패턴
- `Print`를 인터페이스가 아닌 클래스라고 가정, 즉 `Banner` 클래스를 이용하여 `Print` 클래스와 같은 메소드를 갖는 클래스를 실현하는 것
- `Java`는 단일 상속만 가능하기에 `PrintBanner` 클래스를 `Print`와 `Banner` 양쪽의 하위 클래스로 정의할 수 없음
- `PrintBanner` 클래스는 `banner` 필드로 `Banner` 클래스의 인스턴스를 가짐
- 이 인스턴스는 `PrintBanner` 클래스의 생성자에서 생성함
- `printWeak` 및 `printStrong` 메소드에서는 `banner` 필드를 통해 `showWithParen`, `showWithAster` 메소드를 호출
- `PrintBanner` 클래스의 `printWeak`와 `printStrong` 메소드는 `Print` 클래스에서 선언된 메소드를 오버라이드하여 구현했으므로, `@Override` 어노테이션을 사용
- 즉, `PrintBanner` 클래스의 `printWeak` 메소드가 호출되었을 때 자신이 처리하지 않고, 다른 인스턴스(`Banner`의 인스턴스)인 `shoWithParen` 메소드에 위임

### Print 클래스
```java
public abstract class Print{
    public abstract void printWeak();
    public abstract void printStrong();
}
```

### PrintBanner 클래스
```java
public class PrintBanner extends Print{
    private Banner banner;
    
    public PrintBanner(String string){
        this.banner = new Banner(string);
    }
    
    @Override
    public void printWeak(){
        banner.showWithParen();
    }
    
    @Override
    public void printStrong(){
        banner.showWithAster();
    }
}
```

## Adapter 패턴의 등장인물
#### Target(대상)
- 지금 필요한 메소드를 결정한다.
- 예제 프로그램에서는 `Print` 인터페이스와 `Print` 클래스가 이 역할을 맡음

#### Client(의뢰자)
- Target의 메소드를 사용함
- 예제 프로그램에서는 `Main` 클래스

#### Adaptee(적응 대상자)
- 이미 준비된 메소드를 가지는 역할
- 예제 프로그램에서 `Banner` 클래스
- 만약 Adaptee의 메소드가 Target의 메소드와 일치하다면 Adapter는 등장할 필요도 없었을 것

#### Adapter(적응자)
- Adaptee의 메소드를 사용해서 Target을 만족시키는 것이 역할
- 예제 프로그램에서 `PrintBanner` 클래스
- 클래스에 의한 Adapter 패턴일 때는 Adapter는 상속하여 Adaptee를 이용
- 반면, 인스턴스에 의한 Adapter 패턴일 때는 `위임`하여 Adaptee를 이용

## 어떨 때 사용하나
- 이미 존재하는 클래스를 이용하는 경우, 그 클래스를 부품으로 재사용할 때 사용
- `Adapter` 패턴은 기존 클래스에 한겹 덧씌워 필요한 클래스를 만들어 이 패턴을 통해 필요한 메소드군을 빠르게 만들 수 있음
- 만약 버그가 발생하더라도 기존 클래스에는 버그가 없는 것을 알고 있으므로, 검사에도 매우 용이

## Adapter 패턴의 장점
- 기존 클래스를 전혀 수정하지 않고 목적한 인터페이스(API)를 개발할 수 있음
- 신버전과 구버전을 공존시키고 유지 보수에 용이

## 상속과 위임
- 일반적으로 상ㅇ속을 사용한 것 보다 위임을 사용하는 편이 문제가 적다
  - 상위 크래스이 내부 동작을 자세히 모르면, 상속을 효과적으로 사용하기 어려운 경우가 많기 때문


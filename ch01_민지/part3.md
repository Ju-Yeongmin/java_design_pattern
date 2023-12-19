# part 3 : Template Method - 하위 클래스에서 구체적으로 처리한다

## Template Method
- 상위 클래스 쪽에 템플릿이 될 메소드가 정의되어 있고, 그 메소드 정의에 추상 메소드가 사용된다
- 따라서, 상위 클래스의 코드만으로 어떻게 처리되는지 알 수 없다
- 하위 클래스에서 메소들르 구현하면 구체적인 처리 방식이 결정된다
- 하지만 하위 클래스에서 어떻게 구현하더라도 처리의 큰 흐름은 상위 클래스에서 구성한 대로 된다
- 이처럼 **상위 클래스에서 처리의 뼈대를 결정하고 하위 클래스에서 구체적인 냉용을 결정**하는 디자인 패턴을 `Template Method`패턴이라고 한다

## 예제 프로그램
|이름|설명|
|---|---|
|AbstractDisplay|메소드 `display`만 구현된 추상 클래스|
|CharDisplay|메소드 `open`, `print`, `close`를 구현하는 클래스|
|StringDisplay|메소드 `open`,`print`,`close`를 구현하는 클래스|
|Main|동작 테스트용 클래스|

### AbstractDisplay 클래스
- `open`, `print`, `close`, `display` 메소드 중 `open`, `print`, `close`는 추상 메소드이고 `display` 메소드만 구현되어 있음
```java
public abstract class AbstractDisplay{
    // open, print, close는 하위 클래스에 구현을 맡기는 추상 메소드
    public abstract void open();
    public abstract void print();
    public abstract void close();
    
    // display는 AbstractDisplay에서 구현하는 메소드
    public final void display(){
        open();
        for(int i = 0; i < 5; i++){
            print();
        }
        close();
    }
}

```

### CharDisplay 클래스
- 상위 클래스인 `AbstractDisplay` 클래스에서 추상 메소드로 선언된 `open`, `print`, `close`가 모두 구현되어 있으므로 `CharDisplay` 클래스는 추상 클래스가 아님
- 또한, `open`, `print`, `close`는 상위 클래스의 메소드를 오버라이드한 메소드이기에 `@Override` 어노테이션이 적혀 있음

| 메소드 이름 | 처리  |
|--------|-----|
|open|문자열 "<<"를 표시|
|print|생성자에서 주어진 1문자를 표시|
|close|문자열 ">>"를 표시|

```java
public class CharDisplay extends AbstractDisplay{
    private char ch; // 표시해야 하는 문자
    
    // 생성자
    public CharDisplay(char ch){
        this.ch = ch;
    }
    
    @Override
    public void open(){
        // 시작 문자열 "<<"를 표시한다
        System.out.print("<<");
    }
    
    @Override
    public void print(){
        // 필드에 저장해 둔 문자를 1회 표시한다
        System.out.print(ch);
    }
    
    @Override
    public void close() {
        // 종료 문자열 ">>"를 표시한다
        System.out.print(">>");
    }
}
```

### StringDisplay 클래스
| 메소드 이름 | 처리                       |
|--------|--------------------------|
| open   | 문자열 "+----+"을 표시         |
| print  | 생성자에서 주어진 문자열 \와 \사이에 표시 |
| close  | 문자열 "+----+"을 표시         |

```java
public class StringDisplay extends AbstractDisplay{
    private String string; // 표시해야 하는 문자열
    private int width; // 문자열의 길이
    
    // 생성자
    public StringDisplay(String string){
        this.string = string;
        this.width = string.length();
    }
    
    @Override
    public void open(){
        printLine();
    }
    
    @Override
    public void print(){
        System.out.println("|" + string + "|");
    }
    
    @Override
    public void close(){
        printLine();
    }
    
    //open과 close에서 호출되어 "+----+" 문자열을 표시하는 메소드
    private void printLine(){
        System.out.print("+");
        for(int i = 0; i < width; i++){
            System.out.print("-");
        }
        System.out.println("+");
    }
}
```

### Main 클래스
```java
public class Main{
    public static void main(String[] args){
        // 'H'를 가진 CharDisplay 인스턴스를 하나 만든다
        AbstractDisplay d1 = new CharDisplay('H');
        
        //"Hello, world"를 가진 StirngDisplay 인스턴스를 하나 만든다.
        AbstractDisplay d2 = new StringDisplay("Hello, world.");
        
        // d1,d2 모두 같은 AbstractDisplay의 하위 클래스의 인스턴스이므로
        // 상속한 display 메소드를 호출할 수 있다
        // 실제 동작은 CharDisplay나 StringDisply 클래스에서 정해진다
        
        d1.display();
        d2.display();
    }
}
```

## Template Method 패턴의 등장인물
#### AbstractClass(추상 클래스) 역
- 템플릿 메소드를 구현하며, 그 템플릿 메소드에서 사용할 추상 메소드를 선언
- 추상 메소드는 하위 클래스인 `ConcreteClass`에서 구현
- `AbstractDisplay` 클래스가 이 역할을 맡음

#### ConcreteClass(구현 클래스) 역
- `AbstractClass` 역에서 정의된 추상 메소드를 구체적으로 구현함
- `AbstractClass`의 템플릿 메소드에서 호출됨
- `CharDisplay` 클래스와 `StringDisplay` 클래스가 이 역할을 맡음

## 로직을 공통화할 수 있다
#### Template Method의 장점
- 상위 클래스의 템플릿 메소드에 알고리즘이 기술되어 있으므로, 하위 클래스 쪽에는 알고리즘을 일일이 기술할 필요가 없음

## 상위 클래스와 하위 클래스의 연계 플레이
- 상위 클래스에서 선언된 추상 메소드를 실제로 하위 클래스에서 구현할 때는 그 메소드가 어떤 타이밍에 호출되는지 이해해야만 함
- 상위 클래스의 소스 프로그램이 없으면, 하위 클래스 구현이 어려울 수도 있습니다.

## 하위 클래스를 상위 클래스와 동일시 한다
- 상위 클래스형 변수가 있고 그 변수에 하위 클래스 인스턴스가 대입된다고 가정했을 때, `instanceof` 드ㅡㅇ으로 하위 클래스의 종류를 특정하지 않아도 프로그램이 동작하게 만드는 것이 좋음

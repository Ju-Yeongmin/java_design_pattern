## ch02. Adapter

Adapter = 어떤 결과를 자신이 필요한 내용으로 변경해주는 기능

-> 이미 만들어진 함수에 Adapter를 사용해서 자신이 필요한 내용을 얻을 수 있음

ex) 교류100V -> (adapter) -> 직류 12V (필요한 내용)

ex2) [version 1.0] -> (adapter) 1.0 => 2.0 -> [version 2.0]

     [version 2.0] ->  ...                 -> [version 2.0]

---- 
(전체 코드)

- main
```java
public class Main {

    public static void main( String[] args ){

        Print print = new PrintBanner("test");

        print.printWeak();

        print.printStrong();
    }

}
```

- Banner
```java
public class Banner {
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
----
1. 상속을 이용한 패턴

- Print
```java
public interface Print {
    public abstract void printWeak();
    public abstract void printStrong();
}
```

- PrintBanner
```java
public class PrintBanner extends Banner implements Print{

    public PrintBanner(String string) {
        super(string);
    }

    @Override
    public void printWeak() {
        showWithParen();
    }

    @Override
    public void printStrong() {
        showWithAster();
    }
}
```
----

2. 위임을 이용한 패턴

- Print
```java
public abstract class Print {
    public abstract void printWeak();
    public abstract void printStrong();
}
```

- PrintBanner
```java
public class PrintBanner extends Print{

    private Banner banner;

    public PrintBanner(String string){
        this.banner = new Banner(string);
    }

    @Override
    public void printWeak() {
        banner.showWithParen();
    }

    @Override
    public void printStrong() {
        banner.showWithAster();
    }
}
```
----
// 문제 2-1
Print = interface
PrintBanner = print를 상속받은 class


// 문제 2-2




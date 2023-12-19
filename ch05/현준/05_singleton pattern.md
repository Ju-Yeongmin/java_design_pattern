# factory pattern

factory pattern = 클래스의 인스턴스를 하나만 만들어서 사용하는 것

# 예제
  
```java
public class Singleton {

    private static Singleton singleton = new Singleton();

    private Singleton(){
        System.out.println("인스턴스 생성됨");
    }

    public static Singleton getInstance(){
        return singleton;
    }
}
```

---

```java
public class Main {

    public static void main(String[] args){

        System.out.println("start");

        Singleton obj1 = Singleton.getInstance();
        Singleton obj2 = Singleton.getInstnace();

        if(obj1 == obj2){
          System.out.println("obj1 = obj2");
        } else {
          System.out.println("obj1 != obj2");
        }
        System.out.println("end");
    }
}
```

---

# enum을 이용한 singleton

```java
enum Singleton {

    Instance;

    public void call(){
        System.out.println("hi!!");
    }
}
```

---

(문제 5-1)
-> 위 예시에서 enum으로 singleton을 구현하면 됨

(문제 5-2)
private으로 인스턴스 수를 3개로 제한하는 변수를 Triple class안에 선언하면 됨

(문제 5-3)
singleton으로 얻은 값에 null값이 들어가면 기존과 다른 새로운 인스턴스가 생기므로 안 됨

# singleton 을 어디다 쓰면 좋을지?
1. 로그파일
2. 설정관리 등

# 장점
1. 여러 객체가 하나의 자원을 공유하면서 자원 관리가 효율적으로 된다
2. 싱글톤 객체는 어디서든 접근 가능하므로 전역 상태 기능을 한다

# 단점
1. 전역적으로 접근 가능해서 상태 관리가 어려울 수 있따
2. 많은 클래스가 싱글톤에 의존하면 결합도가 증가할 수 있다

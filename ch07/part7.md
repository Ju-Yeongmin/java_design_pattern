# part 7 : Builder - 복잡한 인스턴스를 조립한다

## Builder 패턴이란?
- 빌더 패턴(Builder Pattern)은 복잡한 객체의 생성 과정과 표현 방법을 분리하여 다양한 구성의 인스턴스를 만드는 생성 패턴이다.
- 생성자에 들어갈 매개 변수를 메서드로 하나하나 받아들이고 마지막에 통합 빌드해서 객체를 생성하는 방식이다.

### Builder 패턴 이전에는...
#### (1) 점층적 생성자 패턴
- 점층적 생성자 패턴(Telescoping Constructor Pattern)은 필수 매개변수와 함께 선택 매개변수를 0개, 1개, 2개 .. 받는 형태
- 우리가 다양한 매개변수를 입력받아 인스턴스를 생성하고 싶을때 사용하던 생성자를 **오버로딩** 하는 방식
- 하지만, 타입이 다양할수록 매개변수의 값이 기하급수적으로 늘어나 가독성이나 유지 보수 측면에서 좋지 않다

#### (2) 자바 빈 패턴
- 이러한 단점을 보완하기 위해 Setter 메소드를 사용한 자바 빈(Bean) 패턴이 고안
- 매개변수가 없는 생성자로 객체 생성후 Setter 메소드를 이용해 클래스 필드의 초깃값을 설정하는 방식
- 하지만, 일관성 문제와 불변성 문제 발생

## 예제 프로그램
다음과 같은 `Student` 클래스에 대한 객체 생성만을 담당하는 별도의 `Builder 패턴`을 만들려고 한다.
```java
class Student {
    private int id;
    private String name = "김민지";
    private String grade = "hreshman";
    private String phoneNumber = "010-****-****";

    public Student(int id, String name, String grade, String phoneNumber) {
        this.id = id;
        this.name = name;
        this.grade = grade;
        this.phoneNumber = phoneNumber;
    }
    
    @Override
    public String toString() {
        return "Student { " +
                "id='" + id + '\'' +
                ", name=" + name +
                ", grade=" + grade +
                ", phoneNumber=" + phoneNumber +
                " }";
    }
}
```

### Builder 클래스 구현
```java
class StudentBuilder {
    private int id;
    private String name;
    private String grade;
    private String phoneNumber;

    public StudentBuilder id(int id) {
        this.id = id;
        return this;
    }

    public StudentBuilder name(String name) {
        this.name = name;
        return this;
    }

    public StudentBuilder grade(String grade) {
        this.grade = grade;
        return this;
    }

    public StudentBuilder phoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
        return this;
    }
}
```
- 먼저 `Builder` 클래스를 만들고 필드 멤버 구성을 만들고자 하는 `Student` 멤버 구성과 똑같이 한다.
- 각 멤버에 대한 `Setter` 메서드를 구현
- 각 `Setter` 함수 마지막 반환 구문의 `return this`의 `this`는 `StudentBuilder` 객체 자신을 뜻함
- 즉, 빌더 객체 자신을 리턴함으로써 메서드 호출 후 연속적으로 빌더 메서드들을 체이닝(Chaining) 하여 호출할 수 있게 된다.
- ex) `new StudentBuilder().id(값).name(값)`
- 마지막으로 빌더의 목표였던 최종 `Student` 객체를 만들어주는 `Build`를 구성
```java
class StudentBuilder {
    private int id;
    private String name;
    private String grade;
    private String phoneNumber;

    public StudentBuilder id(int id) { ... }

    public StudentBuilder name(String name) { ... }

    public StudentBuilder grade(String grade) { ... }

    public StudentBuilder phoneNumber(String phoneNumber) { ... }

    public Student build() {
        return new Student(id, name, grade, phoneNumber); // Student 생성자 호출
    }
}
```

### 빌더 클래스 실행
```java
public static void main(String[] args) {

    Student student = new StudentBuilder()
                .id(2016120091)
                .name("임꺽정")
                .grade("Senior")
                .phoneNumber("010-5555-5555")
                .build();

    System.out.println(student);
}
```

## 빌더 패턴의 장단점
### 장점
#### (1) 생성 과정이 복잡한 인스턴스를 일관된 프로세스로 생성
- 직관적으로 어떤 데이터에 어떤 값이 설정되는지 한눈에 파악할 수 있게 된다.
- 특히 연속된 동일 타입의 매개 변수를 많이 설정할 경우에 발생할 수 있는 설정 오류와 같은 실수를 방지할 수 있다.

#### (2) 디폴트 매개변수 생략을 간접적으로 지원
- 디폴트 매개변수가 설정된 필드를 설정하는 메서드를 호출하지 않는 방식으로 사용할 수 있음

#### (3) 멤버 변수의 초기화와 검증을 각각의 멤버 변수별로 분리해서 작성
- 초기화가 필수인 멤버는 빌더의 생성자로 받게 하여 필수 멤버를 설정해주어야 빌더 객체가 생성되도록 유도하고, 선택적인 멤버는 빌더의 메서드로 받도록 할 수 있음
- 즉, 사용자로 하여금 필수 멤버와 선택 멤버를 구분하여 객체 생성을 유도

### 단점
#### (1) 코드 복잡성 증가
- 우선 빌더 패턴을 적용하려면 N개의 클래스에 대해 N개의 새로운 빌더 클래스를 만들어야 함
- 이로 인해 클래수 수가 기하급수적으로 늘어나 관리해야 할 클래스가 많아지고 구조가 복잡해질 수 있음
- 또한, 선택적 매개변수를 많이 받는 객체를 생성하기 위해서는 먼저 빌더 클래스부터 정의해야 함


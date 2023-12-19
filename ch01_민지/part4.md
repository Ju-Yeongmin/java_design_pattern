# part 4 : Factory Method - 하위 클래스에서 인스턴스를 만든다

## Factory Method
- 인스턴스를 생성하는 공장을 `Template Method` 패턴으로 구성한 것이 `Factory Method` 패턴
- 인스턴스 생성 방법을 상위 클래스에서 결정하되, 구체적인 클래스 이름까지는 결정하지 않음
- 인스턴스 생성을 위한 뼈대(프레임워크)와 실제 잉ㄴ스턴스를 생성하는 클래스를 나누어 생각함

## 예제 프로그램
| 패키지       | 클래스 이름        | 설명                     |
|-----------|---------------|------------------------|
| framework | Product       | 추상 메소드 use만 정의한 추상 클래스 |
| framework | Factory       | 메소드create를 구현한 추상 클래스|
| idcard    | IDCard        | 메소드 use를 구현한 클래스 |
| idcard    | IDCardFactory |메소드 createProduct, registerProduct를 구현한 클래스|
|이름없음| Main| 동작 테스트용 클래스|

### Product 클래스
- 추상 메소드 `use`만 선언되어 있음
- 구체적인 `use`의 구현은 모두 `Product`의 하위 클래스에 맡기고 있다
```java
package framework;

public abstract class Product{
    public abstract void use();
}
```

### Factory 클래스
- framework 패키지의 `Factory` 클래스에서는 `Template Method`패턴이 사용된다
- 추상메도스 `createProduct`로 '제품을 만들고', 만든 제품을 추상 메소드 `registerProduct`로 '등록'한다.
- 이는 하위 클래스에서 구현한다
- 이 프레임워크에서 공장이란 `create` 메소드로 `Product` 인스턴스를 생성하는 것으로 규정하고 있음
- `create` 메소드는 `createProduct`로 제품을 만들고 `registerProduct`로 등록하는 절차로 구현되어있음
- 구체적인 구현 내용은 `Factory Method` 패턴을 적용한 프로그램에 따라 달라진다
- `Factory Method`패턴에서 인스턴스를 생성할 때 `Template Method` 패턴을 사용한다
```java
package framework;
public abstract class Factory {
    public final Product create(String owner) {
        Product p = createProduct(owner);
        registerProduct(p);
        return p;
    }
    
    protected abstract Product createProduct(String owner);
    protected abstract void registerProduct(Product product);
}
```

### IDCard 클래스
- 프레임워크에서 분리되는 것임을 명시하고자 `idcard` 패키지란는 별도의 패키지를 만들고, `IDCard` 클래스를 제품 `Product` 클래스의 하위 클래스로서 정의함
```java
package idcard;
import framework.Product;

public class IDCard extends Product{
    private String owner;
    IDCard(String owner){
        System.out.println(owner + "의 카드를 만듭니다.");
        this.owner = owner;
    }
    
    @Override
    public void use(){
        System.out.println(this + "을 사용합니다.");
    }
    
    @Override
    public String toString(){
        return "[IDCard:" + owner + "]";
    }
    
    public String geetOwner(){
        return owner;
    }
}
```

### IDCardFactory 클래스
- `IDCardFactory` 클래스에서는 `createProduct`와 `registerProduct` 메소드를 구현
- `createProduct`는 `IDCard` 인스턴스를 생성하여 실제로 '제품을 만들고', `registerProduct`는 만들어진 제품을 등록한다

```java
package idcard;

import framework.Factory;
import framework.Product;

public class IDCardFactory extends Factory{
    @Override
    protected Product createProduct(String owner){
        return new IDCard(owner);
    }
    
    @Override
    protected void registerProduct(Product product){
        System.out.println(product + "을 등록했습니다.");
    }
}
```

### Main 클래스
```java
import framework.Factory;
import framework.Product;
import idcard.IDCardFactory;

public class Main{
    public static void main(String[] args){
        Factory factory = new IDCardFactory();
        Product card1 = factory.create("Youngin Kim");
        Product card2 = factory.create("Heungmin son");
        Product card3 = factory.create("Kane");
        card1.use();
        card2.use();
        card3.use();
    }
}
```

## Factory Method의 등장인물
#### Product(제품)
- 프레임워크
- 이 패턴으로 생성되는 인스턴스가 가져야할 인터페이스(API)를 결정하는 추상 클래스
- 구체적인 내용은 하위 클래스 `ConcreteProduct`에서 결정
- 예제프로그램의 `Product` 클래스

#### Creator(작성자)
- 프레임워크, `Product` 역을 생성하는 추상 클래스
- 구체적인 내용은 하위 클래스 `ConcreteCreator`가 결정
- 예제 프로그램의 `Factory` 클래스
- `Product`역과 인스턴스 생성 메소드를 호출하면 `Product`가 생성된다는 사실만 앎
- new를 사용해 실제 인스턴스를 생성하는 대신에, 인슽턴스를 생성하는 메소들르 호출함으로써 구체적인 클래스 이름에 의한 속박에서 상위 클래스를 자유롭게 함

#### ConcreteProduct(구체적인 제품)
- 구체적으로 살을 뿥이는 쪽으로, 구체적인 제품을 결정
- 예제프로그램의 `IDCard` 클래스

#### ConcreteCreator(구체적인 작성자)
- 구체적으로 살을 뿥이는 쪽으로, 구체적인 제품을 만들 클래스를 결정
- 예제프로그램의 `IDCardFactory` 클래스

## 프레임워크와 구체적인 내용
- 새로운 클래스를 같은 프레임워크로 생성하는 경우에도 framework 패키지 수정은 필요 없음
- 이것을 의존하지 않는다고 표현

## 인스턴스 생성 - 메소드 구현 방법
- 예제 프로그램에서 `Factory` 클래스의 `createProduct` 메소드는 추상 메소드
- 즉, 이 메소드는 하위 클래스에서 구현해야함
- `createProduct` 메소드를 기술하는 두가지 방법
#### 추상 메소드로 기술한다
- 추상 메소드로 기술하면, 하위 클래스에서는 반드시 이 메소드를 구현해야만 함
```java
abstract class Factory{
    public abstract Product createProduct(String name);
    ...
}
```

#### 디폴트 구현을 준비해 둔다
- 하위 클래스에서 구현하지 않은 경우에 디폴트 구현이 사용됨
```java
class Factory{
    public Product createProduct(String name){
        return new Product(name);
    }
}
```
- 단, 이 경우에는 `Product` 클래스에 대해 직접 `new`를 실행하므로 `Product` 클래스를 추상 클래스로 둘 수는 없음

## Static Factory Method
- 인스턴스 생성을 위한 클래스 메소드 전반을 `Factory Method`라고 부르는 경우가 있다
- 이것은 `Java`에서 인스턴스를 생성할 때 매우 자주 사용되는 기법이며, `Java API` 레퍼런스에서도 인스턴스 생성을 위한 클래스 메소드를 `static Factory Method`라고 표현하기도 함
- `static Factory Method`로서는 `create`, `newInstance`, `getInstance` 등의 이름이 자주 사용되지만, 그 밖의 이름도 있음

#### getInstance 메소드
난수 샏성 알고리즘 이름을 지정해서 `SecureRandom` 인스턴스를 생성함
```java
SecureRandom random = SecureRandom.getInstance("NativePRNG");
```

#### of 메소드
구체적인 요소를 주면 `List` 인스턴스를 생성함
```java
// Alice, Bob, Chris로 구성된 List를 얻는다
List<String> list = List.of("Alice", "Bob", "Chris");
```

##### asList 메소드
지정되 배열이나 열거한 요소로부터 `List` 인스턴스를 생성함
```java
Stirng[] arr = {"Alice", "Bob", "Chris"};
List<String> list1 = Arrays.asList(arr);
List<String> list2 = Arrays.asList("Alice", "Bob", "Chris");
```

#### valueOf 메소드
다양한 형태의 문자열 표현을 얻음
```java
// char 형 'A'로부터 String형 "A"를 얻는다.
String string = String.valueOf('A');
```

#### now 메소드
현재 시간을 나타내는 Instant의 인스턴스를 생성함
```java
Instant instant = Instant.now();
```

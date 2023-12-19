# factory method

factory method = 부모 클래스에서 객체들을 생성할 수 있는 인터페이스를 제공하지만, 자식 클래스들이 생성될 객체들의 유형을 변경할 수 있도록 하는 생성 패턴

# 예제
framwork > Factory
  
- create 메소드가 있는 추상 클래스
- createProduct -> 하위 클래스에서 구현
- registerProdudct -> 하위 클래스에서 구현 
  
```java
package framework;

public abstract class Factory {

    public final Product create(String owner){
        Product product = createProduct(owner);
        registerProduct(product);
        return product;
    }

    protected abstract Product createProduct(String owner);
    protected abstract void registerProduct(Product product);
}
```
---

framwork > Product
  
- 상품을 표현
- 추상 클래스 use만 있다

-> use 메소드를 인스턴스가 사용할 수 있음
  
```java
package framework;

public abstract class Product {

    public abstract void use();
}
```

---

idcard > Product
- 상품 생성하기 메소드
- 상품 등록하기 메소드
  
-> IDCardFactory는 Factory를 상속받는데 -> 구체적인 내용은 factory에서 작성
  ( Factory는 어떤 기능을 할 건지만 정의되어있다 )
  
```java
package idcard;

import framework.Factory;
import framework.Product;

public class IDCardFactory extends Factory {

    @Override
    protected Product createProduct(String owner) {
        return new IDCard(owner);
    }

    @Override
    protected void registerProduct(Product product) {
        System.out.println(product + " 등록됨");
    }
}
```

---

idcard > Product

- IDCard는 product를 상속받아서 상품처럼 쓸 수 있다
- Product에 있는 use로 카드를 사용할 수 있음
  
```java
package idcard;

import framework.Product;

public class IDCard extends Product {

    private String owner;

    public IDCard(String owner) {
        System.out.println(owner + "의 카드 만들기");
        this.owner = owner;
    }

    @Override
    public void use() {
        System.out.println(this + " 사용하기");
    }

    public String getOwner(){
        return owner;
    }
}
```

---

main

- 여기서 확인하기
  
```java
import framework.Factory;
import framework.Product;
import idcard.IDCardFactory;

public class Main {

    public static void main(String[] args){

        Factory factory = new IDCardFactory();

        Product card1 = factory.create("user1");
        Product card2 = factory.create("user2");
        Product card3 = factory.create("user3");

        card1.use();
        card2.use();
        card3.use();
    }

}
```


# factory method에 등장하는 내용

1. product (제품) 역할

프레임워크 기능을 하고 -> 어떤 내용이 있어야 될 지 정의만 하고 구체적 내용은 하위 클래스에서 작성
-> 다양한 상품을 만들어 각각 구분할 수 있다

2. Creator (작성자) 역할

기존에는 구체적 클래스의 생성자를 사용했는데 팩토리 패턴으로 생성된 객체를 얻을 수 있다 -> 유지보수 + 확장 좋아짐

---

# factory method 의 장점
  
새로운 객체를 만들거나 기존 객체를 변경할 때 쉽게 대체할 수 있고
다른 코드가 객체를 만들 때 정확한 유형을 알 필요가 없다 -> 코드 간의 의존성?이 감소
+ 동일한 객체가 여러번 만들어지는게 방지된다

# factory method 단점

추상화가 너무 많으면 코드가 복잡해질 수 있다.

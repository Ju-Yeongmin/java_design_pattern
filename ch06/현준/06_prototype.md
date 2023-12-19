# prototype

factory pattern = 프로토타입의 인스턴스가 생성할 객체의 타입을 정하면서 새 객체들을 복사할 수 있다

# 예제

product
  
```java
package framework;

public interface Product extends Cloneable {

    public abstract void use(String s);
    public abstract Product createCopy();
}
```

---

Manager

```java
package framework;

import java.util.HashMap;
import java.util.Map;

public class Manager {
    private Map<String,Product> showcase = new HashMap<>();

    public void register(String name, Product prototype){
        showcase.put(name, prototype);
    }

    public Product create(String prototypeName){
        Product p = showcase.get(prototypeName);
        return p.createCopy();
    }
}

---

MessageBox

```java
import framework.Product

public class MessageBox implements Product {
    private char decochar;

    public MessageBox(char decochar){
        this.decochar = decochar;
    }

    @Override
    public void use(String s){
        int decolen = 1 + s.length() + 1;
        for(int i=0;i<decolen;++i){
            System.out.print(decochar);
        }
        System.out.println();
        System.out.println(decochar + s + decochar);
        for(int i=0;i<decolen;++i){
            System.out.print(decochar);
        }
        System.out.println();
    }

    @Override
    public Product createCopy() {
        Product p = null;
        try{
            p=(Product)clone();
        }catch(CloneNoSuppotrtedException e){
            e.printStackTrace();
        }
        return p ;
    }
}
```

---

UnderlinePen

```java
import framework.Product

public class UnderlinePen implements Product {
    private char ulchar;

    public MessageBox(char ulchar){
        this.decochar = ulchar;
    }

    @Override
    public void use(String s){
        int ulen = s.length();
        for(int i=0;i<ulen;++i){
            System.out.print(ulchar);
        }
        System.out.println();
    }

    @Override
    public Product createCopy() {
        Product p = null;
        try{
            p=(Product)clone();
        }catch(CloneNoSuppotrtedException e){
            e.printStackTrace();
        }
        return p ;
    }
}
```

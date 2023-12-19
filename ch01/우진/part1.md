# Reference
https://www.yes24.com/Product/Goods/115576266

> 해당 포스팅은 `JAVA 언어로 배우는 디자인 패턴 입문` 의 내용을 정리 한 내용입니다. 

# Intro

Iterator 패턴에 대해 말씀 드리기 전에 Java의 Iterator 를 사용 해 본 경험에 대해 질문 하고 싶습니다.  

흔히들 처음 언어를 배울 때 아래와 같은 코드를 작성하곤 합니다.

```java
int[] arr = new int[] {1,2,3,4,5,6,7,8,9,10};

for(int i = 0; i < 10; i++) {
    System.out.println(arr[i]);
}
```

아주 자주 사용하는 패턴입니다만, 해당 패턴의 문제점은 인덱스로 접근 가능 한 데이터에 대해서만 사용 가능하다는 점입니다. 
또한 for loop 를 통해 탐색하는 객체의 데이터의 갯수를 명확히 알고 있어야 한다는 단점이 존재합니다. 

Java에서 지원하는 다양한 컬렉션 내의 데이터를 Linear Search 하기 위해서 위와 같은 방법을 쓸 수 있을까요?  
List라면 사실 가능 할 지도 모르겠다만, Set이나 Queue 와 같은 인덱스로 접근하기 어려운 구조에서는 사용하기 어렵습니다.  

# Iterator 인터페이스

Java에서는 다양한 Collection 에 대해 Iterable 인터페이스를 통한 반복적인 처리를 지원하고 있습니다. 
```java
public interface Iterable<E> {
    public abstract boolean hasNext();
    public abstract E next();
}
```

`java.util.iterator` 를 살펴보면 위와 같은 메소드들을 확인할 수 있습니다. 

iterator는 다음 요소가 있는 지 알아보는 `hasNext` 메소드와 다음 요소를 가져오는 `next` 메소드를 가지고 있습니다. 

해당 메소드들을 통해 다음 값이 있는 지 확인할 수 있고, 다음 값을 가져올 수 있습니다. 

# 예제 프로그램

보관 된 책의 정보를 조회할 수 있는 프로그램을 살펴 보겠습니다. 

## Book.java
```java
public class Book {
    private String name;
    public Book(String name) {
        this.name = name;
    }
    public String getName() {
        return name;
    }
}
```  
위와 같은 `Book` 클래스가 존재한다고 생각 해 보면, 우리는 ArrayList나 Book array 를 통해 값을 저장하고 for 루프로 확인할 수 있을 것입니다. 

하지만, Iterable, Iterator 인터페이스를 통해 아래와 같이 책을 조회하고 저장할 수 있는 클래스를 생성할 수 도 있습니다. 

## BookShelf.java
```java
import java.util.ArrayList;

public class BookShelf implements Iterable<Book> {
    private ArrayList<Book> books;

    public BookShelf() {
        this.books = new ArrayList<Book>();;
    }
  
    public Book getBookAt(int index) {
        return this.books.get(index);
    }
  
    public void appendBook(Book book) {
        this.books.add(book);
    }
  
    public int getLength() {
        return this.books.size();
    }
  
    @Override
    public Iterator<Book> iterator() {
        return new BookShelfIterator(this);
    }
}
```

## BookShelfIterator.java
```java
public class BookShelfIterator implements Iterator<Book> {
    private BookShelf bookShelf;
    private int index;

    public BookShelfIterator(BookShelf bookShelf) {
        this.bookShelf = bookShelf;
        this.index = 0;
    }
  
    @Override
    public boolean hasNext() {
        if (index < bookShelf.getLength()) {
            return true;
        } else {
            return false;
        }
    }
  
    @Override
    public Book next() {
        if(!hasNext()) {
            throw new NoSuchElementException();
        }
        Book book = bookShelf.getBookAt(index) ;
        index++;
        return book;
    }
}
```

## Main.java
```java
public class Main {
  public static void main(String[] args) {
        BookShelf bookShelf = new BookShelf(4);
        bookShelf.appendBook(new Book("Around the World in 80 Days"));
        bookShelf.appendBook(new Book("Bible"));
        bookShelf.appendBook(new Book("Cinderella"));
        bookShelf.appendBook(new Book("Daddy-Long-Legs"));

        Iterator it = bookShelf.iterator();
    
        while (it.hasNext()) {
            Book book = (Book)it.next();
            System.out.println(book.getName());
        }

        System.out.println();

        for(Book book : bookShelf) {
            System.out.println(book.getName());
        }

        System.out.println();
    }
}
```

iterator는 두 가지 방법을 사용할 수 있습니다. `bookShelf.iterator()` 를 통해 탐색하는 방법과 확장 for문입니다.

해당 방법들을 통해 더이상 데이터의 갯수를 알지 않는 상황이라도, 인덱스를 사용하지 않고도 모든 데이터를 선형 탐색 할 수 있습니다. 

# Iterator 패턴의 등장인물

- Iterator
    - 요소를 순서대로 검색하는 인터페이스(API) 를 결정합니다.
- ConcreteIterator
   - Iterator가 결정한 인터페이스(API) 를 실제로 구현합니다. 
- Aggregate
   - Iterator 를 만들어내는 인터페이스(API) 를 결정합니다.
- ConcreteAggregate
   - Aggregate 가 결정한 인터페이스(API) 를 실제로 구현합니다. 


# Iterator 를 사용하는 이유

앞서 말씀 드렸듯이 모든 데이터의 갯수를 알지 못해도 선형 탐색이 가능합니다.  
가장 큰 이유는 Iterator를 사용하기 때문에 구현과 분리해서 반복할 수 있습니다.

```java
while (it.hasNext()) {
    Book book = (Book)it.next();
    System.out.println(book.getName());
}
```
while 루프는 BookShelf의 구현에 따로 의존하지 않고 Iterator를 통해 탐색을 진행합니다.  
BookShelf의 구현이 달라진다고 해도 while 루프 내의 코드는 변하지 않습니다. Iterator를 통해 탐색하기 때문입니다. 

# 주의해야 할 점

Aggregate 와 Iterator는 상호간에 짝을 이룹니다. 만약 BookShelf 의 구현을 변경해서 getBookAt 이라는 메소드를 변경한다면, BookShelfIterator 또한 수정해야 합니다.

next와 hasNext 또한 주의해야 합니다. _next는 현재 요소를 반환하고 다음 위치로 진행하는 메소드입니다._ 마찬가지로 hasNext는 다음에 반환 할 요소가 있을 때 `true`, 마지막 요소일 때 `false` 를 반환합니다. 

# TIP
Aggregate 의 역할을 외부에 두는 게 Iterator 패턴의 핵심입니다. 즉 ConcreteAggregate 역할에 대해 여러 개의 ConcreteIterator 를 구현할 수도 있습니다. 

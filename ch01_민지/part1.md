# part 1 : Iterator - 처리를 반복한다

## Iterator 패턴
```java
for(int i = 0; i<arr.length; i++){
    System.out.println(arr[i]);
        }
```
**변수 `i`의 기능을 추상화하여 일반화한 것을 디자인 패턴에서는 `Iterator 패턴` 이라함**

## Iterator 패턴을 이용한 예제 프로그램
### 1. Iterable<E> 인터페이스
> 집합체를 나타내는 인터페이스 
> 예제 프로그램에서는 Iterable<Book>으로 사용

Iterable<E> 인터페이스는 `처리를 반복할 대상`을 나타내는 것으로, 이 인터페이스를 구현하는 클래스는 `집합체`가 됨

```java
// Iterable<E> 인터페이스
public interface Iterable<E>{
    public abstract Iterator<E> iterator();
}
```
- `iterator` 메소드는 집합체에 대응하는 Iterator<E>를 만들기 위한 것
- 집합체에 포함된 요소를 반복하고 싶을 때는 이 `iterator` 메소드를 사용해 `Iterator<E>` 인터페이슬르 구현한 클래스의 인스턴스를 하나 만듦

### 2. Iterator<E> 인터페이스
> 하나하나의 요소 처리를 반복하기 위한 것으로 루프 변수와 같은 역할을 합니다.

```java
// Iterator<E> 인터페이스
public interface Interator<E>{
    public abstract boolean hashNext();
    public abstract E next();
}
```
- `hashNext` 메소드는 다음 요소가 존재하는지 여부를 반환함
  - 다음 요소가 존재하지 않으면 false값을 반환하게되며, 루프 종료 조건으로 사용하기 위한 것
- `next` 메소드는 집합체의 요소를 1개 반환하고, 다음 `next`메소드를 호출할 때 제대로 다음 요소를 반환할 수 있도록 내부 상태를 다음으로 진행시켜 놓는 역할을 수행

### 3.Book 클래스
> 책 이름을 `getName` 메소드로 얻음

```java
public class Book{
    private String name;
    public Book(String name){
        this.name = name;
    }
    
    public String getName(){
        return name;
    }
}
```

### 4. BookShelf 클래스
> 집합체로 다루기 위해 `Iterable<Book>` 인터페이스를 구현
```java
import java.util.Iterator;

public class BookShelf implemnts Iterable<Book>{
    private Book[] books;
    private int last = 0;
    
    public BookSelf(int maxsize){
        this.books = new Book[maxsize];
        }
    public Book getBookAt(int index){
        return books[index];
        }
    
    public void appendBook(Book book){
        this.books[last] = book;
        last++;
        }
    
    public int getLength(){
        return last;
        }
    @Override
    public Iterator<Book> iterator(){
        return new BookShelfIterator(this);
        }
    }
```
- `books` 필드를 `private`로 지정하여 이 클래스 밖에서 접근하는 것을 방지
- `iterator` 메소드로 `BookShelfIterator` 클래스의 인스턴스를 생성하여 반환함

### 5. BookShelfIterator 클래스

```java
import java.util.NoSuchElementException;

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
    if (!hasNext()) {
      thorw new NoSuchElementException();
    }
    Book book = bookShelf.getBookAt(index);
    index++;
    return book;
  }
}
```
- `hasNext` 는 `Iterator<Book>` 인터페이스에서 선언된 메소드를 구현
  - 다음 책이 있는지 없는지 조사
- `next` 는 책을 반환하고 다음으로 진행시킴
  - 반환값으로 돌려 줄 책을 `book` 변수에 저장해두고, index를 다음으로 진행시킨 후 book return

### 6. Main 클래스
```java
// 명시적으로 Iterator를 사용하는 방법
Iterator<Book> it = bookShelf.iterator();
while(it.hasNext()){
    Book book = it.next();
    System.out.println(book.getName());
        }

// 확장 for문을 사용하는 방법
for(Book book : bookShelf){
    System.out.println(book.getName());
        }
```

- **명시적으로 Iterator를 사용**
  - `it`이 책장을 검색할 때 사용할 `Iterator<Book>`의 인스턴스
  - `while`문 조건에 `it.hasNext()`라고 쓰면, 검색하지 않은 책이 남아 있는 한 `while`문의 루프가 돌아감
- **확장 for문을 사용**
  - 일반적으로 `java`의 확장 for문은 `Iterable` 인터페이스를 구현한 클래스의 인스턴스에 대해 내부적으로 `Iterator`을 사용하여 처리
  - 결국, `java`의 확장 for문 배후에서는 `Iterator`패턴이 사용 됨

## Iterator 패턴의 요소
### Iterator(반복자)
- 요소를 순서대로 검색하는 인터페이스(API)를 결정함
- 예제 프로그램에서는 `Iterator<E>` 인터페이스가 이 역할을 맡아서 다음 요소가 존재하는지 조사하는 `hasNext` 메소드, 다음 요소를 가져오는 `next`메소드를 결정

### Concretelterator(구체적인 반복자)
- `Iterator`가 결정한 인터페이스(API)를 실제로 구현
- 예제의 `BookShelfIterator` 클래스가 이 역항르 맡았ㅇ음
- 검색에 필요한 정보를 가지고 있어야 함
- `BookShelf` 클래스의 인스턴스를 `bookshelf` 필드에서 기억하고 검색중인 책을 `index` 필드에서 기억함

### Aggregate(집합체)
- `Iterator`을 만들어 내는 인터페이스(API)를 결정
- **내가 가진 요소를 차례로 검색해주는 사람**을 만들어 냄
- 예제의 `Iterable<E>` 인터페이스가 이 역할을 맡아 `iterator` 메소드를 결정함

### ConcreteAggregate(구체적인 집합체)
- `Aggregate`가 결정한 인터페이스(API)를 실제로 구현
- `Concretelterator`의 인스턴스를 만들어 냄
- 예제의 `BookShelf` 클래스


## Iterator 패턴을 사용해야 하는 이유
##### `Iterator`를 사용함으로써 구현과 분리하여 반복할 수 있기 때문이다
```java
while(it.hasNext()){
    Book book = it.next();
    System.out.println(book.getName());
        }
```
여기서 사용한 것은 `hasNext`와 `next`라는 `Iterator`의 메소드뿐이다. `BookShelf` 구현에 사용된 메소드는 호출되지 않는다.   
결국, **`while` 루프는 `BookShelf` 구현에 의존하지 않는다**
> **디자인 패턴은 클래스 사용의 재사용을 촉진하게 됨**

## 추상 클래스와 인터페이스
- 결합을 약화하고 클래스를 부품으로 재사용하기 쉽게 하고자 추상 클래스나 인터페이스를 도입하는 것
- 즉. 구체적인 클래스로만 프로그래밍하는 것이 아니라 **추상 클래스나 인터페이스를 사용하여 프로그래밍한다**

## Aggregate와 Iterator
- `Iterable<E>`와 `Iterator<E>`라는 두개의 인터페이스는 짝을 이룸

## 복수의 Iterator
- **현재 어디까지 조사했는지 기억하는 구조를 Aggregate 역할 외부에 두는 것**이 `Iterator` 패턴의 특징 중 하나임
- 이러한 특징에 따라 하나의 `ConcreteAggregate` 역할에 대해 여러 개의 `ConcreteIterator` 역할을 만들 수 있음

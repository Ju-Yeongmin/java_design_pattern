# part 8 : Abstract Factory 패턴 - 관련 부품을 조합하여 제품을 만든다

## Abstract Factory 패턴이란?
- 객체지향에서 `추상적`의 의미는 구체ㅔ적으로 어떻게 구현되어 있는지 생각하지 않고 인터페이스(API)에만 주목하는 상태
- `Abstract Factory` 에서는 부품의 구체적인 구현에는 주목하지 않고 인터페이스(API)에 주목함, 그리고 그 인터페이스만 사용해서 부품을 조립하고 제품으로 완성

## 예제 프로그램
| 패키지          | 이름      | 설명                                                |
|--------------|---------|---------------------------------------------------|
| factory      | Factory | 추상적인 공장을 나타내느 클래스(Link, Tray, Page를 만듦)           |
| factory      | Item    | Link와 Tray를 통일적으로 다루기 위한 클래스                      |
| factory      | Link    | 추상적인 부품 : HTML 링크를 나타내는 클래스                       |
| factory      | Tray    | 추상적인 부품 : Link나 Tray를 모은 클래스                      |
| factory      | Page    | 추상적인 제품 : HTML 페이지를 나타내는 클래스                      |
| 이름없음    | Main    | 동작 테스트용 클래스                                       |
| listfactory  | ListFactory | 구체적인 공장을 나타내는 클래스(ListLink,ListTray,ListPage를 만듦) |
| listfactory  | ListLink | 구체적인 부품 : HTML 링크를 나타내는 클래스                       |
| listfactory  | ListTray | 구체적인 부품 : Link나 Tray를 모은 클래스                      |
| listfactory  | ListPage | 구체적인 제품 : HTML 페이지를 나타내는 클래스                      |


### 추상적인 부품 : Item 클래스
```java
package factory;
public abstract class Item{
    protected String caption;
    public Item(String caption){
        this.caption = caption;
    }
    
    public abstract String makeHTML();
}
```
- `Item` 클래스는 `Link`와 `Tray`의 상위 클래스
  - `Link`와 `Tray`를 동일시 하기 위한 클래스
- `makeHTML` 메소드는 추상 메소드이므로, 하위 클래스에서 구현해야만함 

### 추상적인 부품 : Link 클래스
```java
package factory;

public abstract class Link extends Item{
    protected String url;
    
    public Link(SString caption, String url){
        super(caption);
        this.url = url;
    }
}
```

- `Link` 클래스에서는 상위 클래스(Item)의 추상 메소드(makeHTML)를 구현하지 않았기 때문에 `Link` 클래스도 추상 클래스가 됨

### 추상적인 부품 : Tray 클래스
```java
package factory;

import java.util.*;

public abstract class Tray extends Item{
    protected List<Item> tray = new ArrayList<>();
    
    public Tray(String caption){
        super(caption);
    }
    
    public void add(Item item){
        tray.add(item);
    }
}
```
- `add` 메소드에서는 `Tray`의 상위 클래스인 `Item`을 이수로 받음
  - `Link`나 `Tray`는 `add`메소드를 사용하기 때문
- `Tray` 클래스도 `Item`클래스의 추상 메소드  `makeHTML`을 상속받지만 구현하지는 않기 때문에 추상 클래스가 됨

### 추상적인 제품 : Page 클래스

```java
package factory;

import java.io.*;
import java.nio.*;
import java.nio.file.StandardOpenOption;
import java.util.*;

public abstract class Page {
    protected String title;
    protected String author;
    protected List<Item> content = new ArrayList<>();

    public Page(String title, String author) {
        this.title = title;
        this.author = author;
    }

    public void add(Item item) {
        content.add(item);
    }

    public void output(String filename) {
        try {
            Files.writeString(Path.of(filename), makeHTML(),
                    StandardOpenOption.CREATE,
                    StandardOpenOption.TRUNCATE_EXISTING,
                    StandardOpenOption.WRITE);
            System.out.println(filename + " 파일을 작성했습니다.");
        } catch (IOException e){
            e.printStackTrace();
        }
    }
    public abstract String makeHTML();
}
```

- 페이지에는 `add` 메소드를 상용해 `Item` 즉 `Link` 또는 `Tray`을 추가
- `output` 메소드 안에서는 제목을 바탕으로 파일명을 결정
- `makeHTML` 메소드를 사용해 자신의 HTML 내용을 파일에 기록함

### 추상적인 공장 : Factory 클래스
```java
package factory;

public abstract class Factory{
    public static Factory getFactory(String classname){
        Factory factory = null;
        
        try{
            factory = (Factory) Class.forName(classname).getDeclaredConstructors().newInstance();
        } catch (ClassNotFoundException e){
          System.out.println(classname + " 클래스가 발견되지 않았습니다.");
        } catch (Exception e){
            e.printStackTrace();
        }
        return factory;
    }
    
    public abstract Link createLink(String caption, String url);
    public abstract Tray createTray(String caption);
    public abstract Page createPage(String title, String author);
}
```
- `getFactory` 안에서는 `Class` 클래스의 `forName` 메소드를 사용하여 해당 클래스를 동적으로 가져옴
- `getDeclaredConstructor` 메소드로 생성자를 얻고 `newInstance`메소드로 인스턴스를 만듬
- 클래스나 생성자와 같은 프로그램의 구성 요소를 컴파일러가 다루지 않고 프로그램 자신이 다루고 있음
  - 일반적으로 `Reflection` 이라고 부름
- `getFactory` 메소드에서는 구체적인 공장의 인스턴스를 만들지만, 반환값의 타입은 추상적인 공장(Factory)
- `createLink`, `createTray`, `createPage` 메소드는 추상적인 공장에서 부품이나 제품을 작성할 때 이용하는 메소드
  - 모두 추상 메소드로 되어있고, 실제 구체적인 부품이나 제품 작성은 `Factory`의 하위 클래스에 맡기고 있음

### 공장을 사용해서 부품을 조합하고 제품을 만든다 : Main 클래스
```java
import factory.*;

public class Main{
    public static void main(String[] args){
        if(args.length != 2){
          System.out.println("Usage: java Main filename.html class.name.of.concreteFactory");
          System.out.println("Example 1: java Main list.html listfactory.ListFactory");
          System.out.println("Example 2: java Main div.html divfactory.DivFactory");
          System.exit(0);
        }
        
        String filename = args[0];
        String classname = args[1];
        
        Factory factory = Factory.getFactory(classname);
        
        //Blog
        Link blog1 = factory.createLink("Blog 1", "https://example.com/blog1");
        Link blog2 = factory.createLink("Blog 2", "https://example.com/blog2");
        Link blog3 = factory.createLink("Blog 3", "https://example.com/blog3");
        
        Tray blogTray = factory.createTray("Blog Site");
        blogTray.add(blog1);
        blogTray.add(blog2);
        blogTray.add(blog3);
        
        //News
        Link news1 = factory.createLink("News 1", "https://example.com/news1");
        Link news2 = factory.createLink("News 2", "https://example.com/news2");
        Tray news3 = factory.createTray("News 3");
        news3.add(factory.createLink("News 3 (US)", "https://example.com/news3us"));
        news3.add(factory.createLink("News 3 (Korea)", "https://example.com/news3kr"));
        
        Tray newsTray = factory.createTray("News Site");
        newsTray.add(news1);
        newsTray.add(news2);
        newsTray.add(news3);
        
        //Page
        Page page = factory.createPage("Blog and News", "Minji.com");
        page.add(blogTray);
        page.add(newsTray);
        
        page.output(filename);
    }
}
```

### 구체적인 공장 : ListFactory 클래스
```java
package listfactory;

import factory.*;

public class ListFactory extends Factory{
    @Override
    public Link createLink(String caption, String url){
        return new ListLink(caption, url);
    }
    
    @Override
    public Tray createTray(String caption){
        return new ListTray(caption);
    }
    
    @Override
    public Page createPage(String title, String author){
        return new ListPage(title, author);
    }
}
```

### 구체적인 부품 : ListLink 클래스
```java
package listfactory;

import factory.Link;

public class ListLink extends Link{
    public ListLink(String caption, String url){
        super(caption, url);
    }
    
    @Override
    public String makeHTML(){
        return " <li><a href=\"" + url + "\">" + caption + "</a></li>\n";
    }
}
```
- 상위 클래스에서 추상 메소드였던 `makeHTML`을 구현함

### 구체적인 부품 : ListTray 클래스
```java
package listfactory;

import factory.Tray;
import factory.Item;

public class ListTray extends Tray{
    public ListTray(String caption){
        super(caption);
    }
    
    @Override
    public String makeHTML(){
        StringBuilder sb = new StringBuilder();
        sb.append("<li>\n");
        sb.append(caption);
        sb.append("\n<ul>\n");
        for(Item item : tray){
            sb.append(item.makeHTML());
        }
        sb.append("</ul>\n");
        sb.append("</li>\n");
        return sb.toString();
    }
}
```
- 개개의 `Item`을 `HTML`로 만드는 방법으로 개별 `Item`의 `makeHTML`을 호출해주면 됨
  - 이때, for문 안의 변수 `item`의 내용이 실제로 무엇인지 조사해서 switch문이나 if문을 사용하는 프로그램을 작성해서는 안됨(비객체지향적)

### 구체적인 제품 : ListPage 클래스
```java
package listfactory;

import factory.Item;
import factort.Page;

public class ListPage extends Page{
    public ListPage(String title, String author){
        super(title, author);
    }
    
    @Override
    public String makeHTML(){
        StringBuilder sb = new StringBuilder();
        sb.append("<!DOCTYPE html>\n");
        sb.append("<html><head><title>");
        sb.append(title);
        sb.append("</title></head>\n");
        sb.append("<body>\n");
        sb.append("<h1>");
        sb.append(title);
        sb.append("</h1>\n");
        sb.append("<ul>\n");
        for(Item item : content){
            sb.append(item.makeHTML());
        }
        sb.append("</ul>\n");
        sb.append("<hr><address>");
        sb.append(author);
        sb.append("</address>\n");
        sb.append("</body></html>\n");
        return sb.toString();
    }
}
```

## 예제 프로그램에 다른 구체적인 공장 추가하기
| 패키지        | 이름         | 설명                                               |
|------------|------------|--------------------------------------------------|
| divfactory | DivFactory | 구체적인 공장을 나타내는 클래스(DivLink, DivTray, DivPage를 만듦) |
| divfactory | DivLink    | 구체적인 부품 : HTML 링크를 나타내는 클래스                      |
| divfactory | DivTray    | 구체적인 부품 : DivLink와 DivTray를 모은 클래스               |
| divfactory | DivPage    | 구체적인 제품 : HTML 페이지를 나타내는 클래스                     |

### 구체적인 공장 : DivFactory 클래스
```java
package divfactory;

import factory.Factory;
import factory.Link;
import factory.Page;
import factory.Tray;

public class DivFactory extends Factory{
    @Override
    public Link createLink(String caption, String url){
        return new DivLink(caption, url);
    }
    
    @Override
    public Tray createTray(String caption){
        return new DivTray(caption);
    }
    
    @Override
    public Page createPage(String title, String author){
        return new DivPage(title,author);
    }
}
```


### 구체적인 부품 : DivLink 클래스
```java
package divfactory;

import factory.Link;

public class DivLink extends Link{
    public DivLink(String caption, String url){
        super(caption, url);
    }
    
    @Override
    public String makeHTML(){
        return "<div class=\"LINK\"><a href=\"" + url + "\">" + caption + "</a></div>\n";
    }
}
```

### 구체적인 부품 : DivTray 클래스
```java
package divfactory;

import factory.Item;
import factory.Tray;

public class DivTray extends Tray{
    public DivTray(String caption){
        super(caption);
    }
    
    @Override
    public String makeHTML(){
        StringBuilder sb = new StringBuilder();
        sb.append("<p><b>");
        sb.append(caption);
        sb.append("</b></p>\n");
        sb.append("<div class=\"TRAY\">");
        for (Item item : tray){
            sb.append(item.makeHTML());
        }
        sb.append("</div>\n");
        return sb.toString();
    }
}
```

### 구체적인 제품 : DivPage 클래스
```java
package divfactory;

import factory.Item;
import factort.Page;

public class DivPage extends Page{
    public DivPage(String title, String author){
        super(title, author);
    }
    
    @Override
    public String makeHTML(){
        StringBuilder sb = new StringBuilder();
        sb.append("<!DOCTYPE html>\n");
        sb.append("<html><head><title>");
        sb.append(title);
        sb.append("</title></style>\n");
        sb.append("div.TRAY { padding:0.5em; margin-left:5em; border:1px solid black; }\n");
        sb.append("div.LINK { padding:0.5em; background-color: lightgray; }\n");
        sb.append("</style></head><body>\n");
        sb.append("<h1>");
        sb.append(title);
        sb.append("</h1>\n");
        for(Item item : content){
            sb.append(item.makeHTML());
        }
        sb.append("<hr><address>");
        sb.append(author);
        sb.append("</address>\n");
        sb.append("</body></html>\n");
        return sb.toString();
    }
}
```


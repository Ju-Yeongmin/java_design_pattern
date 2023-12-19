# abstract factory

- factory method
 >> 객체를 생성하는 class

- abstract factory
 >> 관련된 객체들을 묶어 factory class로 만드는데 이 factory class를 조건에 따라 생성하는 factory

# 예제

Item class
  
```java
package factory;

public abstract class Item {
    protected String caption;

    public Item(String caption) {
      this.caption = caption;
    }

    public abstract String makeHTML();
}
```

---

Link class
  
```java
package factory;

public abstract class Link extends Item {
    protected String url;

    public Link(String caption, String url) {
        super(caption);
        this.url = url;
    }
}
```

---

Tray class

```java
package factory;

import java.util.ArrayList;
import java.util.List;

public abstract class Tray extends Item {
    protected List<Item> tray = new ArrayList<>();

    public Tray(String caption) {
        super(caption);
    }

    public void add(Item item) {
        tray.add(item);
    }
}
```

---

Page class

```java
package factory;

import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.StandardOpenOption;
import java.util.ArrayList;
import java.util.List;

public abstract class Page {
    protected String title;
    protected String author;
    protected List<Item> content = new ArrayList<>();

    public Page(String title. String author) {
        this.title = title;
        this.author = author;
    }

    public void add(Item item) {
        content.add(item);
    }

    public void output(String filename) {
        try {
            File.writeString(Path.of(filename), makeHTML(),
                              StandardOpenOption.CREATE,
                              StandardOpenOption.TRUNCATE_EXISTING,
                              StandardOpenOption.WRITE);
            System.out.println(filename + " file 작성");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public abstract String makeHTML();
}
```

---

Factory class

```java
package factory;

public abstract class Factory {
    public static Factory getFactory(String classname) {
        Factory factory = null;

        try {
            factory = (Factory)Class.forName(classname).getDeclaredConstructor().newInstance();
        } catch (ClassNotFoundException e){
            System.out.println(classname + " class가 발견되지 않음");
        } catch (Exception e) {
            e.printStackTrace();
        }

        return factory;
    }

    public abstract Link createLink(String caption, String url);
    public abstract Tray createTray(String caption);
    public abstract Page createPage(String title, String author);
}
```

---

Main class

```java
import factory.*;

public class Main {
    public static void main(String[] args) {
        if(args.length != 2) {
            System.out.println("~~~");
            System.exit(0);
        }

        String filename = args[0];
        String classname = args[1];

        // blog
        Link blog1 = factory.createLink(" blog 1 ");
        Link blog2 = factory.createLink(" blog 2 ");

        Tray blogTray = factory.createTray("blog site");
        blogTray.add(blog1);
        blogTray.add(blog2);

        // news
        Link news1 = factory.createLinik(" news 1 ");

        Tray newsTray = factory.createTray("blog site");
        blogTray.add(news);

        // page
        // ~~~

        page.output(filename);
    }
}
```

---

ListFactory class

```java
package listfactory;

import factory.Factory;
import factory.Link;
import factory.Page;
import factory.Tray;

public class ListFacory extends Factory {
    @Override
    public Link createLink(String caption, String url) {
        return new ListLink(caption, url);
    }

    @Override
    public Tray createTray(String caption) {
        return new ListTray(caption);
    }

    @Override
    public Page createPage(String title, String author) {
        return new ListPage(title, author);
    }
}
```

---

ListLink class

```java
package listfactory;

import factory.Link;

public class ListLink extends Link {
    public ListLink(String caption, String url){
        super(caption, url);
    }

    @Override
    public String makeHTML() {
        return " html 관련 code ~~ ";
    }
}
```

---

ListTRay class

```java
package listfactory;

import factory.Tray;
import factory.Item;

public class ListTray extends Tray {
    public ListTray(String caption) {
        super(caption);
    }

    @Override
    public String makeHTML() {
        StringBuilder sdb = new StrinbBuilder();
        sb.append("  html 관련 code1 ");
        sb.append("  html 관련 code2 ");
        for(Item item : tray) {
            sb.append("  html 관련 code ");
        }
        return sb.toString();
    }
}
```

---

ListPage class

```java
package listfactory;

import factory.Item;
import factory.Page;

public class ListPage extends Page {
    public ListPage(String title, String author) {
        super(title, author);
    }

    @Override
    public String makeHTML() {
        StringBuilder sdb = new StrinbBuilder();
        sb.append("  html 관련 code1 ");
        sb.append("  html 관련 code2 ");
        for(Item item : tray) {
            sb.append("  html 관련 code ");
        }
        return sb.toString();
    }
}
```

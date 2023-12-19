# builder

factory pattern = 프로토타입의 인스턴스가 생성할 객체의 타입을 정하면서 새 객체들을 복사할 수 있다

# 예제

Builder
  
```java
public abstract class Builder {
    public abstract void makeTitle(String title);
    public abstract void makeString(String str);
    public abstract void makeItems(String[] items);
    public abstract void close();
}
```

---
  
Director
  
```java
public class Director {

    private Builder builder;
    
    public Director(Builder builder) {
        this.builder = builder;
    }

    public void construct() {
      builder.makeTitle("Greeting");
      builder.makeString("hi");
      builder.makeItems(new String[]{
          "hi1";
          "hi2";
      });
  
      builder.makeString("시간대별인사");
      builder.makeItems(new String[]{
          "아침";
          "점심";
          "저녁";
      });
      builder.close();
    }
}
  
    
  
}
```

---
  
TextBuilder
  
```java
public class TextBuilder extends Builder{
    private StringBuilder sb = new StringBuilder();

    @Override
    public void makeTitle(String title) {
        sb.append("-------\n");
        sb.append("[");
        sb.append(title);
        sb.append("]\n\n");
    }

    @Override
    public void makeString(String str) {
        sb.append("ㅁ");
        sb.append(str);
        sb.append("\n\n");
    }

    @Override
    public void makeItems(String[] items) {
        for(String s : items){
          sb.append(" .");
          sb.append(s);
          sb.append("\n");
        }
        sb.append("\n");
    }

    @Override
    public void close() {
        sb.append("-------\n");
    }

    public String getTextResult() {
        return sb.toString();
    }
}
  
```

---
  
HTMLBuilder

```java
import java.io.*

public class HTMLBuilder extends Builder {
    private String filename = "intitled.html";
    private StringBuidler sb = new StringBuilder();

    @Override
    public void makeTitle(String title) {
        filename = title + ".html";
        sb.append("<html ~~~ >\n");
        // html code
    }

    @Override
    public void makeString(String str) {
        // html code
    }

    @Override
    public void makeItems(String[] items) {
        // html code
    }

    @Override
    public void close() {
        // html code
        try {
            Writer wrtier = new FileWriter(filename);
          writer.wrtie(sb.toString());
          writer.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public String getHTMLResult() {
        return filename;
    }
}

---
  
Main

```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;

public class Main() {
    public static void main(String[] args) {
        if(args.length != 1) {
            usage();
            System.exit(0);
        }
        if(args[0].equals("text")) {
            TextBuilder textbuilder = new TextBuilder();
            Director director = new Director(textbuilder);
            director.construct();
            String result = textbuilder.getTextResult();
            System.out.println(result);
        }
        else if(args[0].equals("html")) {
            HTMLBuilder = htmlbuilder = new HTMLBuilder();
            Director director = new Director(htmlbuilder);
            director.construct();
            String filename = htmlbuoilder.getHTMLResult();
            Sytstem.out.println("HTML file " + filename + "작성 됨");
        }
        else{
            usage();
            System.exit(0);
        }
    }

    public static void usage() {
        System.out.println("Usage : java main text 텍스트로 문서 작성");
        System.out.println("Usage : java main text html로 문서 작성");
    }
}

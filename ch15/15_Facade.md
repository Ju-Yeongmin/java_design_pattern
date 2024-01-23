# Ch.15 Facade 패턴
단순한 창구를 만든다

<br>

## Facade 패턴이란?
프로그램을 만들다보면 점점 커지고 복잡해지기 마련이다. 비대해진 프로그램의 다양한 클래스들을

적절하게 제어하기 위해서는 처리를 위한 '창구'를 따로 준비하는 것이 좋다

그렇게 하면 많은 클래스를 개별적으로 제어하지 않아도 '창구'에 요청만 하면 끝나기 때문

여기서 '창구'의 역할을 하는 것이 Facade 패턴 (파사드 라고 읽으며 건물의 정면 이라는 뜻)

<br>

## 예제 프로그램
위에서 설명한 바와 같이 Facade 패턴의 예제는 원래 복잡하게 얽힌 많은 클래스가 있을때 적합하지만

예제 프로그램을 짧게하고자 3개의 클래스로만 이루어진 간단한 시스템으로 구현

이메일 주소에서 이름을 구하는 데이터베이스, HTML 파일을 작성하는 클래스, Facade 역으로서

높은 수준의 인터페이스(API)를 제공하는 클래스로 구성

<br>

### 1. Database 클래스
데이터베이스 이름을 지정하여 대응하는 Properties를 생성하는 클래스

Properties의 인스턴스를 만들지 않고 getProperties라는 static 메서드를 통해 Properties의 인스턴스를 얻는다.


```java
public class Database {
  private Database() {
  }
  // 데이터베이스 이름에서 Properties를 얻음
  public static Properties getProperties(String dbname) throws IOException {
    String filename = dbname + ".txt";
    Properties prop = new Properties();
    prop.load(new FileReader(filename));
    return prop;
  }
}
```

```java
// 데이터 파일 (maildata.txt)
hyuki@example.com = Hiroshi Yuki
ym@gmail.com = yeongmin
hj@gmail.com = hyunjun
hg@gmail.com = honggi
```

### 2. HTML Writer 클래스
간단한 웹 페이지를 만드는 클래스

인스턴스 생성시 Writer를 주고 그 Writer에 HTML을 출력

```java
public class HtmlWriter {
  private Writer writer;

  public HtmlWriter(Writer writer) {
    this.writer writer;
  }

  // 타이틀 출력
  public void title(String title) throws IOException {
    writer.write("<!DOCTYPE html>");
    writer.write("<html>")
    writer.write("<head>")
    ... 중략
  }

  // 단락 출력
  public void paragraph(String msg) throws IOException {
    writer.write("<p>" + msg + "</p>")
  }

  ... 중략

  //HTML 닫기
  public void close() throws IOException {
    writer.write("</body>")
    writer.write("</html>")
    writer.close();
  }
}
```

### 3. PageMaker 클래스
Database 클래스와 HtmlWriter 클래스를 조합해서 웹 페이지를 만드는 클래스

이 클래스에서 정의된 public 메서드는 makewWelcomePage 밖에 없는데

HtmlWriter 클래스의 메서드를 호출하는 복잡한 부분을 PageMaker 클래스가 도맡고,

외부에는 makeWelcomePage 메서드 단 하나만 보여줌 -> 이곳이 단순한 창구 역할

```java
public class PageMaker {
  private PageMaker() {
  }  

  public static void makeWelcomePage(String mailaddr, String filename) {
    try {
      Properties mailprop = Database.getProperties("maildata");
      String username = mailprop.getProperty(mailaddr);
      HtmlWriter writer = new HtmlWriter(new FileWriter(filename));
      writer.title(username + "'s web page");
      writer.paragraph("welcome to " + username + "'s web page!")
      .. 중략
      writer.close();
    } catch (IOException e) {
      // 에러처리
    }
  }
}
```

### 4. Main 클래스
메인 클레스에서는 PageMaker 클래스(창구)만을 간단하게 사용

```java
public class Main {
  public static void main(String[] args) {
    PageMaker.makeWelcomePage("ym@gmail.com", "welcome.html");
  }
}
```

<br>

위 코드를 실행하면 <br>
![image](https://github.com/Ju-Yeongmin/java_design_pattern/assets/110506500/3831b589-bb84-46a8-867c-b8f449872acd)

<br>

브라우저로 확인하면 <br>
![image](https://github.com/Ju-Yeongmin/java_design_pattern/assets/110506500/e953d146-98a9-4e03-8791-650392f39498)



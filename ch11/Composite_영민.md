# 11.Composite 패턴 - 그릇과 내용물을 동일시 한다
![image](https://github.com/Ju-Yeongmin/java_design_pattern/assets/110506500/ba099f78-dc2b-479e-bb3d-35683e7fede9)



### 그릇과 내용물을 같은 종류로 취급하면 편리한 경우가 있다!
 - 컴퓨너 파일 시스템에는 디렉토리(폴더)가 있고, 디렉토리 안에는 파일이나 다른 디렉토리가 들어감
    - 이러한 디렉토리는 중첩된 구조, 즉 재귀적인 구조를 만듦
 - 디렉토리와 파일을 합쳐서 '디렉토리 엔트리'라고 함
    - 즉, 디렉토리와 파일을 같은 종류로 동일시하는 것
    - 예를들어 디렉토리 안에 무엇이 있는지 차례로 조사한다면 이때 조사하는 것이 하위 디렉토리일수도 파일일수도 있음
    - 한마디로 '디렉토리 엔트리'를 차례로 조사한다는 뜻
 - 이처럼 그릇과 내용물을 같은 종류로 취급하면 편리한 경우가 있음
    - 그릇안에 내용물을 담을수도 더 작은 그릇을 담을 수도 있기때문에 중첩 / 재귀적인 구조를 만들 수 있기 때문

  `이장에서 학습할 Composite 패턴은 이러한 구조를 만들기 위한 것 (그릇과 내용물을 동일시하여 재귀적인 구조를 만드는 패턴)`

<br>
<br>
  
## 예제 프로그램

![image](https://github.com/Ju-Yeongmin/java_design_pattern/assets/110506500/49116e68-94be-43cf-a5ce-21b4d33ecabd)

### 1. Entity 클래스
추상 클래스이고 디렉터리 엔트리를 표현. 이 하위 클래스로 File 클래스와 Directory 클래스가 만들어짐
- 디렉토리 엔트리는 이름과 크기를 가짐
- printList는 목록을 표시하는 메서드

getName, getSize
```java
publi abstract class Entry {
  // 이름을 얻는다
  public abstract String getName();

  // 크기를 얻는다
  public abstract int getSize();

  public void printList() {
    printList("");
  }

  // printList() 메서드 오버로딩
  // protected로 Entry의 하위 클래스에서만 사용할 수 있음
  protected abstract void printList(String prefix);

  // 하위 클래스에서 getName과 getSize를 구현하기를 기대하며 toString에서 호출 (Template Method 패턴)
  @Override
  public String toString() {
    return getName() + "(" + getSize() + ")";
  }
}
```

<br>

### File 클래스
파일을 표현하는 클래스. Entry의 하위 클래스
- 파일 이름을 나타내는 name, 크기를 나타내는 size 필드가 있음
- printList(String prefix) 메서드들 구현

```java
public class File extends Entry {
  private String name;
  private int size;

  public File(String name, int size) {
    this.name = name;
    this.size = size;
  }

  @Override
  public String getName() {
    return name;
  }

  @Override
  public int getSize() {
    return size;
  }

  @Override
  protected void printList(String prefix) {
    System.out.println(prefix + "/" + this);
    // == System.out.println(prefix + "/" + this.toString());
    // == System.out.println(prefix + "/" + toString());
  }
}
```

<br>

### 3. Directory 클래스
디렉터리를 표현하는 클래스. Entry이 하위 클래스
 - directory, name 필드가 있음 (크기를 나타내는 필드는 없음 -> 동적으로 크기를 계산)
 - directory는 디렉토리 엔트리를 보관해두는 필드

```java
public class Directory extends Entry {
  private String name;
  private List<Entry> directory = new ArrayList<>();

  public Directory(String name) {
    this.name = name;
  }

  @Override
  public String getName() {
    this.name = name;
  }

  @Override
  public int getSize() {
    int size = 0;
    for (Entry entry: directory) {
      size += entry.getSize();
    }
    return size;
  }

  @Override
  protected void printList() {
    System.out.println(prefix + "/" + this);
    for (Entry entry: directory) {
      entry.printList(prefix + "/" + name);
    }
  }

  // 디렉토리에 디렉토리 엔트리 추
  public Entry add (Entry entry) {
    directory.add(entry);
    return this;
  }
}
```

<br>

### 4. Main 클래스

```java
public class Main {
  public static void main(String[] args) {
    System.out.println("Making root entries...");

    // root, bin, tmp, usr 디렉토리 생성
    Directory rootdir = new Directory("root");
    Directory bindir = new Directory("bin");
    Directory tmpdir = new Directory("tmp");
    Directory usrdir = new Directory("usr");

    // root 아래에 bin, tmp, usr
    rootdir.add(bindir)
    rootdir.add(tmpdir)
    rootdir.add(usrdir)

    // bin 아래에 vi, latex
    bindir.add(new File("vi", 10000));
    bindir.add(new File("latex", 20000));

    rootdir.printList();



    System.out.println("Making user entries...")

    // 영민, 서빈, 영진 디렉토리 생성
    Directory yeongmin = new Directory("yeongmin")
    Directory seobin = new Directory("seobin")
    Directory yeongjin = new Directory("yeongjin")

    // usr 아래에 영민, 서빈, 영진
    userdir.add(yeongmin)
    userdir.add(seobin)
    userdir.add(yeongjin)

    yeongmin.add(new File("diary.html", 100));
    yeongmin.add(new File("Composite.java", 200));
    seobin.add(new File("memo.tex", 300));
    yeongjin.add(new File("game.doc", 400));
    yeongjin.add(new File("junk.mail", 500));
    
    
  
  }
}
```

![image](https://github.com/Ju-Yeongmin/java_design_pattern/assets/110506500/83d3407c-1b7c-4efd-9a4e-923632928297)

<br>

![image](https://github.com/Ju-Yeongmin/java_design_pattern/assets/110506500/9e2cea46-7dc6-47d6-ba5b-45f1cc082b5b)

<br>

![image](https://github.com/Ju-Yeongmin/java_design_pattern/assets/110506500/11e05718-9195-4470-8b43-4c4a26e5a934)

---

### 등장인물
![image](https://github.com/Ju-Yeongmin/java_design_pattern/assets/110506500/87b7f3b4-b77a-4a27-a531-27be81f7fc42)

<br>

### 사고를 넓혀 주는 힌트
#### 1. 복수와 단수 동일시하기
Composite 패턴은 복수와 단수의 동일시로 부를 수도 있음. 즉, 여러 개를 모아서 마치 하나의 것처럼 취급하는 것

예를들어 많은 도형을 그리거나 변형하는 그래픽 툴을 만든다면 삼각형이나 원 등 도형을 여러개 묶어 그룹을 만들때 Composite 패턴 활용 가능
복수의 도형을 모은 그룹도 개개의 도형처럼 편집하고 싶기 때문이다. 여러 그룹을 묶어서 더 큰 그룹을 만들 수도 있음

<br>

#### 2. add 메서드의 위치
예제에서는 디렉터리 엔트리를 추가하는 add 메서드를 Directory 클래스에 정의 했음.
디렉토리 엔트리를 추가할 수 있는 것은 디렉토리뿐이기 때문

만약 자식을 조작해야 하는 경우 Composite(Directory) 와 Component(Entry) 중 어느쪽에 정의를 하는게 좋을까에 대해 고민해보기
설계자는 클래스가 가져야할 메서드를 정하고 해당 클래스가 가져야 할 책무를 명확히 해야 함

<br>

#### 3. 재귀적 구조는 모든 장면에서 등장
프로그래밍을 하다보면 재귀적 구조 및 Composite 패턴은 곳곳에서 등장함
예를들어 매크로 명령을 만들 때 재귀적인 구조로 구현하면 매크로 명령의 매크로 명령을 만들 수 있음
또한 일반적인 트리 구조로 된 데이터 구조는 Composite 패턴에 해당당





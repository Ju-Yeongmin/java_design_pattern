# Adapter - 사이에 끼워 재사용 한다.

### 중요 단어 : 위임, 상속

## Adapter 패턴을 사용하는 이유
- 이미 존재하는 클래스를 이용하는 경우 재사용 하기 위함.<br>
- 왜? 해당 클래스가 충분히 사용되어 왔고, 버그가 거의 없다면 다시 재활용하기 좋을 것임.<br>
- 내 생각 : 비슷한 기능을 하는 클래스를 다시 만들기에는 시간이 많이 들지 않을까?<br>

<br>

## 그래서 Adapter 패턴이 뭔데?
- 사용하고 싶은 A라는 클래스를 포괄(?) 해줄 수 있는 B 클래스를 만들어서 사용한다.<br>
- 왜? B 클래스에서 A클래스의 기능을 사용할 수 있으니까.<br>
- 그럼 굳이 왜 덮지 A클래스를 그냥 바로 사용하면 안되나? <br>
- 내 생각 : A클래스의 필드들을 호출해서 사용하다보면 코드가 많아질 우려가 있을 것 같음. "사용 목적"을 명확히 하기 위함? <br>
- 또는, 내가 사용하려는 클래스나 인터페이스의 기능에 호환되지 않는 클래스들을 사용하려고 사용하지 않을까? <br>
- 포괄하다, 덮는다. 등등을 상징하는 Wrapper을 따와 Wrapper 패턴이라고도 함.<br>

``` java
예시 만드는 중.. 미완..
public class Main {

  public static void main(String[] args) {
    Combo cb = new ThreeCombo();
    Combo cb2 = new infiniteCombo();
  }

}

public interface Combo {
  public abstract void comboAttack();
}

public class ThreeCombo extends Skill {
  @Override
  public void comboAttack() {
    hand1();
    hand2();
    kick3();
  }
}

public class infiniteCombo extends Skill {
  @Override
  public void comboAttack() {
    hand1();
    hand2();
    hand1();
    kick3();
    hand2();
    hand1();
    kick3();
    head();
  }
}

public class Skill() {

  public void hand1() {
    System.out.println("정권 지르기");
  }

  public void hand2() {
    System.out.println("손날 치기");
  }

  public void hand3() {
    System.out.println("지건");
  }

  public void foot1() {
    System.out.println("코브라 킥");
  }

  public void foot2() {
    System.out.println("로우 킥");
  }

  public void foot3() {
    System.out.println("하이 킥");
  }

  public void head() {
    System.out.println("박치기");
  }

}

```

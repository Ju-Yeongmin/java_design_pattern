# Intro

Adapter 패턴은 우리가 마치 전기를 220V, 110V 등으로 변환해서 쓰는 것과 같이 코드를 필요한 형태로 변환해서 사용하는 방법을 말합니다.

`이미 제공 된 것` 과 `필요한 것` 사이의 갭을 메우는 패턴입니다.

흔히 Wrapper 라고도 부르는데, Wrapper의 뜻은 감싸는 것이죠. 우린 Java를 사용하면서 해당 패턴을 자주 볼 수 있습니다. 바로 `Integer`, `Long` 등의 원시타입 래퍼클래스입니다.

```java
Long x = 10L;
System.out.println(x.toString());
```

우리는 Wrapper 클래스를 통해 원시타입 데이터를 Nullable 하게 다룰 수도 있고, Object 클래스에서 상속받은 메소드들을 사용할 수 있습니다.

Adapter는 크게 두가지 종류가 있습니다.

- 클래스에 의한 Adapter 패턴 (상속을 사용)
- 인스턴스에 의한 Adapter 패턴 (위임을 사용)

# Example

```text
(Hello)
*Hello*
```

만약 Hello 라는 문자열을 다음과 같이 표시하기 위해선 어떻게 해야 할까요?

```java
public class Banner {
	private String string;

	public Banner(String string) {
		this.string = string;
	}

	public void showWithParen() {
		System.out.println("[" + string + "]");
	}

	public void showWithAster() {
		System.out.println("*" + string + "*");
	}
}
```

위와 같은 클래스가 있다고 가정 해 보겠습니다.

```java
public interface Print {
    public abstract void printWeak();
    public abstract void printStrong();
}
```

## 상속을 사용한 패턴

```java
public class PrintBanner extends Banner implements Print {
    public PrintBanner(String string) {
        super(string);
    }

    @Override
    public void printWeak() {
        showWithParen();
    }

    @Override
    public void printStrong() {
        showWithAster();
    }
}
```

```java
public class Main {
	public static void main(String[] args) {
		Print p = new PrintBanner("Hello");
		p.printWeak();
		p.printStrong();
	}
}
```

Banner 클래스, 그리고 Print 인터페이스를 상속해서 자연스럽게 `printWeak()`, `printString()` 메소드를 Banner 클래스의 메소드를 활용하여 구현 할 수 있습니다.

## 위임을 사용한 패턴

```java
public class PrintBanner extends Print {
	private Banner banner;
	public PrintBanner(String string) {
		this.banner = new Banner(string);
	}

	@Override
	public void printWeak() {
		banner.showWithParen();
	}

	@Override
	public void printStrong() {
		banner.showWithAster();
	}
}
```

```java
public class Main {
	public static void main(String[] args) {
		Print p = new PrintBanner("Hello");
		p.printWeak();
		p.printStrong();
	}
}
```

위와는 달리 Print 인터페이스를 통해서만 구현 한 케이스입니다. Banner 클래스를 따로 상속받는 게 아니라, Banner 인스턴스를 통해 메소드들을 구현하였습니다.

# Adapter 패턴의 등장인물

- Target
  - 필요한 메소드를 정의합니다.
  - Print 인터페이스가 해당 역할을 맡았습니다.
- Client
  - Target의 메소드를 활용 해 일합니다.
  - Main 클래스가 해당 역할을 맡았습니다.
- Adaptee
  - 적응 대상자로써, Banner 클래스가 해당 역할을 맡았습니다.
- Adapter
  - PrintBanner 클래스의 역할입니다. Adaptee의 메소드를 활용해서 Target을 만족시키는 역할입니다.

# 정리

Adapter 패턴을 쓰기 전에 왜 사용하는 지 고민 해 보셨나요? 사실 처음 사용하게 될 때는 당장 와닿지 않을 수 있습니다. '그냥 새로 프로그래밍 하는 게 더 낫지 않을까?' 하는 생각이 들 수도 있죠.

하지만, 개발을 하는 과정에서 매번 새로운 코드를 작성하게 된다면 개발 기간이 늘어날 뿐만 아니라 해당 코드의 검증 과정에 대한 비용이 발생하게 됩니다.

그러므로 이미 잘 작성되어서 충분히 검증 된 코드를 부품으로서 재사용 하기 위해 Adapter 패턴이 필요합니다. 기존의 코드를 전혀 수정하지 않고 현실 세계의 어댑터 처럼 Adapter 클래스를 통해 해당 코드를 사용할 수 있도록 해 주는 것이 Adapter 패턴 인 것입니다.

> 바퀴를 발명 할 필요는 없는 것과 일맥상통합니다. 전기가 워낙 잘 되어있다고 전기를 새로 만들어 (어감이 이상하네요) 쓰는 게 아니라, 어뎁터를 통해 우리가 쓸 수 있도록 변환해서 쓰는 샘이죠.

또한 기존의 소프트웨어를 버전 업 해야 하는 상황에서, 모든 소프트웨어의 소스코드를 갈아엎는 게 아니라 기존의 소스코드와의 하위호환을 지원하기 위해서도 Adapter 패턴을 사용합니다.

물론 Adaptee의 역할과 Target의 기능에 따라 Adapter 패턴을 사용하지 못 할수도 있습니다. 요구사항이 변경 된다면 결국 다시 코드를 작성 해서 개발을 진행해야겠지요.

# 실무 적용 예

책에서도 나오지만, 위임을 사용한 패턴이 안전한 편이라고 저 또한 생각합니다. 기존의 클래스를 온전히 그대로 사용하는 것이다 보니 직관적이기 때문입니다.

저는 보통 `Spring Security` 를 사용할 때 `UserDetails` Interface를 활용하여 Adapter 클래스를 개발하는 편입니다.

`Spring Security` 가 적용되었을 때 로그인 한 사용자 정보를 User 인스턴스를 통해 다시 찾아올 게 아니라, Adapter를 통해 유저의 정보 그 자체를 User 인스턴스와 함께 관리하도록 구현할 때 주로 사용합니다.

```java
@Getter
public class AccountInfoAdapter extends User {
  private AccountInfoDTO accountInfoDTO;
  public AccountInfoAdapter(AccountInfo accountInfo) {
    	super(accountInfo.getEmail(), accountInfo.getPassword(),
            List.of(new SimpleGrantedAuthority(accountInfo.getRole())));
        this.accountInfoDTO = AccountInfoDTO.from(accountInfo);
  }
}
```

```java
@Override
public UserDetails loadUserByUsername(String email) throws UsernameNotFoundException {
    AccountInfo accountInfo = accountInfoRepository.findByEmail(email);
    if (account == null) {
        throw new UsernameNotFoundException(email);
    }
    return new AccountInfoAdapter(account);
}
```

Adapter 패턴을 활용해서 User 에 대한 Adapter 클래스를 생성 해 둔다면, 이미 `loadUserByUsername` 메소드가 실행되는 과정에서 유저의 정보를 Repository 인스턴스를 통해 가져오게 됩니다.

```java
@GetMappting("/test")
public ResponseEntity<ApiResponse<TestResVO>> testApi(
    @AuthenticationPrincipal(expression = "accountInfoDTO") AccountInfoDTO accountInfoDTO
) {
    //매개변수로 전달 된 유저 DTO를 활용 한 로직 구현
}
```

해당 정보를 통해 인증 정보만 생성할 게 아니라 추후에 인증 정보를 조회할 때 유저의 정보 또한 조회할 수 있게 됩니다.
위의 예시는 Adapter를 통해 @AuthenticationPrincipal 어노테이션을 활용해 바로 인증을 요청한 유저 정보를 확인 하는 예시입니다.

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.PARAMETER)
@AuthenticationPrincipal(expression = "accountInfoDTO")
public @interface AuthUserDTO {
}
```

```java
@GetMappting("/test")
public ResponseEntity<ApiResponse<TestResVO>> testApi(
    @AuthUserDTO AccountInfoDTO accountInfoDTO
) {
    //매개변수로 전달 된 유저 DTO를 활용 한 로직 구현
}
```

만약 좀 더 코드를 줄이고 싶다면, 어노테이션으로 선언해서 관리할 수도 있습니다.

# strategy pattern

#### 어떤 문제를 해결할 때 원하는 해결 방법을 자유롭게 변경할 수 있게 해주는 패턴<br/><br/>
    
ex) 다른 지역으로 이동할 때 버스, 지하철, 비행기 등 다양한 방법이 있고<br/> 
사람은 자신이 원하는 방법으로 가면 됨<br/><br/>


어떤 동작을 할 때 선택지를 여러 개 만든 뒤 유저가 그 중 하나를 선택<br/>
여기서 선택지는 class가 되고 여러개의 class는 하나의 interface에 모여있음<br/>
유저가 interface에 어떤 class를 선택할 지 알려주면 된다<br/><br/>

#### 장점
1. 유연성 + 확장성 : 알고리즘( 각각의 class )을 독립적으로 정의 -> 교체가 쉽다<br/>
2. 코드 재사용     : -<br/>

#### 단점
1. class 증가 : 불필요한 class가 있을 수 있다<br/>
2. 복잡성 증가 : 사용하지 않는 경우에도 각각의 전략이 있어 단순한 알고리즘의 경우에도 오버헤드가 발생할 수 있음<br/>

# 교재 code ( 가위바위보 예시 )

Hand ( enum )

```java
public enum Hand {

    ROCK("바위", 0 ),
    SCISSORS("가위", 1),
    PAPER("보",2) ;

    private String name ;
    private int handvalue ;

    private static Hand[] hands = {
        ROCK, SCISSORS, PAPER
    } ;

    private Hand(String name, int handvalue) {
        this.name = name ;
        this.handvalue = handvalue ;

    }

    public static Hand getHand(int handvalue){
        return hands[handvalue] ;
    }

    public boolean isStrongerThan(Hand hand){
        return fight(hand) == 1 ;
    }

    public boolean isWeakerThan(Hand hand){
        return fight(hand) == -1 ;
    }

    private int fight(Hand hand){
        if(this == hand){
            return 0 ;
        }
        else if((this.handvalue+1)%3==hand.handvalue){
            return 1 ;
        }
        else{
            return -1 ;
        }
    }

    @Override
    public String toString(){
        return name ;
    }
}
```

---

Strategy ( interface )

```java
public interface Strategy {
    public abstract Hand nextHand() ;
    public abstract void study(boolean win) ;
}
```

---

WinningStrategy ( class )

```java
import java.util.Random ;

public class WinningStrategy implements Strategy{

    private Random random ;
    private boolean won = false ;
    private Hand prevHand ;

    public WinningStrategy(int seed){
        random = new Random(seed) ;
    }

    @Override
    public Hand nextHand() {
        if(!won){
            prevHand = Hand.getHand(random.nextInt(3)) ;
        }

        return prevHand ;
    }

    @Override
    public void study(boolean win) {
        won = win ;
    }
}
```

---

ProbStrategy ( class )

```java
import java.util.Random ;

public class ProbStrategy implements Strategy{

    private Random random ;
    private int prevHAndValue = 0 ;
    private int currentHandValue = 0 ;
    private int[][] history = {
        {1, 1, 1},
        {1, 1, 1},
        {1, 1, 1}
    } ;

    public ProbStrategy(int seed){
        random = new Random(seed) ;
    }

    @Override
    public Hand nextHand() {
        int bet = random.nextInt(getSum(currentHandValue)) ;
        int handvalue = 0 ;
        if(bet<history[currentHandValue][0]){
            handvalue = 0 ;
        } else if(bet<history[currentHandValue][0] + history[currentHandValue][1]){
            handvalue = 1 ;
        } else{
            handvalue = 2 ;
        }

        prevHAndValue = currentHandValue ;
        currentHandValue = handvalue ;
        return Hand.getHand(handvalue) ;
    }

    private int getSum(int handValue){
        int sum = 0 ;
        for(int i=0;i<3;++i){
            sum += history[handValue][i] ;
        }
        return sum ;
    }

    @Override
    public void study(boolean win) {
        if(win){
            history[prevHAndValue][currentHandValue]++ ;
        }else{
            history[prevHAndValue][(currentHandValue+2)%3]++ ;
            history[prevHAndValue][(currentHandValue+1)%3]++ ;
        }
    }
}
```

---

Player ( class )

```java
public class Player {
    private String name ;
    private Strategy strategy ;
    private int wincount ;
    private int losecount ;
    private int gamecount ;

    public Player(String name, Strategy strategy){
        this.name = name ;
        this.strategy = strategy ;
        
        this.wincount = 0 ;
        this.losecount = 0 ;
        this.gamecount = 0 ;
    }

    public Hand nextHand(){
        return strategy.nextHand() ;
    }

    public void win(){
        strategy.study(true) ;
        wincount++ ;
        gamecount++ ;
    }

    public void lose(){
        strategy.study(false) ;
        losecount++ ;
        gamecount++ ;
    }

    public void even(){
        gamecount++ ;
    }

    @Override
    public String toString(){
        return "["
            + name + ":"
            + gamecount + " games, "
            + wincount + " win. "
            + losecount + " lose"
            + "]" ;
    }
}
```

---

Main ( class )
```java
public class Main {

    public static void main(String[] args){
        if(args.length != 2){
            System.exit(0) ;
        }

        int seed1 = Integer.parseInt(args[0]) ;
        int seed2 = Integer.parseInt(args[1]) ;

        Player player1 = new Player("user1", new WinningStrategy(seed1)) ;
        Player player2 = new Player("user2", new WinningStrategy(seed2)) ;

        for(int i=0;i<10;++i){
            Hand nextHand1 = player1.nextHand() ;
            Hand nextHand2 = player2.nextHand() ;

            if(nextHand1.isStrongerThan(nextHand2)){
                System.out.println("Winner : "+player1) ;
                player1.win();
                player2.lose();
            } else if (nextHand2.isStrongerThan(nextHand1)) {
                System.out.println("Winner : "+player2) ;
                player1.lose() ;
                player2.win() ;
            } else{
                System.out.println("Even") ;
                player1.even() ;
                player2.even() ;
            }
        }
        System.out.println("total result : ");
        System.out.println(player1) ;
        System.out.println(player2) ;
    }
}
```

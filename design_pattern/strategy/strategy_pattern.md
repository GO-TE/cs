# Strategy Pattern

**Strategy 전략**

적을 해치우는 작전, 군대를 움직이는 방안, 문제를 풀어나가는 방법 등

프로그래밍 경우 알고리즘이라고 생각해도 좋음

모든 프로그램은 문제를 해결하고자 개발되며, 문제를 풀기 위한 특정 알고리즘으로 구현됨

Strategy 패턴에서 구현한 알고리즘을 모조리 교환할 수 있고, 스위치 전환하듯 알고리즘을 바꿔 같은 문제를 다른 방법으로 해결하게 만들어주는 패턴임

예제를 봅시다

예제는 컴퓨터랑 가위바위보 하는 프로그램입니다.

![image.png](Strategy%20Pattern%2010abd15cbfe78028a9efe8e9468cd8f9/image.png)

`enum` 이 드디어 나왔습니다? enum은 단순 상수 타입 변수가 아닌 독립된 특수한 클래스 취급을 받아요 자세히 알아보려면 정리 따로 하겠습니다.

```java
public enum Hand {
	// 가위바위보 enum 상수
	ROCK("바위", 0);
	SCISSORS("가위", 1);
	PAPER("보", 2);
	
	// enum 필드
	private String name; // 가위바위보 이름
	private int handvalue; // 값
	
	// 손의 값으로 상수를 얻기 위한 배열
	private static Hand[] hands = {
		ROCK, SCISSORS, PAPER
	};
	
	// 생성자
	public Hand(String name, int handvalue) {
		this.name = name;
		this.handvalue = handvalue;
	}
	
	// 
	public static Hand getHand(int handvalue) {
		return hands[handvalue];
	}
	
	public boolean isStrongerThan(Hand h) {
		return fight(h) == 1;
	}
	
	public boolean isWeakerThan(Hand h) {
		return fight(h) == -1;
	}
	
	// 강 약을 판단하는 메소드
	private int fight(Hand h) {
		if (this == h) {
			return 0;
		}
		// 여기가 좀 헷갈릴텐데, 손에 1더해서 3으로 나눈 나머지를 봄
		// 나머지랑 매개변수 hand랑 같으면 이긴거고 다르면 진거...
		// 직접 해보면 내가 보 2 이고 상대가 주먹 0라면
		// 3 % 3 = 0임
		// 근데 0이랑 주먹(0) 이랑 같으니까 return 1을 함
		else if ((this.handvalue + 1) % 3 == h.handvalue) {
			return 1;
		}
		else {
			return -1;
		}
	}
	
	@Override
	public String toString() {
		return name;
	}
}
```

Hand를 일종의 클래스로 생각하고, enum의 상수인 ROCK, SCISSORS, PAPER를 인스턴스로 봐도 무방해요…

Hand Type enum 상수들은 hands 배열에 저장되고, getHand 메소드로 Hand형 인스턴스를 얻을 수 있습니다.

handvalue로 각 대응하는 인스턴스를 얻을수 있음 (Singleton 패턴의 일종)

또한 Factory Method 패턴의 일종임

```java
// 전략을 위한 인터페이스
// nextHand : 다음 낼 손을 얻는 메소드
// study : 직전에 낸 손으로 이겼는지 졌는지 학습하는 메소드
public interface Strategy {
	public abstract Hand nextHand();
	public abstract void study(boolean win);
}
-------------------------------------------------------------
import java.util.Random;

// 이전에 이겼으면 다음에도 똑같은 손을 내는 전략
public class WinningStrategy implements Strategy {
	private Random random;
	private boolean won = false;
	private Hand preHand;
	
	public WinningStrategy(int seed) {
		random = new Random(seed);
	}
	
	@Override
	public Hand nextHand() {
		if (!won) { // 못이기면 랜덤하게 내
			preHand = Hand.getHand(random.nextInt(3));
		}
		return preHand; // 이기면 그냥 고대로 내라
	}
	
	@Override
	public void study(boolean win) { // 이겼냐 ? 졌냐 저장
		won = win;
	}
}
-------------------------------------------------------------
import java.util.Random;

// 과거 전적으로 확률을 계산해서 내는 전략
public class ProbStrategy implements Strategy {
	private Random random;
	private int preHandValue = 0;
	private int currentHandValue = 0;
	private int[][] history = { // history[이전에 낸 손][이제 낼 손]
		{1, 1, 1},
		{1, 1, 1},
		{1, 1, 1},
	}; // 확률 계산용
	
	public ProbStrategy(int seed) {
		random = new Random(seed);
	}
	
	@Override
	public Hand nextHand() {
		int bet = random.nextInt(getSum(currentHandValue);
		int handvalue = 0;
		
		if (bet < history[currentHandValue][0]) { //바위 냈을때 승수
			handvalue = 0;
		}
		else if (bet < histroy[currentHandValue[0] + histroy[currentHandValue][1]) {
			handvalue = 1; // 가위 냈을때 승수
		}
		else {
			handvalue 2; // 보 냈을 때 승수
		}
	}
	
	private int getSum(int handValue) {
		int sum = 0;
		for (int i = 0; i < 3; i++) {
			sum += history[handvalue][i];
		}
		return sum;
	}
	
	@Override
	public void study(boolean win) {
		if (win) {
			history[preHandValue][currentHandValue]++;
		}
		else {
			history[preHandValue][(currentHandValue + 1) % 3]++;
			history[preHandValue][(currentHandValue + 2) % 3]++;
		}
	}
}

-------------------------------------------------------------
public class Player {
	private String name;
	private Strategy strategy;
	private int wincount;
	private int losecount;
	private int gamecount;

	// 플레이어에게 전략을 위임해줌 ㅇㅇ
	public Player(String name, Strategy strategy) {
		this.name = name;
		this.strategy = strategy;
	}
	
	public Hand nextHand() {
		return startegy.nextHand();
	}
	
	public void win() {
		startegy.study(true);
		wincount++;
		gamecount++;
	}
	
	public void lose() {
		startegy.study(false);
		losecount++;
		gamecount++;
	}
	
	public void draw() {
		gamecount++;
	}
	
	@Override
	public String toString() {
		return "["
					+ name + ":"
					+ gamecount + "games, "
					+ wincount + " win, "
					+ losecount + " lose"
					+ "]";
	}
}	
```

```java
public class Main {
	public static void main(String[] args) {
		if (args.length != 2) {
			System.out.println("Usage: java Main randomseed1 randomseed2");
			System.out.println("Example: java Main 314 15");
			System.exit(0);
		}
		
		int seed1 = Integer.parseInt(args[0]);
		int seed2 = Integer.parseInt(args[1]);
		
		Player player1 = new Player("Min", new WinningStrategy(seed1));
		Player player2 = new Player("Go-te", new ProbStrategy(seed2));
		
		for (int i = 0; i < 1000; i++) {
			Hand nextHand1 = player1.nextHand();
			Hand nextHand2 = player2.nextHand();
			
			if (nextHand1.isStrongerThan(nextHand2)) {
				System.out.println("Winner:" + player1);
				player1.win();
				player2.lose();
			}
			else if (nextHand2.isStrongerThan(nextHand1)) {
				System.out.println("Winner:" + player2);
				player2.win();
				player1.lose();
			}
			else {
				System.out.println("Draw");
				player1.draw();
				player2.draw();
			}
		}
		System.out.println("Total result:");
		System.out.println(player1);
		System.out.println(player2);
	}
}
```

### 정리

- Strategy (전략)
    
    전략을 이용하기 위한 인터페이스를 정의함 (Straegy 예제)
    

- Concrete Strategy (구체적인 전략)
    
    Strategy를 구현함. 여기서 구체적인 전략을 실제로 프로그래밍함
    
    (예제 WinningStrategy, ProbStrategy)
    
- Context (문맥)
    
    Strategy를 이용하고, Concrete Strategy의 인스턴스를 가지고 있다가, 필요에 사용함. 호출하는 건 Strategy의 인터페이스일 뿐 (예제에선 Player)
    

> 근데 일부러 Strategy 역을 굳이 만들어야하나?
> 

잘 보셈.

메소드 안에 녹아드는 형태로 알고리즘을 구현하면 쉬워 보이는데, Strategy는 알고리즘 부분을 다른 부분과 의도적으로 분리해둠

알고리즘이 있는 인터페이스 부분만 규정해 두고, 프로그램에서 위임을 통해 알고리즘을 이용함

근데, 복잡해 보이지? 아님 잘 봐라 진짜로

알고리즘을 개량해서 더 빠르게 만들고 싶다고하면, Concrete Strategy만 살짝 수정해 주면 됨. (안썼더라면, 처음부터 갈아야겠죠?)

위임으로 약한 결합이라서 바꿔 쓰기도 쉬움.. (다른 전략으로 새로 만들어서 원래 코드에 붙여넣기 쉬움)

그리고 런타임에 교체도 가능한게 진짜 히트임

예를 들어 메모리가 작은 환경에선 SlowButLessMemoryStrategy를 사용하고

메모리가 많은 환경에선 FastButMoreMemoryStrategy를 사용할 수도 있음

속도차이가 나면 한쪽 알고리즘은 다른 알고리즘 검산 과정에 활용도 가능하게 됨
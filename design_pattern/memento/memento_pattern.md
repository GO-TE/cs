# Memento Pattern

메멘토는 기념품, 유품, 추억거리라는 뜻이 있습니다잉

텍스트 에디터를 사용할 실수로 지워도 ctrl+z 누르면 복원되잖아요?

객체 지향 프로그램으로 실행 취소를 구현하려면 인스턴스가 가진 정보를 저장하는 행위가 필요합니다.

단 저장으로만은 안되고 인스턴스를 원 상태로 복원할 수가 있어야 함

복원하려면, 인스턴스 내부 정보에 자유롭게 접근할 수 있어야하지만 너무 열려있다면 내부 구조에 의존하는 코드가 프로그램 이곳 저곳에 흩어져 클래스 수정하기가 어려워집니다…

이를 캡슐화의 파괴라고 하는데, 인스턴스의 상태를 나타내는 역할을 도입해 파괴하지 않고 저장하고 복원하는 것이 메멘토 패턴의 핵심입니다 ^.^

메멘토 패턴으로 구현하는 기능들은 주로

undo, redo, 작업 이력 작성, 현재 상태 저장 등이 있습니다.

이제 예제를 봅시다

과일 모으기 주사위 게임인데 단순함

![image.png](Memento%20Pattern%20162bd15cbfe78081a890dd7afdbf9bb8/image.png)

![image.png](Memento%20Pattern%20162bd15cbfe78081a890dd7afdbf9bb8/image%201.png)

- 자동진행
- 주사위를 던져 다음 상태를 결정
- 좋은 수가 나온다면 돈이 증가
- 나쁜 수가 나온다면 돈이 감소
- 특히 좋은 수가 나온다면 과일을 얻는다.
- 돈이 없다면 종료

```java
package game;

import java.uitl.ArrayList;
import java.util.List;

public class Memento {
	private int money;
	private List<String> fruits;
	
	public int getMoney() {
		return money;
	}
	
	Memento(int money) {
		this.money = money;
		this.fruits = new ArrayList<>();
	}
	
	void addFruit(String fruit) {
		fruits.add(fruit);
	}
	
	List<String> getFruits() {
		return new ArrayList<>(fruits);
	}
}

-----------------------------------------------------------
package game;

import java.uitl.ArrayList;
import java.util.List;
import java.util.Random;

public class Gamer {
	private int money;
	private List<String> fruits = new ArrayList<>();
	private Random random = new Random();
	
	private static String[] fruitsName = {
		"사과", "포도", "바나나", "오렌지",
	};
	
	public Gamer(int money) {
		this.money = money;
	}
	
	public int getMoney() {
		return money;
	}
	
	public void bet() {
		int dice = random.nextInt(6 + 1);
		if (dice == 1) {
			money += 100;
			System.out.println("소지금이 증가했습니다.");
		} else if (dice == 2) {
			money /= 2;
			System.out.println("소지금이 절반으로 줄었습니다.");
		} else if (dice == 6) {
			String f = getFruit();
			System.out.println("과일(" + f + ")를 받았습니다.");
			fruits.add(f);
		} else {
			System.out.println("변동 사항이 없습니다.");
		}
	}
	
	public Memento createMemento() {
		Memento m = new Memento(money);
		for (String f: fruits) {
			if (f.startWith("맛있는 ")) {
				m.addFruit(f);
			}
		}
		return m;
	}
	
	public void restoreMemento(Memento memento) {
		this.money = memento.getMoney();
		this.fruits = memento.getFruits();
	}
	
	@Override
	public String toString() {
		return "[money = " + money + ", fruits = " + fruits + "]";
	}
	
	private String getFruit() {
		String f = fruitsName[random.nextInt(fruitsName.length)];
		if (random.nextBoolean()) {
			return "맛있는 " + f;
		} else {
			return f;
		}
	}
}

-----------------------------------------------------------
import game.Memento;
import game.Gamer;

public class Main {
	public static void main(String[] args) {
		Gamer gamer = new Gamer(100);
		Memento memento = gamer.createMemento();
		
		for (int i = 0; i < 100; i++) {
			System.out.println("==== " + i);
			System.out.println("상태:" + gamer);
			
			gamer.bet();
			
			System.out.println("소지금은 " gamer.getMoney() + "원 입니다.");
			
			if (gamer.getMoney() > memento.getMoney()) {
				System.out.println("=현재 상태 저장");
				memento = gamer.createMemento();
			} else if (gamer.getMoney() < memento.getMoney() / 2) {
				System.out.println("=이전 상태 복원");
				gamer.restoreMemento(memento);
			}
			
				try {
					Thread.sleep(1000);
				} catch (InterruptedExecption e) {
				}
				System.out.println();
			}
		}
	}
```

### 정리

- Originator (작성자)
    
    자신의 현재 상태를 저장하고 싶을 때, Memento를 만들고 이전 Memento를 넘겨 받으면 그 Memento를 만든 시점으로 되돌리는 처리를 함 (예제 Gamer)
    

- Memento (기념품)
    
    Originator의 내부 정보를 정리함 Memento는 Originator 내부 정보를 갖고 있지만 그 정보를 누구에게나 공개하지않음
    
    - wide interface - 넓은 인터페이스
        
        Memento가 제공하는 넓은 인터페이스는 오브젝트의 상태를 되돌리는데 필요한 정보를 모두 얻을 수 있는 메소드 집합
        
        넓은 인터페이스는 Memento의 내부 상태를 드러내기에, 이 인터페이스를 사용하는 건 오직 Originator 뿐이다.
        
    - narrow interface - 좁은 인터페이스
        
        메멘토가 제공하는 좁은 인터페이스는 외부 Caretaker에 보여주는 것
        
        좁은 인터페이스로 할 수 있는 일은 한계가 있어 내부 상태를 외부에 공개하지 않음
        
    
    두 종류의 인터페이스를 구분 해 사용함으로 캡슐화의 파괴를 막을 수 있음
    
    (예제 Memento)
    

- Caretaker (관리인)
    
    현재 Originator의 상태를 저장하고 싶을 때, Originator에 요청함
    
    요청을 받으면 Memento를 만들어 Caretaker에 넘기고, 이를 저장해 둠
    
    (예제 Main)
    
    근데, Caretaker는 좁은 인터페이스에서만 사용할 수 있어 Memento 내부에 접근할 수 없음
    
    만들어준 Memento를 한 덩어리의 블랙박스로 통째로 보관만 함
    

### 메멘토는 최대 몇 개 까지?

예제에선 Memento를 단 한개만 가지고 있지만, 배열이나 다른 자료구조를 만들어 다양한 시점의 상태를 저장할 수 있음

### 유효기간?

위에선 메모리에서만 보관해놨지만 파일 시스템으로 영속저장한다면, 유효기간이 문제일듯

특정 시점의 Memento를 저장해놨더라도, 그 후 시스템의 버전이 증가하면 호환이 안 될 수도 있음

사실 undo기능이 필요하면 Originator에 기능을 만들면 되지 않나? 라고 생각할 수 있는데, Caretaker는 어느 시점에 스냅샷을 찍을지, 언제 취소를 할지, Memento를 저장하는 일을 함

반면 Originator는 Memento를 만드는 일과 주어진 Memento를 사용해 자신의 상태를 되돌리는 일을 함

이렇게 역할을 분리해 놓으면, Originator를 변경할 필요가 없음

- 여러 단계의 실행 취소를 가능케 함
- 실행 취소뿐만 아니라, 현재 상태를 파일에 저장하기
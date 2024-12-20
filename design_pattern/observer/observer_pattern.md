# Observer Pattern

스타크래프트 옵저버를 아시나요?

공격할 수 없지만, 투명 상태이며 투명 유닛들을 볼 수 있는 유닛입니다.

비슷한데, 관찰 대상(투명 유닛)의 상태가 변화하면(감지하면) 관찰자에게 알리는(유저에게 알리는) 패턴입니다.

예제를 볼까요?

수를 생성하는 객체를 관찰하고 그 값을 표시하는 단순한 프로그램

![image.png](Observer%20Pattern%20162bd15cbfe78003ae5beb87379aa326/image.png)

```java
public interface Observer {
	public abstract void update(NumberGenerator generator);
}

----------------------------------------------------------
import java.uitl.ArrayList;
import java.uitl.List;

public abstract class NumberGeneartor {
	private List<Observer> observer = new ArrayList<>();
	
	public void addObserver(Observer observer) {
		observers.add(observer);
	}
	
	public void removeObserver(Observer observer) {
		observers.removeo(observer);
	}
	
	public void notifyObservers() {
		for (Observer o: observers) {
			o.update(this);
		}
	}
	
	public abstract int getNumber();
	public abstract void execute();
}

----------------------------------------------------------
import java.util.Random;

public class RandomNumberGenerator extends NumberGenerator {
	private Random random = new Random();
	private int number;
	
	@Override
	public int getNumber() {
		return number;
	}
	
	@Override
	public void execute() {
		for (int i = 0; i < 20; i++) {
			number = random.nextInt(20);
			notifyObservers**();
		}
	}
}**

----------------------------------------------------------
public class DigitObserver implements Observer {
	@Override
	public void update(NumberGenerator generator) {
		System.out.println("DigitObserver:" + genearator.getNumber());
		try {
			Thread.sleep(100);
		} catch (InterruptedException e) {
		}
		}
	}
}

----------------------------------------------------------
public class GraphObserver implements Observer {
	@Override
	public void update(NumberGenerator generator) {
		System.out.print("GraphObserver:");
		int count = generator.getNumber();
		for (int i = 0; i < count; i++) {
			System.out.print("*");
		}
		System.out.println();
		try {
			Thread.sleep(100);
		} catch (InterruptedException e) {
		}
	}
}

----------------------------------------------------------
public class Main {
	public static void main(String[] args) {
		NumberGenerator generator = new RandomNumberGenerator();
		
		Observer observer1 = new DigitObserver();
		Observer observer2 = new GraphObserver();
		generator.addObserver(observer1);
		generator.addObserver(observer2);
		generator.execute();
	}
}
```

### 정리

- Subject (관찰 대상자)
    
    관찰되는 대상자를 나타냄 Subject는 관찰자인 Observer를 등록하는 메소드와 삭제하는 메소드를 가지며 또 ‘현재 상태를 가져오는’도 선언되어 있음.
    
    (예제에서 NumberGenerator)
    
- Concrete Subject (구체적 관찰 대상자)
    
    상태가 변경되면 등록된 Observer에 알림 (예제 RandomNumberGenerator)
    
- Observer
    
    Subject의 상태 변화를 전달받음 이 받는 메소드가 update
    
- Concrete Observer
    
    update호풀 시 그 메소드에서 Subject의 상태를 취득
    
    예제에서 (GraphObserver, DigitObserver)
    

### 교환 가능성

디자인 패턴 목적 중 하나는 클래스를 재사용 가능한 부품으로 만드는 것임

Observer는 상태를 가진 Concrete Subject와 상태 변화를 통보받는 Concrete Observer가 등장함. 그리고 그 둘을 연결하는 것이 인터페이스로서 Subject와 Observer임

매번 강조하는 거 있잖아요

- 추상 클래스나 인터페이스를 사용하여 구상 클래스로부터 추상 메소드를 분리한다
    
    Observer - Concrete Observer, Subject - Concrete Subject
    
- 인수로 인스턴스를 전달할 때나 필드로 인스턴스를 저장할 때는 구상 클래스형으로 하지 않고 추상 클래스나 인터페이스형으로 해둔다
    
    `List<String> names = new ArrayList<>();` 
    

### 옵저버의 순서

예제에서 notifyObservers 메소드는 먼저 등록된 옵저버한테 update가 `먼저` 호출됨

일반적으로 구체적인 옵저버 클래스를 설계할 때, update 메소드 호출되는 순서가 바뀌어도 문제가 없도록 해야함

DigitObserver 호출하고 GraphObserver가 작동 안된다는 둥 이런게 있으면 안됨

애초에 클래스의 독립성이 제대로 유지만 되면 의존성의 혼란은 일어나지 않음

### Observer의 행위가 Subject에 영향을 미친다면?

예제에선 RandomNumberGenerator가 내부에서 데이터를 생성하고 notifyObservers 메소드를 통해 update를 호출함

근데 일반 Observer 패턴에선 Subject가 update를 호출하는 계기가 다른 클래스로부터 오기도 함

예를 들어 GUI에서 사용자가 버튼을 누르는 이벤트를 계기로 update가 호출되는 경우도 있음

근데 subject가 update를 호출하는 계기가 해당 observer인 경우도 있음

이런 경우에는 무한 루프가 됨…

### 갱신을 위한 힌트 정보 다루기

예제에서 다룬 subject는 observer에게 알리기 위해 update 인수로 그 subject 자체를 보내버림

`void update(NumberGenerator generator)` 처럼 말이죠

근데 예제에선 사실 생성한 Number만 보내도 되긴 함

`void update(int number)` 처럼

그런데도 불구하고 그 객체 자체를 보낸 이유는, 프로그램이 더 복잡해진다고 했을 때,

숫자만 띡 보내면 어떤 객체가 상태가 변화하여 알린지 알 수 가 없음

생략하면 좋긴 하겠지만.. 앵간하면 하지 않는게 바람직 해 보임

### 관찰하기보단 입만 벌리고 있는…

예제에서 보면 사실 Observer가 상태 변화를 계속 체크하기보단, Subject가 알려 주는 것을 수동적으로 기다린다. Publish-Subscribe 패턴이라고도 함

MVC에선 Model과 View의 관계가 Observer 패턴임
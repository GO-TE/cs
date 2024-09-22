# Bridge Pattern

브릿지… 다리죠. 현실 세계의 다리가 강 양쪽을 연결하듯이

디자인 패턴도 두 장소를 연결하는 역할을 합니다.

두 장소는 기능의 클래스 계층과 구현의 클래스 계층입니다

### 기능 클래스 계층

어떤 클래스 A가 있다고 가정하고 A에 새로운 기능을 추가하려고 한다면, A의 하위클래스 B를 만들었다고 합시다.

```java
A
↳ B
```

이렇게 계층이 생기는데, 이 계층은 A의 새로운 기능을 추가하기 위해 생긴 계층입니다.

여기서 또 새로운 기능을 추가하려고 하면 계층이 더욱 깊어지겠죠?

상위 클래스에서는 기본적인 기능을 가지고 있고, 하위 클래스에서는 새로운 기능을 추가합니다.

```java
A
↳ B
	↳ C
```

새로운 기능을 추가하고 싶을 때, 클래스 계층에서 추가하려는 기능의 목적과 가까운 클래스를 찾아 그 하위 클래스를 만들고, 원하는 기능을 추가한 새로운 클래스를 만든다

이게 바로 기능의 클래스 계층임 ㅇㅇ (일반적으로 너무 깊게 하면 읽는 사람이 불편함)

### 구현 클래스 계층

위에선 기능을 추가했고, 여기에선 구현을 추가하려고 합니다.

구현 클래스 계층은 템플릿 메소드 패턴에서도 등장하게 되는데, 추상클래스를 선언하고 인터페이스를 규정 합니다.

그리고 하위클래스로 추상메소드를 실제로 구현합니다.

이러한 상위클래스와 하위클래스의 역할을 분담 해 부품으로서의 가치가 높은 클래스를 만들 수 있음

여기서 클래스 계층이 있다는건데, 예를 들어 상위 클래스 AbstractClass의 추상 메소드를 구현한 ConcreteClass 합시다

```java
AbstractClass
↳ ConcreteClass
```

이렇게 계층이 만들어 지겠죠? 여기서, 사용되는 계층은 기능을 추가 하는 것이 아닌 새로운 메소드를 만들기 위함도 아닌, 그저 추상 메소드를 구현했을 뿐입니다.

즉 새로운 메소드나 기능이 생긴게 아니란 말이죠

그러면 위에서 언급했듯이

상위 클래스는 추상 메소드로 인터페이스를 규정함

하위 클래스는 구상 메소드로 인터페이스를 구현함

여기서 또 다른 구현 클래스를 만든다고 하면

```java
AbstractClass
↳ ConcreteClass
↳ AnotherConcreteClass
```

이런식으로 되겠죠?

기능과 구현 클래스 계층의 뉘양스를 가져가보자구요

서브 클래스를 만드려고 할 때, 기능을 추가하는건가 구현을 하는건가?

이렇게 계층을 나누지 않고

하나로 하면 한 계층에서 복잡한 트리가 그려지겠죠? 그래서

두 개의 독립된 클래스 계층으로 나눕니다.

그리고 이렇게 나눈 계층을 이어주는 패턴이 브릿지 패턴이구요

예제를 봅시다. 예제는 무언가 출력하는 프로그램입니당

![image.png](Bridge%20Pattern%20108bd15cbfe7800889f2f1f4ad04f5f6/image.png)

```java
// 무언가를 출력하는 기능 계층의 최상위 클래스
public class Display {
	private DisplayImpl impl; // 구현을 나타내는 인스턴스
	// 생성자로 인스턴스 전달하고, 얘가 두 계층의 다리 역할을 해줌
	
	public Display(Display impl) {
		this.impl = impl;
	}
	
	public void open() {
		impl.rawOpen();
	}
	
	public void print() {
		impl.rawPrint();
	}
	
	public void close() {
		impl.rawClose();
	}
	
	public final void display() {
		open();
		print();
		close();
	}
}
------------------------------------------------------------
public class CountDisplay extends Display {
	public CountDisplay(DisplayImpl impl) {
		super(impl);
	}

	public void multiDisplay(int times) { // 기능의 추가
		open();
		for (int i = 0; i < times; i++) {
			print();
		}
		close();
	}
}
------------------------------------------------------------
// 구현 계층의 최상위 클래스
public abstract class DisplayImpl {
	public abstract void rawOpen();
	public abstract void rawPrint();
	public abstract void rawClose();
}
------------------------------------------------------------
// 진짜 구현하는 클래스 DisplayImpl를 구현하고 있음
public class StringDisplayImpl extends DisplayImpl {
	private String string;
	private int width;
	
	public StringDisplayImpl(String string) {
		this.string = string;
		this.width = string.length();
	}
	
	@Override
	public void rawOpen() {
		printLine();
	}
	
	@Override
	public void rawPrint() {
		System.out.println("|" + string + "|");
	}
	
	@Override
	public void rawClose() {
		printLine();
	}
	
	private void printLine() {
		System.out.print("+");
		for (int i = 0; i < width; i++) {
			System.out.print("-");
		}
		System.out.println("+");
	}
}
------------------------------------------------------------
public class Main {
	public static void main(String[] args) {
		Display d1 = new Display(new StringDisplayImpl("Hello world");
		Display d2 = new CountDisplay(new StringDisplayImpl("Hello java");
		CountDisplay d3 = new CountDisplay(new StringDisplayImpl("Hello Seoul");
		
		d1.display();
		d2.display();
		d3.display();
		d3.multiDisplay(5);
	}
}
```

기능의 추가, 구현의 추가가 보이시나요?

### 정리

- Abstraction (추상화)
    
    기능 클래스 계층의 최상위 클래스 : Implementor의 메소드를 사용하여 기본 기능만 기술된 클래스 (Implementor 역을 가지고 있음)
    
    (예제에선 Display)
    
- Refined Abstraction (개선된 추상화)
    
    Abstraction에 기능을 추가했음 (예제에선 CountDisplay)
    
- Implementor (구현자)
    
    구현 클래스 계층의 최상위 클래스 : Abstraction의 인터페이스를 구현하기 위한 메소드를 규정하는 역할 (예제에선 DisplayImpl)
    
- ConcreteImplement (구체적인 구현자)
    
    Implementor가 정의한 인터페이스를 구현하는 클래스 (예제에서 StringDisplayImpl)
    

> 분리의 이유가 다 있읍니다.
> 

기능과 구현의 클래스를 계층으로 나눠서 관리하게 되면, 각각 계층을 독립적으로 확장이 가능합니다. 

기능을 추가하고 싶으면 클래스 계층에 클래스를 추가하고, 이때 구현의 클래스는 수정 되는게 없습니다. 그리고 새로 추가한 기능은 “모든 구현”에서 이용 가능합니다.

와닿지 않으실 수 있겠는데, 예시로 운영체제가 다른 환경에서 다 동작하도록 하는 프로그램을 구현한다고 쳐 봅시다.

Window, Mac, Linux 이렇게 두고 프로그램 버전이 나뉜다고 가정해봐요

그리고 이때 실행 환경에 의존하는 부분을 Bridge 패턴으로 구현의 클래스 계층으로 놔봐유

실행 환경에 공통된 인터페이스를 정해서 구현자, 구체적인 구현자로 나누어서 Window, Mac, Linux 세 개 클래스로 나누면, 기능 클래스 계층에서 아무리 기능을 추가해도

세가지 환경에 동시 대응 가능하게 되겠죠?

> 상속과 위임
> 

상속은 클래스를 확장하는 편한 방법인데, 클래스간 결합도가 강해지죠

`class A extends B { … }` A가 B를 상속하고있는데, 코드를 수정하지 않는 이상 관계를 바꿀 수가 없습니다.

필요에 따라 클래스간 관계를 전환하고 싶을 때, 상속보단 위임이 낫죠

예제에서 Display가 DisplayImpl에게 일을 떠 넘긴게 보이시나요?

open에선 그냥 impl.rawOpen()을 호출하고, close도 impl.rawClose()를 호출합니다.

이게 역할을 위임이고, is-a 관계가 아닌 has-a관계로 유연하게 클래스를 교체 할 수 있습니다.

의존성 주입의 예죠

상속은 강한 결합이고, 위임은 느슨한 결합입니다 !!! 모르면 외우지말고 이해해
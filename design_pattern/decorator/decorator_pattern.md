# Decorator Pattern

장식물과 내용물을 동일시한다 !

중심이 되는 베이스 객체가 있고, 이 객체에 기능을 추가해서 목적에 더 맞는 객체로 만들어 가는 과정을 Decorator 패턴이라고 한다.

케잌의 베이스가 쉬폰케잌인 것 처럼 초코 크림을 바르거나, 생크림을 바르는 것 처럼

근데 장식물과 내용물을 동일시하는건 뭘까?

예제를 보면서 이해해보자

문자열을 꾸미는 프로그램으로 보자

![image.png](Decorator%20Pattern%2010ebd15cbfe78041b4d3fb229244e5fb/image.png)

```java
public abstract class Display {
	public abstract int getColumns();
	public abstract int getRows();
	public abstract String getRowText(int row);
	
	public void show() {
		for (int i = 0; i < getRows(); i++) {
			System.out.println(getRowText());
		}
	}
}
-------------------------------------------------------------
public class StringDisplay extends Display {
	private String string;
	
	public StringDisplay(String string) {
		this.string = string;
	}
	
	@Override
	public int getColumns() {
		return string.length();
	}
	
	@Override
	public int getRows() {
		return 1;
	}
	
	@Override
	public String getRowText(int row) {
		if (row != 0) {
			throw new IndexOutOfBoundsException();
		}
		return string;
	}
}
-------------------------------------------------------------
public abstract class Border extends Display {
	protected Display display;
	
	protected Border(Display display) {
		this.display = display;
	}
}
-------------------------------------------------------------
public class SideBorder extends Border {
	private char borderChar;
	
	public SideBorder(Display display, char ch) {
		super(display);
		this.borderChar = ch
	}
	
	@Override
	public int getColumns() {
		return 1 + display.getColumns() + 1;
	}
	
	@Override
	public int getRows() {
		return display.getRows();
	}
	
	@Override
	public String getRowText(int row) {
		return border + display.getRowText(row) + borderChar;
	}
-------------------------------------------------------------
public class FullBorder extends Border {
	public FullBorder(Display display) {
		super(display);
	}
	
	@Override
	public int getColumns() {
		return 1 + display.getColumns() + 1;
	}
	
	@Override
	public int getRows() {
		return 1 + display.getRows() + 1;
	}
	
	@Pverride
	public String getRowText(int row) {
		if (row == 0) {
			return "+" + makeLine('-', display.getColumns()) + "+";
		}
		else if (row == display,getRows() + 1) {
			return "+" + makeLine('-', display.getColumns()) + "+";
		}
		else {
			return "|" + display.getRowText(row - 1) + "|";
		}
	}
	
	private String makeLine(char ch, int count) {
		StringBuilder line = new StringBuilder();
		
		for (int i = 0; i < count; i++) {
			line.append(ch);
		}
		return line.toString();
	}
}
-------------------------------------------------------------
public class Main {
	public static void main(Stirng[] args) {
		Display d1 = new StringDisplay("Hello world");
		Display d2 = new SideBorder(b1, '#');
		Display d3 = new FullBorder(b2);
		b1.show();
		b2.show();
		b3.show();
		Display b4 = new SideBorder(
									new FullBorder(
										new FullBorder(
											new SideBorder(
												new FullBorder(
													new StringDisplay("Hello world")
												),
												'*'
											)
										)
									),
									'/'
								);
		b4.show();
	}
}
```

출력

```java
Hello world
#Hello world#
+-------------+
|#Hello world#|
+-------------+
/+-----------------+/
/|+---------------+|/
/||*+-----------+*||/
/||*|Hello world|*||/
/||*+-----------+*||/
/|+---------------+|/
/+-----------------+/
```

### 정리

- Component
    
    기능을 추가할 때 핵심이 되는 역으로 베이스라고 보면 됨
    
    기본 베이스가 되는 객체의 인터페이스만 정의함 (예제 Display)
    
- Concrete Component
    
    Component의 인터페이스를 구현하는 구체적인 Component (예제에선 StringDisplay)
    
- Decorator
    
    Component와 동일한 인터페이스를 가지고, Decorator가 장식할 대상이 되는 Component도 가지고 있음. 이 역은 자신이 장식할 대상을 알고 있음 (예제에선 Border)
    
- Concrete Decorator
    
    Decorator를 구체적으로 구현한 클래스 (예제에서 SideBorder, FullBorder)
    

> 예제에서 장식물과 기반 베이스가 되는 객체를 동일시 하는게 보이나요?
> 

Border와 Display의 하위 클래스로 되어 있는 부분에서 동일하게 여기는게 보이시나요?

같은 인터페이스를 구현하고 있고

장식을 사용해 내용물을 감싸도 인터페이스가 가려지지 않습니다.

getColumn, getRows, getRowText, show 메소드는 다른 클래스에서도 그대로 표현되는 부분이 이를 인터페이스가 투과적이다 라고 표현합니다.

인터페이스가 투과적이므로 Decorator 패턴에서 Composite 패턴과 유사한 재귀적 구조가 보이지만 목적이 다릅니다.

Decorator는 바깥 테두리를 겹쳐 기능을 추가해 나가는게 주 목적입니다.

그리고 위임을 사용해서 꾸밈을 담당하는 클래스가 Display의 메소드를 사용하는게 보이시나요?

그대로 갖다 쓰면서 내용물은 변하지 않고 기능만 추가할 수 있도록 할 수 있게 되죠…

그리고 위임으로 하니 동적으로 결합해서 프레임워크 소스를 변경 안해도 객체의 관계를 변경시키기 편합니다.

요아정마냥 기본 베이스에 여러가지 토핑 추가하듯이 단순한 출발이라도 화려한 마무리를 지을 수 있습니다.

1. 투과적 인터페이스로 양파 껍질처럼 생긴 구조
2. 내용물을 바꾸지 않고 기능을 추가할 수 있음
3. 동적으로 기능을 추가할 수 있음
4. 단순한 구성이라도 다양한 기능을 추가하기 쉬움

++ 상속의 동일시와 위임의 동일시

상속의 동일시

```java
class Parent {
	void parentMethod() {
		...
	}
}

class Child extends Parent {
	void childMehtod() {
		...
	}
}
```

Parent를 상속하는 Child 클래스가 있다고 하자.

여기서 Child의 인스턴스는 Parent 타입 변수에 그대로 대입할 수 있다.

그리고 Parent를 상속받는 메소드를 그대로 호출 할 수 있다.

`Parent obj = new Child();`  `obj.parentMethod();` 

위와 반대로 상위클래스를 하위 클래스로 대입하려면 캐스팅이 필요하다.

```java
Parent obj = new Child();
((Child)obj).childMethod();
```

위임의 동일시

위임을 통해 인터페이스가 투과적으로 되면 자신과 위임할 곳을 동일 시 할 수 있음

```java
class Rose {
	Violet obj = new Violet();
	void method() {
		obj.method();
	}
}

class Violet {
	void method() {
		...
	}
}
```

Rose와 Violet은 같은 메소드를 가지고 있고 Rose는 Violet에게 위임하고 있음

두 클래스는 연결 된 것 같기도 하고 안된 것 같기도 하다.

공통 메소드를 가지고 있지만, 공통이라는 정보가 코드에 명시되어 있지 않아 햇갈린다.

이럴 때 공통 추상 클래스가 있다면 훨씬 강하게 연결된다.

```java
abstract class Flower {
	abstract void method();
}

class Rose extends Flower{
	Violet obj = new Violet();
	
	@Override
	void method() {
		obj.method();
	}
}

class Violet extends Flower{
	@Override
	void method() {
		...
	}
}
```

여기까지 보면 Rose안의 필드 obj를 Violet으로 특정시켜도 괜찮을걸까? 라는 생각이 들 수도 있음 (Flower 타입으로 선언한던가)

근데 이건 실제 프로그램 상황마다 달라서 뭐가 좋다라고 말해줄 수 없음
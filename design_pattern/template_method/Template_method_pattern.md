# Template Method Pattern

상위 클래스에서 처리의 뼈대를 결정하고, 하위 클래스에서 그 구체적 내용을 결정하는 디자인 패턴

템플릿은 문자 모양대로 구멍난 플라스틱 판인데, 연필이건 사인펜이건 결국 문자모양대로 나온다는게 똑같음

상위 클래스에서 템플릿이 될 메소드가 정의 되어있고, 하위 클래스에서 구현하는 것

### 예제

![image.png](Template%20Method%20Pattern%20b97136c5658a4fc9835ae2a61b70fed7/image.png)

문자나 문자열을 5번 반복해서 표시하는 프로그램을 구현

```java
public abstract class AbstractDisplay {
	// 하위 클래스에 구현을 맡기는 추상 메소드
	public abstract void open();
	public abstract void print();
	public abstract void close();
	
	// 이건 직접 구현하는 메소드 (문자 모양이라고 생각할까?)
	public final void display() {
		open();
		for (int i = 0; i < 5; i++) {
			print();
		}
		close();
	}
}

----------------------------------------------------------
public class CharDisplay extends AbstractDisplay {
	private char ch;
	
	public CharDisplay(char ch) {
		this.ch = ch;
	}
	
	@Override
	public void open() {
		System.out.println("<<");
	}

	@Override
	public void print() {
		System.out.println(ch);
	}

	@Override
	public void close() {
		System.out.println(">>");
	}
}
---------------------------------------------------------
public class StringDisplay extends AbstractDisplay {
	private String string;
	private int width;
	
	public StringDisplay(String string){
		this.string = string
		this.width = string.length;
	}
	
	@Override
	public void open() {
		printLine();
	}
	
	@Override
	public void print() {
		System.out.println("|" + string + "|");
	}
	
	@Override
	public void close() {
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
---------------------------------------------------------
public class Main {
	public static void main(String[] args) {
		AbstractDisplay d1 = new CharDisplay('H');
		AbstractDisplay d2 = new StringDisplay("Hello Design Pattern");
		
		d1.display();
		d2.display();
	}
}
```

d1, d2 모두 AbstractDisplay의 하위 클래스의 인스턴스임

→ 상속한 display 메소드를 호출할 수 있음 (실제 동작은 하위 클래스가 직접 실행중)

자바 처음 배울 때 자주 쓰는 패턴임

중요한건 행하려는 목적의 코드를 추상화해서

이를 하위 클래스에 구현해서 각기 다른 목적에 맞는 로직을 구현하는거임

### 정리

- AbstractClass (추상 클래스)
    
    템플릿 메소드를 구현하고, 그 템플릿 메소드에서 사용할 추상 메소드를 선언함
    
    이 추상 메소드는 위 클래스인 ConcreteClass에서 구현 (예제에서 AbstractDisplay)
    
- ConcreteClass (구현 클래스)
    
    AbstractClass가 추상화한 메소드를 구현함
    
    여기서 구현된 메소드는 AbstractClass의 템플릿 메소드가 호출됨 (예제에서 StringDisplay, CharDisplay)
    

> 왜 씀?
> 
> 1. 유지보수
>     
>     예제를 보자
>     
>     5번 반복해서 출력하도록 하는게 프로그램의 목적으로 잡았다
>     
>     그러면 우리는 그 5번 반복하는걸 상위 클래스에서 구현함으로써 하위클래스가 또 구현 할 필요도 없고, 3번 출력으로 바꾼다고 했을 때 상위클래스만 고쳐주면 된다.
>     
>     편함 ㅇㅇ
>     
> 2. 상위 클래스와 하위 클래스의 세트피스
>     
>     상위 클래스에서 선언된 추상 메소드를 실제로 하위 클래스에서 구현할 때는 그 메소드가 어떤 타이밍에 호출되는지 이해해야함…
>     
>     상위 클래스의 소스 파일이 없으면 하위 클래스의 구현이 어려울 수도 있고
>     
> 
> 1. 하위 클래스와 상위클래스를 동일시 하기
>     
>     Charplay와 StringDisplay의 인스턴스 모두 AbstractDisplay타입 참조 변수에 대입하여 display 메소드를 호출함
>     
>     상위 클래스형 변수가 있고 그 변수에 하위 클래스 인스턴스가 대입된다고 가정했을 때, 하위 클래스의 종류를 특정하지 않아도 프로그램이 동작하게 만드는게 좋음
>     
>     상위 클래스형 변수에 하위 클래스의 인스턴스 중 어느 것을 대입해도 제대로 동작할 수 있게 하는 원칙을 LSP(The Liskov substitution Principle)라고 함
>     
> 

## 클래스 계층과 추상 클래스

### 상위 클래스가 하위 클래스에게…

우리가 클래스 계층에 대해 학습하면 대부분 하위 클래스 관점으로 생각함

예를들면

- 상위 클래스에서 정의된 메소드를 하위 클래스에서 이용 가능
- 하위 클래스에 약간의 메소드를 기술하는 것만으로 새로운 기술 추가 가능
- 하위 클래스에서 메소드 오버라이드하면 동작을 변경 가능

근데 관점을 상위클래스로 바꿔보자

상위 클래스에서 추상 메소드가 선언되어 있다면, 그 메소드는 당연히 하위 클래스에서 구현하게 됨. 다시 말하면 추상 메소드 선언은 프로그램을 사용해 다음과 같이 주장한다고 할 수 있다.

- 하위 클래스에서 그 추상 메소드를 구현하길 기대한다.
- 하위 클래스에 해당 메소드 구현을 요청한다.

하위 클래스에는 상위 클래스가 선언한 추상 메소드를 구현할 **책임**이 있다고 볼수도 있다.

이것을 subclass responsibility라고 함

### 추상 클래스의 의의

추상 클래스는 인스턴스를 만들 수 없음.

처음 배울 때 인스턴스를 만들 수 없는 클래스가 무슨 도움이 되는지 떠오르지가 않음

추상 메소드는 메소드의 본체가 기술되어 있지 않아 구체적인 내용은 알 수 없지만,

메소드 이름을 정의하고 메소드를 사용한 템플릿 메소드에 의해 처리를 기술 할 수는 있음

실제 처리 내용은 하위 클래스가 해줘야하지만 추상 클래스 단계에서 처리의 흐름을 형성하는 건 중요함

### 상위 클래스와 하위 클래스의 협조

상위 클래스에 코드가 길어지면 하위 클래스 만들긴 편하지만 제약 조건이 많이 생김

반대로 상위 클래스의 코드가 짧으면 하위 클래스를 작성하기 힘들기도 하고, 중복되는 기술이 생길수도 있음

각 상황에 맞는 트레이드 오프를 잘 해야함 (이것은 개발자 몫)
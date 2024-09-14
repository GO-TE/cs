# Prototype Pattern

- 인스턴스에서 인스턴스 만들기

우리가 인스턴스를 생성할 때 `new` 키워드로 생성한다 `new SomeClass()` 

인스턴스를 생성하면 클래스 이름을 반드시 지정해 줘야함

근데 클래스 이름을 지정하지 않고 생성하고 싶을 때도 있음

1. 종류가 너무 많아 클래스로 정리할 수 없을 때
    
    취급할 오브젝트 종류가 너무 많아, 하나하나 다른 클래스로 만들면 소스파일을 많이 작성해야하는 경우…
    
2. 클래스로부터 인스턴스 생성이 어려울 때
    
    생성하고 싶은 인스턴스가 복잡한 과정을 거칠 때 (만들어지기 과정이 어려움)
    
    ex) 그래픽 에디터에서 사용자가 마우스로 그린 도형을 나타내는 인스턴스가 있음
    
    근데 사용자 조작으로 만들어지는 인스턴스를 프로그래밍 해서 만들긴 어려움
    
    사용자 조작으로 만들어진 인스턴스 같은 것을 다시 만드려면 만들어진 것을 일단 저장해 두고 만들고 싶을 때 그것을 복사함
    
3. 프레임워크와 생성하는 인스턴스를 분리하고 싶을 때
    
    인스턴스를 생성하는 프레임워크를 특정 클래스에 의존 관계를 분리하고 싶을 때
    
    클래스 이름을 지정해서 만들지 않고 원형이 될 인스턴스를 등록해두고 등록된 인스턴스를 복사해서 인스턴스를 생성하기
    
    인스턴스로부터 다른 인스턴스를 생성하는 것은 복사기로 문서를 복사하는것과 같음
    
    원본 서류를 어떻게 만들었는지 몰라도 복사기로 복사할 수 있음
    
    클래스에서 인스턴스를 생성하는 대신 인스턴스 → 인스턴스를 생성하는 방법을 배울거임
    

prototype : 원형, 모범

예제로 살펴보자

![image.png](Prototype%20Pattern%204e928605d71a44bf9faa94088d5b8726/image.png)

```java
package framework;

// java.lang.Cloneable 인터페이스를 상속하면 인스턴스 복제 가능
// 인스턴스는 clone 메소드를 사용해 복제 가능함

public interface Product extends Clonable {
	public abstract void use(Sting s);
	public abstract void Product createCopy();
}
---------------------------------------------------------
package framework;

import java.util.HashMap;
import java.util.Map;

// Product 인터페이스를 이용해 인스턴스를 복제하는 클래스

public class Manager {
	private Map<String,Product> showcase = new HashMap<>();
	// showcase에 구현한 클래스와 대응관계를 저장함
	
	public void register(Stirng name, Product prototype) {
		showcase.put(name, prototype);
	} // showcase에 인스턴스 저장하는 메소드
	// 무슨 클래스인지 모르고 Product로 하긴 했구나 하고 저장함

	public Product create(String prototypeName) {
		Product p = showcase.get(prototypeName);
		return p.createCopy();
	} // 인스턴스 복제하는 메소드
}
// 소스 코드 내 클래스 이름이 기술하면 그 클래스와 밀접한 관계가 형성됨
// 하지만 밀접하지 않다면 Product와 Manager는 독립적으로 수정 가능
---------------------------------------------------------
import framework.Product;

// Product를 구현하는 클래스

public class MessageBox implements Product {
	private char decochar;
	
	public MessageBox(char decochar) {
		this.decochar = decochar;
	}

	// use() 구현
	@Override
	public void use(String s) {
		int decoLen = 1 + s.legnth() + 1;
		for (int i = 0; i < decoLen; i++) {
			System.out.print(decochar);
		}
		System.out.println();
		System.out.println(decochar + s + decochar);
		for (int i = 0; i < decoLen; i++) {
			System.out.print(decochar);
		}
		System.out.println();
	}
	
	// 자기 자신을 복제하는 메소드 : clone() Java 언어 사양으로 규정
	// 복제를 생성할 때, 인스턴스가 가진 필드값도 그대로 복사함
	// Cloneable 구현 되있지 않으면 예외 발생
	// clone()은 자신 혹은 하위 클래스에서만 호출 가능
	// (다른 클래스의 요청으로 복제되니 메소드를 감 쌀 필요가 있음)
	@Override
	public Product createCopy() {
		Product p = null;
		try {
			p = (Product)clone();
			} catch (CloneNotSupportedException e) {
				e.printStackTrace();
			}
		return p;
	}
}
---------------------------------------------------------
import framework.Product;

public class UnderlinePen implements Product {
	pricate char ulchar;
	
	public UnderlinePen(char ulchar) {
		this.ulchar = ulchar;
	}
	
	@Override
	public void use(String s) {
		int ulen = s.length();
		System.out.println(s);
		for (int i = 0; i < ulen; i++) {
			System.out.print(ulchar);
		}
		System.out.println();
	}
	
	@Override
	public Product createCopy() {
		Product p = null;
		try {
			p = (Product)clone();
		} catch {
			e.printStackTrace();
		}
		return p;
	}
}
---------------------------------------------------------
import framework.Manager;
import framework.Product;

public class Main {
	public static void main(String[] args) {
		Manager manager = new Manager();
		UnderlinePen upen = new UnderLinePen('-');
		MessageBox mbox = new MessageBox('*');
		MessageBox sbox = new MessageBox('/');
		
		manager.register("strong message", upen);
		manager.register("warning box", mbox);
		manager.register("slash box", sbox);
		
		Product p1 = manager.create("strong message");
		p1.use("Hello, world");
		
		Product p2 = manager.create("warning box");
		p2.use("Hello, world");
		
		Product p3 = manager.create("slash box");
		p3.use("Hello, world");
	}
}
```

## 정리

- Prototype (원형)
    
    인스턴스를 복사해 새로운 인스턴스를 만들기 위한 메소드를 결정 (Product)
    
- ConcretePrototype (구체적인 원형)
    
    인스턴스를 복사하여 새로운 인스턴스를 만드는 메소드를 구현함
    
    (MessageBox, UnderlinePen)
    
- Client (이용자)
    
    인스턴스를 복사하는 메소드를 이용해 새로운 인터페이스를 만듦 (Manager)
    

### 클래스에서 인스턴스를 만들면 되지 않나?

**그냥 클래스 내부에서 new로 만들면 외않됨?**

목적이 있다… 다 목적이 있어.. 위에서 언급했긴 했지만 다시 짚고 가자

1. 종류가 너무 많아 클래스로 정리할 수 없는 경우
    
    위에선 3개의 프로토타입이 등장했음
    
    예제도 3개인데, 구현하기에 나름으로 많이 증식시킬수 있음
    
    코드를 일일히 읽기도 힘든데, 원형을 많이 만들어서 클래스가 너무 많으면 관리하기 힘듦…
    
2. 클래스로부터 인스턴스 생성이 어려울 때
    
    실감하기 어렵긴 함
    
    마우스로 도형을 그리는 앱을 상상해보자
    
    사용자가 마우스로 그린 도형을 나타내는 인스턴스와 똑같이 만들고 싶은데,
    
    무슨 도형인지 클래스로 모두 정의하기 어려움… 이럴 땐 클래스를 사용하지 말고 그냥 복사하는게 나음
    
3. 프레임워크와 생성하는 인스턴스를 분리하기
    
    clone하는 부분이 framework 패키지에 있음
    
    Manager의 create 메소드는 클래스 이름 대신 문자열로 인스턴스 생성을 위한 이름을 대체하고 있음
    
    이는 `new`  대신 더욱 일반화하여 클래스의 이름의 속박으로부터 프레임워크를 분리했다고 볼 수 있겠다.
    

### 클래스 이름은 속박이야 그럼?

소스 내부에 클래스 이름을 적는게 문제가 되는건가 싶음 그러면

근데 당연히 써야 뭔가가 되지 않음?이라고 생각이 든다

객체 지향의 원론을 다시 되짚어 보자. **“부품으로서 재사용을 지향한다”**

항상 옳고 그름을 나타낼 수 있는 법칙은 아니지만, 소스 내부에 이용할 클래스 이름이 있다면, 그 클래스와 분리해서 사용할 수 없게된다.

근데 코드를 수정하면 그만이겠지만, 위 원론에선 코드 수정을 고려하지 않는다.

다시말하면  `.java` 확장자로 된 파일이 없이 `.class` 파일만 있어도 그 클래스를 재사용 할 수 있도록 환경을 가정 했다고 보면 편함

밀접하게 결합하는 클래스 이름이 소스 내에서 사용되는건 문제가 없겠지만… 부품으로 독립시켜야하는 클래스 이름이 소스 내부에서 사용되는건 문제임

예제에선 Product가 부품이니까
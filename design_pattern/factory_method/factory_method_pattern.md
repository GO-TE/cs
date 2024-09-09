# Factory Method

인스턴스를 생성하는 공장을 Template Method 패턴으로 구성한 것

: 인스턴스 생성 방법을 상위 클래스에서 결정하고, 구체적인 클래스 이름까지는 결정하지 않음

구체적인 코드는 하위 클래스에서 구현

→ 인스턴스 생성을 위한 프레임워크와 실제 인스턴스를 생성하는 클래스를 나눌 수 있음

원래라면 그냥 하위 클래스에 생성자 만들어서 구현하지 않을까요 ?

분리 하는 이유를 열심히 생각해보면서 지켜봅시다.

예시로 신분증 카드를 만드는 클래스로 보자

여기서 `Factory`, `Product`는 프레임워크로, 인스턴스 생성을 위한 클래스이고

`IDCardFactory`, `IDCard` 는 프레임 워크를 상속하여 실제 인스턴스를 생성하는 클래스 ( = 구체적인 코드를 구현하는 클래스)

![image.png](Factory%20Method%20d03de1d2a689433c9158a279e29b3673/image.png)

실제 동작하는 

```java
package framework;
// 추상 메소드 use만 정의한 추상 클래스
public abstract class Product {
	public abstract void use();
}
-----------------------------------------------------------
package framework;
// 템플릿 메소드 패턴이 사용되는 클래스 (인스턴스를 생성할 때, 이미 작업은 정해져 있음)
// 여기서 제품을 만들고, 제품을 등록하는 코드는 추상화 했음
// 이전에 package로 모아서 쓰니까 마음대로 호출이 가능 하죠?
public abstract class Factorty {
	public final Product create(String owner) {
		Product p = createProduct(owner);
		registerProduct(p);
		return p;
	}
	
	protected abstract Product createProduct(String owner);
	protected abstract void registerProduct(Product product);
}
-----------------------------------------------------------
package idcard;

import framework.Product;
// 프레임워크와 분리됨을 명시하고자 idcard 패키지를 따로 만듦
// Product 클래스의 하위 클래스

public class IDCard extends Product {
	private String owner;
	
	IDCard(String owner) { // 같은 패키지만 호출 가능...
		System.out.println(owner + "의 카드를 발급합니다.");
		this.owner = owner;
	}
	
	@Override // use 메소드 구현
	public void use() {
		System.out.println(this + "를 사용합니다");
	}

	@Override // object 객체의 기본 메소드 그래서 오버라이드
	public String toString() {
		return "[IDCard:" + owner + "]";
	
	public String getOwner();
		return owner;
	}
}
-----------------------------------------------------------
package idcard;

import framework.Factory;
import framework.Product;
// 얘는 Factory의 하위 클래스
// IDCard의 생성자가 아닌 얘가 직접 IDcard의 인스턴스를 생성함
// 만들고 등록해주는 관리자가 따로 있다는 점

public class IDCardFactory extends Factory {
	@Override
	protected Product createProduct(String owner) {
		return new IDCard(owner);
	}
	
	@Override
	protected void registerProduct(Product product) {
		System.out.println(product + "을 등록했습니다.");
	}
}
-----------------------------------------------------------
import framework.Factory;
import framework.Product;
import idcard.IDCardFactory;

public class Main {
	public static void main(String[] args) {
		Factory factory = new IDCardFactory();
		Product card1 = factory.create("Gyeongtae Min");
		Product card2 = factory.create("Minho Seo");
		Product card3 = factory.create("Hun Choi");
		
		card1.use();
		card2.use();
		card3.use();
	}
}
-----------------------------------------------------------
```

### 정리

- Product (제품)
    
    프레임워크 쪽, 이 패턴으로 생성되는 인스턴스가 가져야 할 인터페이스를 결정하는 클래스 (예제에선 Product)
    
- Creator (작성자)
    
    프레임워크 쪽, Product 역을 생성하는 추상 클래스
    
    Creator 역은 실제로 생성한 Product 하위 클래스에 대해 알 수가 없음
    
    아는건 단지 Product와 인스턴스 생성 메소드를 호출하면 Product가 생성된다는것만 앎
    
    `new`를 사용해 실제 인스턴스를 생성하는 대신, 인스턴스를 생성하는 메소드를 호출함으로써 구체적인 클래스 이름에 의한 속박에서 상위 클래스를 자유롭게 만듦
    

- ConcreteProduct (구체적인 제품)
    
    Product의 하위 클래스 (예제에선 IDCard)
    
- ConcreteCreator (구체적인 작성자)
    
    Creator의 하위 클래스 (예제에선 IDCardFactory)
    

> **근데 이렇게 분리하면 뭐가 좋음?**
> 
> 
> `프레임워크` 와 `구체적인 내용` 이라는 두가지 측면이 존재함
> 
> 여기서 같은 프레임워크를 사용해 전혀 다른 제품과 공장을 만든다고 하자.
> 
> 그러면 새로운 framework의 패키지를 만들어야 함 (구체적으로 작성하거나, 하위 클래스에 의존 되어 있다면…)
> 
> 근데, 기존에 쓰던 프레임워크를 수정하지 않고 다른 제품, 공장을 만들 수 있음에 주목하자 (하위 클래스에 의존하지 않아, 그냥 framework를 그대로 쓸 수 있다)
> 
> → framework 패키지는 idcard 패키지에 의존하지 않는다.
> 
> 우리가 디자인 패턴을 사용하는 궁극적인 이유는 `유지보수` 를 편하게 하기 위해서다.
> 

인스턴스 생성 - 메소드 구현 방법

Factory 클래스의 createProduct는 추상 메소드임 → 하위 클래스에서 구현해야함

이 메소드를 작성하는 법은 두 가지로 나눌 수 있음

1. 추상 메소드로 기술
    
    예제처럼 상속받은 하위 클래스가 구현하지 않으면 컴파일 단계에서 오류 검출
    
2. 디폴트 구현을 준비하기
    
    하위 클래스에서 따로 구현하지 않을 때 디폴트 구현 사용
    
    이 경우에는 Product 클래스에 대해 직접 new를 실행해서 Product를 추상 클래스로 둘 수가 없음
    
    ```java
    class Factory {
    	public Product createProduct(String name) {
    		return new Product(name);
    	}
    	...
    }
    ```
    

근데 Template method, Factory도 굳이 쪼개놔서 하나의 클래스만 읽어서 어떻게 동작하는지 어려워 보임

상위 클래스 → 추상 메소드 → 하위 클래스의 소스코드 이런식으로 내려가면서 이해해야함

일반적으로 디자인 패턴을 사용해 어떤 클래스를 설계 할 때,

그 클래스를 보수하는 사람에게 설계자가 의도한 디자인 패턴이 뭔지 잘 전달해야 함

그렇지 않다면 의도를 벗어난 수정 …이 있을지도? (주석이나 도큐멘트로 잘 남겨두자)
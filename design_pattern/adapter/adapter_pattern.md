# Adapter Pattern

이미 제공된 코드를 그대로 사용할 수 없을 때, 필요한 형태로 변환한 후 이용하는 패턴

`이미 제공된 것`과 `필요한 것`의 `차이`를 매워주는 패턴이 어댑터 패턴

예를 들어 노트북을 충전할 때, 220볼트를 제공하는 콘센트에 충전기를 꽂으면 12볼트로 변환해서 노트북 전원장치에 전류를 제공한다.

여기서 우리는 전기를 노트북에 제공하는게 포인트다.

제공 된 것 : 220볼트

변환 장치 : 어댑터

필요한 것 : 12볼트

차이 : 단위

Adapter pattern은 두 종류가 있음

1. 클래스에 의한 Adapter 패턴(상속을 사용한 패턴)
    
    ![image.png](Adapter%20Pattern%20c2201717c904489b83dfeb1ebe68c59d/image.png)
    
    ```java
    public interface Print {
    	public abstract void printWeak();
    	public abstract void printStrong();
    }
    
    ------------------------------------------------------
    
    public class Banner {
    	private String string;
    	
    	public Banner(String string) {
    		this.string = string;
    	}
    	
    	public void showWithParen() {
    		System.out.println("(" + string + ")");
    	}
    	
    	public void showWithAster() {
    		System.out.println("*" + string + "*");
    	}
    }
    
    ------------------------------------------------------
    
    /*
    PrintBanner가 어댑터 역할을 함
    준비된 Banner Class를 확장해서 메소드를 상속받음
    그리고 Print 인터페이스를 구현해서 메소드를 구현함
    */
    
    public class PrintBanner extends Banner implements Print {
    	public PrintBanner(String string) {
    		super(String);
    	}
    	
    	@Override
    	public void printWeak() {
    		showWithParen();
    	}
    	
    	@Override
    	public void printStrong() {
    		showWithAster();
    		
    ------------------------------------------------------
    public class Main {
    	public static void main(String[] args} {
    		Print p = new PrintBanner("Hello");
    		p.printWeak();
    		p.printStrong();
    	}
    }
    ```
    
    Main 클래스는 Print의 Interface를 사용해서 기능을 사용중임
    
    Banner나 Banner의 메소드는 Main에서 완전히 숨겨져 있음
    
    노트북이 12볼트로 충전하는데 들어오는 전류가 220인지도 모르고 쓰는거 처럼
    

1. 인스턴스에 의한 Adapter 패턴(위임을 사용한 패턴)
    
    ![image.png](Adapter%20Pattern%20c2201717c904489b83dfeb1ebe68c59d/image%201.png)
    
    ```java
    public abstract class Print {
    	public abstract void printWeak();
    	public abstract void printStrong();
    }
    
    ------------------------------------------------------
    // 위와 동일
    public class Banner {
    	private String string;
    	
    	public Banner(String string) {
    		this.string = string;
    	}
    	
    	public void showWithParen() {
    		System.out.println("(" + string + ")");
    	}
    	
    	public void showWithAster() {
    		System.out.println("*" + string + "*");
    	}
    }
    
    ------------------------------------------------------
    
    public class PrintBanner extends Print {
    	private Banner banner;
    	
    	public PrintBanner(String string) {
    		this.banner = new Banner(string);
    	}
    
    	@Override
    	public void printWeak() {
    		banner.showWithParen();
    	}
    	
    	public void printStrong() {
    		banner.showWithAster();
    	}
    }
    
    ------------------------------------------------------
    // 위와 동일
    public class Main {
    	public static void main(String[] args} {
    		Print p = new PrintBanner("Hello");
    		p.printWeak();
    		p.printStrong();
    	}
    }
    ```
    
    앞에선 `Banner` 를 상속받아서 구현했는데, 여기선 추상 클래스 `Print` 를 확장해서 `Banner` 를 내부에서 생성해서 써버림
    
    사실 그냥 껍데기에 불과하고 일은 Banner가 다 하게 됨 → 다른 인스턴스에게 위임함
    

### 정리

- Target (대상)
    
    지금 필요한 메스드를 결정함 (노트북에 전류 공급해주는 12볼트)
    
    위 예제에선 `Print` 가 담당
    
- Client (의뢰자)
    
    Target의 메소드를 사용해 일함 (노트북 그 자체)
    
    위 예제에선 `Main`이 담당
    

- Adapter (적응자)
    
    Adapter의 메소드를 이용해 Target을 동작하도록 하는 역할이자 임무
    
    (220볼트를 12볼트로 변환해주는 어댑터 역할)
    
    예제에선 `PrintBanner` 가 수행함
    

> **근데 언제 씀?**
> 
> 
> 프로그래밍 할 때, 늘 백지 상태에서 시작하는게 아님
> 
> 이미 존재하는 클래스 쓰는 경우가 많을 텐데, 해당 클래스가 충분히 테스트를 해서 검증되었다면 다른 클래스로 막 바꾸기 좀 그럴 것임 (레거시..?)
> 
> 사실 바꾸기 보다는 재사용을 원함 (유지보수가 편함) → 테스트 코드도 다시 해야됨…
> 
> 그래서 Adapter Pattern은 기존에 쓰던 Class에 한 겹 덧씌워 필요한 Class를 만듬
> 
> 만약 버그가 생겨도 Adapter 역할의 클래스를 중점적으로 살펴보면 편함
> 
> 소프트웨어는 버전 업이 거의 필수적이긴 함
> 
> → 구버전과 호환성이 관건
> 
> 흔히 말하는 레거시 시스템을 버리면, 유지 보수는 잠시나마 편해질 수 있는데, 100%는 아님
> 
> Adapter 패턴은 여기서 신버전과 구버전을 공존시키고 좀 더 편하게 도와줌
> 
> (구버전 = Target, 신버전 = Client, 이를 대응해주는 Adapter 만들기)
> 

> 그리고 상속과 위임 중 어디서 어떻게 쓰냐?
> 
> 
> 일반적으로 상속보단 위임이 더 사용하는데 문제가 적음
> 
> 상위 클래스의 내부동작을 잘 모르면, 상속을 효과적으로 활용하기 어려움
>
# Singleton Pattern

이 패턴은 클래스에 대한 인스턴스를 단 하나만 만드는 패턴이다.

왜 단 하나만 존재해야하는지 이유가 뭘까요?

예를 들면 시스템 전체를 표현한 클래스나 현재 시스템 설정을 표현한 클래스 혹은 각종 상수를 위해 하나만 존재할 수도 있다.

일단 위 예시들은 상수적인 면모를 띈다.

무슨말이냐면, 일단 다른 클래스들이 갖다 쓰는 상수라고 했을 때, 메모리 상에 여러 인스턴스가 존재할 필요가 없다. (자원만 먹음)

그리고 시스템적으로 불변하는 같은 값을 쓴다는 것을 보증해 줄 수도 있다.

개발자들이 약속으로 하나만 만들자~ 이런 것보단 시스템적으로 보증되는게 좋다.

아무리 인스턴스를 만드려고 해도 계속 단 하나만 존재 하는 것이죠

예시로 들어볼게요

![image.png](Singleton%20Pattern%208d67b192dfc34dee8c52395e145a9131/image.png)

근데 그냥 `new Singleton()` 하면 되는거 아님?

이라고 생각할수도 있겠는데, 생성자 권한을 `private`로 줄여버리고, 생성 자체를 `Singleton` 클래스가 관리하면 외부 클래스에서 건들지도 못한다. (컴파일 에러 뜸)

[근데 `private` 는 무적이 아니에요..](./근데_private는_무적이_아니에요.md)

```java
public class Singleton {
	private static Singleton singleton = new Singleton();
	
	private Singleton() {
		System.out.println("인스턴스를 생성");
	}
	
	public static Singleton getInstance() {
		return singleton;
	} // 정적 메소드로 호출하면 인스턴스를 만들지 않아도 메소드를 쓸 수 있음
	
------------------------------------------------------------
public class Main {
	public static void main(String[] args) {
		Singleton s1 = Singleton.getInstane();
		Singleton s2 = Singleton.getInstane();
			
		if (s1 == s2) {
			System.out.println("같은 인스턴스");
		} else {
			System.out.println("다른 인스턴스");
		}
	}
}
/*
같은 인스턴스
*/
```

또 다른 방식으로 싱글톤 할 수 있음

```java
public class Singleton {
	private static Singleton singleton;
	
	private Singleton() {
	}
	
	public static Singleton getInstance() {
		if (singletoneObject == null) {
			singleton = new Singleton();
		}
		return singleton;
	}
}
```

근데 이런식으로 생성하면 멀티 스레드 환경에서 동시성 문제 발생할 수도 있음

그리고 이전 방법은 static해서 인스턴스를 안쓴다고 해도 이미 생성되서 메모리 먹긴 함

### 동시성 문제 해결 방법

1. Synchronized 적용 (Lazy initialization)
    
    `genInstnace()` 에 한 스레드만 접근 가능하게 만들기
    
    ```java
    ...
    public static synchronized Singleton getInstance() {
    	if (singleton == null) {
    		singleton = new Singleton();
    	}
    	return singleton;
    } ...
    ```
    
    단점 : 여러 스레드가 접근할 시 동기화 작업(Lock) 때문에 성능 저하 우려
    
2. 이른 초기화 (eager initialization)
    
    싱글톤 객체의 오버헤드가 크지 않은 경우에 추천
    
    ```java
    public class Singleton {
        private static final Singleton SINGLETON_OBJECT = new Singleton();
    
        private Singleton() {
        }
    
        public static synchronized Singleton getInstance() {
            return SINGLETON_OBJECT;
        }
    }
    ```
    
    변수 선언 동시에 초기화해서 동시성 문제 ㄱㅊ음
    
    `final` 로 생성해서 불변하도록 만들고, `static` 으로 컴파일하면서 생성시켜버림
    
    근데 위에서 언급했듯이, 사용안하면 메모리 낭비일 뿐…
    
3. Double checked locking
    
    1번의 성능 저하를 가능성을 개선한 방법
    
    ```java
    public class Singleton {
        private static volatile Singleton singleton;
        
        private Singleton() {
        }
        
        public static Singleton getInstance() {
            if (singleton == null) {
                synchronized (Singleton.class) {
                    if (singleton == null) {
                        singleton = new Singleton();
                    }
                }
            }
            return singleton;
        }
    }
    ```
    
    `volatile` 키워드를 처음 볼수도 있겠는데, 얘는 뭐냐면
    
    `volatile` 을 사용해서 변수를 선언하면 메인 메모리에서 모든 연산 작업이 수행되도록 해주는 키워드
    
    멀티 스레드 환경에서 스레드는 메인 메모리의 값을 복사해서 CPU cache에 저장하고 작업을 수행함
    
    이 말은 즉슨 각 스레드가 인스턴스 원본 가지고 작업하는게 아니라 메인 메모리에서 복사한 짭가지고 작업한다는 소리임
    
    `volatile` 키워드는 모든 스레드가 항상 같은 공유 변수의 값을 읽도록 보장시켜주는 것임
    
    그리고 `synchronized (Singleton.class)` 는 Singleton 클래스에 대한 메타 데이터를 가지고 있는 Class 객체에 대해 한 스레드만 접근하도록 하는 것…
    
    일단 다른점은 최초로 인스턴스를 할당 할 때만 접근한다는 점이 다름
    
    1번은 이미 할당 되었어도 하나의 스레드만 접근하는데
    
    2번은 일단 접근하고, 최초 생성일때만 하나의 스레드가 접근한다는 점이 다름
    
4. Bill Pugh Singleton
    
    정적 내부 클래스를 활용해서 Singleton 객체를 생성하는 방법
    
    → 클래스가 처음 로드될 때 로드되지 않고, 해당 클래스가 실제로 참조될 때 로드되기에 지연 초기화가 가능함
    
    ```java
    public class Singleton {
        // Singleton 객체는 내부 정적 클래스에서 생성
        private Singleton() {
            // private 생성자로 외부에서의 객체 생성을 방지
            System.out.println("인스턴스 생성");
        }
        
        // 정적 내부 클래스: SingletonHolder
        private static class SingletonHolder {
            // Singleton 클래스의 인스턴스 생성
            private static final Singleton INSTANCE = new Singleton();
        }
        
        // getInstance() 메서드에서 SingletonHolder 클래스의 INSTANCE를 반환
        public static Singleton getInstance() {
            return SingletonHolder.INSTANCE;
        }
    }
    ```
    
    내부에 클래스 하나 더 넣어서 Singleton 클래스가 호출될 때 SingletonHolder가 같이 호출됨 (Singleton 클래스가 로딩될 때 로딩되는건 아님)
    
    단점 : 자바 리플렉션과 직렬화를 통해 싱글톤이 파괴 될 수도 있음
    
5. 그냥 Enum 쓰셈
    
    생성자를 private하게 만들고 상수만 갖는 클래스라 싱글톤의 성질을 가짐
    
    단점은 Enum외 클래스는 상속 불가능
    

## 정리

- singleton
    
    유일한 인스턴스를 얻기 위한 static method를 가지고 있음 ( 늘 하나의 동일한 인스턴스를 반환)
    

> 도시테 하나의 인스턴스만…?
> 

제한을 둔다는것은 전제 조건을 늘린다는 뜻

인스턴스가 여러개 있으면 서로 영향을 미쳐 뜻밖에 버그를 만들수도있음

그러나 하나뿐이라는 보장이 있다면 그 전제 조건하에 프로그래밍 가능
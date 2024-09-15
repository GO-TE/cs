# 근데 private 는 무적이 아니에요..

`private`로 다른 클래스의 접근을 막고 싶을수도 있겠지만… 무적이 아니에요

이걸 깨 부숴버릴수가 있는데, 2가지 방법을 찾아버림

1. 역직렬화 (Deserialization)
    
    직렬화(Serialization)는 JVM의 힙 메모리에 있는 객체 데이터를 바이트 스트림 형태로 바꿔 외부 파일로 내보낼 수 있게 만드는 기술을 말함
    
    역직렬화는 반대로 외부로 내보낸 직렬화 데이터를 다시 읽어 자바 객체로 변환하는 것을 말함
    
    직렬화 된 파일은 DB에 저장하거나 네트워크를 통해 전송되기도 함
    
    이 직렬화를 적용하기 위해선 클래스에 Serializable 인터페이스를 구현하면 됨
    
    ```java
    class Singleton implements Serializable {
    	private Singleton() {}
    	
    	private static class SingletonHolder {
    		private static final Singleton INSTANCE = new Singleton();
    	}
    	
    	public static Singleton getInstance() {
    		return SingletonHolder.INSTANCE;
    	}
    }
    -------------------------------------------------------------
    public class Main {
    	public static void main(String[] args) {
    		Singleton s1 = Singleton.getInstnace();
    		
    		String fineName = "singleton.obj";
    		
    		ObjectOutputStream out = new ObjectOubptStream(
    															new BufferedOutputStream(
    																new FileOutputStream(fileName)));
    		out.writeObject(s1);
    		out.close();
    		
    		ObjectInputStream in = new ObjectInputStream(
    														new BufferedInputStream(
    															new FileInputStream(fileName)));
    		Singleton s2 = (Singleton) in.readObject();
    		in.close();
    		
    		System.out.println(s1 == s2);
    	}
    } // false 출력
    ```
    
    근데 싱글톤이 어떻게 깨지냐
    만약에 어떤 클래스를 직렬화해서 다른 호스트로 전송하려고 하는데, 이 클래스를 싱글톤으로 구성했다고 치자
    
    그러면 이 클래스는 송신자가 파일을 받고 역직렬화를 한다면 더 이상 싱글톤이 아니게 된다.
    
    → 역직렬화 자체가 보이지 않는 생성자의 역할을 수행함 (또 다른 인스턴스를 만들어 버림)
    
- **Solution**
    
    `readResolve()` 메소드를 정의하자
    
    이게 뭐냐면 역직렬화 과정에서 `readObject()`를 통해서 만들어진 인스턴스 대신 `readResolve()` 에서 원하는 것을 반환하도록 수정할 수 있음
    
    이렇게 하면 기존 역직렬화되어 생성된 인스턴스는 가비지 컬렉션 해버림
    
    ```java
    class Singleton implements Serializable {
    	private Singleton() {}
    	
    	private static class SingletonHolder {
    		private static final Singleton INSTANCE = new Singleton();
    	}
    	
    	public static Singleton getInstance() {
    		return SingletonHolder.INSTANCE;
    	}
    	
    	private Object readResolve() { 
    		return SingletonHolder.INSTANCE;
    	}
    }
    ```
    
    이거뿐 아니라 직렬화 된 싱글톤 인스턴스는 어차피 실행중인 프로그램의 인스턴스가 데이터를 가지고 있기 때문에 `transient` 키워드를 사용할 수도 있다.
    
    이게 뭐냐면 직렬화 할 때, 해당 `transient` 로 선언된 변수들은 직렬화 하지 않도록 하는 키워드임
    
    불필요한 데이터 직렬화 방지 (설정 값이나 캐시 값 등등) → 성능 ㄱㅊ, 일관성 유지
    

1. 리플렉션 (reflection)
    
    자바 리플렉션은 객체를 통해 클래스의 정보를 분석해서 런타임에 클래스 동작을 조작하는 기법
    
    클래스 파일 위치나 이름만 알면 정보를 알고 객체 생성도 가능함
    
    ```java
    public class Main {
    	public static void main(String[] args) {
    		// 클래스 생성자 가져오기
    		Constructor<Singleton> constructor = Singleton.class.getDeclaredConstructor();
    		// private 된 생성자 바꿔버리기
    		constructor.setAccessible(true);
    		
    		Singleton s1 = constructor.newInstance();
    		Singleton s2 = constructor.newInstance();
    		
    		System.out.println(s1 == s2);
    	}
    }
    ```
    
- **Solution**
    
    내가 이전에 언급함
    
    그냥 enum 쓰자.
    
    얘는 그냥 어찌됐든간에 단 하나만 있도록 시스템에서 보장해주는 애다.
    
    직렬화도 가능하고, 멀티 쓰레드 환경에서도 안전함
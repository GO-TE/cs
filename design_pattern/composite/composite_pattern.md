# Composite Pattern

그릇과 내용물을 동일시한다… 동일시하여 재귀적인 구조를 만든다..

이게 무슨말일까? 

컴퓨터 파일 시스템은 디렉터리가 있음.

이 디렉터리 안에 또 디렉터리가 있을수도 있고 파일이 있을 수 있음.

→ 중첩된 구조, 재귀적인 구조

그런데, 디렉터리와 파일은 다르지만, 둘 다 “디렉터리 안에 넣을 수 있는 것”으로 묶을 수 있음

이를 합쳐서 디렉터리 엔트리라고 하고 디렉터리 엔트리라는 이름으로 디렉터리와 파일을 같은 종류로 간주한다.

이렇게 디렉터리(그릇), 파일(내용물)을 동일시하여 같은 종류로 취급하면 편해지는 경우가 있음

이러한 패턴을 Composite Pattern이라고 함 (Composite은 혼합물, 복합물이라는 뜻)

예제로 봐서 편한지 검증해보자구요

파일과 디렉터리를 도식적으로 표현한 프로그램으로 클래스 다이어 그램을 봅시다

![image.png](Composite%20Pattern%2010dbd15cbfe780a0b820cb073348dc39/image.png)

```java
public abstract class Entry {
	public abstract String getName();
	public abstract int getSize();
	
	public void printList() {
		printList("");
	}
	
	protected abstract void printList(String prefix);
	
	@Override
	public String toString() {
		return getName + " (" + getSize() + ")"'
	}
}
------------------------------------------------------------
public class File extends Entry {
	private String name;
	private int size;
	
	public File(String name, int size) {
		this.name = name;
		this.size = size;
	}
	
	@Override
	public String getName() {
		return name;
	}
	
	@Override
	public int getSize() {
		return size;
	}
	
	@Override
	protected void printList(String prefix) {
		System.out.println(prefix + "/" + this);
	}
}
------------------------------------------------------------
import java.util.ArrayList;
import java.util.List;

public class Directory extends Entry {
	private String name;
	private List<Entry> directory = new ArrayList<>();
	
	public Directory(String name) {
		this.name = name;
	}
	
	@Override
	public String getName() {
		return name;
	}
	
	@Override
	public int getSize() {
		int size = 0;
		for (Entry entry: directory) {
			size += entry.getSize();
		}
		return size;
	}
	
	@Override
	protected void printList(String prefix) {
		directory.add(entry);
		return this;
	}
}
------------------------------------------------------------
public class Main {
	public static void main(String[] args) {
		System.out.println("Making root entries");
		
		Directory rootdir = new Directory("root");
		Directory bindir = new Directory("bin");
		Directory tmpdir = new Directory("tmp");
		Directory userdir = new Directory("usr");
		
		rootdir.add(bindir);
		rootdir.add(tmpdir);
		rootdit.add(userdir);
		
		bindir.add(new File("vi", 10000));
		bindir.add(new File("latex", 20000));
		rootdir.printList();
		System.out.println();
		
		System.out.println("Making user entries");
		Directory gote = new Directory("go-te");
		Directory min = new Directory("min");
		Directory seo = new Directory("seo");
		
		userdir.add(gote);
		userdir.add(min);
		userdir.add(seo);
		
		gote.add(new File("diary.html", 100));
		gote.add(new File("Compiste.java", 200));
		min.add(new File("memo.txt", 300));
		seo.add(new File("game.doc", 400));
		seo.add(new File("trash.trash", 500));
		
		rootdir.printList();
	}
}
```

### 정리

- Leaf (잎)
    
    내용물을 나타냄. 이 안에는 다른 무언가를 넣을 수 없음 (예제에서 File)
    

- Composite (복합체)
    
    그릇을 나태내고 Leaf나 Composite을 넣을 수 있음 (예제에선 Directory)
    

- Component
    
    Leaf와 Composite을 동일시 하기 위한 역할로 두 역할의 공통되는 상위 클래스로 구현됨 (예제에서 Entry)
    

- Client (의뢰자)
    
    Composite 패턴의 사용자로 예제에선 Main
    

개인적인 생각으로, 이런 식으로 구현해야하는 경우는 디렉터리 파일 시스템 말고 뭔가 더 있을까?

고민을 해보았는데…. 간단하게 생각해보면 요소와 배열을 동일시하여 배열 내부에 배열이 또 있고 요소가 있으면 구조적으론 되게 복잡하고 헷갈리는 방향이 아닐까 생각을 했다.

디자인 패턴이 무조건 좋아! 이게 아니라 장단점이 있다는 말을 하고 싶다.

> 복수와 단수 동일시 하기
> 

그릇과 내용물을 동일시하는 패턴인데 복수와 단수의 동일시라고 할 수 있음

예를 들어, 프로그램 동작 테스트를 모아서 할 때 Composite 패턴을 사용할 수 있음

KeyBoardTest에서 키보드 입력 테스트를 하고, FileTest에서 파일 입력 테스트를 하고, NetworkTest에서 네트워크 입력테스트를 한다고 하자

이때 셋을 한번에 다루고 싶으면 Composite 패턴을 사용해 여러 테스트를 모아 InputTest라는 입력 테스트로 묶는 것입니다.

마찬가지로 OutputTest로 묶을수도 있는거구요.

> add의 위치
> 

예제 프로그램에서는 디렉터리 엔트리를 추가하는 add 메소드를 Directory에 정의했음

디렉터리 엔트리를 추가하는건 디렉터리만 가능해서 타당해 보임

GoF 책에서도 자식을 조작하는 메소드도 Component에서 정의하고 그런 경우 Leaf에 대해 자식을 조작하는 요청이 발생하면 오류처리가 필요함

근데 자식을 조작하는 메소드는 Composite과 Component 중 어디에 정의하는게 맞을까?

결국 그릇과 내용물을 동일시 한 결과로 얻어지는 건 무엇인가? 에 대한 질문으로 예제 프로그램으로 말하면 Entry 클래스는 무엇인가라는 질문에 답하는 것임

설계자는 그 클래스가 가져야 할 메소드를 정하고, 그 클래스는 무엇인가에 답하는 일이며 해당 클래스가 가져야 할 책무를 명확히 하는 일임
# Visitor Pattern

데이터 구조를 돌아다니며 처리를 하는 패턴

가장 큰 특징으로 데이터 구조와 처리하는 역할을 분리한다.

데이터 구조 안에 많은 요소가 저장되어 있고, 각 요소에 대해 어떠한 처리를 한다고 가정할 때, 처리하는 코드는 어디에 작성하는게 best일까?

일반적으로 데이터 구조 클래스 내부에 작성하겠지만, 그 task의 종류가 하나가 아니라면 ? → 새로운 task가 생길 때 마다 데이터 구조 클래스를 수정해야함

근데, task를 하는 클래스를 분리하여 데이터 구조의 클래스를 방문하며 task를 처리하도록 만드는 패턴임

새로운 데이터 처리 방법을 추가할 땐, 새로운 방문자 역할의 클래스를 만들면 됨

데이터 구조 클래스는 방문자를 받아주는 코드를 넣고…

예제로 봅시다

파일 시스템에서 디렉터리를 돌아다니며 파일 목록을 표시하는 프로그램

![image.png](Visitor%20Pattern%20111bd15cbfe780f88bb4f5a116e13513/image.png)

```java
/*
방문자를 나타내는 추상클래스로 이 방문자가 방문하는 데이터 구조에 의존함
오버로딩으로 매개변수가 다른 똑같은 함수가 있음
*/
public abstract class Visitor {
	public abstract void visit(File file);
	public abstract void visit(Directory dir);
}
------------------------------------------------------------
// 방문자를 받아들이는 인터페이스
// accpet 메소드로 visitor의 방문을 처리
public interface Element {
	public abstract void accpet(Visirot v);
}
------------------------------------------------------------
/*
element (방문자를 받아들이는 인터페이스)의 구현한 클래스
File과 Directory의 상위 클래스
*/
public abstract class Entry implements Element {
	public abstract String getName();
	public abstract int getSize();
	
	@Override
	public String toString() {
		return getName() + " (" + getSize() + ")";
	}
}
------------------------------------------------------------
// Element를 확장한 File 클래스
public class File extends Element {
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
	public void accept(Visitor v) {
		v.visit(this);
	}
}
------------------------------------------------------------
// entry를 확장하고 Iterable의 인터페이스를 구현함
// iterator:디렉터리에 포함된 목록을 얻기 위한 Iterator<Entry>를 반환
import java.util.ArrayList;
import java.uitl.Iterator;
import java.util.List;

public class Directory extends Entry implements Iterable<Entry> {
	private String name;
	private List<Entry> dir = new ArrayList<>();
	
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
		for (Entry entry: dir) {
			size += entry.getSize();
		}
		return size;
	}
	
	public void add(Entry entry) {
		dir.add(entry);
		return this;
	}
	
	@Override
	public Iterator<Entry> iterator() {
		return dir.iterator();
	}
	
	@Override
	public void accpet(Visitor v) {
		v.visit(this);
	}
}
------------------------------------------------------------
/*
Visitor의 하위 클래스로 추상 메소드를 구현함
currentdir은 현재 주목하는 디렉터리를 저장하기 위함
*/
public class ListVisitor extends Visitor {
	private String currentdir = "";
	
	// 파일을 visit할 때 하는 처리 + accpet 메소드에서 호출됨
	@Override
	public void visit(File file) {
		System.out.println(currentdir + "/" + file);
	}

	// 마찬가지로 directory를 돌면서 accept로 호출됨
	@Override
	public void visit(Directory dir) {
		System.out.println(currentdir + "/" + dir);
		String savedir = currentdir;
		currentdir = currentdir + "/" + dir.getName();
		for (Entry entry: dir) {
			entry.accept(this);
		}
		currentdir = savedir;
	}
}
------------------------------------------------------------
public class Main {
	public static void main(String[] args) {
		System.out.println("Making root entries.");
		
		Directory root = new Directory("root");
		Directory bin = new Directory("bin");
		Directory tmp = new Directory("tmp");
		Directory user = new Directory("usr");
		
		root.add(bin);
		root.add(tmp);
		root.add(user);
		
		bin.add(new File("vi", 10000));
		bin.add(new File("latex", 20000));
		root.accpet(new ListVisitor());
		System.out.println();
		
		System.out.println("Making user entries.");
		Directory gote = new Direcotry("gote");
		Directory min = new Directory("min);
		
		user.add(gote);
		user.add(min);
		
		gote.add(new File("index.html", 100));
		min.add(new File("study.java", 200));
		root.accept(new ListVisitor());
	}
}
```

```java
//result
Making root entries.
/root (30000)
/root/bin (30000)
/root/bin/vi (10000)
/root/bin/latex (200000)
/root/tmp (0)
/root/user (0)

Making user entries.
/root (30300)
/root/bin (30000)
/root/bin/vi (10000)
/root/bin/latex (200000)
/root/tmp (0)
/root/user (300)
/root/user/gote (100)
/root/user/gote/index.html (100)
/root/user/min (200)
/root/user/min/study.java (200)
```

님들아

Visitor이랑 Element의 상호호출을 히해할 수 있음?

시퀸스 다이어그램으로 보면 조금 편함

![image.png](Visitor%20Pattern%20111bd15cbfe780f88bb4f5a116e13513/image%201.png)

### 정리

- Visitor
    
    데이터 구조의 전체적인 요소마다 방문했다는 visit 메소드를 선언함 (인터페이스)
    
    (예제에선 Visitor)
    
- Concrete Visitor
    
    Visitor를 구현하고 각 인스턴스마다 처리할 기능을 구체적으로 작성
    
    예제에서 ListVisitor 
    
- Element
    
    Visitor가 방문할 곳을 나타내고, 방문자를 받아들이는 accept 메소드를 선언함
    
    Visitor가 인수로 전달됨
    
- Concrete Element
    
    Element를 구체적으로 구현 (File, Directory)
    
- Object Structure
    
    Element를 집합으로 다뤄 Concrete Visitor가 각각 Element를 취급할 수 있는 메소드를 갖고 있음 (예제에선 Directory로 iterator 메소드)
    

님들아 Double dispatch라는게 있는데, 

accept 메소드는 `element.accpet(visitor)`  이렇게 호출되고

visit 메소드는 `visitor.visit(element)` 이렇게 호출되잖아요

완전 반대 관계로 보이지 않나요? element는 방문자를 허가하고 visitor는 요소를 방문하고…

Visitor 패턴은 요소와 방문자 역의 조합으로 서로 실제 처리를 결정하는데 이를 더블 디스패치라고 함

근데 헷갈리죠

이렇게 데이터 구조와 일 처리를 하는 클래스를 나눠버리고, 구현한 로직도 헷갈리고

그냥 for문으로 돌리면 되는데 라고 생각하면 꿀밤 한대 갈기셈

예전에 말했죠 객체 지향의 이점을 활용하도록 부품으로서의 역할을 확실히 나누어 유지보수 쉽게 하는게 디자인 패턴이라고

Task와 데이터 구조를 분리해서 File과 Directory의 부품으로서의 독립성을 높여줄 수 있음

데이터 구조를 이루는 Directory나 File에 visitor의 기능인 printList라는 메소드를 추가하고, 또다른 기능을 추가하려면 이미 완성된 코드를 수정하는 것과

처리를 하는 visitor 클래스를 확장해서 또다른 기능을 구현하는것과 무엇이 더 쉬운진 조금만 생각해보면 나올거같아요

여기서 OCP (The Open-Close Principle) 개방 폐쇄 원칙이 나오는데, 

확장에 대해선 열려있고, 수정에 대해서는 닫는다. 라는 원칙임

클래스를 설계할 때 이유없이 확장을 막으면 안된다

그리고 확장할 때마다 기존 클래스를 수정하는건 번거롭고 확장하더라도 기존 클래스를 수정할 필요가 없이 확장할 수 있도록 해야한다. 라는 뜻임

클래스의 요구가 빈번하게 바뀌는데, 대체로 기능을 확장해달라는 요구임

근데 클래스의 기능을 확장 못하게 해버리면 곤란하겠죠?

그리고 이미 완성되어 테스트까지 마친 클래스를 수정하면 소프트웨어 완성도가 떨어지겠죠?

확장에 대해 열려있고 수정에 대해서 닫혀있는 클래스가 부품으로서 재사용이 높은 클래스가 됨 ㅎㅎ
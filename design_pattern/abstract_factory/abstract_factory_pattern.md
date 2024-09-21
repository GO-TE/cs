# Abstract Factory Pattern

추상적인 공장이라…

공장같은 경우 실질적인 제품을 생산하는 곳인데 추상적인 공장이란 말은 되게 애매하다

추상적인 공장은 추상적인 부품을 조합하여 추상적인 제품을 만드는 곳이다.

“추상적이다”라는 말에 포커스를 둬보자

구체적으로 어떻게 구현 되어있는지 생각하지 않고 인터페이스에만 주목하는 상태

> 인터페이스
> 
> 
> 사전적 정의는 다음과 같다.
> 
> 1. 전기 신호의 변환으로 중앙 처리 장치와 그 주변 장치를 서로 잇는 부분 또는 접속 장치
> 2. 키보드나 디스플레이처럼 사람과 컴퓨터를 연결하는 장치
> 
> 여기서 주목할 것은 무언가와 무언가를 연결하는 (이어주는) 장치다.
> 
> 그러면 자바에서 인터페이스는 객체와 객체를 연결하는, 중간 매개의 역할을 하는 일종의 추상 클래스라고 생각하면 되지 않을까요?
> 

부품의 구체적인 구현에는 주목하지 않고 인터페이스 주목합니다. 그리고 그 인터페이스만 사용해서 부품을 조립하고 제품으로 완성합니다.

어우.. 무슨소린가 싶죠? 예제로 보시죠

계층 구조로 링크된 페이지를 HTML 파일로 만드는 프로그램

![image.png](Abstract%20Factory%20Pattern%20107bd15cbfe780e99c61fa1f40529d4e/image.png)

클래스 다이어 그램이구요

```java
package factory;
// 이 블록의 클래스들은 모두 factory 패키지에 속함을 알림
-----------------------------------------------------------
// Link와 Tray의 상위 클래스 : 하위 클래스가 makeHTML을 구현하길 바람 (부품)
public abstract class Item {
	protected String caption;
	
	public Item(String caption) {
		this.caption = caption;
	}
	
	public abstract String makeHTML();
}

------------------------------------------------------------
// Link : Item의 하위 클래스 : 아직 makeHTML 구현 안함 그래서 추상클래스
public abstract class Link extends Item {
	protected String url;
	
	public Link(String caption, String url) {
		super(caption); // 부모 클래스의 생성자를 호출하는 메소드
		this.url;
	}
}
------------------------------------------------------------
import java.util.ArrayList;
import java.util.List;

// 얘도 마찬가지
public abstract class Tray extends Item {
	protected List<Item> tray = new ArrayList<>();
	
	public Tray(String caption) {
		super(caption);
	}
	
	public void add(Item item) {
		tray.add(item);
	}
}
------------------------------------------------------------
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.StandardOpenOption;
import java.util.ArrayList;
import java.util.List;

// 얘는 이제 제품임 HTML 페이지 근데 추상을 곁들인...
public abstract class Page {
	protected String title;
	protected String author;
	protected List<Item> content = new ArrayList<>();
	
	public Page(String title, String author) {
		this.title = title;
		this.author = author;
	}
	
	public void add(Item item) {
		contend.add(item);
	}
	
	public void output(String filename) {
		try {
			Files.writeString(Path.of(filename), makeHTML(),
											StandardOpenOption.CREATE,
											StandardOpenOption.TRUNCATE_EXISTING,
											StandardOpenOption.WRITE);
			System.out.println(filename + "을 작성했습니다.");
		}
		catch (IOException e) {
			e.printStackTrace();
		}
	}
	
	public abstract String makeHTML();
}
------------------------------------------------------------
// 얘는 이제 추상적인 공장 역할임
// 전에 싱글톤 공부할 때 정리했던 리플렉션을 여기서 보게되니 감회가 새롭군
public abstract class Factory {
// 얘는 Class 클래스의 forName 메소드로 해당 클래스를 동적으로 가져옴
// 그리고 getDeclaredConstructor로 생성자를 얻고, newInstance로 인스턴스 생성
	public static Factory getFactory(String classname) {
		Factory factory = null;
		try {
			factory = (Factory)Class.forName(classname).getDeclaredConstructor().
								newInstance();
		}
		catch (ClassNotFoundException e) {
			System.out.println(classname + 클래스가 발견되지 않았습니다.)
		}
		catch (Exception e) {
			e.printStackTrace();
		}
		return factory;
	}
// 반환 값은 추상 타입을 리턴함
	
	public abstract Link createLink(String caption, String url);
	public abstract Tray createTray(String caption);
	public abstract Page createPage(String title, String author);
}
```

```java
import factory.*;

public class Main {
	public static void main(String[] args) {
		if (args.length != 2) {
			System.out.println("Usage: java Main filename.html class.name.of.Concrete Factory")
			System.out.println("Example 1: java Main list.html listfactory.ListFactory");
			System.out.println("Example 2: java Main div.html divfactory.DivFactory");
			System.exit(0);
		}
		
		String filename = args[0];
		String classname = args[1];
		
		Factory factory = Factory.getFactory(classname);
		
		Link blog1 = factory.createLink("Blog 1", "https://example.com/blog1");
		Link blog2 = factory.createLink("Blog 2", "https://example.com/blog2");
		Link blog3 = factory.createLink("Blog 3", "https://example.com/blog3");
		
		Tray blogTray = factory.createTray("Blog site");
		blogTray.add(blog1);
		blogTray.add(blog2);
		blogTray.add(blog3);
		
		Link news1 = factory.createLink("News 1", "https://example.com/news1");
		Link news2 = factory.createLink("News 2", "https://example.com/news2");
		Tray news3 = factory.createTary("News 3");
		news3.add(factory.createLink("News 3 (US)", "https://example.com/news3us"));
		news3.add(factory.createLink("News 3 (Korea)", "https://example.com/new3kr"));
		
		Tray newsTray = factory.createTray("News Site");
		newsTray.add(news1);
		newsTray.add(news2);
		newsTray.add(news3);
		
		Page page = new factory.createPate("Blog and News", "gote.com");
		page.add(blogTray);
		page.add(newsTray);
		
		page.output(filename);
	}
}
```

```java
package listfactory;
// 이 블록의 클래스들은 모두 listfactory 패키지에 속함을 알림

import factory.Factory;
import factory.Link;
import factory.Page;
import factory.Tray;

// 구체적인 공장 클래스
public class ListFactory extends Factory {
	@Override
	public Link createLink(String caption, String url) {
		return new ListLink(caption, url);
	}
	
	@Override
	public Tray createTray(String caption) {
		return new ListTray(caption);
	}
	
	@Override
	public Page createPage(String title, String author) {
		return new ListPage(title, author);
	}
}
------------------------------------------------------------
import factory.Link;

public class ListLink extends Factory {
	@Override
	public Link createLink(String caption, String url) {
		return new ListLink(caption, url);
	}
	
	@Override
	public Tray createTray(String caption) {
		return new ListTary(caption);
	}
	
	@Override
	public Page createPage(String title, String author) {
		return new ListPage(title, author);
	}
}
------------------------------------------------------------
import factory.Link;

public class ListLink extends Link {
	public ListLink(String cpation, String url) {
		super(caption, url);
	}
	
	@Override
	public String makeHTML() {
		return " <li><a hrea=\"" + url + "\>" + caption + "</a></li>\n";
	}
}
------------------------------------------------------------
import factory.Tray;
import factory.Item;

public class ListTray extends Tray {
	public ListTray(String caption) {
		super(caption);
	}
	
	@Override
	public String makeHTML() {
		StringBuilder sb = new StringBuilder();
		sb.append("<li>\n");
		sb.append(caption);
		sb.append("\n<ul>\n");
		for (Item item: tray) {
			sb.append(item.makeHTML());
		}
		sb.append("</ul>\n");
		sb.append("</il>\n");
		return sb.toString();
	}
}
------------------------------------------------------------
import factory.Tray;
import factory.Item;

public class ListTray extends Tray {
	public ListTray(String caption) {
		super(caption);
	}
	
	@Override
	public String makeHTML() {
		StringBuilder sb = new StringBuilder();
		sb.append("<li>\n");
		sb.append(caption);
		sb.append("\n<ul>\n");
		for (Item item: tray) {
			sb.append(item.makeHTML());
		}
		sb.append("<ul>\n");
		sb.append("</li>\n");
		return sb.toString();
	}
}
------------------------------------------------------------
import factory.Item;
import factory.Page;

public class ListPage extends Page {
	public ListPage(String title, String author) {
		super(title, author);
	}
	
	@Override
	public String makeHTML() {
		StringBuilder sb = new StringBuilder();
		sb.append("<!DOCTYPE html>\n");
		sb.append("<html><head><title>");
		sb.append(title);
		sb.append("</title></head>\n");
		sb.append("<body>\n");
		sb.append("</ul>\n");
		for (Item item: content) {
			sb.append(item.makeHTML));
		}
		sb.append("</ul>\n");
		sb.append("<hr><address>");
		sb.append(author);
		sb.append("</address>\n");
		sn.append("</body></html>\n");
		return sb.toString();
	}
}

```

길었다 그쵸?

메인이 클라이언트 역할로 구체적인 공장과 부품, 제품을 사용하지 않아요…

그리고 추상 클래스이므로 Factory는 인스턴스화 될 수가 없지 않나..?는 맞는데,

근데 타입으론 설정할 수 있으니 Main의 코드 중

`Factory factory = Factory.getFactory(classname);` 가 나올 수 있음

getFactory는 정적 메소드로 구현이 이미 되었기에

그리고 여기서 직접적인 객체는 커멘드 라인으로 정하는거고

 `java Main index.html listfactory.ListFactory` 

이런 식으로 ListFactory 객체를 저렇게 쓸 수 있음

### 정리

- Abstract Product (추상적 제품)
    
    추상적인 부품이나 제품의 인터페이스를 결정하는 역할 (예제 Link, Page)
    

- Abstract Factory (추상적 공장
    
    Abstract Product역의 인스턴스를 만들기 위한 인터페이스를 결정하는 역할
    
    (예제에선 Factory)
    

- Client (의뢰자)
    
    Abstract Factory와 Abstract Product의 인터페이스를 사용해 작업
    
    Client는 구체적인 부품이나 공장에 관해선 모름
    
    (예제에선 Main)
    

- Concrete Product (구체적인 제품)
    
    Abstract Product 역의 인터페이스를 구현함
    
    (예제 ListLink, ListTray, ListPage)
    

- Concrete Factory (구체적인 공장)
    
    Abstract Factory의 인터페이스를 구현
    
    (예제 ListFactory)
    

이제 팩토리 메소드 패턴의 확장 버전이라고 생각하면 편할 수도 있읍니다.

팩토리 메소드 패턴은 조건에 따른 객체 생성을 팩토리 클래스로 위임하여 팩토리 클래스에서 객체를 생성하는 패턴

추상 팩토리 패턴은 서로 관련 있는 객체들을 통째로 묶어 팩토리 클래스로 만들고, 해당 팩토리를 조건에 따라 생성하게 되는 새로운 팩토리를 만들어 객체를 생성을 합니다.

부품을 또 추상화 해버리는 추상의 추상이라고 보면 되겠습니다.

여기서 구체적인 공장을 추가하기는 간단합니다.

간단하다는 말은 어떤 클래스를 만들고 어떤 메소드를 구현해야하는지 분명하단 것입니다.

예제에서 listpage가 아닌 div 페이지를 추가한다고 했을 때, 해야할 일은 그저 Factory, Tray, Page, Link의 하위 클래스를 구현해주면 됩니다.

아무리 공장을 추가하고 수정해도 추상적인 공장이나 클라이언트는 수정할 필요가 없습니다

근데, 부품을 새로 추가하는건 조금 어렵습니다.

Abstract Factory에서 새로운 부품을 추가한다고 했을 때, factory 패키지에서 Image를 추가한다고 하면, 이미 존재하는 구체적인 공장 전부를 Picture를 대응하도록 수정해줘야해서 손이 많이 갑니다.
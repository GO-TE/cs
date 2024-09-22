# Builder Pattern

빌딩을 만들 때를 생각해보자.

지반 다지고, 뼈대 만들고, 공구리 치고… 창문달고, 샷시랑 내부 치고… 순서대로 차근차근 하나씩 만들어간다. 구조물을 만들 때 한번에 짜잔 만들지는 않음

이 구조처럼 단계적으로 인스턴스를 만들어가는게 Builder 패턴임

예제를 보며 이해해보자

예제에선 문서를 작성하는 프로그램을 만들거임

타이틀을 한 개 포함하기 → 문자열 몇개 포함하기 → 항목을 몇개 포함하기

![image.png](Builder%20Pattern%20101bd15cbfe78028a2a9ec13a5172111/image.png)

```java
// 문서를 만드는 메소드를 선언하는 추상 클래스
public abstract class Builder {
	public abstract void makeTitle(String title);
	public abstract void makeString(String str);
	public abstract void makeItems(String[] items);
	public abstract void close();
}
-------------------------------------------------------------

public class Director {
	private Builder builder;
	
	public Director(Builder builder) {
		this.builder = builder;
	}
	
	public void construct() {
		builder.makeTitle("Greeting");
		builder.makeString("일반적인 인사");
		builder.makeItems(new String[]{
			"How are you",
			"Hello",
			"Hi"
		});
		builder.makeString("시간대 별 인사");
		builder.makeItems(new String[]{
			"곤니찌와",
			"오하요",
			"곰방와"
		});
		builder.close();
	}
}
-------------------------------------------------------------

public class TextBuilder extends Builder {
	private StringBuilder sb = new StringBuilder();
	
	@Override
	public void makeTitle(String title) {
		sb.append("============================\n");
		sb.append("[");
		sb.append(title);
		sb.append("]\n\n");
	}
	
	@Override
	public void makeString(String str) {
		sb.append("# ");
		sb.append(str);
		sb.append("\n\n");
	}
	
	@Override
	public void makeItems(String[] items) {
		for (String s: items) {
			sb.append("- ");
			sb.append(s);
			sb.append("\n");
		}
		sb.append("\n")
	}
	
	@Override
	public void close() {
		sb.append("============================\n");
	}
	
	public String getTextResult() {
		return sb.toString();
	}
}
-------------------------------------------------------------
import java.io.*;

public class HTMLBuilder extends Builder {
	private String filename = "untitle.html";
	private StringBuilder sb = new StringBuilder();
	
	@Override
	public void makeTitle(String title) {
		filename = title + ".html";
		sb.append("<!DOCTYPE html>\n");
		sb.append("<html>"\n);
		sb.append("<head><title>");
		sb.append(title);
		sb.append("</title></head>");
		sb.append("<body>\n");
		sb.append("<h1>");
		sb.append(title);
		sb.append("</h1>\n\n");
	}
	
	@Override
	public void makeString(String str) {
		sb.append("<p>");
		sb.append(str);
		sb.append("</p>\n\n");
	}
	
	@Override
	public void makeItems(String[] items) {
		sb.append("<ul>\n");
		for (String s: items) {
			sb.append("<li>");
			sb.append(s);
			sb.append("</li>\n");
		}
		sb.append("</ul>\n\n");
	}
	
	@Override
	public void close() {
		sb.append("</body>");
		sb.append("</html>\n");
		try {
			Writer writer = new FileWriter(filename);
			wrtier.write(sb.toString());
			writer.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
	}
	
	public String getHTMLResult() {
		return filename;
	}
}
-------------------------------------------------------------
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;

public class Main {
	public static void main(String[] args) {
		if (args.length != 1) {
			usage();
			System.exit(0);
		}
		
		if (args[0].equals("text")) {
			TextBuilder textBuilder = new TextBuilder();
			Director director = new Director(textBuilder);
			director.construct();
			String result = textBuilder.getTextResult();
			System.out.println(result);
		}
		else if (args[0].equals("html")) {
			HTMLBuilder htmlBuilder = new HTMLBuilder();
			Director director = new Direcotr(htmlbuilder);
			director.construct();
			String filename = htmlBuilder.getHTMLResult();
			System.out.println("HTML파일" + filename + "이 작성되었습니다.");
		}
		else {
			usage();
			System.exit(0);
		}
}
	
	public static void usage() {
		System.out.println("Usage: java Main text 텍스트로 문서 작성");
		System.out.println("Usage: java Main html HTML로 문서 작성");
	}
}
		
```

### 정리

- Builder (건축가)
    
    인스턴스를 생성하기 위한 인터페이스를 결정
    
    Builder 역에는 인스턴스의 각 부분을 만들 메소드가 준비 됨 (예제 Builder)
    
- ConcreteBuilder (구체적인 건축가)
    
    Builder 인터페이스를 구현하는 클래스
    
    실제 인스턴스 생성으로 호출되는 메소드가 정의됨 → 최종적 완성된 결과를 얻는 메소드
    
    (예제 TextBuilder, HTMLBuilder)
    

- Director (감독관)
    
    Builder의 인터페이스를 사용하여 인스턴스를 생성시킴
    
    ConcreteBuilder 역에 의존하는 프로그래밍은 하지 않고 ConcreteBuilder가 무엇이든 잘 작동하도록 Builder 메소드만 사용함 (예제 Director)
    

> **누가 무엇을 알고 가는데?**
> 
> 
> 어느 클래스가 어느 메소드를 사용할 지, 객체 지향에서 “누가 무엇을 알고 있는지?”는 매우 중요함
> 
> 예제에서 Main 클래스는 Builder의 메소드들을 모름
> 
> Main은 그냥 Director의 construct 메소드만 호출함 → Director 클래스 안에서 조용히 진행되고 문서가 완성됨
> 
> Director 클래스는 Builder만 알고 있고, Builder의 메소드를 이용해서 문서를 구축함
> 
> 실제로 이용하는 클래스가 무엇인진 몰라도 됨 어차피 Builder API를 기반으로 구현되었기에
> 
> 모른다는건 또 유지보수에 있어 청신호임 → 다른 클래스로 교체 가능하기에.
> 
> 부품으로서의 가치가 높음. 이 “교체 가능성”을 설계자는 항상 염두에 둬야 함
> 

> **의존성 주입 (Dependency Injection)**
> 
> 
> Director 클래스는 Builder를 알고 있지만 하위 클래스는 모름
> 
> “Director는 하위 클래스에 의존하지 않는다”라고 할 수 있음
> 
> 근데, Director가 동작하려면 Builder의 구체적인 인스턴스가 필요함
> 
> 그래서 Director의 생성자를 호출할 때 TextBuilder나 HTMLBuilder를 인스턴스 인수로 보낸다.
> 
> 그러면 Director의 소스코드에는 Builder의 하위 클래스가 없지만, 얘네들에 의존해서 동작하게 된다.
> 
> → 소스 코드에는 없지만, 실제로 이 인스턴스를 이용해 동작해 주세요
> 
> 라는 의미를 내포하여 인스턴스를 건내는 방법을 의존성 주입이라고 함
> 
> 클래스 간 결합도를 낮추고 재사용을 높이는 기법임
> 
> 예를 들어, 글 쓰는 사람에게 “이걸로 글 써줘”라고 구체적인 필기구를 건내는것과 비슷함
> 
> 전달하는건 필기구로 정해져 있지만, 구체적으로 연필인지 볼펜인지 건네는 시점에 결정할 수 있음
> 

> **설계 시 결정하는 것과 결정할 수 없는 것**
> 
> 
> Builder (Interface)는 클래스 문서를 구축하기 위해 필요하고 충분한 메소드를 선언해야 합니다.
> 
> Director 클래스에 주어지는 도구는 Builder 클래스가 제공하므로, Builder 메소드로 무엇을 준비해야 할 지가 관건입니다.
> 
> 게다가 Builder는 앞으로 늘어날지도 모르는 하위 클래스의 요구에도 대응을 해야하기에 예제에선 2개밖에 없지만 다른 형식의 문서를 만들고 싶다고 가정해보자
> 
> 이때 어떻게 다른 형식의 빌더 클래스를 만들고 메소드를 어떻게 알까?
> 
> 설계하는 입장은 모든 것을 대비할 수 없기에 그냥 가까운 미래정도는 견딜 수 있도록 설계해야한다.
> 

> **소스를 읽고 수정하기**
> 
> 
> 프로그래밍 할 때, 처음부터 하거나 이미 만들어진 소스를 수정하는 경우도 있음
> 
> 그럴 땐 먼저 기존 코드 먼저 읽자. 근데 추상 클래스만 본다고 해도 정보가 그다지 많이 늘어나진 않음
> 
> 예제 프로그램에서 Builder 추상 클래스를 읽어도 결론이 나지 않음
> 
> 적어도 Director의 코드를 읽고 Builder라는 클래스의 사용법을 이해해야함
> 
> 그런 다음 TextBuilder와 HTMLBuilder 클래스를 읽고 Builder 추상 메소드에 어떤 동작이 기대되는지를 알 수 있음 (어노테이션도 단서가 됨)
> 
> 클래스의 역할을 이해하지 못하면, 수정할 때 어느 클래스를 변경해야 할 지 판단하기 어려움
>
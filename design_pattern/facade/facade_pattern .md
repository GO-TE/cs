# Facade Pattern

많은 클래스가 만들어지고 서로 연관되어 복잡해지니 프로그램은 점점 커지는 경향이 있음

클래스를 사용하는 경우에는 클래스 간의 관계를 올바르게 이해하고, 올바른 순서로 메소드를 호출 할 필요가 있음

큰 프로그램을 이용해서 처리하려면 관련된 많은 클래스를 적절하게 제어해야함

그럴려면 처리하기 위한 ‘창구’도 필요한 법. 그렇게 하면 많은 클래스를 개별적으로 제어하지 않아도 ‘창구’에 요청만 하면 끝남

그런 ‘창구’가 Facade Pattern임 (건물의 정면)

이건 예제로 봐야 편할듯

쉽게 얘기하면 복잡한 내부 비즈니스 로직은 모르고 클라이언트가 원하는 결과만 내뱉을 수 있도록 요청하는 창구가 있다고 생각하면 편할듯

![image.png](Facade%20Pattern%204eb7cf9d29d6479183efed3a9d6985e2/image.png)

사용자의 웹페이즈를 작성하는 프로그램

```java
package pagemaker;

import java.io.FIleReader;
import java.io.IOException;
import java.util.Properties;

public class Database {
	private Database() {
	}
	
	public static Properties getProperties(String dbName) throw IOExcpetion {
		String fileName = dbname + ".txt";
		Properties prop = new Properties();
		prop.load(new FileReader(fileName));
		return prop;
	}
}

---------------------------------------------------------------------------

package pagemaker;

import java.io.Writer;
import java.io.IOException;

public class HtmlWriter {
	private Writer writer;
	
	public HtmlWriter(Writer writer) {
		this.writer = writer;
	}
	
	// 타이틀 출력
	public void title(String title) throws IOException {
		writer.write("<!DOCTYPE html>");
		writer.write("<html>");
		writer.write("<head>");
		writer.write("<title>" + title + "</title>");
		writer.write("</head>");
		writer.write("\n");
		writer.write("<h1>" + title + "</h1>");
		writer.write("\n");
	}
	
	// 단락 출력
	public void paragraph(String msg) throws IOException {
		writer.write("<p>" + msg + "</p>");
	}
	
	// 링크 출력
	public void link(String href, String caption) throws IOException {
		paragraph("<a href=\"" + href + "\">" + caption + "</a>");
	}
	
	// 이메일 주소 출력
	public void mailto(String mailAddr, String userName) throws IOException {
		link("mailto:" + mailAddr, userName);
	}
	
	public void close() throw IOException {
		writer.write("<body>");
		writer.write("</html>");
		writer.write("\n");
		writer.close();
	}
}

---------------------------------------------------------------------------

package pagemaker;

import java.io.FileWriter;
import java.io.IOException;
import java.util.Properties;

public class PageMaker {
	private PageMaker() {
	}
	
	public static void makeWelcomPage(String mailAddr, String fileName) {
		try {
			Properties mailProp = Database.getProperties("mailAddr");
			String userName = mailProp(mailAddr);
			HtmlWriter writer = new HtmlWriter(new FileWriter(fileName));
			
			wrtier.title(user.name)
			writer.paragraph("Welcome to " + userName + "'s web page!");
			writer.paragraph("Nice to meet you");
			writer.mailto(mailAddr, userName);
			writer.close();
			
			System.out.println(fileName + " is created for " + mailAddr + " (" + userName + ")");
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
}

---------------------------------------------------------------------------
import pagemaker.PageMaker;

public class Main {
	public static void main(String[] args) {
		PageMaker.makeWelcome("kyo9810@gmail.com", "welcome.html");
	}
}
```

## 정리

- Facade(정면)
    
    시스템을 구성하는 그 밖의 많은 역을 위한 “단순한 창구”로 높은 수준의 단순한 인터페이스를 시스템 외부로 제공함 (진짜 말그대로 창구역할) (pageMaker 담당)
    
- 시스템을 구성하는 그 밖은 많은 역할
    
    그 밖의 많은 역할은 각각 일을 하고 Facade가 있는지 조차 모른다.
    
    Facade의 호출을 받고 메소드를 실행하지만, 얘네들이 Facade를 호출하는 경우는 없음(Facade가 의존하지 역은 성립 X) (예제에선 Database, HtmlMaker가 담당)
    

- Client
    
    Main이 담당 
    

### Facade는 복잡한 것을 단순하게 보여주는 역할

복잡한 것이라고 하면 내부에서 동작하는 많은 클래스 사이의 관계나 사용 방법을 말하고, Facade는 그 복잡함을 의식 하지 않게 해줌 (Client가) MVC에서도 컨트롤러라고 생각하면 편할까요?

근데, 핵심은 인터페이스(API) 수를 줄이는 것임

클래스와 메소드가 많이 보이면 프로그래머는 어떤 것을 사용할 지 망설이게 되고, 호출 순서에도 주의해야 한다. 주의한다는 것은 실수하기 쉽다는 뜻이 되고, 인터페이스가 적은 Facade 역을 고려하는 것이 좋음

인터페이스가 적으면 외부와 결합이 느슨하다는 소리가 되고, 패키지를 부품으로 재사용하기 쉬워짐

클래스를 설계할 때, 어떤 메소드를 public으로 둬야할 지 생각을 해야함

너무 많은 메소드를 public으로 두면 클래스 내부를 수정하기 어려워짐… (결합이 강해짐)

필드에 대해도 마찬가지로 적용됨 (사실 필드 변수는 외부에서 접근하는 것 보다 메소드를 통해 일을 시키는게 더 낫긴함)

### 재귀적인 Facade

패키지의 관점에서 Facade 역할을 하는 클래스 집합이 여러개 있다고 치면 그 집합을 또 모아 새로운 Facade 역을 만들수도 있음 → Facade의 재귀적으로 적용

많은 클래스와 패키지를 가진 시스템에서 각 요소에 Facade를 적용하면 시스템이 더 편리해짐

### Facade를 잘 만들지 않는 이유

복잡한 내부 로직을 숙지한 개발자는 Facade를 안만드려는 경향이 있을 수도 있음

→ 머릿속에 다 들어있으니까… 좀 자랑식으로 아는척 할수도 있으니 말이다

근데 이럴수록 Facade가 필요하다는 신호로 보여짐

→ 명확하게 언어로 표현할 수 있는 노하우는 프로그래머의 머릿속에 두는 것이 아니라, 코드로 표현해야함.
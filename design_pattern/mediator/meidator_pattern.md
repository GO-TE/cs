# Mediator Pattern

님들아 친구들끼리 싸움나면 어떻게 되나요

친구로서 말리겠죠? 맞읍니다. 이게 바로 중재자(Mediator)입니당

싸움을 말리기 위해 싸운 친구 A와 B에게 각자의 입장을 들어봅니다 (보고)

그러고 제가 판단해서 누구 잘못인지 알려주고 상황을 마무리 짓겠습니다. 이 일 외 상세한 이야기까지는 참견하지 않겠습니다.

친구들 사이에서 싸운 A, B는 저에게 보고를 하고 저만 멤버들에게 사건의 결말을 판단하고 싸우지말라고 지시를 내립니다.

싸운 친구들끼리 다시 투닥거리지 않도록이요

곤란한 일, 그룹 전체로 파급 효과가 날 사건이라면 중재자에게 알리고, 중재자가 지시한대로 행동합니다. (A,B = Colleague, 나 = Mediator)

이게 Mediator Pattern입니다.

판단을 안하고 각자 맡은 역할에만 책임을 다하니 일하는 각 객체들은 교체되기 쉽겠죠?

예제를 보자구요?

이름과 패스워드를 입력하는 로그인 대화상자 GUI 앱입니다.

![image.png](Mediator%20Pattern%2014ebd15cbfe780a5ae00d73cafd441d2/image.png)

![image.png](Mediator%20Pattern%2014ebd15cbfe780a5ae00d73cafd441d2/image%201.png)

```java
public interface Mediator {
	// Colleague 생성
	public abstract void createColleague();
	
	// Colleague 상태 변화시 호출
	public abstract void colleagueChanged();
}

-------------------------------------------------------------

public interface Colleague {
	// Mediator 설정
	public abstract void setMediator(Mediator mediator);
	
	// Mediator 활성/비활성 지시
	public abstract void setColleagueEnabled(boolean enabled);

-------------------------------------------------------------
import java.awt.Button;

public class ColleagueButton extends Button implements Colleague {
	private Mediator mediator;
	
	public ColleagueButton(String caption) {
		super(caption);
	}
	
	@Override
	public void setMediator(Mediator mediator) {
		this.mediator = mediator;
	}
	
	@Override
	public void setColleagueEnabled(boolean enabled) {
		setEnabled(enabled);
	}
}

-------------------------------------------------------------
import java.awt.Color;
import java.awt.TextField;
import java.awt.event.TextEvent;
import java.awt.evnet.TextListener;

public class ColleagueTextField implements TextListener, Colleague {
	private Mediator mediator;
	
	public ColleagueTextField(String text, int columns) {
		super(text, columns);
	}
	
	@Override
	public void setMediator(Mediator mediator) {
		this.mediator = mediator;
	}
	
	@Override
	public void setColleagueEnabled(boolean enabled) {
		setEnabled(enabled);
	}
	
	@Override
	public void textValueChanged(TextEvent e) {
		mediator.colleagueChanged();
	}
}

-------------------------------------------------------------
import java.awt.Checkbox;
import java.awt.CheckboxGroup;
import java.awt.event.ItemEvent;
import java.awt.event.ItemListener;

public class ColleagueTextField extends TextField implements TextListener, Colleauge {
	private Mediator mediator;
	
	public ColleagueTextField(String text, int columns) {
		super(text, columns);
	}
	
	@Override
	public void setMediator(Mediator mediator) {
		this.mediator = mediator;
	}
	
	@Override
	public void setColleagueEnabled(boolean enabled) {
		setEnabled(enabled);
	}
	
	@Override
	public void textValueChanged(TextEvent e) {
		mediator.colleagueChanged();
	}
}

-------------------------------------------------------------
import java.awt.Checkbox;
import java.awt.CheckboxGroup;
import java.awt.event.ItemEvent;
import java.awt.event.ItemListener;

public class ColleagueCheckbox extends Checkbox implements ItemListener, Colleauge {
	private Mediator mediator;
	
	@Override
	public void setMediator(Mediator mediator) {
		this.mediator = mediator;
	}
	
	@Override
	public void setColleagueEnabled(boolean enabled) {
		setEnabled(enabled);
	}
	
	@Override
	public void itemStateChanged(ItemEvent e) {
		mediator.colleagueChanged();
	}
}

-------------------------------------------------------------
import java.awt.CheckboxGroup;
import java.awt.Color;
import java.awt.Frame;
import java.awt.GridLayout;
import java.awt.Label;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class LoginFrame extends Frame implements ActionListener, Mediator {
	private ColleagueCheckBox checkGuest;
	private ColleagueCheckBox checkLogin;
	private ColleagueTextField textUser;
	private ColleagueTextField textPass;
	private ColleagueButton buttonOk;
	private ColleagueButoon buttoncancel;
	
	private LoginFrame(String title) {
		super(title);
		
		setBackground(Color.lightGray);
		setLayout(new GridLayout(4, 2));
		createColleagues();
		
		add(checkGuest);
		add(checkLogin);
		add(textUser);
		add(new Label("Password:"));
		add(textPass);
		add(buttonOk);
		add(buttoncancel);
		
		colleagueChanged();
		
		pack();
		setVisible(true);
	}
	
	@Override
	public void createColleagues() {
		CheckboxGroup g = new CheckboxGroup();
		checkGuest = new ColleagueCheckbox("Guest", g, true);
		checkLogin = new ColleagueCheckbox("Login", g, false);
		
		textUser = new ColleagueTextField("", 10);
		textPass = new ColleagueTextField("", 10);
		textPass.setEchoChar('*');
		
		buttonOk = new ColleagueButton("OK");
		buttoncancel = new ColleagueButton("cancel");
		
		checkGuest.setMediator(this);
		checkLogin.setMediator(this);
		textUser.setMediator(this);
		textPass.setMediator(this);
		buttonOk.setMediator(this);
		buttoncancel.setMediator(this);
		
		checkGuest.addItemListener(checkGuest);
		checkLogin.addItemListener(checkLogin);
		textUser.addItemListener(textUser);
		textPass.addItemListener(textPass);	
		buttonOk.addActionListener(this);
		buttoncancel.addActionListener(this);
	}
	
	@Override
	public void colleagueChanged() {
		if (checkGuest.getState()) {
			textUser.setColleagueEnabled(false);
			textPass.setColleagueEnabled(false);
			buttonOk.setColleagueEnabled(true);
		} else {
			textUser.setColleagueEnabled(true);
			userpassChanged();
		}
	}
	
	private void userpassChanged() {
		if (textUser.getText().length() > 0) {
			textPass.setColleagueEnabled(true);
			if (textPass.getText().length() > 0) {
				buttonOk.setColleagueEnabled(true);
			} else {
				buttonOk.setColleagueEnabled(false);
			}
		} else {
			textPass.setColleagueEnabled(false);
			buttonOk.setColleagueEnabled(false);
		}
	}
	
	@Override
	public void actionPerformed(ActionEvent e) {
		System.out.println(e.toString());
		System.exit(0);
	}
}

-------------------------------------------------------------
public class Main {
	public static void main(String[] args) {
		new LoginFrame("Mediator Sample");
	}
}
```

### 정리

사실 로그인 GUI라고 하면 되게 간단할거 같죠?

생각보다 고려하고 구현, 설계해야 하는 것들이 넘칩니다.

![image.png](Mediator%20Pattern%2014ebd15cbfe780a5ae00d73cafd441d2/image%202.png)

- 게스트 로그인이 선택되었다면 사용자 이름과 패스워드 입력 칸이 비활성화 되야함
- 사용자 로그인이 선택되었다면 사용자 이름과 패스워드 입력 칸이 활성화
- 사용자 이름에 문자가 없다면 패스워드는 비활성화
- 사용자 이름에 문자가 하나라도 있다면 패스워드 활성화
- 사용자 이름과 패스워드 둘 다 문자가 하나라도 있다면 OK버튼이 활성화
- Cancel버튼은 언제나 활성화 되어있음

말로 나열하면 간단한 프로그램도 되게 복잡해 보입니다.

하지만 로그인 창을 사용을 많이 해봤다면 해볼만 합니다. (설계자 의도를 찾기 쉽거든요)

각 필드와 버튼을 서로 분산하여 서로를 통제시키도록 하면 Mediator 패턴이 아니겠죠?

- Mediator (중재자)
    
    Colleague와 통신하고 조정하는 인터페이스를 정의 (예제에서 Mediator)
    
- Concrete Mediator (구체적 중재자)
    
    예제의 LoginFrame
    
- Colleague (동료)
    
    Mediator와 통신하는 인터페이스 (예제에서 Colleague 인터페이스)
    
- Concrete Colleague (구체적 동료)
    
    ColleagueButton, ColleagueTextField, ColleagueCheckbox
    

님들 근데 LoginFrame 클래스 자체가 좀 길고 복잡해보이죠 특히 colleagueChanged 메소드가

사양이 변경되면 이 복잡한 메소드에 분명히 fix할 부분이 생길거같음

그럴지도 모르는데 버그가 생겨도 표시 활성/비활성에 관한 로직은 colleagueChanged에만 존재하기에 문제가 되지 않음 (저것만 디버깅하면 됨)

객체지향에서는 한 곳으로 집중되는 것을 피하고 분산처리하는 경우가 많은데, 이번 예제에서는 한 곳으로 몰아서 처리했음.

분산할 것은 분산하고 집중할 것은 집중하는 → 무조건 분산하는게 좋지 않음

### 통신 경로의 증가

A와 B라는 인스턴스가 서로 통신(서로 메소드를 호출)한다고 하자.

이때 통신 경로는 A → B, B → A 두 가지임 A, B, C라면 6개가 되고

4개라면 12개 … 같은 입장의 인스턴스가 많이 존재할 때 서로 통신한다면 프로그램이 복잡해짐

인스턴스 수가 적을땐 괜찮은데, 처음 설계대로 점점 인스턴스를 늘리면 기하급수적으로 늘기에 Mediator가 어느정도 증가를 억제함 ^.^
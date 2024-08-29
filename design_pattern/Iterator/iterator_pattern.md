# Iterator Pattern

### 디자인 패턴은 클래스 라이브러리가 아니다.

디자인 패턴은 더 일반적인 개념으로 클래스 라이브러리는 부품이 된 프로그램이고, 디자인 패턴은 부품이 어떻게 조립되어있는지, 개별 부품이 어떻게 관련되어 큰 기능을 하는지 표현한 것이다. 

즉 클래스 끼리 관계를 나타내는 큰 개념이라고 생각하면 될 듯 싶다.

### Iterator Pattern

자바에서 기본적인 for문을 보자

```java
for (int i = 0; i < arr.length; i++) {
	System.out.println(arr[i]);
}
```

여기서 사용되는 i는 처음에 0으로 초기화 되고, `arr`의 끝까지 `arr[i]`를 출력한다

i는 결국 처음부터 `arr`의 순차적으로 검색하게 되는데, i의 기능을 추상화하여 일반화 한 것을 Iterator 패턴이라고 할 수 있다.

Iterator는 어느 것이 많이 모여있을 때, 이를 순서대로 가리키며 전체를 검색하고 처리를 반복하는 것

### 예제

책장안에 책을 넣고, 책 이름을 차례대로 표시하는 프로그램을 예시로 들어보자

```java
import java.util.Iterator;
import java.util.NoSuchElementException;

public interface Iterable<E> { // 집합체를 나타내는 인터페이스
	public abstract Iterator<E> iterator();
} // 이 인터페이스로 구현했을 때, 반복할 수 있음을 알 수 있다.

public interface Iterator<E> { // 처리 반복하는 반복자를 나타내는 인터페이스
	public abstract boolean hasNext(); // 다음이 있는지?
	public abstract E next(); // 다음의 집합체 요소를 1개 리턴
} // 이 인터페이스로 구현한다면, 반복시키는 반복자임을 알 수 있음

public class Book { // 책장에 들어갈 책 클래스 구현
	private String name;
	
	public Book(String name) {
		this.name = name;
	}
	
	public String getName() {
		return name;
	}
}

/*
책장을 나타내는 클래스로, 집합체를 다루기 위해 Iterable<Book> 인터페이스를 구현
즉 책장은 Book이라는 타입을 반복해서 작업을 수행 할 수 있음
*/
public class BookShelf implements Iterable<Book> {
	private Book[] books; // Book의 인스턴스들이 들어갈 배열
	private int last = 0;
	
	public BookShelf(int maxsize) {
		this.books = new Book[maxsize];
	}
	
	public Book getBookAt(int index) {
		return books[index];
	}
	
	public void appendBook(Book book) {
		this.books[last] = book;
		last++;
	}
	
	public int getLength() {
		return last;
	}
	
	@Overide // Iterator 인터페이스의 iterator를 구현함
	public Iterator<Book> iterator() {
		return new BookSelfIterator(this); // this는 인스턴스 그 자체
	} // 반복하여 처리하려할 때 Iterator 객체를 생성해서 반환
}

// Iterator 역할 i의 역할과 작업을 여기에 작성 얘는 그럼 처음부터 끝까지 다음 책을 볼 수 있음
public class BookShelfIterator(BookShelf bookShelf) {
	private BookShelf bookShelf;
	private int index;
	
	public BookShelfIterator(BookShelf bookShelf) {
		this.bookShelf = bookShelf;
		this.index = 0
	}
	
	@Override
	public boolean hasNext() { // 다음 책이 있는지?
		if (index < bookShelf.getLength()) {
			return true; // 현재 책을 가르키는 index가 책장의 길이보다 작다면?
		}
		return false; // 크다면?
	}
	
	@Override
	public Book next() { // 다음 책 리턴 함수
		if (!hasNext()) { // 다음 책이 없다면??
		// 존재하지 않는 것을 호출할 때 발생하는 예외
			throw new NoSuchElementException();
		}
		Book book = bookShelf.getBookAt(index); // 있으면 book 인스턴스 변수에 저장
		index++; // 가리키는 위치를 증가하고
		return book // 현 위치의 책을 리턴
	}
	
	/*
		맨 처음 언급했던 for문에서 i++의 역할을 담당하는 next 메소드
	*/
```

전체적인 클래스를 작성했다. 이제 메인에서 써보자

```java
import java.util.Iterator;

public class Main {
	public static void main(String[] args) {
		BookShelf bookShelf = new BookShelf(4);
		
		bookShelf.appendBook(new Book("클린 코드"));
		bookShelf.appendBook(new Book("자바 인 액션"));
		bookShelf.appendBook(new Book("리팩토링"));
		bookShelf.appendBook(new Book("함께 자라기"));
		
		Iterator<Book> i = bookShelf.iterator();
		
		// 1. 명시적으로 Iterator 사용하기
		while (i.hasNext()) {
			Book book = i.next();
			System.out.println(book.getName());
		}
		
		// 2. 확장 for문을 사용하기
		for (Book book: bookShelf) {
			System.out.println(book.getName());
		}
	}
}
```

확장 for문으로 iterator를 직접적으로 구현하지 않아도 요소에 대한 반복을 처리할 수 있긴 하다.

(확장 for문은 Iterator 인터페이스를 구현한 클래스의 인스턴스에 대해 내부적으로 Iterator를 사용함)

### 정리

- Iterator (반복자)
    
    요소를 순서대로 검색하는 인터페이스를 결정함.
    
    예제에선 `Iterator<E>`인터페이스가 다음 요소의 존재를 확인하는 `hasNext()`, 다음 요소를 가져오는 `next()`를 결정한다.
    
- Concrete Iterator (구체적인 반복자)
    
    Iterator가 결정한 인터페이스를 실제로 구현
    
    예제에선 `BookShelfIterator` 클래스가 검색에 대한 필요 정보 index와 bookshelf 필드를 가지고 있음
    
- Aggregate (집합체)
    
    Iterator를 만들어 내는 인터페이스를 결정
    
    이 인터페이스는 내가 가진 요소를 차례로 검색해 주는 사람을 만드는 메소드
    
    예제에선 `Iterable<E>` 가 이 역할을 맡아 `Iterator`를 결정함
    

- Concrete Aggregate (구체적인 집합체)
    
    Aggregate가 결정한 인터페이스를 실제로 구현 (= 구체적인 Iterator 역할)
    
    Concrete Iterator를 인스턴스를 만듦
    
    예제에선 BookShelf 클래스가 Iterator 메소드를 구현함
    

> 근데 왜 써야함?
> 
> 
> Q : 그냥 배열이면 for문 쓰면 되는데 진짜 왜 씀?
> 
> A : Iterator를 사용하면서 구현과 분리해서 반복할 수 있기 때문
> 
> 예제같은 경우에는 반복문이 돌아가며 BookShelf가 호출이 안되고 Iterator가 주도함 → 이는 반복하는 행위가 `BookShelf` 구현에 의존하지 않음
> 
> 이해하긴 어렵죠? 그러면 이렇게 생각해보자
> 
> 만약에 `BookShelf` 책을 담는 자료구조를 배열말고 `ArrayList<Book>`으로 바꾼다고 가정하자
> 
> 기존에서 Iterator가 없었다면 이런식으로 작성할것이다.
> 
> ```java
> for (int i=0; i < bookShelf.getLength(); i++) {
> 	System.out.println(bookShelf.getBook(i).getName());
> }
> ```
> 
> 그리고 ArrayList로 바꿨다면 이 코드는 동작하지 않을것이다…
> 
> 사실 java에서 제공하는 `ArrayList`도 비슷한 기능은 있지만, 이러면 코드를 수정해야하는 불가피함이 있을것이다.
> 
> 올바른 `Iterator<Book>`만 반환한다면 기존 코드로도 잘 돌아 갈 것이다.
> 
> 이는 유지보수하기 좋음을 알린다 :)
> 

구체적인 클래스를 구현해서 사용하면 클래스간 결합이 강해져 재사용하기 어려워짐

추상적으로 설계해서 프로그래밍 해야함

디자인 패턴은 이렇게 클래스의 재사용을 촉진시켜주고, 기존 코드를 수정할 일을 줄여준다.
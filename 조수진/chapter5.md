# 블로그 링크

[블로그 링크](https://velog.io/@sujinjwa/%ED%81%B4%EB%A6%B0-%EC%BD%94%EB%93%9C-5%EC%9E%A5.-%ED%98%95%EC%8B%9D-%EB%A7%9E%EC%B6%94%EA%B8%B0)


---


# 5장. 형식 맞추기

프로그래머라면 형식을 깔끔하게 맞춰 코드를 짜야 한다.

코드 형식을 맞추기 위한 간단한 규칙을 정하고 그 규칙을 착실히 따라야 한다.

팀으로 일한다면 팀이 합의해 규칙을 정하고 모두가 그 규칙을 따라야 한다. 필요하다면 규칙을 자동으로 적용하는 도구를 활용한다.

<BR />

## 형식을 맞추는 목적

코드 형식은 의사소통의 일환으로, 매우 중요하다!

오늘 작성한 코드의 구현 스타일과 가독성 수준은 앞으로 바뀔 코드의 품질, 유지보수 용이성과 확장성에 계속 영향을 미칠 것이다.

<BR />

## 원활한 소통을 장려하는 코드 형식

그렇다면 원활한 소통을 장려하는 코드 형식은 무엇일까?

<BR />

### 1. 적절한 행 길이를 유지하라

소스 코드는 얼마나 길어야 적당할까?

실제로 500줄을 넘지 않고 대부분 200줄 정도인 파일로도 커다란 시스템을 구축할 수 있다.

일반적으로 큰 파일이 작은 파일보다 이해하기 쉽다.

> 따라서, **코드 길이를 200줄 정도로 제한**하는 것이 바람직한 규칙이다.

<BR />

### 2. 신문 기사처럼 작성하라

기사는 첫 문단에 전체 기사 내용이 요약되어 있고 아래로 쭉 내려갈수록 세세한 사실이 드러난다.

소스코드도 신문 기사와 비슷하게 작성한다.

> 소스 파일 첫 부분은 고차원 개념과 알고리즘을 설명하고, 아래로 내려갈수록 의도를 세세하게 묘사한다. 마지막에는 가장 저차원 함수와 세부 내역을 작성한다.

> 파일 이름만으로도 올바른 모듈인지 아닌지 판단할 수 있을 정도로 이름을 신경써서 짓는다.

> 대다수 기사의 길이가 짧듯이 소스코드 전체 길이도 짧은 것이 좋다.

<BR />

### 3. 개념은 빈 행으로 분리하라

> 일련의 행 묶음 사이에 **빈 행을 넣어 분리**한다.

- 예 : 패키지 선언부, import문, 각 함수 사이에 빈 행을 두는 것이 더 가독성이 좋다.

```java
// 빈 행을 둔 경우
package fitnesse.wikitext.widgets;

import java.util.regex.*;

public class BoldWidget extends ParentWidget {
	public static final String REGEXP = "'''.+?'''";
	private static final Pattern pattern = Pattern.compile("'''(.+?)'''",
	Pattern.MULTILINE + Pattern.DOTALL);
}        

public BoldWidget(ParentWidget parent, String text) throws Exception {
	super(parent);
	Matcher match = pattern.matcher(text); match.find(); 
	addChildWidgets(match.group(1));
}

public String render() throws Exception { 
	StringBuffer html = new StringBuffer("<b>"); 		
	html.append(childHtml()).append("</b>"); 
	return html.toString();
}
```

```java
// 빈 행을 두지 않은 경우
package fitnesse.wikitext.widgets;
import java.util.regex.*;
public class BoldWidget extends ParentWidget {
	public static final String REGEXP = "'''.+?'''";
	private static final Pattern pattern = Pattern.compile("'''(.+?)'''",
	Pattern.MULTILINE + Pattern.DOTALL);
}        
public BoldWidget(ParentWidget parent, String text) throws Exception {
	super(parent);
	Matcher match = pattern.matcher(text); match.find(); 
	addChildWidgets(match.group(1));
}
public String render() throws Exception { 
	StringBuffer html = new StringBuffer("<b>"); 		
	html.append(childHtml()).append("</b>"); 
	return html.toString();
}
```

<BR />

### 4. 세로 밀집도

> **서로 밀접한 코드 행은 세로로 가까이 놓는다**. 즉, 연관성 높은 코드 사이에는 빈 행을 두지 않는다.

- 예 : 서로 연관성 있는 행끼리 세로로 가까이 놓으면 가독성이 좋아진다.

```java
// 의미없는 주석으로 변수를 떨어뜨려 놓아서 한눈에 파악이 잘 안된다.

public class ReporterConfig {
	/**
	* The class name of the reporter listener 
	*/
	private String m_className;
	
	/**
	* The properties of the reporter listener 
	*/
	private List<Property> m_properties = new ArrayList<Property>();
	public void addProperty(Property property) { 
		m_properties.add(property);
	}
}
```

```java
// 의미 없는 주석을 제거함으로써 코드가 한눈에 들어온다.

public class ReporterConfig {
	private String m_className;
	private List<Property> m_properties = new ArrayList<Property>();
    
	public void addProperty(Property property) { 
		m_properties.add(property);
	}
}
```

<br />

### 5. 수직 거리

타당한 근거가 없다면 서로 밀접한 개념은 한 파일에 속해야 마땅하고, 세로로 가까이 둬야 한다.

그렇지 않으면 시스템이 하는 일을 이해하기 위해, 이 조각 저 조각이 어디에 있는지 찾고 기억하려는 데 시간이 많이 소모된다.

> **변수 선언** : 변수는 사용하는 위치에 최대한 가까이 선언한다.

```java
private static void readPreferences() {
	InputStream is = null;   // 지역 함수는 각 함수 맨 처음에 선언
	try {
		is = new FileInputStream(getPreferencesFile()); 
		setPreferences(new Properties(getPreferences())); 
		getPreferences().load(is);
	} catch (IOException e) { 
		try {
			if (is != null) 
				is.close();
		} catch (IOException e1) {
		} 
	}
}
```

```java
public int countTestCases() { 
	int count = 0;
	for (Test each : tests) // 루프를 제어하는 변수는 보통 루프 문 내부에 선언
		count += each.countTestCases(); 
	return count;
}
```

```java
for (XmlTest test : m_suite.getTests()) {
	TestRunner tr = m_runnerFactory.newTestRunner(this, test);  // 다소 긴 함수에서 블록 상단이나 루프 직전에 변수 선언하는 경우도 있음
	tr.addListener(m_textReporter); 
	m_testRunners.add(tr);

	invoker = tr.getInvoker();
	
	for (ITestNGMethod m : tr.getBeforeSuiteMethods()) { 
		beforeSuiteMethods.put(m.getMethod(), m);
	}

	for (ITestNGMethod m : tr.getAfterSuiteMethods()) { 
		afterSuiteMethods.put(m.getMethod(), m);
	} 
}
```

<br />

> **인스턴스 변수** : 인스턴스 변수는 **클래스 맨 처음에 선언**하고, **변수 간에 세로로 거리를 두지 않는다**.

```java
public class TestSuite implements Test {
	public static Test warning(final String message) { 
		...
	}
	
	private static String exceptionToString(Throwable t) { 
		...
	}
    
    private String fName;   // 코드 중간에 인스턴스 변수 두지 말 것.
    
    public TestSuite() { }
}
```

<br />

> **종속 함수** : 한 함수가 다른 함수를 호출한다면 두 함수는 세로로 가까이 배치한다. 또한 가능하다면 **호출하는 함수를 호출되는 함수보다 먼저 배치**한다.

```java
public class WikiPageResponder implements SecureResponder { 
	protected WikiPage page;
	protected PageData pageData;
	protected String pageTitle;
	protected Request request; 
	protected PageCrawler crawler;
	
	public Response makeResponse(FitNesseContext context, Request request) throws Exception {
		String pageName = getPageNameOrDefault(request, "FrontPage");  // getPageNameOrDefault 함수 호출
		loadPage(pageName, context); 
		if (page == null)
			return notFoundResponse(context, request); 
		else
			return makePageResponse(context); 
		}

	private String getPageNameOrDefault(Request request, String defaultPageName) {  // 위에서 호출됨
		String pageName = request.getResource(); 
		if (StringUtil.isBlank(pageName))
			pageName = defaultPageName;

		return pageName; 
	}
}
```

위 코드에서, `getPageNameOrDefault` 함수 호출 시 두번째 인자로 `FrontPage` 상수를 넘겨준다.

`getPageNameOrDefault` 함수 안에서 `FrontPage` 상수를 사용할 수도 있지만, 그러면 잘 알려진 상수가 적절하지 않은 저차원 함수에 묻힌다.


상수를 알아야 마땅한 함수(`makeResponse`)에서 실제로 사용하는 함수(`getPageNameOrDefault`)로 상수를 넘겨주는 방법이 더 좋다.

<br />

> **개념적 유사성** : 코드 간 개념적 친화도가 높으면 서로 가까이 배치한다.

앞서 봤듯, 한 함수가 다른 함수를 호출해 생기는 직접적인 종속성이나,

변수와 그 변수를 사용하는 함수도 개념적 유사성이 있다고 본다.

- 다른 예 : 비슷한 동작을 수행하는 일군의 함수들

```java
// 같은 assert 관련된 동작들을 수행하며, 명명법이 똑같고 기본 기능이 유사한 함수들로써 개념적 친화도가 높다.

public class Assert {
	static public void assertTrue(String message, boolean condition) {
		if (!condition) 
			fail(message);
	}

	static public void assertTrue(boolean condition) { 
		assertTrue(null, condition);
	}

	static public void assertFalse(String message, boolean condition) { 
		assertTrue(message, !condition);
	}
	
	static public void assertFalse(boolean condition) { 
		assertFalse(null, condition);
	} 
}
```

<br />

> **세로 순서** : (1) 호출되는 함수를 호출하는 함수보다 나중에 배치하고, (2) 신문 기사처럼 가장 중요한 개념을 가장 먼저 표현한다.

<br />

### 6. 가로 형식 맞추기

한 행은 가로로 얼마나 길어야 적당할까?

> 120자 정도로 행 길이를 제한하는 것이 좋다.

#### 가로 공백과 밀집도

가로로는 공백을 사용해 밀접/느슨한 개념을 표현한다.

```java
private void measureLine(String line) { 
	lineCount++;
	int lineSize = line.length();
	totalChars += lineSize;  // 할당 연산자 좌우로 공백을 주어 왼쪽,오른쪽 요소를 확실히 구분한다. 
	
    lineWidthHistogram.addLine(lineSize, lineCount);
    // 반면 함수이름과 괄호 사이에는 공백을 없앰으로써 함수와 인수의 밀접함을 보여준다
	// 괄호 안의 인수끼리는 쉼표 뒤의 공백을 통해 인수가 별개라는 사실을 보여준다.
}
```

<br />

#### 가로 정렬

```java
public class FitNesseExpediter implements ResponseSender {
	private		Socket		  		socket;
	private 	InputStream 	  	input;
	private 	OutputStream 	  	output;
	private 	Reques		  		request; 		
	private 	Response 	  		response;	
	private 	FitNesseContext	  	context; 
	protected 	long		  		requestParsingTimeLimit;
	private 	long		  		requestProgress;
	private 	long		  		requestParsingDeadline;
	private 	boolean		  		hasError;
	
	public FitNessExpediter(Socket FitNessContext context) throw Exception {
    	this.context = context;
        socket = s;
        input = s.getInputStream();
        output = s.getOutputStream();
        requestParsingTimeLimit = 10000;
    }
}
```

위 선언부를 읽다 보면 변수 유형은 무시하고 변수 이름부터 읽게 되어 진짜 의도가 가려진다.

그러므로 선언문과 할당문을 별도로 정렬할 필요가 없다.

정렬이 필요할 정도로 목록이 길다면 목록의 길이가 문제이지 정렬이 부족해서가 아니다.

선언부가 길다는 것은 클래스를 쪼개야 한다는 것을 의미한다.

<br />

#### 들여쓰기

개발자는 **범위**로 이뤄진 계층을 표현하기 위해 코드를 들여쓴다.

> 클래스 내 메서드는 클래스보다 한 수준 들여쓴다.

> 메서드 코드는 메서드 선언보다 한 수준 들여쓴다.

> 블록 코드는 블록을 포함하는 코드보다 한 수준 들여쓴다.

때로는 간단한 if문, 짧은 while문, 짧은 함수에서 들여쓰기 규칙을 무시하고 싶을 때도 있다.

그러나 **한 행에 범위를 뭉뚱그린 코드보다, 들여쓰기로 범위를 제대로 표현한 코드가 더 가독성이 좋다**.

```java
// 들여쓰기 하지 않은 코드
public class CommentWidget extends TextWidget {
	public static final String REGEXP = "^#[^\r\n]*(?:(?:\r\n)|\n|\r)?";
	
	public CommentWidget(ParentWidget parent, String text){super(parent, text);}  // 들여쓰기 안 함
	public String render() throws Exception {return ""; }   // 들여쓰기 안함
}
```

```java
// 들여쓰기 한 코드
public class CommentWidget extends TextWidget {
	public static final String REGEXP = "^#[^\r\n]*(?:(?:\r\n)|\n|\r)?";
	
	public CommentWidget(ParentWidget parent, String text){
		super(parent, text);
	}
	
	public String render() throws Exception {
		return ""; 
	} 
}
```

<br />

### 7. 팀 규칙

프로그래머 각자 선호하는 코드 작성 방식이 있겠지만, **팀에 속한다면 팀 규칙을 따라야 한다**.

모든 팀원이 규칙을 따라야 소프트웨어가 일관적인 스타일을 보인다.

**좋은 소프트웨어 시스템은 읽기 쉬운 문서로 이뤄진다**.

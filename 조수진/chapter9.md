# 블로그 링크

[블로그 9장 정리 링크](https://velog.io/@sujinjwa/%ED%81%B4%EB%A6%B0-%EC%BD%94%EB%93%9C-9%EC%9E%A5.-%EB%8B%A8%EC%9C%84-%ED%85%8C%EC%8A%A4%ED%8A%B8)

블로그를 보는 것이 더 가독성이 좋습니다!

<br />

# 9장. 단위 테스트

## TDD 법칙 세 가지

1. 첫째 법칙: 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 않는다.
2. 둘째 법칙: 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.
3. 셋째 법칙: 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.

위 세 가지 규칙을 따르면, 개발과 테스트가 대략 30초 주기로 묶이며, 테스트 코드와 실제 코드가 함께 나온다.

<br/>

## 깨끗한 테스트 코드 유지하기

> 테스트 코드를 **깨끗하게** 짜야 한다.

테스트 코드는 실제 코드 못지 않게 중요하다.

실제 코드가 진화하면 테스트 코드도 변해야 하는데, 테스트 코드가 지저분할수록 변경하기 어려워진다.

---

### 테스트는 유연성, 유지보수성, 재사용성을 제공한다

테스트 케이스가 없다면 모든 변경이 잠정적인 버그가 되지만,

테스트 케이스가 있으면 **변경이 쉬워진다**. 안심하고 코드를 개선할 수 있다.

---

### 깨끗한 테스트 코드

> 깨끗한 테스트 코드를 위해 **가독성**이 중요하다

중복되는 부분은 없앤다.

- 예: 아래 나쁜 코드에서 `addPage`, `assertSubString`과 같이 중복되는 코드를 리팩토링한다

```java
// BAD
public void testGetPageHieratchyAsXml() throws Exception {
  crawler.addPage(root, PathParser.parse("PageOne"));
  crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
  crawler.addPage(root, PathParser.parse("PageTwo"));

  request.setResource("root");
  request.addInput("type", "pages");
  Responder responder = new SerializedPageResponder();
  SimpleResponse response =
    (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
  String xml = response.getContent();

  assertEquals("text/xml", response.getContentType());
  assertSubString("<name>PageOne</name>", xml);
  assertSubString("<name>PageTwo</name>", xml);
  assertSubString("<name>ChildOne</name>", xml);
}
```

```java
// GOOD (리팩토링 버전)
public void testGetPageHierarchyAsXml() throws Exception {
  makePages("PageOne", "PageOne.ChildOne", "PageTwo");

  submitRequest("root", "type:pages");

  assertResponseIsXML();
  assertResponseContains(
    "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
}
```

---

### 도메인에 특화된 테스트 언어

앞서 리팩토링한 코드는 시스템 조작 API를 사용하는 대신, 

**API 위에다 함수와 유틸리티를 구현한 후 그 함수(`makePages`, `submitRequest`)와 유틸리티를 사용**하므로 테스트코드를 짜기도 읽기도 쉬워진다.

---

### 이중 표준

> 테스트 코드는 단순하고, 간결하고, 표현력이 풍부해야 하지만, **실제 코드만큼 효율적일 필요는 없다**

테스트 API 코드에 적용하는 표준과 실제 코드에 적용하는 표준은 **다르다**.

- 예: 온도가 '급격하게 떨어지면' 경보, 온풍기, 송풍기가 모두 가동되는지 확인하는 코드

```java
// BAD
@Test
public void turnOnLoTempAlarmAtThreashold() throws Exception {
  hw.setTemp(WAY_TOO_COLD);   // 최종 상태에서 온도가 급강하시킴 
  controller.tic();
  assertTrue(hw.heaterState());   
  assertTrue(hw.blowerState()); 
  assertFalse(hw.coolerState()); 
  assertFalse(hw.hiTempAlarm());       
  assertTrue(hw.loTempAlarm());
}
```

```java
// GOOD (리팩토링 버전)
@Test
public void turnOnLoTempAlarmAtThreshold() throws Exception {
  wayTooCold();
  assertEquals("HBchL", hw.getState()); 
}
```
위 리팩토링한 코드에서 `wayTooCold` 함수를 만들어 기존의 `tic` 함수를 숨겼다.

`assertEquals` 함수에 들어있는 문자열 `"HBchL"`은 대문자는 '켜짐'이고, 소문자는 '꺼짐'을 뜻한다.

```java
@Test
public void turnOnCoolerAndBlowerIfTooHot() throws Exception {
  tooHot();
  assertEquals("hBChl", hw.getState());  // 너무 뜨거워서 바람(Blower), 에어컨(Cooler) 켠다
}
  
@Test
public void turnOnHeaterAndBlowerIfTooCold() throws Exception {
  tooCold();
  assertEquals("HBchl", hw.getState());  // 너무 추워서 히터(Heater), 바람(Blower) 켠다 
}

@Test
public void turnOnHiTempAlarmAtThreshold() throws Exception {
  wayTooHot();
  assertEquals("hBCHl", hw.getState());  // 'way'가 붙으면 alarm까지 켜준다
}

@Test
public void turnOnLoTempAlarmAtThreshold() throws Exception {
  wayTooCold();
  assertEquals("HBchL", hw.getState());  // 'way'가 붙으면 alarm까지 켜준다
}
```

위 코드에서 `hw`의 `getState` 함수는 다음과 같다.

```java
public String getState() {
  String state = "";
  state += heater ? "H" : "h"; 
  state += blower ? "B" : "b"; 
  state += cooler ? "C" : "c"; 
  state += hiTempAlarm ? "H" : "h"; 
  state += loTempAlarm ? "L" : "l"; 
  return state;
}
```

위 `getState` 함수는 `String` 자료형에 `+` 연산을 사용함으로써 코드가 효율적이지 못하지만,

**테스트 환경은 자원이 제한적일 가능성이 낮다**. 

즉, 실제 환경에서는 메모리, CPU 효율을 고려해야 하지만, 테스트 환경에서는 코드의 깨끗함이 더 중요하다.

---

만약 코드의 효율을 위해서라면 `StringBuffer`를 사용하는 게 맞지만, 

`StringBuffer`는 가독성이 좋지 않아 위의 테스트 코드에서 사용되지 않았다.

> 🌟 **참고)** 덧셈(`+`) 연산자 vs. StringBuffer   (출처: [Inpa Dev 님의 티스토리](https://inpa.tistory.com/entry/JAVA-%E2%98%95-String-StringBuffer-StringBuilder-%EC%B0%A8%EC%9D%B4%EC%A0%90-%EC%84%B1%EB%8A%A5-%EB%B9%84%EA%B5%90))

- 덧셈(`+`) 연산자

덧셈(`+`) 연산자를 이용해 `String` 인스턴스 문자열을 결합하면, 내용이 합쳐진 새로운 `String` 인스턴스를 생성하게 되어, 

문자열을 많이 결합하면 결합할수록 공간의 낭비 + 속도가 매우 느려진다.

```java
String result = "";
result += "hello";
result += " ";
result += "jump to java";
System.out.println(result); // hello jump to java
```
- `StringBuffer` 클래스

자바의 `StringBuffer` 클래스는 내부적으로 버퍼(buffer)라는 독립적인 공간을 가지게 해,

문자열을 바로 추가할 수 있어 공간의 낭비도 없고, 문자열 연산 속도도 매우 빠르다.

```java
StringBuffer sb = new StringBuffer();  // StringBuffer 객체 sb 생성
sb.append("hello");
sb.append(" ");
sb.append("jump to java");
String result = sb.toString();
System.out.println(result); // hello jump to java
```

<br />

## 테스트당 assert 하나

(javascript로 치면 테스트당 expect 하나인가?)

> 가능하면 함수마다 assert 문 수를 최소로 줄인다

- 나쁜 예: "출력이 XML이다"라는 assert문과 "특정 문자열을 포함한다"는 assert문이 하나의 테스트 코드로 병합되어 있다

```java
// BAD
public void testGetPageHierarchyAsXml() throws Exception {
  makePages("PageOne", "PageOne.ChildOne", "PageTwo");

  submitRequest("root", "type:pages");

  assertResponseIsXML();
  assertResponseContains(
    "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
}
```
---

> **given-when-then** 관례를 사용한다

- 좋은(리팩토링한) 예: 함수 하나애 assert문이 하나이도록 나누고, `given-when-then`으로 가독성을 높인다

```java
// GOOD (리팩토링 버전)
public void testGetPageHierarchyAsXml() throws Exception { 
  givenPages("PageOne", "PageOne.ChildOne", "PageTwo");
  
  whenRequestIsIssued("root", "type:pages");
  
  thenResponseShouldBeXML(); 
}

public void testGetPageHierarchyHasRightTags() throws Exception { 
  givenPages("PageOne", "PageOne.ChildOne", "PageTwo");
  
  whenRequestIsIssued("root", "type:pages");
  
  thenResponseShouldContain(
    "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>"
  ); 
}
```

앞서 리팩토링한 코드는 읽기에는 쉽지만, **중복되는 코드가 많아졌다**.

모두가 배보다 배꼽이 더 크다. 때로는 assert 문을 여럿 사용하는 편이 좋을 때도 있다.

<br />

### 테스트당 개념 하나

> 테스트 함수 하나는 개념 하나만 테스트한다

- 나쁜 예

```java
public void testAddMonths() {
  SerialDate d1 = SerialDate.createInstance(31, 5, 2004);

  SerialDate d2 = SerialDate.addMonths(1, d1); 
  assertEquals(30, d2.getDayOfMonth()); 
  assertEquals(6, d2.getMonth()); 
  assertEquals(2004, d2.getYYYY());
  
  SerialDate d3 = SerialDate.addMonths(2, d1); 
  assertEquals(31, d3.getDayOfMonth()); 
  assertEquals(7, d3.getMonth()); 
  assertEquals(2004, d3.getYYYY());
  
  SerialDate d4 = SerialDate.addMonths(1, SerialDate.addMonths(1, d1)); 
  assertEquals(30, d4.getDayOfMonth());
  assertEquals(7, d4.getMonth());
  assertEquals(2004, d4.getYYYY());
}
```

위의 `addMonths()` 메서드 테스트하는 코드는 3가지 기능을 수행한다.

1. (5월처럼) 31일로 끝나는 달의 마지막 날짜가 주어졌을 때, (6월처럼) 30일로 끝나는 한 달을 더하면, 날짜는 31일이 아닌 30일이어야 한다
2. 두 달을 더했을 때 두 번째 달이 31일로 끝나면, 날짜는 31일이어야 한다
3. (6월처럼) 30일로 끝나는 달의 마지막 날짜가 주어졌을 때, 31일로 끝나는 한 달을 더하면 날짜는 31일이 아닌 30일이어야 한다

이처럼 한 테스트 함수에서 여러 개념을 테스트하면,

독자는 각 절이 존재하는 이유와 각 절이 테스트하는 개념을 모두 이해해야 한다.

<br />

## F.I.R.S.T

> 깨끗한 테스트의 다섯 가지 규칙

1. **빠르게 Fast**

테스트는 빨리 돌아야 한다. 테스트가 느리면 자주 돌릴 엄두를 못 낸다.

2. **독립적으로 Independent**

각 테스트는 서로 의존하면 안 된다.

서로에게 의존할 경우 하나가 실패할 때 나머지도 잇달아 실패하므로 원인 진단이 어려워진다.

3. **반복가능하게 Repeatable**

테스트는 어떤 환경에서도 반복 가능해야 한다.

4. **자가검증하는 Self-Validating**

테스트는 부울 값으로 결과를 내야 한다. 성공 아니면 실패!

테스트가 스스로 성공과 실패를 가늠할 줄 알아야 한다.

5. **적시에 Timely**

테스트는 적시에 작성해야 한다.

단위 테스트는 테스트하려는 실제 코드를 구현하기 전에 구현한다.

실제 코드를 구현한 뒤 테스트 코드를 만들면 실제 코드가 테스트하기 어렵다는 사실을 발견할지도 모른다.

<br />

## 함께 참고하면 좋은 레퍼런스

- [깃헙 오픈소스 - 클린코드 9장 정리된 문서](https://github.com/Yooii-Studios/Clean-Code/blob/master/Chapter%2009%20-%20%EB%8B%A8%EC%9C%84%20%ED%85%8C%EC%8A%A4%ED%8A%B8.md)

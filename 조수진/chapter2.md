# 블로그 링크

[블로그 정리 링크](https://velog.io/@sujinjwa/%ED%81%B4%EB%A6%B0-%EC%BD%94%EB%93%9C-2%EC%9E%A5.-%EC%9D%98%EB%AF%B8-%EC%9E%88%EB%8A%94-%EC%9D%B4%EB%A6%84)

블로그에서 보는 것이 더 가독성이 좋습니다!

<br />

# 2장. 의미 있는 이름

들어가기 전, [변수명을 짓는 것을 도와주는 사이트](https://www.curioustore.com/#!/)를 공유한다.

100% 완벽한 변수명을 찾지 못하더라도, 변수명을 고민하는 데 드는 시간을 줄이는 데 도움을 줄 수 있을 것 같다!

## 이름을 잘 짓는 간단한 규칙

### 1. 의도를 분명히 밝혀라

- 좋지 않은 예) **코드를 <u>함축적으로</u> 작성하여, 코드 맥락이 코드 자체에 명시적으로 드러나지 않는다**

```java
public List<int[]> getThem() {
	List<int[]> list1 = new ArrayList<int[]>();
    
    for (int[] x: theList)
    	if (x[0] == 4)
        	list1.add(x);
    return list1;
}
```

위 코드 샘플엔 다음과 같은 정보가 드러나지 않는다.

> 1. `theList`에 무엇이 들었는가?
2. `theList`에서 0번째 값이 어째서 중요한가?
3. 값 `4`는 무슨 의미인가?
4. 함수가 반환하는 리스트 `list1`을 어떻게 사용하는가?

---

- 개선된 코드 예)

만약 지뢰찾기 게임을 만든다고 가정한다면, `theList`가 게임판이라는 사실을 알 수 있다.

아래 코드처럼 각 개념에 이름만 붙여도 코드는 훨씬 나아진다.

```java
public List<int[]> getFlaggedCells() {
	List<int[]> flaggedCells = new ArrayList<int[]>();
    for (int[] cell: gameBoard) {
    	if (cell[STATUS_VALUE] == FLAGGED) {
        	flaggedCells.add(cell);
        }
    }
    return flaggedCells;
}
```

<br />

### 2. 그릇된 정보를 피하라

> 약어를 쓰지 않는다

- 예: 직각삼각형의 빗변(`hypotenuse`)을 구현할 때 `hp`가 훌륭한 약어처럼 보이더라도, 독자에게는 그릇된 정보를 제공할 수 있다

---

> `List`와 같은 컨테이너 유형을 변수명에 넣지 않는다

- 예: 여러 계정을 그룹으로 묶은 변수를 `accountList`라 명명하지 말고, 단순히 `accounts`라 명명하라

---

> 변수 간에 서로 흡사한 이름을 사용하지 않는다

- 프로그래머 입장에서 각 개념 차이가 명백히 드러난다면 코드 자동 완성 기능이 굉장히 유용해질 수 있다

<br />

### 3. 의미 있게 구분하라

서로 다른 변수 간의 차이를 읽는 사람이 알 수 있도록 이름을 지어야 한다.

- 예: `moneyAmount`와 `money`, `customerInfo`와 `customer`, `accountData`와 `account`, `theMessage`와 `message`는 서로 구분이 안된다

> 해결 방법: 불용어(**`분석에 큰 의미가 없는 단어`**)를 사용하지 않는다

위 예시에서 `Info`와 `Data`, `the` 는 의미가 불분명한 불용어이다.

이러한 불용어를 사용하지 말고, 변수 간 의미가 분명히 다르다면 읽는 사람이 차이를 알도록 이름을 지어라.

<br />

### 4. 발음하기 쉬운 이름을 사용하라

프로그램은 사회 활동이다.

회사에서 직원들과 코드에 대해 토론할 때 `DtaRcd102` 라는 변수명이 나을까, `Customer`라는 변수명이 나을까?

<br />

### 5. 검색하기 쉬운 이름을 사용하라

> 상수를 사용하라

```java
const int WORK_DAYS_PER_WEEK = 5; 
```

만약 위 코드에서 `5`를 상수로 두지 않고 그대로 사용한다면, `5`가 들어가는 이름을 모두 찾은 후 의미를 분석해 원하는 상수를 가려내야 할 것이다.

하지만 상수로 두면 해당 `5`가 어떤 의도를 가지는지 상수 이름을 통해 빠르게 알 수 있다.

---

> 긴 이름이 짧은 이름보다 좋다

만약 변수 이름이 `e`라면, 십중팔구 거의 모든 프로그램, 거의 모든 문장에서 `e`가 검색될 것이다.

이런 관점에서 `e` 같은 짧은 이름보다 긴 이름이 좋다.

---

> 이름 길이는 범위 크기에 비례해야 한다

변수나 상수를 코드 여러 곳에서 사용한다면 검색하기 쉬운 이름이 바람직하지만,

클린코드 책 저자는 간단한 메서드에서 사용되는 로컬 변수는 한 문자를 사용한다고 한다.

<br />

### 6. 인코딩을 피하라

- 여기서 `인코딩`이란? -> **`변수에 부가 정보를 덧붙여 표기하는 것`**

> 변수 명에 변수의 타입(ex.`String`, `Int`)를 적지 않는다

---

> 멤버 변수에 접두어 붙이지 않는다

옛날에는 클래스나 객체의 멤버 변수에 `m_`을 붙였었나보다.

이제는 멤버 변수를 다른 색상으로 표시해주는 IDE가 있으므로, 접두어는 더욱 필요가 없어졌다.

- 예) `private String m_dsc;` -> `String description;`

---

> 인터페이스 클래스와 구현 클래스 중 하나를 인코딩해야 한다면 구현 클래스에 하라

만약 `ShapeFactory`라는 클래스가 있다면, 옛날에는 인터페이스 클래스 이름에 `I`라는 접두어를 붙여 `IShapeFactory`라고 표기했다.

그러나 이는 주의를 흐트리고 과도한 정보를 제공한다.

인터페이스 클래스와 구현 클래스 중 하나를 꼭 인코딩해야 한다면 차라리 구현 클래스에 `CShapeFactory`나 `ShapeFactoryImp` 를 명명하는 것이 낫다.

<br />

### 7. 자신의 기억력을 자랑하지 마라

> 본인의 기억력만 믿고 변수 이름을 문자 하나만으로 만들지 않는다

** 🌟 예외:  루프에서 반복 횟수를 세는 변수 `i`, `j`, `k`는 괜찮다. 루프에서 반복 횟수 변수는 전통적으로 한 글자를 사용하기 때문이다.**

---

> 남들이 이해할 수 있도록 **명료한** 변수 이름을 사용한다

#### ✅ 명료한 클래스 이름

클래스, 객체 이름은 **명사, 명사구**가 적합하다. **동사는 피한다.**

- 좋은 예: `Customer`, `WikiPage`, `Account`, `Addressparser`

- 나쁜 예: `Manager`, `Processor`, `Data`, `Info`


#### ✅ 명료한 메서드 이름

메서드 이름은 **동사, 동사구**가 적합하다. 

- 좋은 예: `postPayment`, `deletePage`, `save`



> **🌟 메서드 중 접근자, 변경자, 조건자는 값 앞에 `get`, `set`, `is`를 붙인다**

```java
string name = employee.getName();  // 접근자
customer.setName("sujin");         // 변경자
if (paycheck.isPosted()) ...       // 조건자
```

---

(무슨 말인지 이해 못함)

> 생성자를 오버로드할 경우 정적 팩토리 메서드를 사용하고 해당 생성자를 private으로 선언한다.

```java
// 첫번째 보다 두 번째 방법이 더 좋다.  
Complex fulcrumPoint = new Complex(23.0);  
Complex fulcrumPoint = Complex.FromRealNumber(23.0);
```

<br />

### 8. 기발한 이름은 피하라

재미난 이름보다 **의도를 분명하고 솔직하게 표현하는 명료한 이름이 좋다**.

기발한 이름은 짧은 시간 동안만 기억에 남고, 나중에 다시 보면 이게 뭐지? 싶을 수 있다.

<br />

### 9. 한 개념에 한 단어를 사용하라

> 추상적인 개념 하나에 단어 하나를 선택해 이를 고수한다

메서드 이름은 독자적이고 **일관적**이어야 한다.

- 나쁜 예: `DeviceManager`와 `ProtocolController`는 이름은 다르지만 근본적으로 의미가 다르지 않아 혼란을 준다.

<br />

### 10. 말장난을 하지 마라

앞에서 "한 개념에 한 단어를 사용하라"는 규칙을 따랐다고 해서 **한 단어를 두 가지 목적으로 사용해서는 안된다**.

```javascript
add(number) {
	this.score += number;
}
```

예를 들어 위와 같이 기존 값에 숫자를 더하는`add` 메서드를 만들었다고 가정하자.

새로 작성하는 메서드는 배열에 값 하나를 추가한다. 이 메서드를 `add`라고 불러도 괜찮을까? No!

새 메서드는 기존 `add` 메서드와 맥락이 다르다. 다음과 같이 `insert`나 `append`라는 이름이 적당하다.

```javascript
insert(number) {
	let scores = [];
  	scores.push(number);
}
```

<br />

### 11. 해법 영역에서 가져온 이름을 사용하라

내 코드를 읽는 사람은 **프로그래머**이기 때문에, 모든 이름을 도메인 영역에서 가져올 필요는 없다.

`JobQueue`, `Visitor 패턴` 등 전산 용어, 알고리즘 이름, 패턴 이름, 수학 용어를 사용해도 동료들은 이해할 것이다.

<br />

### 12. 문제 영역에서 가져온 이름을 사용하라

적절한 '프로그래머 용어'가 없다면 문제(도메인) 영역에서 이름을 가져온다.

우수한 프로그래머라면 **해법 영역과 문제 영역 구분**할 줄 알아야 하고, 문제 영역 개념과 관련 깊은 코드라면 문제 영역에서 이름을 가져와야 한다.

<br />

### 13. 의미 있는 맥락을 추가하라

> 의미가 분명하지 않은 변수들을 클래스, 함수 등으로 감싸서 맥락(Context)를 표현한다

- 나쁜 예) 
```java
private void printGuessStatistics(char candidate, int count) {
    String number;
    String verb;
    String pluralModifier;
    if (count == 0) {  
        number = "no";  
        verb = "are";  
        pluralModifier = "s";  
    }  else if (count == 1) {
        number = "1";  
        verb = "is";  
        pluralModifier = "";  
    }  else {
        number = Integer.toString(count);  
        verb = "are";  
        pluralModifier = "s";  
    }
    String guessMessage = String.format("There %s %s %s%s", verb, number, candidate, pluralModifier );

    print(guessMessage);
}
```

- 좋은 예)

```java
public class GuessStatisticsMessage {
    private String number;
    private String verb;
    private String pluralModifier;

    public String make(char candidate, int count) {
        createPluralDependentMessageParts(count);
        return String.format("There %s %s %s%s", verb, number, candidate, pluralModifier );
    }

    private void createPluralDependentMessageParts(int count) {
        if (count == 0) {
            thereAreNoLetters();
        } else if (count == 1) {
            thereIsOneLetter();
        } else {
            thereAreManyLetters(count);
        }
    }

    private void thereAreManyLetters(int count) {
        number = Integer.toString(count);
        verb = "are";
        pluralModifier = "s";
    }

    private void thereIsOneLetter() {
        number = "1";
        verb = "is";
        pluralModifier = "";
    }

    private void thereAreNoLetters() {
        number = "no";
        verb = "are";
        pluralModifier = "s";
    }
}
```

---

> 그래도 불분명하다면 접두어를 사용한다

- 나쁜 예)

```javascript
const name = '신도림';
const street = '거리공원로';
const houseNumber = '25';
const city = '서울시';
```

- 좋은 예)

```javascript
const addName = '신도림';
const addStreet = '거리공원로';
const addHouseNumber = '25';
const addCity = '서울시';
```

<br />

### 14. 불필요한 맥락을 없애라

'고급 휘발유 충전소(`Gas Station Deluxe`)'라는 애플리케이션을 짠다고 가정했을 때,

모든 클래스 이름을 `GSD`로 시작하겠다는 생각을 전혀 바람직하지 못하다.

비슷한 예로, `accountAddress`와 `customerAddress`를 `Address` 클래스 인스턴스로는 좋은 이름이나 클래스 이름으로는 적합하지 못하다.


<br />

## 함께 참고하면 좋은 레퍼런스

- [깃헙 오픈소스 - 클린코드 2장 정리된 문서](https://github.com/Yooii-Studios/Clean-Code/blob/master/Chapter%2002%20-%20%EC%9D%98%EB%AF%B8%20%EC%9E%88%EB%8A%94%20%EC%9D%B4%EB%A6%84.md)

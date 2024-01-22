# 블로그 링크

[블로그 링크](https://velog.io/@sujinjwa/%ED%81%B4%EB%A6%B0-%EC%BD%94%EB%93%9C-6%EC%9E%A5.-%EA%B0%9D%EC%B2%B4%EC%99%80-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0)

# 6장. 객체와 자료구조

<br />

## 자료 추상화

구현을 감추려면 추상화가 필요하다.

추상 인터페이스를 제공해 사용자가 구현을 모른 채 자료의 핵심을 조작할 수 있어야 한다.

즉, 객체는 추상화 뒤로 자료를 숨긴 채 자료를 다루는 함수만 공개한다.

- 예시 1
```java
public interface Vehicle {
	double getFuelTankCapacityInGallons();
    double getGallonsOfGasoline();
}
```

- 예시 2
```java
public interface Vehicle {
	double getPercentFuelRemaining();
}
```

`예시 1` 보다 `예시 2` 가 더 좋다.

`예시 2` 가 **자료를 세세하게 공개하기 보다 추상적인 개념으로 표현하고 있기 때문**이다.

<br />

## 자료/객체 비대칭

- 객체는 추상화 뒤로 자료를 숨긴 채 자료를 다루는 함수만 공개한다.

- 자료 구조는 객체와 달리 자료를 그대로 공개하며 별다른 함수는 제공하지 않는다.

```java
// 절차지향적 도형
public class Square { 
  public Point topLeft; 
  public double side;
}

public class Rectangle { 
  public Point topLeft; 
  public double height; 
  public double width;
}

public class Circle { 
  public Point center; 
  public double radius;
}

public class Geometry {
  public final double PI = 3.141592653589793;
  
  public double area(Object shape) throws NoSuchShapeException {
    if (shape instanceof Square) { 
      Square s = (Square)shape; 
      return s.side * s.side;
    } else if (shape instanceof Rectangle) { 
      Rectangle r = (Rectangle)shape; 
      return r.height * r.width;
    } else if (shape instanceof Circle) {
      Circle c = (Circle)shape;
      return PI * c.radius * c.radius; 
    }
    throw new NoSuchShapeException(); 
  }
}
```

위 예제에서, 각 도형 클래스는 간단한 자료 구조로, 아무 메서드도 제공하지 않으며, 도형이 동작하는 방식은 `Geometry` 클래스에서 구현한다.

`Geometry` 클래스에 둘레 길이를 구하는 `perimeter()` 함수를 추가한다면, 도형 클래스에는 아무 영향도 주지 않는다.

반대로 새 도형을 추가한다면, `Geometry` 클래스에 속한 함수를 모두 고쳐야 한다.

<br />

반면, 아래의 객체지향적인 도형 클래스의 경우, 새 도형을 추가해도 기존 함수에 아무런 영향을 미치지 않는다.

반면 새 함수를 추가하고 싶다면 도형 클래스를 전부 고쳐야 한다.

```java
// 객체지향적 도형
public class Square implements Shape { 
  private Point topLeft;
  private double side;
  
  public double area() { 
    return side * side;
  } 
}

public class Rectangle implements Shape { 
  private Point topLeft;
  private double height;
  private double width;

  public double area() { 
    return height * width;
  } 
}

public class Circle implements Shape { 
  private Point center;
  private double radius;
  public final double PI = 3.141592653589793;

  public double area() {
    return PI * radius * radius;
  } 
}
```

<br />

객체와 자료 구조는 근본적으로 양분된다.

> 자료 구조를 사용하는 절차적인 코드는 **기존 자료 구조를 변경하지 않으면서 새 함수를 추가하기 쉽다**. 반면, 객체 지향 코드는 **새로운 함수를 추가하기 어렵다. 그러려면 모든 클래스를 고쳐야 한다**.

> 자료 구조를 사용하는 절차적인 코드는 **새로운 자료 구조를 추가하기 어렵다. 그러려면 모든 함수를 고쳐야 한다**. 반면, 객체 지향 코드는 **기존 함수를 변경하지 않으면서 새 클래스를 추가하기 쉽다**.

객체 지향 코드에서 어려운 변경은 절차적인 코드에서 쉽고,

절차적인 코드에서 어려운 변경은 객체 지향 코드에서 쉽다.

**새로운 함수가 아니라 새로운 자료 타입이 필요한 경우, 클래스와 객체지향 기법이 가장 적합**하다.

반면, **새로운 자료 타입이 아니라 새로운 함수가 필요한 경우 절찾거인 코드와 자료 구조가 더 적합**하다.

> 꼭 객체가 답이 아니고, 단순한 자료 구조와 절차적인 코드가 적합한 상황도 있다는 걸 기억하자!

<br />

## 디미터 법칙

> 디미터 법칙 : 모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다는 법칙

즉, 객체는 조회 함수(`get`, `set`)로 내부 구조를 공개하면 안된다는 의미이다.

> 디미터 법칙은 **"클래스 C의 메서드 f는 다음과 같은 객체의 메서드만 호출해야 한다"**고 주장한다.
- 클래스 C
- f가 생성한 객체
- f 인수로 넘어온 객체
- C 인스턴스 변수에 저장된 객체

하지만 위 객체에서 허용된 메서드가 반환하는 객체의 메서드는 호출하면 안 된다.

### 기차 충돌

> 기차 충돌 : 함수가 반환하는 객체의 함수를 연속적으로 호출하는 방식 (여러 기차가 한 줄로 이어진 기차처럼 보임)

```java
final String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();
```

기차 충돌은 조잡하다 여겨지므로 피하는 것이 좋고, 다음과 같이 코드를 나누는 편이 좋다.

```java
Options opts = ctxt.getOptions();
File scratchDir = opts.getScratchDir();
final String outputDir = scratchDir.getAbsolutePath();
```

위 예제가 디미터 법칙을 위반하는지 여부는 위의 변수들이 객체인지 자료 구조인지에 달렸다.

객체라면 내부 구조를 숨겨야 하므로 확실히 디미터 법칙을 위반한다.

반면, 자료 구조라면 당연히 내부 구조를 노출하므로 문제되지 않는다.

<br />

### 잡종 구조

> 잡종 구조 : 절반은 객체, 절반은 자료 구조인 경우

잡종 구조는 새로운 함수, 새로운 자료 구조도 추가하기 어렵다,

즉, 잡종 구조는 객체와 자료 구조의 단점만 모아놓은 구조라 피하는 편이 좋다.


<br />

### 구조체 감추기

```java
ctxt.getAbsolutePathOfScratchDirectoryOption();
```

위 예시는 `ctxt` 객체의 `get` 함수를 이용해 속을 드러내는 코드이다.

객체에게는 속을 드러내지 않고, **뭔가를 하라고** 말해야 한다.

```java
BufferedOutputStream bos = ctxt.createScratchFileStream(classFileName);
```

위 예시처럼 객체에게 '임시 파일을 생성하라'고 적당한 임무를 맡기는 것이 좋다.

`ctxt`는 내부 구조를 들어내지 않으며, 자신이 몰라야 하는 여러 객체를 탐색할 필요가 없다.

<br />

## 자료 전달 객체

> 자료 전달 객체(DTO, Data Transfer Object) : 공개 변수만 있고 함수가 없는 클래스, 즉 자료 구조체를 자료 전달 객체라고도 한다.

```java
public class Address { 
  public String street; 
  public String streetExtra; 
  public String city; 
  public String state; 
  public String zip;
  
  public Address(String street, String streetExtra, String city, String state, String zip) {
  	this.street = street;
    this.streetExtra = streetExtra;
    this.city = city;
    this.state = state;
    this.zip = zip;
  }
  
  public String getStreet() {
  	return street;
  }
  
  public String getStreetExtra() {
  	return streetExtra;
  }
  
  public String getCity() {
  	return city;
  }
  
  ...
}
```

위 예시처럼 공개/비공개 변수에 조회/설정 함수를 추가한 자료 구조를 **활성 레코드**라고 한다.

불행히도 활성 레코드에 비즈니스 규칙 메서드를 추가해 이런 자료 구조를 객체로 취급하는 개발자가 흔하다.

하지만 이는 바람직하지 않다. 자료 구조도, 객체도 아닌 잡종 구조가 나오기 때문이다.

> 해결책 : **활성 레코드는 자료 구조로 취급한다**. 비즈니스 규칙을 담으면서 내부 자료(활성 레코드의 인스턴스)를 숨기는 객체는 따로 생성한다.

<br />

## 결론

- 시스템 구현 시, 새로운 자료 타입을 추가하는 유연성이 필요하다면 객체가 더 적합하다.

- 새로운 동작을 추가하는 유연성이 필요하면, 자료 구조와 절차적인 코드가 더 적합하다.

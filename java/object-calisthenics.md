# 객체지향 생활 체조

[소트웍스 앤솔러지 (저 소트웍스, 마틴 파울러 | 역 이창신 외 4명 | 위키북스 |2009)](http://book.naver.com/bookdb/book_detail.nhn?bid=5441199) 의 6장 객체지향 생활 체조 부분에서, 실생활에서 좋은 객체지향 설계 습관을 기를 수 있는 훈련을 소개 한다. 평소 보다 훨씬 엄격한 코드 작성 표준을 제시함으로써 객체지향적인 사고 개발을 돕는다. 기록해 두고 자주 읽고 실천하기 위해 옮긴다.



# 규칙

1. 한 메서드에 오직 한 단계의 들여쓰기만 한다.
2. else 예약어를 사용하지 않는다.
3. 모든 원시값과 문자열을 포장(Wrap) 한다.
4. 한 줄에 점(.)을 하나만 찍는다.
5. 줄여쓰지 않는다 (축약금지).
6. 모든 엔티티를 작게 유지한다.
7. 2개 이상의 인스턴스 변수를 가진 클래스를 쓰지 않는다.
8. 1급(First-Class) 콜렉션을 사용한다.
9. 게터(Getter) / 세터(Setter) / 프로퍼티(Property) 를 사용하지 않는다.



## 규칙1. 한 메서드에 오직 한 단계의 들여쓰기만 한다.

메서드당 하나의 제어 구조나 하나의 문장 단락으로 구성 되도록 노력한다. 중첩 된 제어 구조가 있다면 다단계의 추상화를 코드로 짠 것이며, 고로 한 가지 이상의 일을 하고 있다는 뜻이다.

```java
class Board {
    String board() {
        StringBuffer buffer = new StringBuffer();
        for (int i=0; i<10; i++) {
            for (int j=0; j<10; j++) {
                buffer.append(data[i][j]);
            }
            buffer.append("\n");
        }
        return buffer.toString();
    }
}
```

위와 같은 코드는 메소드 추출을 통해 아래와 같이 변경될 수 있다.

```java
class Board {
    String board() {
        StringBuffer buffer = new StringBuffer();
        collectRows(buffer);
        return buffer.toString();
    }

    void collectRows(StringBuffer buffer) {
        for (int i=0; i<10; i++)
          collectRow(buffer, i);
    }

    void collecRow(StringBuffer buffer, int row) {
        for (int i=0; i<10; i++)
          buffer.append(data[row][i]);
        buffer.append("\n");
    }
}
```

각각의 메서드는 역할에 맞는 이름을 가지며, 더 적어진 코드에서 버그의 존재를 판별하기란 대체로 훨씬 쉽다.

## 규칙2. else 예약어를 사용하지 않는다.

if-else 구문은 거의 모든 프로그래밍 언에에 포함 된 간단한 조건 논리이다. 더욱이 리팩토링 보다는 기존 조건문에 분기 하나 더 치기가 무척 쉽기 때문에 가끔 따라가기 불가능할 정도로 지저분한 중첩 조건문이 나타나기도 한다.

```java
public static void endMe() {
    if (status == DONE) {
        doSomething();
    } else {
        otherSomething();
    }
}
```

위와 같은 코드는 보호절(guard clause)과 조기 반환(early return) 을 활용 하여 아래와 같이 변경될 수 있다.

```java
public static void endMe() {
    if (status == DONE) {
        doSomething();
        return;
    }
    otherSomething();
}
```

다만 조기 반환 코드를 너무 많이 사용하면, 오히려 간결함을 저해 하기 쉽다는 점을 주의 해야 한다. 객체지향 언어는 다형성이라는 강력한 도구를 통해 복잡한 조건문을 처리할 수 있다. 다형성을 채택한 설계는 읽고 유지하기 쉬우며 더욱 분명히 코드의 의도를 표현하기에 이른다.

## 규칙3. 모든 원시값과 문자열을 포장(Wrap) 한다.

int 값 하나 자체는 아무 의미 없는 스칼라 값일 뿐이다. 어떤 메서드가 int 값을 매개변수로 받는다면 (int 값 자체는 아무 의미가 없기 때문에) 그 의도를 나타내는 메서드 이름과 매개변수 이름을 짓기 위해 노력해야 한다. 시간이나 돈을 나타내는 원시 값들도 오브젝트로 만들면 프로그래머에게 그 값이 어떤 값이며 왜 쓰이고 있는지 정보를 전할 수 있다.

## 규칙4. 한 줄에 점(.)을 하나만 찍는다.

```java
buffer.append(location.current.representation.substring(0, 1));
```

위 코드와 같이 어떠한 코드 한 줄에서 점이 하나 이상 있으면 그른 곳에서 동작이 일어나고 있다고 볼 수 있다. 이 경우 그 오브젝트는 중개상, 즉 너무 많은 사람들에 대해 지나치게 알고 있는 꼴이다. 이런 중복된 점들은 캡슐화를 어기고 있다는 방증이기도 하다. 오브젝트가 자기 속을 들여다 보려 하기 보다는 뭔가 작업을 하도록 만들려고 해야 한다. 캡슐화의 주요점은 클래스 경계를 벗어나 알 필요가 없는 타입으로 진입하지 않는 것이다.

## 규칙5. 줄여쓰지 않는다 (축약금지).

누구나 클래스, 메서드, 변수 이름을 줄이려는 유혹에 곧잘 빠진다. 왜 축약을 하고 싶은지 생각해보라.

- 계속 반복해서 똑같은 단어를 치기 때문인가? 메서드가 너무 대대적으로 사용되고 있는지 확인하라.
- 메서드 이름이 길어지고 있기 때문인가? 이는 책임소재의 오류나 클래스의 부재라는 신호탄일지 모른다.

클래스와 메서드 이름을 한 두 단어로 유지하려 애쓰고 문맥을 중복하는 이름을 피하자. 클래스 이름이 Order 라면 shipOrder 라고 메서드 이름을 지을 필요 없다. 짧게 ship 이라고 하면 클라이언트가 order.ship()  이라고 간결하게 표현할 수 있다.

## 규칙6. 모든 엔티티를 작게 유지한다.

50줄 이상 되는 클래스와 10개 파일 이상 되는 패키지는 없어야 한다. 보통 50줄 이상의 클래스는 한 가지 이상의 일을 하는 것이며 코드의 이해와 재사용을 점점 더 어렵게 끌고 간다. 클래스를 작게 작성할 때 난관은 같이 있어야 말이 되는 동작의 묶음이 종종 있는 경우이다. 이는 패키지를 최대한 활용해야 하는 곳이기도 하다. 패키지도 클래스처럼 응집력 있고 단일한 목표가 있어야 한다. 패키지를 작게 유지함으로써 패키지 자체로 진정한 정체성을 지니기에 이른다.

## 규칙7. 2개 이상의 인스턴스 변수를 가진 클래스를 쓰지 않는다.

```java
class Name {
    String first;
    String middle;
    String last;
}
```

새로운 인스턴스 변수를 하나 더 기존 클래스에 추가하면 클래스의 응집도는 즉시 떨어진다. 일반적으로 많은 인스턴스 변수를 지닌 클래스를 대상으로 응집력 있는 단일 작얼을 설명할 수 있는 경우는 거의 없다. 위 코드는 다음과 같이 세 클래스로 분해할 수 있다.

```java
class Name {
    Surname family;
    GivenNames given;
}
class Surname {
    // 성(family name)의 판별은 다른 이름 요소와 근본적으로 다르다.
    String family;
}
class GivenNames {
    // first name, middle name 등 그 밖의 이름을 흡수할 수 있다.
    List<String> names;
}
```

속성의 집합에서 오브젝트를 협력 오브젝트의 계층 구조로 분해하면 더 직접적으로 효율적인 오브젝트 모델에 이른다.



## 규칙8. 1급(First-Class) 콜렉션을 사용한다.

콜렉션을 포함한 클래스는 반드시 다른 멤버 변수가 없어야 한다. 각 콜렉션은 그 자체로 포장 되어 있으므로 이제 콜렉션과 관련된 동작의 근거지가 마련된 셈이다. 필터가 이 새 클래스의 일부가 됨을 알 수 있다.

> Comment. 이 규칙이 어떤 의미인지 명확히 이해하지 못했다. 클래스에 콜렉션이 포함 된다면 다른 멤버 변수가 있어서는 안되고 그 콜렉션과 관련 된 동작들 예를 들어, 콜렉션을 돌면서(Iterate) 필터하는 것과 같은 메소드만 존재해야 한다고 주장하는 것 같다. 하지만 이 규칙을 실천함으로써 얻는 장점이 잘 이해가 가지 않는다.



## 규칙9. 게터(Getter) / 세터(Setter) / 프로퍼티(Property) 를 사용하지 않는다.

책의 내용은 잘 이해 되지 않아 개인적인 생각을 정리한다.

- 세터를 사용하지 않으면 객체가 생성된 후 임의로 값이 변하는 것을 방지할 수 있다. 외부에서 임의로 객체 내부의 값을 변경하지 않고 그 책임을 객체가 지도록 한다.
- 클라이언트에서 게터를 통해 값을 가져와 확인하고(if-else) 뭔가 처리를 하려고 하지 말고 그냥 그 객체에 책임을 넘기도록 한다. `Don't ask! Just Tell` 이라고 한다.
- 프로퍼티? 어떤 것을 의미하는건지 잘 모르겠다.







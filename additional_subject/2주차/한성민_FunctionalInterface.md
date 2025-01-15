# 함수형 인터페이스 (Functional Interface)

---

## 함수형 프로그래밍

---

**절차지향언어 :** 절차를 알고 진행하는 순서가 중요

예시) 커피를 만드는 공장에서 커피를 만들 때

커피 원두 키우기 → 열매에서 원두 가져오기 → 원두 로스팅 → 원두 분쇄 → 커피 추출(드립)

해당 절차는 꼭 지켜야하고 해당 프로세스는 양 옆의 업무와 연관이 있을 수 있다.

<br>

하지만 **함수형 언어**는 비슷하게 진행이 되지면 의미적으로 다르다.

예를 들어 원두 분쇄의 업무가 있다면 해당 작업자는 단지 원두라는게 들어오면 양 옆의 프로세스를 모르고 단순 분쇄만 진행한다. 즉 인풋과 아웃풋이 중요하다.

### 특징
1. 인풋과 아웃풋이 존재한다.
2. 외부 환경으로부터 철저히 독립적이다.
3. 같은 인풋에 있어서 언제나 동일한 아웃풋을 생산한다. (순수 함수)
4. First Class(일급 함수_함수를 변수에 할당), Higher-Order Functions(고차 함수_함수 자체를 인자로 전달, 함수에서 또 다른 함수를 리턴)

> 함수형 프로그래밍이 아닌 경우 (3가지)
>
> 1. 함수에서 외부의 상태값을 참조할 때 or 외부의 상태를 변경할 때
> ```java
> int num = 1;
>
> public int add(int a) { 
>   return a + num;
> }
>
> // num = 2 -> add(2) = 4;
> // num = 3 -> add(2) = 5;
> ```
> ```java
> public int add(int a, int b) { 
>   return a + b;
> }
>
> int result = add(2, 3); // a가 2, b가 3이면 항상 5를 반환 (동일한 결과값) 
> ```
> 2. 비상태, 불변성을 지키지 못할 경우
> ```java
> Person person = new Person("성민", 25);
> 
> public Person increaseAge(Person p) {
>   // Person 객체의 상태를 변경 및 Person의 불변성 위반
>   p.setAge(p.getAge() + 1);
>   return p;
> }
> ```
> ```java
> public Person increaseAge(Person p) {
>  // 새로운 객체를 만듦으로써 불변성을 유지
>  // 부작용 X
>   return new Person(p.getName(), p.getAge() + 1);
> }
>```
> 3. if, switch, for loop 같이 statements로 함수가 작성된 경우
> ```java
> public void multiply(int[] numbers, int multiplier) {
>    for (int i = 0; i < numbers.length; i++) {
>       numbers[i] = numbers[i] * multiplier;  // 배열의 값을 직접 수정하는 부작용 발생
>    }
> }
> ```
>```java
> public int[] multiply(int[] numbers, int multiplier) {
>    return Arrays.stream(numbers)
>                 .map(num -> num * multiplier)
>                 .toArray();
> }
> // 입력 배열 numbers를 직접 수정하지 않고, 새로운 배열을 반환
> // 루프 대신 스트림 사용
>```

## 함수형 인터페이스

---

**함수형 인터페이스** : 정확히 하나의 추상 메서드를 지정하는 인터페이스

함수형 인터페이스의 목적은 자바에서 람다 표현식(Lambda Expression)을 이용해 함수형 프로그래밍을 구현하기 위해서다.

우리가 자주 볼 수 있는 함수형 인터페이스는 Comparator, Runnable 등이 있다.

`Comparator<T>`

```java
@FunctionalInterface
public interface Comparator<T> {
    int compare(T o1, T o2);

    boolean equals(Object obj);

    default Comparator<T> reversed() {
        return Collections.reverseOrder(this);
    }
	  public static<T> Comparator<T> comparingDouble(ToDoubleFunction<? super T> keyExtractor) {
        Objects.requireNonNull(keyExtractor);
        return (Comparator<T> & Serializable)
            (c1, c2) -> Double.compare(keyExtractor.applyAsDouble(c1), keyExtractor.applyAsDouble(c2));
    }
    ...
}
```

>❗
>
> 함수형 인터페이스는 `default 메소드`(기본 구현을 제공하는 바디를 포함한 메서드)를 포함할 수 있다.
> 
> 많은 디폴트 메서드가 있더라도 추상 메서드가 오직 하나면 함수형 인터페이스다.

<br>

`@FunctionalInterface`

@FunctionalInterface는 함수형 인터페이스를 가리키는 애노테이션이다.

@FunctionalInterface를 인터페이스에 선언했지만 함수형 인터페이스가 아닌 경우, 컴파일 시점에 에러가 발생한다.

## 함수형 인터페이스 사용

---

함수형 인터페이스는 표준 API를 제공한다. (`java.util.function`)

![함수형 인터페이스 표준 API](https://github.com/user-attachments/assets/bb99e4d6-8c0d-41ee-9dba-fb659e192a51)

### Predicate<T>

Predicate는 T 형식의 객체를 사용하는 boolean 표현식이 필요한 상황에서 사용할 수 있다.

```java
public static void main(String[] args) {
    Predicate<String> isNotEmpty = str -> str != null && !str.isEmpty();

    System.out.println(isNotEmpty.test("Hello"));  // true
    System.out.println(isNotEmpty.test(""));       // false
    System.out.println(isNotEmpty.test(null));     // false
}
```

### Consumer<T>

Consumer는 제네릭 형식 T객체를 받아서 void를 반환하는 accept 추상 메서드를 정의한다.

T 형식의 객체를 인수로 받아서 어떤 동작을 수행하고 싶을 때 사용한다.

```java
public static void main(String[] args) {
    Consumer<String> printMessage = message -> System.out.println("Message: " + message);

    printMessage.accept("Hello, World!"); // Message: Hello, World!
    printMessage.accept("Java Functional Interface!"); // Message: Java Functional Interface!
}
```

### Function<T, R>

제네릭 형식 T를 인수로 받아서 제네릭 형식 R 객체를 반환하는 추상 메서드 apply를 정의한다.

입력을 출력으로 매핑하는 람다를 정의할 때 Function 인터페이스를 활용할 수 있다.

```java
public static void main(String[] args) {
    Function<Integer, String> numberToString = number -> "Number is: " + number;

    System.out.println(numberToString.apply(5));   // Number is: 5
    System.out.println(numberToString.apply(10));  // Number is: 10
}
```

출처:

[함수형 프로그래밍이 뭔가요?](https://www.youtube.com/watch?v=jVG5jvOzu9Y&t=2s)

[☕ 함수형 인터페이스 표준 API 총정리](https://inpa.tistory.com/entry/%E2%98%95-%ED%95%A8%EC%88%98%ED%98%95-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4-API)

[함수형-프로그래밍이란](https://jongminfire.dev/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D%EC%9D%B4%EB%9E%80)

[함수형프로그래밍이 대세다?! (함수형 vs 객체지향)](https://www.youtube.com/watch?v=4ezXhCuT2mw)

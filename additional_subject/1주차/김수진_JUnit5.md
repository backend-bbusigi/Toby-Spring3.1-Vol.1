## JUnit 5란?

Java의 테스트 프레임워크로, Java 8 이상에서 작동한다.

### JUnit5 구조

> **`JUnit Platform`** + **`JUnit Jupiter`** + **`JUnit Vintage`**
> 
- **JUnit Platform**: **JVM에서 테스트 프레임워크를 실행하기 위한 기반**을 제공하며, TestEngine API를 통해 다양한 테스트 프레임워크의 개발을 지원한다.
- **JUnit Jupiter**: JUnit 5에서 새로운 기능과 API를 제공하는 테스트 엔진
- **JUnit Vintage**: 하위 호환성을 위해 기존 JUnit 3와 JUnit 4 기반으로 돌아가는 플랫폼에 테스트 엔진을 제공한다.

### JUnit 4와의 차이점

JUnit 4는 단일 jar 파일로 구성된 단일 아키텍처로, 모든 기능이 단일 라이브러리에 포함되어 있어 확장이 제한적이었다.

JUnit5는 Platform, Jupiter, Vintage로 나뉘는 모듈화된 설계로 더 확장에 유연하다.

- **JUnit Platform**: 테스트 실행 및 통합을 위한 기반
- **JUnit Jupiter**: JUnit 5의 새로운 API와 확장 모델을 제공
- **JUnit Vintage**: JUnit 3 및 4의 테스트를 지원하기 위한 호환성 레이어

**annotation 변화**

| JUnit 4 | JUnit 5 | 설명 |
| --- | --- | --- |
| `@Before` | `@BeforeEach` | 각 테스트 메서드 실행 전에 실행 |
| `@After` | `@AfterEach` | 각 테스트 메서드 실행 후에 실행 |
| `@BeforeClass` | `@BeforeAll` | 테스트 클래스의 모든 테스트 전에 실행 (static 메서드여야 함) |
| `@AfterClass` | `@AfterAll` | 테스트 클래스의 모든 테스트 후에 실행 (static 메서드여야 함) |
| `@Ignore` | `@Disabled` | 테스트를 비활성화 |
| 없음 | `@DisplayName` | 테스트 이름을 명시적으로 지정 |
| 없음 | `@Nested` | 중첩 테스트 클래스를 지원 |
| 없음 | `@Tag` | 테스트를 그룹화하거나 필터링 |

**Assertions, Assumptions**

JUnit 4의 `org.junit.Assert` 클래스 대신, JUnit 5에서는 `org.junit.jupiter.api.Assertions`와 `org.junit.jupiter.api.Assumptions`를 사용한다.

<추가된 기능>

- **`assertAll`**: 여러 Assertions를 그룹화
- **`assertThrows`**: 예외 발생 확인
- **`assertTimeout`**: 코드 실행 시간 제한

**조건부 테스트 실행**

JUnit5는 조건에 따라 테스트 실행을 제어할 수 있는 API를 제공한다.

- **`@EnabledOnOs`**: 특정 운영 체제에서만 테스트 실행
- **`@EnabledIf`**: 커스텀 조건으로 실행 여부 결정

**Parameterized Tests (파라미터화 테스트)**

JUnit 4에서는 `@RunWith(Parameterized.class)`를 사용해 비교적 제한적인 방식으로 파라미터화 테스트를 작성했다.

JUnit 5에서는 새로운 **`@ParameterizedTest`** 를 도입해 다양한 소스(`@ValueSource`, `@CsvSource`, `@MethodSource` 등)에서 데이터를 제공받아 테스트를 작성할 수 있다.

```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.ValueSource;
import org.junit.jupiter.params.provider.CsvSource;
import static org.junit.jupiter.api.Assertions.assertEquals;

public class JUnit5ParameterizedTest {

    @ParameterizedTest
    @ValueSource(ints = {1, 2, 3, 4})
    void testMultiplyByTwo(int input) {
        assertEquals(input * 2, input + input);
    }

    @ParameterizedTest
    @CsvSource({
        "1, 2",
        "2, 4",
        "3, 6",
        "4, 8"
    })
    void testMultiplyByTwoWithExpected(int input, int expected) {
        assertEquals(expected, input * 2);
    }
}
```

**중첩 테스트 지원**

JUnit 5는 `@Nested`를 도입해 내부 클래스에서 논리적으로 연관된 테스트를 작성할 수 있다.

```java
import org.junit.jupiter.api.Nested;
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.assertEquals;

public class JUnit5NestedTest {

    @Test
    void outerTest() {
        assertEquals(2, 1 + 1);
    }

    @Nested
    class InnerTests {
        @Test
        void innerTest1() {
            assertEquals(3, 1 + 2);
        }

        @Test
        void innerTest2() {
            assertEquals(4, 2 + 2);
        }
    }
}
```

**Java 8+ 기능 활용**

- **람다 표현식**: `assertThrows`나 `assertTimeout`에 람다를 사용할 수 있다.
    
    ```java
    import org.junit.jupiter.api.Test;
    
    import static org.junit.jupiter.api.Assertions.assertThrows;
    
    public class JUnit5LambdaTest {
        @Test
        void testException() {
            assertThrows(ArithmeticException.class, () -> {
                int result = 1 / 0;
            });
        }
    }
    ```
    
- **메서드 참조**
    
    ```java
    import org.junit.jupiter.api.Test;
    import static org.junit.jupiter.api.Assertions.assertThrows;
    
    public class ExceptionTest {
    
        @Test
        void testExceptionWithMethodReference() {
            assertThrows(ArithmeticException.class, this::divideByZero);
        }
    
        private void divideByZero() {
            int result = 1 / 0;
        }
    }
    ```
    

### JUnit5 주요 Annotation

| 어노테이션 | 설명 |
| --- | --- |
| `@Test` | 테스트 메소드를 표시 |
| `@BeforeEach` | 각 테스트 메소드 실행 전 수행 |
| `@AfterEach` | 각 테스트 메소드 실행 후 수행 |
| `@BeforeAll` | 모든 테스트 실행 전에 한 번 수행 (static 메소드여야 함) |
| `@AfterAll` | 모든 테스트 실행 후 한 번 수행 (static 메소드여야 함) |
| `@Disabled` | 테스트를 비활성화 |
| `@DisplayName` | 테스트 이름을 사용자 정의로 지정 |
| `@Nested` | 중첩 테스트 클래스를 정의 |
| `@Tag` | 테스트를 그룹화 |
| `@ExtendWith` | 커스텀 확장을 적용 |

### Assertions

테스트에서 예상 결과와 실제 결과를 비교해 특정 조건을 확인하고 테스트가 실패하면 Assertions는 예외를 발생시킨다.

1. **assertEquals(expected, actual)**
    
    두 값이 같은지 확인
    
    ```java
    @Test
    void testEquals() {
        assertEquals(5, 2 + 3);
    }
    ```
    
2. **assertNotEquals(expected, actual)**
    
    두 값이 같지 않은지 확인
    
    ```java
    @Test
    void testNotEquals() {
        assertNotEquals(4, 2 + 3);
    }
    ```
    
3. **assertTrue(condition)**
    
    조건이 true인지 확인
    
    ```java
    @Test
    void testTrue() {
        assertTrue(3 > 2);
    }
    ```
    
4. **assertFalse(condition)**
    
    조건이 false인지 확인
    
    ```java
    @Test
    void testFalse() {
        assertFalse(3 < 2);
    }
    ```
    
5. **assertNull(actual)**
    
    값이 null인지 확인
    
    ```java
    @Test
    void testNull() {
        assertNull(null);
    }
    ```
    
6. **assertNotNull(actual)**
    
    값이 null이 아닌지 확인
    
    ```java
    @Test
    void testNotNull() {
        assertNotNull("JUnit 5");
    }
    ```
    
7. **assertArrayEquals(expectedArray, actualArray)**
    
    두 배열의 모든 요소가 같은지 확인
    
    ```java
    @Test
    void testArrayEquals() {
        int[] expected = {1, 2, 3};
        int[] actual = {1, 2, 3};
        assertArrayEquals(expected, actual);
    }
    ```
    
8. **assertThrows(expectedType, executable)**
    
    실행 코드가 특정 예외를 던지는지 확인
    
    ```java
    @Test
    void testThrows() {
        assertThrows(ArithmeticException.class, () -> {
            int result = 1 / 0;
        });
    }
    
    ```
    
9. **assertAll(heading, executable...)**
    
    여러 개의 Assertions를 그룹화하여 모두 실행
    
    ```java
    @Test
    void testAll() {
        assertAll("Multiple Assertions",
            () -> assertEquals(4, 2 * 2),
            () -> assertTrue(5 > 1),
            () -> assertNotNull("JUnit")
        );
    }
    ```
    
10. **assertTimeout(duration, executable)**
    
    코드 실행 시간이 특정 시간 제한을 초과하지 않는지 확인
    
    ```java
    @Test
    void testTimeout() {
        assertTimeout(Duration.ofMillis(100), () -> {
            Thread.sleep(50);
        });
    }
    ```
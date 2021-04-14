# Chapter 6

## 6.1 Given-When-Then으로 테스트 구조화

- 주어졌을 때(Given), 경우(When), 그러면(Then) 을 기준으로 그루핑 하기 
- 주석 까지 달아주면 좋다 

- 문제 코드

```java
class CruiseControlTest {

    @Test
    void setPlanetarySpeedIs7667() {
        CruiseControl cruiseControl = new CruiseControl();
        cruiseControl.setPreset(SpeedPreset.PLANETARY_SPEED);
        Assertions.assertTrue(7667 == cruiseControl.getTargetSpeedKmh());
    }
}
```

- 개선 코드

```java
class CruiseControlTest {

    @Test
    void setPlanetarySpeedIs7667() {
        CruiseControl cruiseControl = new CruiseControl();

        cruiseControl.setPreset(SpeedPreset.PLANETARY_SPEED);

        Assertions.assertTrue(7667 == cruiseControl.getTargetSpeedKmh());
    }
}
 
class Other {  //주석까지 달았을 때

    void otherTest() {
        // given
        CruiseControl cruiseControl = new CruiseControl();

        // when
        cruiseControl.setPreset(SpeedPreset.PLANETARY_SPEED);

        // then
        Assertions.assertTrue(7667 == cruiseControl.getTargetSpeedKmh());
    }
}
```
## 6.2 의미 있는 어서션 사용하기 

- assertTrue 보다는 assertEquals를 사용 이유는 메시지가 바로 나오기 때문.

- 문제 코드 
```java
class CruiseControlTest {

    @Test
    void setPlanetarySpeedIs7667() {
        CruiseControl cruiseControl = new CruiseControl();

        cruiseControl.setPreset(SpeedPreset.PLANETARY_SPEED);

        Assertions.assertTrue(7667 == cruiseControl.getTargetSpeedKmh());
    }
}
```

- 개선 코드 
```java
class CruiseControlTest {

    @Test
    void setPlanetarySpeedIs7667() {
        CruiseControl cruiseControl = new CruiseControl();

        cruiseControl.setPreset(SpeedPreset.PLANETARY_SPEED);

        Assertions.assertEquals(7667, cruiseControl.getTargetSpeedKmh()); // assertEquals 사용
    }
}
```

## 6.3 실제 값보다 기대 값을 먼저 보이기 

- assertEquals 안에 인수를 쓸때 순서 잘 지키기
- 먼저 무엇을 원하는 지 생각 

- 문제 코드 
```java
class CruiseControlTest {

    @Test
    void setPlanetarySpeedIs7667() {
        CruiseControl cruiseControl = new CruiseControl();

        cruiseControl.setPreset(SpeedPreset.PLANETARY_SPEED);

        Assertions.assertEquals(cruiseControl.getTargetSpeedKmh(), 7667);
    }
}
```
- 실제 코드
```java
class CruiseControlTest {

    @Test
    void setPlanetarySpeedIs7667() {
        CruiseControl cruiseControl = new CruiseControl();

        cruiseControl.setPreset(SpeedPreset.PLANETARY_SPEED);

        Assertions.assertEquals(7667, cruiseControl.getTargetSpeedKmh()); // 첫번째가 바라는 값, 두번째가 실제값 (o)
    }
}
```
## 6.4 합당한 허용값 사용하기 

- 산술연산으로 인해 근사값이 되면 부동 소수점 수와 비교하는 테스트에서 실패할 수도 있음.
- 그래서 허용값을 명시해준다. 

- 문제 코드
```java
class OxygenTankTest {

    @Test
    void testNewTankIsEmpty() {
        OxygenTank tank = OxygenTank.withCapacity(100);
        Assertions.assertEquals(0, tank.getStatus());
    }

    @Test
    void testFilling() {
        OxygenTank tank = OxygenTank.withCapacity(100);

        tank.fill(5.8);
        tank.fill(5.6);

        Assertions.assertEquals(0.114, tank.getStatus()); // 0.114 값을 원하지만 실제 값은 0.113999999... 가 나올 수 있음.
    }
}
```
- 개선 코드 
```java
class OxygenTankTest {
    static final double TOLERANCE = 0.00001;


    @Test
    void testNewTankIsEmpty() {
        OxygenTank tank = OxygenTank.withCapacity(100);

        Assertions.assertEquals(0, tank.getStatus(), TOLERANCE);
    }

    @Test
    void testFilling() {
        OxygenTank tank = OxygenTank.withCapacity(100);

        tank.fill(5.8);
        tank.fill(5.6);

        Assertions.assertEquals(0.114, tank.getStatus(), TOLERANCE);  //최소 TOLERANCE = 0.00001로 설정 하면 됨
    }
}

```
## 6.5 예외 처리는 JUnit에게 맡기기


- 문제 코드
```java
class LogbookTest {

    @Test
    void readLogbook() {
        Logbook logbook = new Logbook();

        try {
            List<String> entries = logbook.readAllEntries();
            Assertions.assertEquals(13, entries.size());
        } catch (IOException e) {
            Assertions.fail(e.getMessage()); //원인 사슬이 깨지는 코드 
        }
    }

    @Test
    void readLogbookFail() {
        Logbook logbook = new Logbook();

        try {
            logbook.readAllEntries(); //테스트 실패했다만 알지 구체적인 이유를 알 수 없음.
            Assertions.fail("read should fail");
        } catch (IOException ignored) {}
    }
}
```

- 개선 코드 
```java
class LogbookTest {

    @Test
    void readLogbook() throws IOException {
        Logbook logbook = new Logbook();

        List<String> entries = logbook.readAllEntries();

        Assertions.assertEquals(13, entries.size()); //Junit에 예외 처리를 하는 내장 함수가 있다. 굳이 예외처리를 따로 할 필요x
    }

    @Test
    void readLogbookFail() {
        Logbook logbook = new Logbook();

        Executable when = () -> logbook.readAllEntries();

        Assertions.assertThrows(IOException.class, when); // assertThrows로 어떤 종류의 예외가 던져지길 바라는지 명시적으로 나타냄
    }
}

```
 ## 6.6 테스트 설명하기 
 
 - 문제 코드 
 ```java
 class OxygenTankTest {
    static final double PERMILLE = 0.001;

    @Test
    @Disabled
    void testFill() {
        OxygenTank smallTank = OxygenTank.withCapacity(50);

        smallTank.fill(22);

        Assertions.assertEquals(0.44, smallTank.getStatus(), PERMILLE);
    }

    @Test
    private void testFill2() {
        OxygenTank bigTank = OxygenTank.withCapacity(10_000);
        bigTank.fill(5344.0);

        Executable when = () -> bigTank.fill(6000);

        Assertions.assertThrows(IllegalArgumentException.class, when);
    }
}
 
 ```
 
 - 개선 코드 
 ```java
 class OxygenTankTest {
    static final double PERMILLE = 0.001;

    @Test
    @DisplayName("Expect 44% after filling 22l in an empty 50l tank") //테스트 설명을 구체적으로 가능
    @Disabled("We don't have small tanks anymore! TODO: Adapt for big tanks") //왜 비활성하는지 설명
    void fillTank() {
        OxygenTank smallTank = OxygenTank.withCapacity(50);

        smallTank.fill(22);

        Assertions.assertEquals(0.44, smallTank.getStatus(), PERMILLE);
    }

    @Test
    @DisplayName("Fail if fill level > tank capacity")
    void failOverfillTank() {
        OxygenTank bigTank = OxygenTank.withCapacity(10_000);
        bigTank.fill(5344.0);

        Executable when = () -> bigTank.fill(6000);

        Assertions.assertThrows(IllegalArgumentException.class, when);
    }
}
 ```
 ## 6.7 독립형 테스트 사용하기 
 
 - @BeforeEach를 사용하면 테스트의 독립성이 떨어진다. 
 
 - 문제 코드
 ```java
 class OxygenTankTest {
    OxygenTank tank;

    @BeforeEach
    void setUp() {
        tank = OxygenTank.withCapacity(10_000);
        tank.fill(5_000);
    }

    @Test
    void depressurizingEmptiesTank() {
        tank.depressurize();

        Assertions.assertTrue(tank.isEmpty());
    }

    @Test
    void completelyFillTankMustBeFull() {
        tank.fillUp();

        Assertions.assertTrue(tank.isFull());
    }
}
 
 ```
 - 개선 코드
 ```java
 class OxygenTankTest {
    static OxygenTank createHalfFilledTank() { // BeforeEach 대신에 static 메서드 
        OxygenTank tank = OxygenTank.withCapacity(10_000);
        tank.fill(5_000);
        return tank;
    }

    @Test
    void depressurizingEmptiesTank() {
        OxygenTank tank = createHalfFilledTank();

        tank.depressurize();

        Assertions.assertTrue(tank.isEmpty());
    }

    @Test
    void completelyFillTankMustBeFull() {
        OxygenTank tank = createHalfFilledTank();

        tank.fillUp();

        Assertions.assertTrue(tank.isFull());
    }
}
```

## 6.8 테스트 매개변수화 

- 문제 코드 

```java

class DistanceConversionTest {

    @Test
    void testConversionRoundTrip() {
        assertRoundTrip(1);
        assertRoundTrip(1_000);  //뒤이어 나오는 assertion을 숨겨버림
        assertRoundTrip(9_999_999);
    }

    private void assertRoundTrip(int kilometers) {
        Distance expectedDistance = new Distance(
                DistanceUnit.KILOMETERS,
                kilometers
        );

        Distance actualDistance = expectedDistance
                .convertTo(DistanceUnit.MILES)
                .convertTo(DistanceUnit.KILOMETERS);

        Assertions.assertEquals(expectedDistance, actualDistance); 
    }
}
```

- 개선 코드 
```java
class DistanceConversionTest {

    @ParameterizedTest(name = "#{index}: {0}km == {0}km->mi->km") // 테스트 설명
    @ValueSource(ints = {1, 1_000, 9_999_999}) //매개변수를 정수 배열로 
    void testConversionRoundTrip(int kilometers) {
        Distance expectedDistance = new Distance(
                DistanceUnit.KILOMETERS,
                kilometers
        );

        Distance actualDistance = expectedDistance
                .convertTo(DistanceUnit.MILES)
                .convertTo(DistanceUnit.KILOMETERS);

        Assertions.assertEquals(expectedDistance, actualDistance); 
    }
}

```
## 6.9 경계 케이스 다루기

- 문제 코드
```java
class TransmissionParserTest {

    @Test
    void testValidTransmission() {
        TransmissionParser parser = new TransmissionParser();

        Transmission transmission = parser.parse("032Houston, UFO sighted!");

        Assertions.assertEquals(32, transmission.getId());
        Assertions.assertEquals("Houston, UFO sighted!",
                transmission.getContent());
    }
}
```

- 개선 코드 
```java
class TransmissionParserTest {

    @Test
    void testValidTransmission() {
        TransmissionParser parser = new TransmissionParser();

        Transmission transmission = parser.parse("032Houston, UFO sighted!");

        Assertions.assertEquals(32, transmission.getId());
        Assertions.assertEquals("Houston, UFO sighted!",
                transmission.getContent());
    }

    @Test
    void nullShouldThrowIllegalArgumentException() { // null일 때
        Executable when = () -> new TransmissionParser().parse(null);
        Assertions.assertThrows(IllegalArgumentException.class, when);
    }

    @Test
    void malformedTransmissionShouldThrowIllegalArgumentException() { // 기형문자열일때
        Executable when = () -> new TransmissionParser().parse("t͡ɬɪŋɑn");
        Assertions.assertThrows(IllegalArgumentException.class, when);
    }
}
```

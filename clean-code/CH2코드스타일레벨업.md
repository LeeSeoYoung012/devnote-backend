# Chapter2
## 2.1 매직 넘버를 상수로 대체  

- 매직넘버: 표면상 의미가 없는 숫자이지만 프로그램의 동작을 제어
- 매직 넘버를 static final 대문자로 만들어준다.

- 문제 코드
```java
class CruiseControl {

   void setPreset(int speedPreset) {
        if (speedPreset == 2) { //매직넘버
            setTargetSpeedKmh(16944);
        } else if (speedPreset == 1) { //매직넘버
            setTargetSpeedKmh(7667); 
        } else if (speedPreset == 0) { //매직넘버
            setTargetSpeedKmh(0);
        }
    }
   void setTargetSpeedKmh(double speed) {
        targetSpeedKmh = speed;
    }
}
```

- 개선 코드
```java
class CruiseControl {

    static final int STOP_PRESET = 0;
    static final int PLANETARY_SPEED_PRESET = 1;
    static final int CRUISE_SPEED_PRESET = 2;

    static final double CRUISE_SPEED_KMH = 16944;
    static final double PLANETARY_SPEED_KMH = 7667;
    static final double STOP_SPEED_KMH = 0;

   private double targetSpeedKmh;

    void setPreset(int speedPreset) {
        if (speedPreset == CRUISE_SPEED_PRESET) { //유의미하고 이해하기 쉬운 이름으로 변경
            setTargetSpeedKmh(CRUISE_SPEED_KMH); 
        } else if (speedPreset == PLANETARY_SPEED_PRESET) {
            setTargetSpeedKmh(PLANETARY_SPEED_KMH);
        } else if (speedPreset == STOP_PRESET) {
            setTargetSpeedKmh(STOP_SPEED_KMH);
        }
    }

    void setTargetSpeedKmh(double speed) {
        targetSpeedKmh = speed;
    }
  }
```

## 2.2 정수 상수 대신 열거 형(Enum)

- 위의 코드에서는 음수와 같은 유효하지 않는 값이 speedPreset으로 들어올 있음.
- 자바 타입 시스템을 통해 유효하지 않은 입력값을 막는다. 

- 개선 코드
```java
class CruiseControl {

  private double targetSpeedKmh;
  
  void setPreset(SpeedPreset speedPreset){
    
    Object.requireNonNull(speedPreset); // 유효하지 않으면 자바 컴파일러가 중지시킴.
    
    setTargetSpeedKmh(speedPreset.speedKmh); //if, else if 문이 없어져 더 간단해짐
  
  }
  
  void setTargetSpeedKmh(double speedKmh){
    targetSpeedKmh = speedKmh;
  }
 }
 
 enum SpeedPreset {
     STOP(0), PLANETARY_SPEED(7667), CRUISE_SPEED(16944);
     
     final double speedKmh;
     
     SpeedPreset(double speedKmh){
        this.speedKmh = speedKmh;
     }
}
```

## 2.3 For 루프 대신 For-Each

- for-each는 set 처럼 인덱싱 되지 않는 컬렉션에도 동작한다.
 
- 개선 코드
```java
class LaunchChecklist {

    List<String> checks = Arrays.asList("Cabin Pressure",
                                        "Communication",
                                        "Engine");

    Status prepareForTakeoff(Commander commander) {
        for (String check : checks) {  // for 문 대신에 for-each 적용
            boolean shouldAbortTakeoff = commander.isFailing(check);
            if (shouldAbortTakeoff) {
                return Status.ABORT_TAKE_OFF;
            }
        }
        return Status.READY_FOR_TAKE_OFF;
    }
}


```
## 2.4 순회하며 컬렉션 수정하지 않기 

- while문과 Iterator 사용하면서 다음 원소가 있는지 확인하면서 제거.

- 문제 코드
```java
class Inventory {

    private List<Supply> supplies = new ArrayList<>();

    void disposeContaminatedSupplies() {
        for (Supply supply : supplies) {
            if (supply.isContaminated()) {
                supplies.remove(supply); //자료구조 변경시 충돌 발생, List, Queue, Set Collection은 ConcurrentModificationException 발생
            }
        }
    }
}
```
- 개선 코드
```java
class Inventory {

    private List<Supply> supplies = new ArrayList<>();

    void disposeContaminatedSupplies() {
        Iterator<Supply> iterator = supplies.iterator();
        while (iterator.hasNext()) { // hasNext를 통해 다음 원소가 남아 있는지 묻는다.
            if (iterator.next().isContaminated()) { // 그다음에 확인하고 지운다.
                iterator.remove();
            }
        }
    }
}
```

## 2.5 순회하며 계산 집약적 연산하지 않기 

- java.utio.regex.Pattern 의 Pattern.matches(regex,supply.toString()) 은 오토마톤을 만들고 컴파일을 함. 
- for 문 안에 matches를 쓰면 컴파일을 반복하면서 시간과 처리 전력을 소모함. 
- 그래서 Pattern.matches -> Pattern.compile, Pattern.matcher로 구분지어서 사용

- 문제 코드
```java
class Inventory {

    private List<Supply> supplies = new ArrayList<>();

    List<Supply> find(String regex) {
        List<Supply> result = new LinkedList<>();
        for (Supply supply : supplies) {
            if (Pattern.matches(regex, supply.toString())) { 
                result.add(supply);
            }
        }
        return result;
    }
}
```
- 개선 코드
```java
class Inventory {

    private List<Supply> supplies = new ArrayList<>();

    List<Supply> find(String regex) {
        List<Supply> result = new LinkedList<>();
        Pattern pattern = Pattern.compile(regex); // 단 한번만 정규식 객체를 컴파일하여 연산을 최소화
        for (Supply supply : supplies) {
            if (pattern.matcher(supply.toString()).matches()) {
                result.add(supply);
            }
        }
        return result;
    }
}
```
## 2.6 새 줄로 그루핑

- 문제 코드
```java
enum DistanceUnit {

    MILES, KILOMETERS;

    static final double MILE_IN_KILOMETERS = 1.60934;
    static final int IDENTITY = 1;
    static final double KILOMETER_IN_MILES = 1 / MILE_IN_KILOMETERS;

    double getConversionRate(DistanceUnit unit) {
        if (this == unit) {
            return IDENTITY;
        }
        if (this == MILES && unit == KILOMETERS) {
            return MILE_IN_KILOMETERS;
        } else {
            return KILOMETER_IN_MILES;
        }
    }
}
```
- 개선 코드
```java
enum DistanceUnit {

    MILES, KILOMETERS;

    static final int IDENTITY = 1; // 독립적인 단위라 분리

    static final double MILE_IN_KILOMETERS = 1.60934;
    static final double KILOMETER_IN_MILES = 1 / MILE_IN_KILOMETERS;

    double getConversionRate(DistanceUnit unit) {
        if (this == unit) { //같은 단위인지 확인
            return IDENTITY;
        }
        
        if (this == MILES && unit == KILOMETERS) { //변환
            return MILE_IN_KILOMETERS;
        } else {
            return KILOMETER_IN_MILES;
        }
    }
}
```
## 2.7 이어붙이기 대신 서식화

- 문제 코드
```java
class Mission {

    Logbook logbook;
    LocalDate start;

    void update(String author, String message) {
        LocalDate today = LocalDate.now();
        String month = String.valueOf(today.getMonthValue());
        String formattedMonth = month.length() < 2 ? "0" + month : month;
        String entry = author.toUpperCase() + ": [" + formattedMonth + "-" +
                today.getDayOfMonth() + "-" + today.getYear() + "](Day " +
                (ChronoUnit.DAYS.between(start, today) + 1) + ")> " +
                message + System.lineSeparator();
        logbook.write(entry);
    }
}

class Usage {
    static void main(String[] args) {
        new Mission().update("LInUS", "message");
    }
}
```
- 개선 코드
```java
class Mission {

    Logbook logbook;
    LocalDate start;

    void update(String author, String message) {
        final LocalDate today = LocalDate.now();
        String entry = String.format("%S: [%tm-%<te-%<tY](Day %d)> %s%n", //스트링 레이아웃 과 데이터 분리
                author, today,
                ChronoUnit.DAYS.between(start, today) + 1, message);
        logbook.write(entry);
    }
}

class Usage {
    static void main(String[] args) {
        new Mission().update("LInUS", "message");
    }
}
```
## 2.8 직접 만들지 말고 자바 API 사용하기 

- 문제 코드

```java
class Inventory {

    private List<Supply> supplies = new ArrayList<>();

    int getQuantity(Supply supply) {
        if (supply == null) {
            throw new NullPointerException("supply must not be null");
        }

        int quantity = 0;
        for (Supply supplyInStock : supplies) {
            if (supply.equals(supplyInStock)) {
                quantity++;
            }
        }

        return quantity;

    }
}
```

- 개선 코드

```java

class Inventory {

    private List<Supply> supplies = new ArrayList<>();

    int getQuantity(Supply supply) {
        Objects.requireNonNull(supply, "supply must not be null"); //api 사용

        return Collections.frequency(supplies, supply); //api 
    }
}
```

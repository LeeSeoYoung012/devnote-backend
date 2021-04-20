# Chapter 7

## 7.1 불 매개변수로 메서드 분할 

- 코드를 읽으면 누구나 불 매개변수의 목적을 알 수 있어야 한다.
- boolean을 호출하게 되면 매개변수가 실제로 어떤 역할을 하는지 알기 어려워 일반적으로 코드이해가 어려워진다.

- 문제 코드
```java
class Logbook {

    static final Path CAPTAIN_LOG = Paths.get("/var/log/captain.log");
    static final Path CREW_LOG = Paths.get("/var/log/crew.log");

    void log(String message, boolean classified) throws IOException {
        if (classified) {
            writeMessage(message, CAPTAIN_LOG);
        } else {
            writeMessage(message, CREW_LOG);
        }
    }

    void writeMessage(String message, Path location) throws IOException {
        String entry = LocalDate.now() + " " + message;
        Files.write(location, Collections.singleton(entry),
                StandardCharsets.UTF_8, StandardOpenOption.APPEND);
    }
}

class Main {
    static void usage() throws IOException {
        Logbook logbook = new Logbook();
        logbook.log("Aliens sighted!", true);
        logbook.log("Toilet broken.", false);
    }
}

```
- 개선 코드 
```java
class Logbook {

    static final Path CAPTAIN_LOG = Paths.get("/var/log/captain.log");
    static final Path CREW_LOG = Paths.get("/var/log/crew.log");

    void writeToCaptainLog(String message) throws IOException { 
        writeMessage(message, CAPTAIN_LOG);
    }

//메서드 명을 보면 메시지가 어떤 로그에 속하는지 분명히 알 수 있고 호출하는 코드만 보아도 메서드가 무엇을 하는지 알 수 있다.
    void writeToCrewLog(String message) throws IOException {  
        writeMessage(message, CREW_LOG);
    }
    
    void writeMessage(String message, Path location) throws IOException {
        String entry = LocalDate.now() + " " + message;
        Files.write(location, Collections.singleton(entry),
                StandardCharsets.UTF_8, StandardOpenOption.APPEND);
    }
}

class Main {
    static void usage() throws IOException {
        Logbook logbook = new Logbook();
        logbook.writeToCaptainLog("Aliens sighted!");
        logbook.writeToCrewLog("Toilet broken. Again...");
    }
}
```

## 7.2 옵션 매개변수로 메서드 분할 

- null 입력값을 받지 않고 메서드 분할을 한다.

- 문제 코드  

```java
class Logbook {

    static final Path CREW_LOG = Paths.get("/var/log/crew.log");

    List<String> readEntries(LocalDate date) throws IOException {
        final List<String> entries = Files.readAllLines(CREW_LOG,
                StandardCharsets.UTF_8);
        if (date == null) { 
            return entries;
        }

        List<String> result = new LinkedList<>();
        for (String entry : entries) {
            if (entry.startsWith(date.toString())) {
                result.add(entry);
            }
        }
        return result;
    }
}

class Main {
    static void usage() throws IOException {
        Logbook logbook = new Logbook();
        List<String> completeLog = logbook.readEntries(null);

        final LocalDate moonLanding = LocalDate.of(1969, Month.JULY, 20);
        List<String> moonLandingLog = logbook.readEntries(moonLanding);
    }
}
```

- 개선 코드 : readEntries(LocalDate date), readAllEntries() 메서드 두개로 분할

```java
class Logbook {

    static final Path CREW_LOG = Paths.get("/var/log/crew.log");

    List<String> readEntries(LocalDate date) throws IOException {  
        Objects.requireNonNull(date);
        
        List<String> result = new LinkedList<>();
        for (String entry : readAllEntries()) {
            if (entry.startsWith(date.toString())) {
                result.add(entry);
            }
        }
        return result;
    }

    List<String> readAllEntries() throws IOException { 
        return Files.readAllLines(CREW_LOG, StandardCharsets.UTF_8); 
    }
}

class Main {
    static void usage() throws IOException {
        Logbook logbook = new Logbook();
        List<String> completeLog = logbook.readAllEntries();

        final LocalDate moonLanding = LocalDate.of(1969, Month.JULY, 20);
        List<String> moonLandingLog = logbook.readEntries(moonLanding); 
    }
}

```

## 7.3 구체 타입보다 추상 타입 

- 문제 코드
 
```java
class Inventory {
    LinkedList<Supply> supplies = new LinkedList();

    void stockUp(ArrayList<Supply> delivery) {
        supplies.addAll(delivery);
    }

    LinkedList<Supply> getContaminatedSupplies() {
        LinkedList<Supply> contaminatedSupplies = new LinkedList<>();
        for (Supply supply : supplies) {
            if (supply.isContaminated()) {
                contaminatedSupplies.add(supply);
            }
        }
        return contaminatedSupplies;
    }
}

class Usage {
    static void main(String[] args) {
        CargoShip cargoShip = null;
        Inventory inventory = null;
        Stack<Supply> delivery = cargoShip.unload();
        ArrayList<Supply> loadableDelivery = new ArrayList<>(delivery); //delivery를 arrayList로 바꾸고 stockUp호출해야하는 번거러움이 있음
        inventory.stockUp(loadableDelivery);
    }
}
```

- 개선 코드 

```java

class Inventory {
    List<Supply> supplies = new LinkedList(); // 기존의 LinkedList 대신 List

    void stockUp(Collection<Supply> delivery) { // 어떤 collection 이던지 허용
        supplies.addAll(delivery);
    }

    List<Supply> getContaminatedSupplies() { // 구체적 타입이 아닌 List를 반환
        List<Supply> contaminatedSupplies = new LinkedList<>();
        for (Supply supply : supplies) {
            if (supply.isContaminated()) {
                contaminatedSupplies.add(supply);
            }
        }
        return contaminatedSupplies;
    }
}
class Usage {
    static void main(String[] args) {
        CargoShip cargoShip = null;
        Inventory inventory = null;
        Stack<Supply> delivery = cargoShip.unload();
        inventory.stockUp(delivery);
    }
}
```

## 7.4 가변 상태봗 불변 상태 사용하기 

- 객체는 유효하지 않은 변경이 일어나지 않도록 보호해야 한다 
- 값 객체를 불변으로 만드는 것은 값 객체를 처리하는 방법으로서 백분율, 돈, 통화, 시간 , 날짜, 좌표 거리를 표현.

- 문제 코드 

```java
class Distance {
    DistanceUnit unit;
    double value;

    Distance(DistanceUnit unit, double value) {
        this.unit = unit;
        this.value = value;
    }

    static Distance km(double value) {
        return new Distance(DistanceUnit.KILOMETERS, value);
    }

    void add(Distance distance) {
        distance.convertTo(unit);
        value += distance.value;
    }

    void convertTo(DistanceUnit otherUnit) {
        double conversionRate = unit.getConversionRate(otherUnit);
        unit = otherUnit;
        value = conversionRate * value;
    }
}


class Main {
    static void usage() {
        Distance toMars = new Distance(DistanceUnit.KILOMETERS, 56_000_000);
        Distance marsToVenus = new Distance(DistanceUnit.LIGHTYEARS, 0.000012656528);
        Distance toVenusViaMars = toMars; // 가르키는 객체가 같다
        toVenusViaMars.add(marsToVenus); // toMars의 값까지 간접적으로 변경
    }
}
```


- 개선 코드 

```java
final class Distance {
    final DistanceUnit unit; // final 키워드를 추가하면 바꿀수 없다.
    final double value; // final 키워드를 추가하면 바꿀수 없다.


    Distance(DistanceUnit unit, double value) {
        this.unit = unit;
        this.value = value;
    }

    Distance add(Distance distance) {
        return new Distance(unit, value + distance.convertTo(unit).value);
    }

    Distance convertTo(DistanceUnit otherUnit) {
        double conversionRate = unit.getConversionRate(otherUnit);
        return new Distance(otherUnit, conversionRate * value);
    }
}

class Main {
    static void usage() {
        Distance toMars = new Distance(DistanceUnit.KILOMETERS, 56_000_000);
        Distance marsToVenus = new Distance(DistanceUnit.LIGHTYEARS, 0.000012656528);
        Distance toVenusViaMars = toMars.add(marsToVenus)
                                        .convertTo(DistanceUnit.MILES);
    }
}
```

## 7.5 상태와 동작 결합하기 

- 상태와 동작이 같이 있어야, 정보 은닉이 가능해진다. 
- 웹 프레임워크의 컨트롤러에는 필드 없이 메서드 매개변수만 있는 등 전형적으로 상태가 없어 이러한 규칙을 어기게 됨. 

- 문제 코드 
```java

class Hull {
    int holes; //상태
}


class HullRepairUnit {

    void repairHole(Hull hull) {  //동작
        if (isIntact(hull)) {
            return;
        }
        hull.holes--;
    }

    boolean isIntact(Hull hull) {
        return hull.holes == 0;
    }
}

```

- 개선 코드 
```java

class Hull {
    int holes;  // 상태 

    void repairHole() { // 동작 
        if (isIntact()) {
            return;
        }
        holes--;
    }

    boolean isIntact() {
        return holes == 0;
    }
}

```

## 7.6 참조 누수 피하기 

- 세터와 게터를 보호해야한다
- 애당초에 세터를 허용하지 않는게 좋다. 

- 문제 코드 
```java
class Inventory {

    private final List<Supply> supplies;

    Inventory(List<Supply> supplies) {
        this.supplies = supplies; // null을 생성자에게 반환할 수 있다.
    }

    List<Supply> getSupplies() {
        return supplies; // get을 통해 얻은 supplies에 변경 연산을 수행하면 재고 상태가 변경될 수도 있음
    }
}

class Usage {

    static void main(String[] args) {
        List<Supply> externalSupplies = new ArrayList<>();
        Inventory inventory = new Inventory(externalSupplies);

        inventory.getSupplies().size(); // == 0
        externalSupplies.add(new Supply("Apple"));
        inventory.getSupplies().size(); // == 1

        inventory.getSupplies().add(new Supply("Banana"));
        inventory.getSupplies().size(); // == 2
    }
}

```


- 개선 코드 
```java
class Inventory {

    private final List<Supply> supplies;

    Inventory(List<Supply> supplies) {
        this.supplies = new ArrayList<>(supplies); // ArrayList로 변경 -> null 들어올시 바로 예외 발생
    }

    List<Supply> getSupplies() {
        return Collections.unmodifiableList(supplies); // unmodifiableList로 래핑한 후 읽기 접근만 가능하도록 한다. 
    }
}

class Usage {

    static void main(String[] args) {
        List<Supply> externalSupplies = new ArrayList<>();
        Inventory inventory = new Inventory(externalSupplies);

        inventory.getSupplies().size(); // == 0
        externalSupplies.add(new Supply("Apple"));
        inventory.getSupplies().size(); // == 0

        // UnsupportedOperationException
        inventory.getSupplies().add(new Supply("Banana"));
    }
}

```

## 7.7 널 반환하지 않기 

- 반환할 값이 없으면 그냥 null을 반환하는 프로그램이 가끔있는데 null을 반환하면 안된다. 

- 문제 코드 
```java
class SpaceNations {

    static List<SpaceNation> nations = Arrays.asList(
            new SpaceNation("US", "United States"),
            new SpaceNation("RU", "Russia")
    );

    static SpaceNation getByCode(String code) {
        for (SpaceNation nation : nations) {
            if (nation.getCode().equals(code)) {
                return nation;
            }
        }
        return null; 
    }
}

class SpaceNation {

    final String code;
    final String name;

    SpaceNation(String code, String name) {
        this.code = code;
        this.name = name;
    }

    String getName() {
        return name;
    }

    String getCode() {
        return code;
    }
}

class Usage {

    static void main(String[] args) {
        String us = SpaceNations.getByCode("US").getName();
        // -> "United States"
        String anguilla = SpaceNations.getByCode("AI").getName();
        // -> NullPointerException
    }
}

```


- 개선 코드 
```java
class SpaceNations {

    /** Null object. */
    static final SpaceNation UNKNOWN_NATION = new SpaceNation("", "");

    static List<SpaceNation> nations = Arrays.asList(
            new SpaceNation("US", "United States"),
            new SpaceNation("RU", "Russia")
    );

    static SpaceNation getByCode(String code) {
        for (SpaceNation nation : nations) {
            if (nation.getCode().equals(code)) {
                return nation;
            }
        }
        return UNKNOWN_NATION; // 실질적인 값이 없음을 명시적으로 표현한 객체를 반환하는 방식 
    }
}

class SpaceNation {
    final String code;
    final String name;

    SpaceNation(String code, String name) {
        this.code = code;
        this.name = name;
    }

    String getCode() {
        return code;
    }

    String getName() {
        return name;
    }
}

class Usage {

    static void main(String[] args) {
        String us = SpaceNations.getByCode("US").getName(); // -> "United States"
        String anguilla = SpaceNations.getByCode("AI").getName(); // -> ""
    }
}

```

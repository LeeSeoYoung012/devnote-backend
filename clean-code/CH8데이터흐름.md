# Chapter 8

## 8.1 익명 클래스 대신 람다 사용하기 

- 익명 클래스 : 클래스 명이 없고 클래스에 인스턴스 딱 하나 있는 것
- Java 8 에서는 익명 클래스 대신에 람다를 사용 

- 문제 코드
```java
interface InterCom {

    void broadcast(String message);
}

class Calculator {

    Map<Double, Double> values = new HashMap<>();

    Double square(Double x) {
        Function<Double, Double> squareFunction =
                new Function<Double, Double>() {  //익명 클래스 
                    @Override
                    public Double apply(Double value) {
                        return value * value;
                    }
                };
        return values.computeIfAbsent(x, squareFunction);
    }
}
```

- 개선 코드 
```java
class Calculator {

    Map<Double, Double> values = new HashMap<>();

    Double square(Double value) {
        Function<Double, Double> squareFunction = factor -> factor * factor;
        return values.computeIfAbsent(value, squareFunction);
    }
}

class Calculator2 {

    Map<Double, Double> values;

    Double square(Double value) {
        /*
        // one-liner
        Function<Double, Double> squareFunction = factor -> factor * factor;
        //  multi-liner
        Function<Double, Double> squareFunction = factor -> {
            return factor * factor;
        };
        */
        return 1.0;
    }
}

/*
*/

class Calculator3 {

    Map<Double, Double> values;

    Double square(Double value) {
        /*
        // without type definition and braces
        Function<Double, Double> squareFunction = factor -> factor * factor;
        //  with type definition and braces
        Function<Double, Double> squareFunction = (Double factor) -> factor * factor; //매개변수 두개이상 혹은 명시적인 타입 선언시에는 소괄호 필요 
        */
        return 1.0;
    }
}

```

## 8.2 명령형 방식 대신 함수형

- 함수형의 특징: '무엇'을 한는지만 명시 '어떻게'에 대해서는 관심이 없다. ( 보통 어떻게 보다 '무엇을'에 관심이 많다.) 
- 따라서 람다를 자주 쓰자.

- 문제 코드
```java

interface Supply {

    String getName();

    boolean isUncontaminated();
}

class Inventory {

    List<Supply> supplies = new ArrayList<>();

    long countDifferentKinds() {
        List<String> names = new ArrayList<>();
        for (Supply supply : supplies) {
            if (supply.isUncontaminated()) {
                String name = supply.getName();
                if (!names.contains(name)) {
                    names.add(name);
                }
            }
        }
        return names.size();
    }
}
```

- 개선 코드  
```java

interface Supply {

    String getName();

    boolean isUncontaminated();
}

class Inventory {

    List<Supply> supplies = new ArrayList<>();

    long countDifferentKinds() {
        return supplies.stream()
                       .filter(supply -> supply.isUncontaminated()) // 필터는 특정 조건을 충족하는 것만 통과시킴
                       .map(supply -> supply.getName()) // supply의 이름만 남긴다. ( Function 이 Supply 타입을 String 이라는 다른 타입으로 매핑 )
                       .distinct() // 중복은 버려짐
                       .count(); 
    }
}

class Whatever {
    void main() {
        Predicate<Supply> uncontaminated = supply -> supply.isUncontaminated();
        Function<Supply, String> supplyToName = supply -> supply.getName();
    }
```

## 8.3 람다 대신 메서드 참조 

- 람다 표현식의 일부만 테스트하기가 어렵다. 
- 람다는 filter하는 predicate, map 하는 function 둘중 하나 
- predicate 조건이 복잡하거나 function이 여러 행에 걸쳐 변환하면 잠재적인 오류 가능성이 있음. 
- 이를 해결하기 위해서 메서드 참조라는 것이 있음
- 참조하려는 메서드는 사용하려는 곳에 부합해야함 ex) filter 연산: 객체를 받아 불을 변환하는 메서드, map 연산: 객체를 받아 객체를 변환하는 메서드 

- 문제 코드
```java
interface Supply {

    String getName();

    boolean isUncontaminated();

    boolean isContaminated();
}

class Inventory {

    List<Supply> supplies = new ArrayList<>();

    long countDifferentKinds() {
        return supplies.stream()
                       .filter(supply -> !supply.isContaminated()) // 람다 표현식
                       .map(supply -> supply.getName()) //람다 표현식
                       .distinct()
                       .count();
    }
}
```

- 실제 코드 
```java

interface Supply {

    String getName();

    boolean isContaminated();

    boolean isUncontaminated();
}

class Inventory {

    List<Supply> supplies = new ArrayList<>();

    long countDifferentKinds() {
        return supplies.stream()
                       .filter(Supply::isUncontaminated) // 메서드 참조
                       .map(Supply::getName) // 메서드 참조 
                       .distinct()
                       .count();
    }
}
```

## 8.4 부수 효과 피하기

- 코드 내 부수 효과를 최소화 하기 위해 노력해야 한다. 
- 자바는 여러 스레드 간 부수 효과가 발생하는 것에 대해 아무 보장을 하지 않는다. 
- forEach() 대신에 collect() 혹은 reduce() 혹은 count()를 쓴다. 


- 문제 코드
```java
interface Supply {

    String getName();

    boolean isUncontaminated();
}

class Inventory {

    List<Supply> supplies = new ArrayList<>(); 

    long countDifferentKinds() {
        List<String> names = new ArrayList<>(); //람다 표현식을 병렬화 하기만 해도 ArrayList가 스레드 안전이 아님

        Consumer<String> addToNames = name -> names.add(name);

        supplies.stream()
                .filter(Supply::isUncontaminated)
                .map(Supply::getName)
                .distinct()
                .forEach(addToNames); // 람다 표현식 밖에 있는 리스트에 원소를 추가, 동시 실행 가능 시에 고장이 난다. 
        return names.size();
    }
}
```

- 개선 코드 
```java

interface Supply {

    String getName();

    boolean isUncontaminated();
}

class Inventory {

    List<Supply> supplies = new ArrayList<>();

    long countDifferentKinds() {  // 해결방법 : 리스트를 직접 만들지 않고 컬렉션 내 스트림에 남아 있는 각 원소를 collect 한다. 
        List<String> names = supplies.stream()
                                     .filter(Supply::isUncontaminated)
                                     .map(Supply::getName)
                                     .distinct()
                                     .collect(Collectors.toList());
        return names.size();
    }
}

class Inventory2 {

    List<Supply> supplies = new ArrayList<>();

    long countDifferentKinds() {
        return supplies.stream()
                       .filter(Supply::isUncontaminated)
                       .map(Supply::getName)
                       .distinct()
                       .count();
    }
}

```

## 8.5 복잡한 스트림 종료 시 컬렉트 사용하기

- Collectors 를 잘 활용 한다 
- toList(), toSet(), toMap() 이 있음 
- Collectors.groupingBy() -> map과 같은 역할을 한다. 

- 문제 코드
```java
interface Supply {

    String getName();

    boolean isUncontaminated();
}

class Inventory {

    List<Supply> supplies = new ArrayList<>();

    Map<String, Long> countDifferentKinds() {
        Map<String, Long> nameToCount = new HashMap<>();

        Consumer<String> addToNames = name -> {
            if (!nameToCount.containsKey(name)) {
                nameToCount.put(name, 0L);
            }
            nameToCount.put(name, nameToCount.get(name) + 1);
        };

        supplies.stream()
                .filter(Supply::isUncontaminated)
                .map(Supply::getName)
                .forEach(addToNames);
        return nameToCount;
    }
}
```

- 개선 코드 
```java
interface Supply {

    String getName();

    boolean isUncontaminated();
}

class Inventory {

    List<Supply> supplies = new ArrayList<>();

    Map<String, Long> countDifferentKinds() {
        return supplies.stream()
                       .filter(Supply::isUncontaminated)
                       .collect(Collectors.groupingBy(Supply::getName, //grouping by 로 map 연산자가 더이상 필요 없음
                               Collectors.counting()) // Map<String, collection<Supply>> 형태로 변환
                       );
    }
}
```

## 8.6 스트림 내 예외 피하기

- 문제 코드

```java
class LogBook {

    LogBook(Path path) throws IOException {
        Files.readAllLines(path);
    }

    static boolean isLogbook(Path path) {
        return false;
    }
}

class LogBooks {

    private static Path STORAGE = Paths.get("/var/log"); // 파일시스템을 다룰 시에는 IOException 일어날 가능성을 염두한다. 

    static List<LogBook> getAll() throws IOException {
        return Files.walk(STORAGE)
                    .filter(Files::isRegularFile)
                    .filter(LogBook::isLogbook)
                    .map(path -> {
                        try {
                            return new LogBook(path);
                        } catch (IOException e) {
                            throw new UncheckedIOException(e);
                        }
                    })
                    .collect(Collectors.toList());
    }
}

```

- 개선 코드 
 
```java
class LogBook {

    LogBook(Path path) throws IOException {
        Files.readAllLines(path);
    }

    static boolean isLogbook(Path path) {
        return false;
    }
}

class LogBooks {

    static Path STORAGE = Paths.get("/var/log");

    static List<LogBook> getAll() throws IOException {
        try (Stream<Path> stream = Files.walk(STORAGE)) {
            return stream.filter(Files::isRegularFile)
                         .filter(LogBook::isLogbook)
                         .flatMap(path -> {
                             try {
                                 return Stream.of(new LogBook(path));
                             } catch (IOException e) {
                                 return Stream.empty();
                             }
                         })
                         .collect(Collectors.toList());
        }
    }
}

class LogBooks2 {

    static Path STORAGE = Paths.get("/var/log");

    static List<LogBook> getAll() throws IOException {
        try (Stream<Path> stream = Files.walk(STORAGE)) {

            return stream.filter(Files::isRegularFile)
                         .filter(LogBook::isLogbook)
                         .map(path -> {
                             try {
                                 return new LogBook(path);
                             } catch (IOException e) {
                                 return null;
                             }
                         })
                         .filter(Objects::nonNull)
                         .collect(Collectors.toList());
        }
    }
}
```

## 8.7 널 대신 옵셔널

- Optional: 있을 수도 있고 없을 수도 있는 객체를 위한 임시 저장소 

- 문제 코드
```java
class Connection {

    void send(String message) {

    }
}

class Communicator {

    Connection connectionToEarth;

    void establishConnection() {
        // used to set connectionToEarth, but may be unreliable
    }

    Connection getConnectionToEarth() {
        return connectionToEarth; // null 값일 수도 있다. 
    }
}

class Usage {
    static void main() {
        Communicator communicator = new Communicator();
        communicator.getConnectionToEarth()
                    .send("Houston, we got a problem!");
    }
}
```

- 개선 코드  
```java
class Connection {

    void send(String message) {

    }
}

class Communicator {

    Connection connectionToEarth;

    void establishConnection() {
        // used to set connectionToEarth, but may be unreliable
    }

    Optional<Connection> getConnectionToEarth() {
        return Optional.ofNullable(connectionToEarth);
    }
}

class Usage {

    static void main() {
        Communicator communicator = new Communicator();

        Connection connection = communicator.getConnectionToEarth()
                                            .orElse(null); // 만약에 null 이면 -> NullPointerException 발생
        connection.send("Houston, we got a problem!");
    }

    static void main2() {
        Communicator communicationSystem = new Communicator();

        communicationSystem.getConnectionToEarth()
                            .ifPresent(connection -> 
                                connection.send("Houston, we got a problem!") // null 이 아닐때만 실행하도록 만든다. 
                            );
    }
}
```

## 8.8 선택 필드나 매개변수 피하기

- 옵셔널은 부재 아니면 존재라는 두가지 상태가 있음
- 매개변수의 변수는 null 이 될 수도 있는데 이떄 Optional 매개변수 null의 의미가 모호해짐. 즉 부재(Optional.empty()), 존재, null 3가지가 존재 하게 됨
- 따라서 optional은 반환 값에만 써야한다. 

- 문제 코드
```java
class Connection {

    void send(String message) {

    }
}

class Communicator {

    Optional<Connection> connectionToEarth;
    
    void setConnectionToEarth(Optional<Connection> connectionToEarth) { // optional 사용의 나쁜 예
        this.connectionToEarth = connectionToEarth;
    }
    Optional<Connection> getConnectionToEarth() {
        return connectionToEarth;
    }
}
```

- 개선 코드 
```java
class Connection {

    void send(String message) {

    }
}

class Communicator {

    Connection connectionToEarth;

    void setConnectionToEarth(Connection connectionToEarth) {
        this.connectionToEarth = Objects.requireNonNull(connectionToEarth);
    }
    Optional<Connection> getConnectionToEarth() { // optional은 반환 값에만 사용할 것 
        return Optional.ofNullable(connectionToEarth);
    }

    void reset() {
        connectionToEarth = null;
    }

```

## 8.9 옵셔널을 스트림으로 사용하기

- 옵셔널 클래스는 함수형 프로그래밍 방식을 위해 만들어졌다. 
- 중간연산인 filter(), map(), flatMap() 과 종료 연산인 orElse(), orElseThrow(),orElseGet(), ifPresent를 활용하면 된다.

- 문제 코드
```java
interface Connection {

    void send(String message);

    boolean isFree();
}

interface Storage {

    String getBackup();
}

class Communicator {

    private Connection connectionToEarth;

    Optional<Connection> getConnectionToEarth() {
        return Optional.ofNullable(connectionToEarth);
    }
}

class BackupJob {

    Communicator communicator;
    Storage storage;

    void backupToEarth() {
        Optional<Connection> connectionOptional =
                communicator.getConnectionToEarth();
        if (!connectionOptional.isPresent()) {
            throw new IllegalStateException();
        }

        Connection connection = connectionOptional.get();
        if (!connection.isFree()) {
            throw new IllegalStateException();
        }

        connection.send(storage.getBackup());
    }
}
```

- 개선 코드 
```java

interface Connection {

    void send(String message);

    boolean isFree();
}

interface Logbook {

    void log(String message);
}

interface Storage {

    String getBackup();
}

class Communicator {

    private Connection connectionToEarth;

    Optional<Connection> getConnectionToEarth() {
        return Optional.ofNullable(connectionToEarth);
    }
}

class BackupJob {

    Communicator communicator;
    Storage storage;

    void backupToEarth() {  // 함수형으로 바꾸면 훨씬 간단해짐
        Connection connection = communicator.getConnectionToEarth()
                .filter(Connection::isFree)
                .orElseThrow(IllegalStateException::new);
        connection.send(storage.getBackup());
    }
}

class Usage {

    static void main() {
        Communicator communicator = null;  // 함수형으로 바꾸면 훨씬 간단해짐
        String state = communicator.getConnectionToEarth()
                                    .map(Connection::isFree)
                                    .map(isFree -> isFree ? "free" : "busy")
                                    .orElse("absent");
    }
}
```

# Chapter4

## 4.1 자바 명명 규 사용하기  

- 상수는 두드러지게 표현하기 위해 CAPITAL_SNAKE_CASE로 작성하기 
- 매서드명은 동사로 명명 , 변수명은 명사로 명명
- CamelCase 아닌 camelCase 로 작성

- 문제코드

```java

class Rover {
    static final double WalkingSpeed = 3;

    final String SerialNumber;
    double MilesPerHour;

    Rover(String NewSerialNumber) {
        SerialNumber = NewSerialNumber;
    }

    void Drive() {
        MilesPerHour = WalkingSpeed;
    }
    void Stop() {
        MilesPerHour = 0;
    }
}

```
- 개선코드

```java

class Rover {
    static final double WALKING_SPEED = 3; //상수는 대문자로 

    final String serialNumber; //camelCase로 작성
    double milesPerHour; 

    Rover(String serialNumber) {
        this.serialNumber = serialNumber;
    }

    void drive() {
        milesPerHour = WALKING_SPEED;
    }

    void stop() {
        milesPerHour = 0;
    }
}

```

## 4.2 프레임워크에는 Getter/Setter 사용하기 

- 필드명이 foo이면 getFoo() 와 setFoo 로 명명

- 문제코드

```java

class Astronaut {

    String name;
    boolean retired;

    Astronaut(String name) {
        this.name = name;
    }

    String getFullName() {
        return name;
    }

    void setFullName(String name) {
        this.name = name;
    }

    boolean getRetired() {
        return retired;
    }

    void setRetiredState(boolean retired) {
        this.retired = retired;
    }
}
```
- 개선코드

```java

class Astronaut {
    private String name; // 필드의 한정자는 private으로 
    private boolean retired;

    public Astronaut() {
    }

    public Astronaut(String name) { //게터와 세터는 public개
        this.name = name;
    }

    public String getName() { 
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public boolean isRetired() {
        return retired;
    }

    public void setRetired(boolean retired) {
        this.retired = retired;
    }

}
```

## 4.3 한 글자로 명명하지 않기 

- 문제코드 

```java

class Inventory {
    List<Supply> sl = new ArrayList<>();

    boolean isInStock(String n) {
        Supply s = new Supply(n);
        int l = 0;   
        int h = sl.size() - 1;

        while (l <= h) {
            int m = l + (h - l) / 2;   //이처럼 한글자는 알아보기가 힘들다.
            int c = sl.get(m).compareTo(s);

            if (c < 0) {
                l = m + 1;
            } else if (c > 0) {
                h = m - 1;
            } else {
                return true;
            }
        }

        return false;
    }
}

class Supply {
    Supply(String name) {
        this.name = name;
    }

    String getName() {
        return name;
    }

    String name;

    int compareTo(Supply supply) {
        return 0;
    }
}

```
- 개선코드

```java

class Inventory {
    List<Supply> sortedList = new ArrayList<>();

    boolean isInStock(String name) {
        Supply supply = new Supply(name);
        int low = 0;  //l 대신 Low
        int high = sortedList.size() - 1; // h 대신 high

        while (low <= high) {
            int middle = low + (high - low) / 2; // m 대신 middle
            int comparison = sortedList.get(middle).compareTo(supply); // c 대신 comparison

            if (comparison < 0) {
                low = middle + 1;
            } else if (comparison > 0) {
                high = middle - 1;
            } else {
                return true;
            }
        }

        return false;
    }
}

class Supply {
    Supply(String name) {
        this.name = name;
    }

    String getName() {
        return name;
    }

    String name;

    int compareTo(Supply supply) {

        return 0;
    }
}

```
## 4.4 축약 쓰지 않기 

- DIR,CSV,GLOB,csvLn 등 축약어는 이해하기가 어렵다. 
- 목적과 의도를 보여주도록 변수명을 만든다.
- DIR 은 LOG_FOLDER
- CSV 는 STATISTICS_CSV
- GLOB 는 FILE_FILTER
- csvLn 은 csvLine

- 문제코드 

```java

class Logbook {
    static final Path DIR = Paths.get("/var/log"); //축약어
    static final Path CSV = DIR.resolve("stats.csv"); //축약어
    static final String GLOB = "*.log"; //축약어

    void createStats() throws IOException {
        try (DirectoryStream<Path> dirStr =
                     Files.newDirectoryStream(DIR, GLOB);
             BufferedWriter bufW = Files.newBufferedWriter(CSV)) {
            for (Path lFile : dirStr) {
                String csvLn = String.format("%s,%d,%s",
                        lFile,
                        Files.size(lFile),
                        Files.getLastModifiedTime(lFile));
                bufW.write(csvLn);
                bufW.newLine();
            }
        }
    }
}
```

- 개선코드 

```java

class Logbook {
    static final Path LOG_FOLDER = Paths.get("/var/log"); //
    static final Path STATISTICS_CSV = LOG_FOLDER.resolve("stats.csv");
    static final String FILE_FILTER = "*.log";

    void createStatistics() throws IOException {
        try (DirectoryStream<Path> logs =
                     Files.newDirectoryStream(LOG_FOLDER, FILE_FILTER);
             BufferedWriter writer =
                     Files.newBufferedWriter(STATISTICS_CSV)) {
            for (Path log : logs) {
                String csvLine = String.format("%s,%d,%s",
                        log,
                        Files.size(log),
                        Files.getLastModifiedTime(log));
                writer.write(csvLine);
                writer.newLine();
            }
        }
    }
}

```
## 4.5 무의미한 용어 쓰지 않기 

- abstract, invoke, main, manager, data, inf, flag와 같은 무의미한 용어는 쓰지 않는다. 

- 문제코드
```java

class MainSpaceShipManager {
    AbstractRocketPropulsionEngine abstractRocketPropulsionEngine; //과한 정보제공, abstract는 무의미
    INavigationController navigationController; //과한 정보제공
    boolean turboEnabledFlag;

    void navigateSpaceShipTo(PlanetInfo planetInfo) {
        RouteData data = navigationController.calculateRouteData(planetInfo);
        LogHelper.logRouteData(data);
        abstractRocketPropulsionEngine.invokeTask(data, turboEnabledFlag);
    }
}
 class LogHelper {
     static void logRouteData(RouteData data) {
    }
}

interface RouteData {
}

interface PlanetInfo {
}

interface INavigationController {
    void invokeNavigationTask(RouteData someData);

    RouteData calculateRouteData(PlanetInfo someData);
}

class AbstractRocketPropulsionEngine {
    void invokeTask(RouteData someData, boolean b) {
    }

    ;
}

```
- 개선코드

```java

class SpaceShip {
    Engine engine;
    Navigator navigator;
    boolean turboEnabled;

    void navigateTo(Planet destination) {
        Route route = navigator.calculateRouteTo(destination);
        Logger.log(route);
        engine.follow(route, turboEnabled);
    }
}

class Logger {
    static void log(Route r) {
    }
}

interface Route {
}

interface Planet {
}

interface Navigator {
    void invokeNavigationTask(Route someData);

    Route calculateRouteTo(Planet someData);
}

class Engine {
    void follow(Route someData, boolean b) {
    }

    ;
}

```

## 4.6 도메인 용어 사용하기 

- 도메인마다 사용하는 어휘로 명명하기. 

- 문제코드
```java

class Problem {
}


class Person {
    String lastName;
    String role;
    int travels;
    LocalDate employedSince;

    String serializeAsLine() {
        return String.join(",",
                Arrays.asList(lastName,
                        role,
                        String.valueOf(travels),
                        String.valueOf(employedSince))
        );
    }
}
```

- 개선코드 (화성탐사 도메인이라고 가정 )
```java

class Astronaut {  //Person 보다 Astronaut이 어떤 데이터를 저장하고자 하는지 확실히 알 수 있게 해준다. 
    String tagName;
    String rank;
    int missions;
    LocalDate activeDutySince;

    String toCSV() {
        return String.join(",",
                Arrays.asList(tagName,
                        rank,
                        String.valueOf(missions),
                        String.valueOf(activeDutySince))
        );
    }
}
```

# Chapter5

## 5.1 빠른 실패 

- 검증하는 것을 먼저 앞에 오도록 만든다. 

- 문제 코드
```java
class CruiseControl {
    static final double SPEED_OF_LIGHT_KMH = 1079252850;
    static final double SPEED_LIMIT = SPEED_OF_LIGHT_KMH;

    private double targetSpeedKmh;

    void setTargetSpeedKmh(double speedKmh) {
        if (speedKmh < 0) {
            throw new IllegalArgumentException();
        } else if (speedKmh <= SPEED_LIMIT) {
            targetSpeedKmh = speedKmh;
        } else {
            throw new IllegalArgumentException();
        }
    }
}

```

- 개선 코드 
```java
class CruiseControl {
    static final double SPEED_OF_LIGHT_KMH = 1079252850;
    static final double SPEED_LIMIT = SPEED_OF_LIGHT_KMH;

    private double targetSpeedKmh;

    void setTargetSpeedKmh(double speedKmh) {
        if (speedKmh < 0 || speedKmh > SPEED_LIMIT) {
            throw new IllegalArgumentException();
        }

        targetSpeedKmh = speedKmh;
    }
}

```

## 5.2 항상 가장 구체적인 예외 잡기 

- 그냥 exception 을 하면 일반적인 예외 유형이기 때문에 NullPointer Exception과 같은 잡고싶지 않은 예외 유형까지 잡히게 된다. 
- 그래서 NumberFormatException이라고 하는 정수값으로 변환할 수 없는 값이 들어있을 때 Integer.parseInt(rawId)를 던지도록 한다. 
- 문제 코드

```java
class TransmissionParser {
    static Transmission parse(String rawMessage) {
        if (rawMessage != null
                && rawMessage.length() != Transmission.MESSAGE_LENGTH) {
            throw new IllegalArgumentException("Bad message received!");
        }

        String rawId = rawMessage.substring(0, Transmission.ID_LENGTH);
        String rawContent = rawMessage.substring(Transmission.ID_LENGTH);
        try {
            int id = Integer.parseInt(rawId);
            String content = rawContent.trim();
            return new Transmission(id, content);
        } catch (Exception e) {
            throw new IllegalArgumentException("Bad message received!");
        }
    }
}

class Transmission {

    static final int MESSAGE_LENGTH = 146;
    static final int ID_LENGTH = 6;

    final int id;
    final String content;

    Transmission(int id, String content) {
        this.id = id;
        this.content = content;
    }
}

```
- 개선 코드 

```java
class TransmissionParser {
    static Transmission parse(String rawMessage) {
        if (rawMessage != null &&
                rawMessage.length() != Transmission.MESSAGE_LENGTH) {
            throw new IllegalArgumentException("Bad message received!");
        }

        String rawId = rawMessage.substring(0, Transmission.ID_LENGTH);
        String rawContent = rawMessage.substring(Transmission.ID_LENGTH);
        try {
            int id = Integer.parseInt(rawId);
            String content = rawContent.trim();
            return new Transmission(id, content);
        } catch (NumberFormatException e) { // 예외처리를 좀더 구체적으로 한다. 
            throw new IllegalArgumentException("Bad message received!");
        }
    }
}

class Transmission {

    static final int MESSAGE_LENGTH = 146;
    static final int ID_LENGTH = 6;

    final int id;
    final String content;

    Transmission(int id, String content) {
        this.id = id;
        this.content = content;
    }
}

```

## 5.3 메시지로 원인 설명 

- 매시지로 원인을 구체적으로 설명하기 
- 바라는 것, 받는 것, 전체 맥락 등 골고루 제공해야 근본적인 원인을 더 빨리 추적

- 문제 코드

```java
class TransmissionParser {
    static Transmission parse(String rawMessage) {
        if (rawMessage != null
                && rawMessage.length() != Transmission.MESSAGE_LENGTH) {
            throw new IllegalArgumentException();
        }

        String rawId = rawMessage.substring(0, Transmission.ID_LENGTH);
        String rawContent = rawMessage.substring(Transmission.ID_LENGTH);
        try {
            int id = Integer.parseInt(rawId);
            String content = rawContent.trim();
            return new Transmission(id, content);
        } catch (NumberFormatException e) {
            throw new IllegalArgumentException("Bad message received!");
        }
    }
}

class Transmission {

    static final int MESSAGE_LENGTH = 146;
    static final int ID_LENGTH = 6;

    final int id;
    final String content;

    Transmission(int id, String content) {
        this.id = id;
        this.content = content;
    }
}
```
- 개선 코드 

```java
class TransmissionParser {
    static Transmission parse(String rawMessage) {
        if (rawMessage != null
                && rawMessage.length() != Transmission.MESSAGE_LENGTH) {
            throw new IllegalArgumentException(
                String.format("Expected %d, but got %d characters in '%s'",
                    Transmission.MESSAGE_LENGTH, rawMessage.length(),
                    rawMessage)); // 더 구체적으로
        }

        String rawId = rawMessage.substring(0, Transmission.ID_LENGTH);
        String rawContent = rawMessage.substring(Transmission.ID_LENGTH);
        try {
            int id = Integer.parseInt(rawId);
            String content = rawContent.trim();
            return new Transmission(id, content);
        } catch (NumberFormatException e) {
            throw new IllegalArgumentException(
                String.format("Expected number, but got '%s' in '%s'",
                        rawId, rawMessage)); // 더 구체적으로
        }
    }
}

class Transmission {

    static final int MESSAGE_LENGTH = 146;
    static final int ID_LENGTH = 6;

    final int id;
    final String content;

    Transmission(int id, String content) {
        this.id = id;
        this.content = content;
    }
}

```

## 5.4 원인 사슬 깨지 않기 

- 예외를 일으킨 예외와 연결된 리스트를 보여주는 스택 추적을 확인할 수 있게 한다. 
- Throwable을 전달함으로써 예외와 원인을 연관시키고 그로써 원인 사슬이 만들어지도록 한다.

- 문제 코드 

```java

class TransmissionParser {
    static Transmission parse(String rawMessage) {
        if (rawMessage != null
                && rawMessage.length() != Transmission.MESSAGE_LENGTH) {
            throw new IllegalArgumentException(
                String.format("Expected %d, but got %d characters in '%s'",
                        Transmission.MESSAGE_LENGTH, rawMessage.length(),
                        rawMessage));
        }

        String rawId = rawMessage.substring(0, Transmission.ID_LENGTH);
        String rawContent = rawMessage.substring(Transmission.ID_LENGTH);
        try {
            int id = Integer.parseInt(rawId);
            String content = rawContent.trim();
            return new Transmission(id, content);
        } catch (NumberFormatException e) {
            throw new IllegalArgumentException(
                String.format("Expected number, but got '%s' in '%s'",
                        rawId, rawMessage));  //메세지는 보여져도 원인사슬이 꺠진다는 문제점이 있다.
        }
    }
}

class Transmission {

    static final int MESSAGE_LENGTH = 146;
    static final int ID_LENGTH = 6;

    final int id;
    final String content;

    Transmission(int id, String content) {
        this.id = id;
        this.content = content;
    }
}

```
- 개선 코드 

```java

class TransmissionParser {
    static Transmission parse(String rawMessage) {
        if (rawMessage != null
                && rawMessage.length() != Transmission.MESSAGE_LENGTH) {
            throw new IllegalArgumentException(
                String.format("Expected %d, but got %d characters in '%s'",
                        Transmission.MESSAGE_LENGTH, rawMessage.length(),
                        rawMessage));
        }

        String rawId = rawMessage.substring(0, Transmission.ID_LENGTH);
        String rawContent = rawMessage.substring(Transmission.ID_LENGTH);
        try {
            int id = Integer.parseInt(rawId);
            String content = rawContent.trim();
            return new Transmission(id, content);
        } catch (NumberFormatException e) {
            throw new IllegalArgumentException(
                String.format("Expected number, but got '%s' in '%s'",
                    rawId, rawMessage), e);
        }
    }
}

class Main {
    static void main(String[] args) {
        try {
        } catch (NumberFormatException e) {
            // BAD! Cause chain interrupted!
            throw new IllegalArgumentException(e.getCause()); // 최악의 경우 , 원인만 연결되고 예외자체가 ㅇㅕㄴ결 안됨
        }
    }
}

class GoodExample {
    static void main(String[] args) {
        try { 
        } catch (NumberFormatException e) {
            throw new IllegalArgumentException("Message", e); // catch 블록에서 예외를 던질 때는 message와 잡았던 예외를 즉시 원인으로 전달
        }
        
    }
}

class Transmission {

    static final int MESSAGE_LENGTH = 146;
    static final int ID_LENGTH = 6;

    final int id;
    final String content;

    Transmission(int id, String content) {
        this.id = id;
        this.content = content;
    }
}

```

## 5.5 변수로 원인 노출

- "in %s" 라는 방식의 코드 중복이 발생한다 -> 규모가 커지면 일관성을 잃기가 쉽다. 
- 소프트웨어의 최종 사용자에게 어떤 종류의 메시지가 오류를 일으켰는지 알리고 싶을 때 추출하기 어렵다. 

- 문제 코드

```java
class TransmissionParser {
    static Transmission parse(String rawMessage) {
        if (rawMessage != null
                && rawMessage.length() != Transmission.MESSAGE_LENGTH) {
            throw new IllegalArgumentException(
                String.format("Expected %d, but got %d characters in '%s'",
                    Transmission.MESSAGE_LENGTH, rawMessage.length(),
                    rawMessage));
        }

        String rawId = rawMessage.substring(0, Transmission.ID_LENGTH);
        String rawContent = rawMessage.substring(Transmission.ID_LENGTH);
        try {
            int id = Integer.parseInt(rawId);
            String content = rawContent.trim();
            return new Transmission(id, content);
        } catch (NumberFormatException e) {
            throw new IllegalArgumentException(
                String.format("Expected number, but got '%s' in '%s'",
                    rawId, rawMessage), e); 
        }
    }
}
class Transmission {

    static final int MESSAGE_LENGTH = 146;
    static final int ID_LENGTH = 6;

    final int id;
    final String content;

    Transmission(int id, String content) {
        this.id = id;
        this.content = content;
    }
}

```

- 개선 코드

```java
class TransmissionParser {
    static Transmission parse(String rawMessage) {
        if (rawMessage != null
                && rawMessage.length() != Transmission.MESSAGE_LENGTH) {
            throw new MalformedMessageException(
                String.format("Expected %d, but got %d characters",
                    Transmission.MESSAGE_LENGTH, rawMessage.length()),
                    rawMessage);
        }

        String rawId = rawMessage.substring(0, Transmission.ID_LENGTH);
        String rawContent = rawMessage.substring(Transmission.ID_LENGTH);
        try {
            int id = Integer.parseInt(rawId);
            String content = rawContent.trim();
            return new Transmission(id, content);
        } catch (NumberFormatException e) {
            throw new MalformedMessageException(
                String.format("Expected number, but got '%s'", rawId),
                rawMessage, e);
        }
    }
}
final class MalformedMessageException extends IllegalArgumentException {
    final String raw;

    MalformedMessageException(String message, String raw) { // 이렇게 하면 raw 필드 쉽게 추출 
        super(String.format("%s in '%s'", message, raw));
        this.raw = raw;
    }
    MalformedMessageException(String message, String raw, Throwable cause) {
        super(String.format("%s in '%s'", message, raw), cause);
        this.raw = raw;
    }
}

class Transmission {

    static final int MESSAGE_LENGTH = 146;
    static final int ID_LENGTH = 6;

    final int id;
    final String content;

    Transmission(int id, String content) {
        this.id = id;
        this.content = content;
    }
}

```

## 5.6 타입변환 전에 항상 타입 검증하기 

- 문제 코드

```java
class Network {

    ObjectInputStream inputStream;
    InterCom interCom;

    void listen() throws IOException, ClassNotFoundException {
        while (true) {
            Object signal = inputStream.readObject();
            CrewMessage crewMessage = (CrewMessage) signal;
            interCom.broadcast(crewMessage);
        }
    }
}
```

- 개선 코드 

```java
class Network {

    ObjectInputStream inputStream;
    InterCom interCom;

    void listen() throws IOException, ClassNotFoundException {
        while (true) {
            Object signal = inputStream.readObject();
            if (signal instanceof CrewMessage) {  // instance of로 CrewMessage로 변환할수 있는지 없는지 확인
                CrewMessage crewMessage = (CrewMessage) signal;
                interCom.broadcast(crewMessage);
            }
        }
    }
}

```

## 5.7 항상 자원 닫기 

- 자원을 해제 하기 전에 자원을 사용하다가 예외가 발생할 경우 프로그램이 종료될 때까지 해제되지 못한다.
- try-with-resources로 자원을 안전하고 고상하게 닫을 수 있다. 

- 문제 코드

```java
class Logbook {

    static final Path LOG_FOLDER = Paths.get("/var/log");
    static final String FILE_FILTER = "*.log";

    List<Path> getLogs() throws IOException {
        List<Path> result = new ArrayList<>();

        DirectoryStream<Path> directoryStream =
                Files.newDirectoryStream(LOG_FOLDER, FILE_FILTER);
        for (Path logFile : directoryStream) {
            result.add(logFile);
        }
        directoryStream.close();

        return result;
    }
}

```


- 개선 코드

```java
import java.io.IOException;
import java.nio.file.DirectoryStream;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.util.ArrayList;
import java.util.List;


class Logbook {

    static final Path LOG_FOLDER = Paths.get("/var/log");
    static final String FILE_FILTER = "*.log";

    List<Path> getLogs() throws IOException {
        List<Path> result = new ArrayList<>();

        try (DirectoryStream<Path> directoryStream =   //try with resources 구문으로 자원을 안전하고 고상하게 닫는다.       
                     Files.newDirectoryStream(LOG_FOLDER, FILE_FILTER)) {
            for (Path logFile : directoryStream) {
                result.add(logFile);
            }
        }

        return result;
    }
}


class Main {

    private static final Path LOG_FOLDER = null;
    private static final String FILE_FILTER = "";

    static void main(String... args) throws IOException {
        DirectoryStream<Path> resource =
                Files.newDirectoryStream(LOG_FOLDER, FILE_FILTER);
        try {
            // usage of resource 
        } finally {
            if (resource != null) {
                resource.close(); //null이 아닐때만 닫아서 nullpointexception 피함
            }
        }
    }
}

```
## 5.8 항상 다수 자원 닫기 

- 문제 코드 
```java
class Logbook {

    static final Path LOG_FOLDER = Paths.get("/var/log");
    static final Path STATISTICS_CSV = LOG_FOLDER.resolve("stats.csv");
    static final String FILE_FILTER = "*.log";

    void createStatistics() throws IOException {
        DirectoryStream<Path> directoryStream =
                Files.newDirectoryStream(LOG_FOLDER, FILE_FILTER);
        BufferedWriter writer =
                Files.newBufferedWriter(STATISTICS_CSV); //writer 생성에 실패하면 예외와 함께 종료되어 directoryStream 이 닫히지 않는다.
                                
        try {
            for (Path logFile : directoryStream) {
                final String csvLine = String.format("%s,%d,%s",
                        logFile,
                        Files.size(logFile),
                        Files.getLastModifiedTime(logFile));
                writer.write(csvLine);
                writer.newLine();
            }
        } finally {
            directoryStream.close(); //directoryStream 닫기에서 예외가 발생하면 writer가 닫히지 않는다.
            writer.close();
        }
    }
}

```

- 개선 코드 
```java

class Logbook {

    static final Path LOG_FOLDER = Paths.get("/var/log");
    static final Path STATISTICS_CSV = LOG_FOLDER.resolve("stats.csv");
    static final String FILE_FILTER = "*.log";

    void createStatistics() throws IOException {
        try (DirectoryStream<Path> directoryStream =
                     Files.newDirectoryStream(LOG_FOLDER, FILE_FILTER);
             BufferedWriter writer =
                     Files.newBufferedWriter(STATISTICS_CSV)) { // try-with-resources 블록은 자원 하나만 닫을 수 있는게 아니라 동시에 여러자원을 처리 가능
            for (Path logFile : directoryStream) {
                String csvLine = String.format("%s,%d,%s",
                        logFile,
                        Files.size(logFile),
                        Files.getLastModifiedTime(logFile));
                writer.write(csvLine);
                writer.newLine();
            }
        }
    }
}

```
## 5.9 빈 catch 블록 설명하기 

- 예외를 넘기고 아무 것도 안하고 예외 처리를 안하는 경우에는 왜 안하는지 설명을 해주어야한다.

- 문제 코드 

```java
class Logbook {

    static final Path LOG_FOLDER = Paths.get("/var/log");
    static final String FILE_FILTER = "*.log";

    List<Path> getLogs() throws IOException {
        List<Path> result = new ArrayList<>();

        try (DirectoryStream<Path> directoryStream =
                     Files.newDirectoryStream(LOG_FOLDER, FILE_FILTER)) {
            for (Path logFile : directoryStream) {
                result.add(logFile);
            }
        } catch (NotDirectoryException e) {

        }

        return result;
    }
}
```

- 실제 코드 

```java
class Logbook {

    static final Path LOG_FOLDER = Paths.get("/var/log");
    static final String FILE_FILTER = "*.log";

    List<Path> getLogs() throws IOException {
        List<Path> result = new ArrayList<>();

        try (DirectoryStream<Path> directoryStream =
                     Files.newDirectoryStream(LOG_FOLDER, FILE_FILTER)) {
            for (Path logFile : directoryStream) {
                result.add(logFile);
            }
        } catch (NotDirectoryException ignored) {
            // No directory -> no logs!
        }

        return result;
    }
}
```




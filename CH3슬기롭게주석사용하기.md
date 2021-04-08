# Chapter3

## 3.1 지나치게 많은 주석 없애기 

- 한줄만 읽으면 바로 알 수 없는 주석은 없앤다. 
- 클래스 구조를 강조하는 주석도 제거 
- 코드를 해석하는 주석도 제거 
- TODO 주석을 수정하는 대신 null 과 isEmpty() 검증 추가
- 보아서는 드러나지 안흐는 정보만 주석으로 추가 

- 문제 코드 
```java
class Inventory {
    // Fields (we only have one)
    List<Supply> supplies = new ArrayList<>(); // The list of supplies.

    // Methods
    int countContaminatedSupplies() {
        // TODO: check if field is already initialized (not null)

        int contaminatedCounter = 0; // the counter
        // No supplies => no contamination
        for (Supply supply : supplies) { // begin FOR
            if (supply.isContaminated()) {
                contaminatedCounter++; // increment counter!
            } // End IF supply is contaminated
        }// End FOR

        // Returns the number of contaminated supplies.
        return contaminatedCounter; // Handle with care!
    }
} // End of Inventory class

```
- 개선 코드
```java
class Inventory {

    List<Supply> supplies = new ArrayList<>();

    int countContaminatedSupplies() {
        if (supplies == null || supplies.isEmpty()) {
            // No supplies => no contamination
            return 0;
        }

        int contaminatedCounter = 0;
        for (Supply supply : supplies) {
            if (supply.isContaminated()) {
                contaminatedCounter++;
            }
        }

        return contaminatedCounter;
    }
}
```

## 3.2 주석 처리된 코드 제거 

- 특정 기능이 동작하지 못하게 하려고 한 주석 처리를 없앤다. 

- 문제 코드 
```java
class LaunchChecklist {

    List<String> checks = Arrays.asList(
            "Cabin Leak",
            // "Communication", // Do we actually want to talk to Houston?
            "Engine",
            "Hull",
            // "Rover", // We won't need it, I think...
            "OxygenTank"
            //"Supplies"
    );

    Status prepareLaunch(Commander commander) {
        for (String check : checks) {
            boolean shouldAbortTakeoff = commander.isFailing(check);
            if (shouldAbortTakeoff) {
                //System.out.println("REASON FOR ABORT: " + item);
                return Status.ABORT_TAKE_OFF;
            }
        }
        return Status.READY_FOR_TAKE_OFF;
    }
```
- 개선 코드 
```java
class LaunchChecklist {

    List<String> checks = Arrays.asList(
            "Cabin Leak",
            // "Communication", // Do we actually want to talk to Houston?
            "Engine",
            "Hull",
            // "Rover", // We won't need it, I think...
            "OxygenTank"
            //"Supplies"
    );

    Status prepareLaunch(Commander commander) {
        for (String check : checks) {
            boolean shouldAbortTakeoff = commander.isFailing(check);
            if (shouldAbortTakeoff) {
                //System.out.println("REASON FOR ABORT: " + item);
                return Status.ABORT_TAKE_OFF;
            }
        }
        return Status.READY_FOR_TAKE_OFF;
    }
}
```
## 3.3 주석을 상수로 대체 

- 상수명의 장점은 이름으로 의미를 드러낸다는 점

- 문제 코드 
```java
enum SmallDistanceUnit {

    CENTIMETER,
    INCH;

    double getConversionRate(SmallDistanceUnit unit) {
        if (this == unit) {
            return 1; // identity conversion rate
        }

        if (this == CENTIMETER && unit == INCH) {
            return 0.393701; // one centimeter in inch
        } else {
            return 2.54; // one inch in centimeters
        }
    }
}
```
- 개선 코드 
```java
enum SmallDistanceUnit {

    CENTIMETER,
    INCH;

    static final double INCH_IN_CENTIMETERS = 2.54;
    static final double CENTIMETER_IN_INCHES = 1 / INCH_IN_CENTIMETERS;
    static final int IDENTITY = 1;


    double getConversionRate(SmallDistanceUnit unit) {
        if (this == unit) {
            return IDENTITY;
        }

        if (this == CENTIMETER && unit == INCH) {
            return CENTIMETER_IN_INCHES;
        } else {
            return INCH_IN_CENTIMETERS;
        }
    }
}
```
## 3.4 주석을 유틸리티 메서드로 대체 

<장점>
- 이름만으로 코드가 무엇을 하는지 설명할 수 있다.
- 매서드 내에 줄을 추가하지 않아도 됨. 
- 다른 메서드에서 새 메서드를 재사용할 수 있다. 
- 메서드에 계층 구조가 생긴다. -> 상위 계층의 이해도가 개선됨

- 문제 코드
```java
class FuelSystem {

    List<Double> tanks = new ArrayList<>();

    int getAverageTankFillingPercent() {
        double sum = 0;
        for (double tankFilling : tanks) {
            sum += tankFilling;
        }
        double averageFuel = sum / tanks.size();
        // round to integer percent
        return Math.toIntExact(Math.round(averageFuel * 100));
    }
}

class FuelSystemAlternative {

    List<Double> tanks;

    int getAverageTankFillingPercent() {
        double sum = 0;
        for (double tankFilling : tanks) {
            sum += tankFilling;
        }
        double averageFuel = sum / tanks.size();
        int roundedToPercent = Math.toIntExact(Math.round(averageFuel * 100));
        return roundedToPercent;
    }
}

```
- 개선 코드
```java
class FuelSystem {

    List<Double> tanks = new ArrayList<>();

    int getAverageTankFillingPercent() {
        double sum = 0;
        for (double tankFilling : tanks) {
            sum += tankFilling;
        }
        double averageFuel = sum / tanks.size();
        return roundToIntegerPercent(averageFuel); // 유틸리티 메서드로 대체해준다. 
    }

    static int roundToIntegerPercent(double value) {
        return Math.toIntExact(Math.round(value * 100));
    }
}
```

## 3.5 구현 결정 설명하기 

- 그저 빠른 구현이라고만 하지 말고, 사용사례, 우려사항, 해법, 그리고 지불해야할 트레이드 오프나 비용 명시
- 문제 코드 

```java
class Inventory {
    
    private List<Supply> list = new ArrayList<>();

    void add(Supply supply) {
        list.add(supply);
        Collections.sort(list);
    }

    boolean isInStock(String name) {
        // fast implementation(빠른 구현)
        return Collections.binarySearch(list, new Supply(name)) != -1;
    }
}
```
- 개선 코드 
```java
class Inventory {
    // Keep this list sorted. See isInStock().
    private List<Supply> list = new ArrayList<>();

    void add(Supply supply) {
        list.add(supply);
        Collections.sort(list);
    }

    boolean isInStock(String name) {
        /*
         * In the context of checking availability of supplies by name,
         * facing severe performance issues with >1000 supplies
         * we decided to use the binary search algorithm
         * to achieve item retrieval within 1 second,
         * accepting that we must keep the supplies sorted.
         */
        return Collections.binarySearch(list, new Supply(name)) != -1;
    }
}
```
## 3.6 예제로 설명하기 

- 한줄로 요악
- 형식, 유효한 예, 유효하지 않은 예제를 제공해준다. 

- 문제 코드
```java
class Supply {

    /**
     * The code universally identifies a supply.
     *
     * It follows a strict format, beginning with an S (for supply), followed //s로 시작해 숫자 다섯자리 재고 번호가 나오고 
     * by a five digit inventory number. Next comes a backslash that // 뒤이어 앞의 재고 번호와 구분하기 위한 역 슬래시가 나오고
     * separates the country code from the preceding inventory number. This
     * country code must be exactly two capital letters standing for one of
     * the participating nations (US, EU, RU, CN). After that follows a dot
     * and the actual name of the supply in lowercase letters. // 이어서 마침표와 실제 재고명이 소문자로 나온다. 
     */
    static final Pattern CODE =
            Pattern.compile("^S\\d{5}\\\\(US|EU|RU|CN)\\.[a-z]+$");
}
```
- 개선 코드
```java
class Supply {

    /**
     * The expression universally identifies a supply code.      // 요약
     *
     * Format: "S<inventory-number>\<COUNTRY-CODE>.<name>"       // 형식
     *
     * Valid examples: "S12345\US.pasta", "S08342\CN.wrench",    // 유효한 예 
     * "S88888\EU.laptop", "S12233\RU.brush"
     *
     * Invalid examples:    // 유효하지 않은 예
     * "R12345\RU.fuel"      (Resource, not supply)
     * "S1234\US.light"      (Need five digits)
     * "S01234\AI.coconut"   (Wrong country code. Use US, EU, RU, or CN)
     * " S88888\EU.laptop "  (Trailing whitespaces)
    */
    static final Pattern SUPPLY_CODE =
            Pattern.compile("^S\\d{5}\\\\(US|EU|RU|CN)\\.[a-z]+$");
}
```

## 3.7 패키지를 JavaDoc으로 구조화 하기 

- JavaDoc은 자바 API가 제공하는 문서화 기능 
- API를 사용 중이고, API를 사용하는데 다른 요소가 필요하다면 반드시 사용해야한다. 

- 문제 코드 
```java
/**
 * This package called logistics contains classes for logistics.
 * The inventory class in this package can stock up from the cargo ship and
 * dispose of any contaminated supplies.
 * Classes of this package: // 클래스 목록 (별로 안중요)
 * - Inventory
 * - Supply
 * - Hull
 * - CargoShip
 * - SupplyCrate
 *
 * @author A. Lien, H. Uman 
 * @version 1.8 // 버전 정보도 그닥 필요없음
 * @since 1.7
 */
```

- 개선 코드 
```java
/**
 * Classes for managing an inventory of supplies. // 소개문: 간단하게 
 *
 * <p>
 * The core class is the {@link logistics.Inventory} that lets you // 패키지내 주요 클래스로 무엇을 할 수 있는지 설명
 * <ul>
 * <li> stock it up from a {@link logistics.CargoShip}, // 링크 표기로 클래스를 클릭해 바로 이동
 * <li> dispose of any contaminated {@link logistics.Supply},
 * <li> and search for any {@link logistics.Supply} by name.
 * </ul>
 *
 * <p>
 * The classes let you unload supplies and immediately dispose of any supply
 * that was contaminated.
 * <pre> // 사용 사례를 어떻게 구현하는지 보여주는 구체적인 예제를 제공해준다. 
 * Inventory inventory = new Inventory();
 * inventory.stockUp(cargoShip.unload());
 * inventory.disposeContaminatedSupplies();
 * inventory.getContaminatedSupplies().isEmpty(); // true
 * </pre>
 */
package logistics;
```
## 3.8 클래스와 인터페이스를 JavaDoc으로 구조화하기 

- 문제 코드
```java
/**
 * This class represents a cargo ship.
 * It can unload a {@link Stack} of supplies, load a {@link Queue} of
 * supplies, and it can show the remainingCapacity as a long.
 */
interface CargoShip {
    Stack<Supply> unload();
    Queue<Supply> load(Queue<Supply> supplies);
    int getRemainingCapacity();
}
```
- 개선 코드 
```java
/**
 * A cargo ship can load and unload supplies according to its capacity. // 인터페이스명만 반복되는 게 아니라 유용한 조언을 해줌
 *
 * <p>
 * Supplies are loaded sequentially and can be unloaded in LIFO // 후입선출법 
 * (last-in-first-out) order. The cargo ship can only store supplies up to
 * its capacity. Its capacity is never negative.
 */
interface CargoShip {
    Stack<Supply> unload();
    Queue<Supply> load(Queue<Supply> supplies);
    int getRemainingCapacity();
}
```

## 3.9 메서드를 JavaDoc으로 구조화하기 


- 문제 코드 
```java
interface CargoShip {
    
    Stack<Supply> unload();

    /**
     * Loads {@link Supply}.
     *
     * @param supplies the supplies of type {@link Queue}
     * @return not loaded supplies of type {@link Queue}
     */
    Queue<Supply> load(Queue<Supply> supplies);

    int getRemainingCapacity();
}
```
- 개선 코드 
```java
interface CargoShip {
    
    Stack<Supply> unload();

    /**
     * Loads supplies onto the cargo ship.
     *
     * <p>
     * Only lets you load as many supplies as there is remaining capacity.
     *
     * Example:
     * <pre> 
     * int capacity = cargoShip.getRemainingCapacity(); // 1
     * Queue&lt;Supply> supplies = Arrays.asList(new Supply("Apple"));
     * Queue&lt;Supply> spareSupplies = cargoShip.load(supplies);
     * spareSupplies.isEmpty(); // true;
     * cargoShip.getRemainingCapacity() == 0; // true
     * </pre>
     * // 입력과 내부 상태가 특정 출력과 상태 변경을 어떻게 보장하는지 명시
     * @param supplies to be loaded; must not be null 
     * @return supplies that could not be loaded because of too little
     *          capacity; is empty if everything has been loaded
     * @throws NullPointerException if supplies is null
     * @see CargoShip#getRemainingCapacity() check capacity
     * @see CargoShip#unload() unload the supplies
     */
    Queue<Supply> load(Queue<Supply> supplies);

    int getRemainingCapacity();
}
```

## 3.10 생성자를 JavaDoc으로 구조화하기 

- 문제 코드
```java
class Inventory {

    List<Supply> supplies;

    /**
     * Constructor for a new Inventory.
     */
    Inventory() {
        this(new ArrayList<>());
    }

    /**
     * Another Constructor for a new Inventory.
     *
     * It is possible to add some supplies to the Inventory.
     */
    Inventory(Collection<Supply> initialSupplies) {
        this.supplies = new ArrayList<>(initialSupplies);
    }
}
```

- 개선 코드 
```java
class Inventory {

    List<Supply> supplies;

    /**
     * Creates an empty inventory.
     *
     * @see Inventory#Inventory(Collection) instantiate with initial supplies //초기 제품을 초기화 하는 함수 
     */ 
    Inventory() {
        this(new ArrayList<>());
    }

    /**
     * Creates an inventory with an initial shipment of supplies.
     *
     * @param initialSupplies Initial supplies. 
     *                        Must not be null, can be empty. //생성자 종료후에 객체 상태 정보를 알려줌 (사후 정보) , 빈 상태가 되거나 초기제품으로 채워짐
     * @throws NullPointerException if initialSupplies is null  //null 입력시에 NullPointerException 반환 
     * @see Inventory#Inventory() instantiate with no supplies //제품없이 초기화 하는 함수 //두 생성자의 관계를 설명한다. 
     */
    Inventory(Collection<Supply> initialSupplies) {
        this.supplies = new ArrayList<>(initialSupplies);
    }
}
```

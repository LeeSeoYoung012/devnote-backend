# Chapter1
## 1.1 쓸모없는 비교 피하기 

- 불 원시 타입 true, false 를 없애준다. 
```Java

if(microscope.isInorganic(sample)){ // true,false 없음
    return Result.INORGANIC;
}
else{
    return analyzeOrganic(sample);
}
 
if(!microscope.isHumanoid(sample)){ // true,false 없음
    return Result.ALIEN;
}
else{
    return Result.HUMAN;
}

```

## 1.2 부정 피하기 

- 긍정 표현식이 부정표현식 보다 낫다. 

```Java
if(microscope.isOrganic(sample)){ //isInOrganic -> isOrganic
    return analyzeOrganic(sample);
}
else{
    return Result.INORGANIC;
}

if(microscope.isHumanoid(sample)){ //! 없앰
    return Result.HUMAN;
}
else{
    return Result.ALIEN;
}

```

## 1.3 불 표현식을 직접 반환 

```Java

 boolean isValid(){
     return missions>=0 && name!=null && !name.trim().isEmpty();
 }

```
- 조건문 3개 이상일 때는 변수에 의미있는 이름을 지어 조건 덩어리로 표현

```Java
 boolean isValid(){
     boolean isValidMissions = missions >=0; 
     boolean isValidName = name != null && !name.trim().isEmpty(); // 두개의 조건문을 하나로 묶음
     return isValidMissions && isValidName;
 }
```

## 1.4 불 표현식 간소화 

- 한 메서드 안에서 추상화 수준이 비슷하도록 명령문을 합쳐야함.
- 높은 수준의 메서드가 다음으로 낮은 수준의 메서드를 호출하는 것이 이상적.

- 문제 코드

```java
class SpaceShip {

    Crew crew;
    FuelTank fuelTank;
    Hull hull;
    Navigator navigator;
    OxygenTank oxygenTank;

    boolean willCrewSurvive() {
        return hull.holes == 0 &&
                fuelTank.fuel >= navigator.requiredFuelToEarth() &&
                oxygenTank.lastsFor(crew.size) > navigator.timeToEarth();
    }


```

- 개선코드
```java
 class SpaceShip {

    Crew crew;
    FuelTank fuelTank;
    Hull hull;
    Navigator navigator;
    OxygenTank oxygenTank;

    boolean willCrewSurvive() {
        boolean hasEnoughResources = hasEnoughFuel() && hasEnoughOxygen(); //소모성 자원이라는 주제로 묶고 의미 있는 이름으로 지음
        return hull.isIntact() && hasEnoughResources;
    }

    private boolean hasEnoughOxygen() {
        return oxygenTank.lastsFor(crew.size) > navigator.timeToEarth();
    }

    private boolean hasEnoughFuel() {
        return fuelTank.fuel >= navigator.requiredFuelToEarth();
    }
}
```

## 1.5 조건문에서 NullPointerException 피하기 

- 문제 코드
```java
   void writeMessage(String message, Path location) throws IOException {
        if (Files.isDirectory(location)) { //location null 이면 nullPointerException 발생
            throw new IllegalArgumentException("The path is invalid!");
        }
        if (message.trim().equals("") || message == null) { //유효성 검사 먼저 하면 nullPointerException 발생
            throw new IllegalArgumentException("The message is invalid!");
        }
              ...
    }
```

- 개선 코드
```java
    void writeMessage(String message, Path location) throws IOException {
        if (message == null || message.trim().isEmpty()) {  // null을 먼저 확인 한 후에 유효하지 않은지 검사
            throw new IllegalArgumentException("The message is invalid!");
        }
        if (location == null || Files.isDirectory(location)) { // location도 null검사 먼저
            throw new IllegalArgumentException("The path is invalid!");
        }

```
- 위에 처럼 항상 매개변수를 검사할 필요 x
- public, protected, default 메서드에만 하면됨. private 메서드에서는 필요 x

## 1.6 스위치 실패 피하기 && 1.7 항상 괄호 사용하기 

- 각 case 끝에 꼭 break문 붙여주기 
- 의도적으로 break 누락시에는 반드시 주석을 남김. 
- Switch가 항상 옳은 것은 아님. 서로 다른 관심사는 서로 다른 코드블록에 넣어야함. 따라서 이때는 if문 사용
- if문 사용할 때는 (),{} 와 같은 괄호를 사용.

- 문제코드
```java
void authorize(User user) {
        Objects.requireNonNull(user);
        switch (user.getRank()) {
            case UNKNOWN:
                cruiseControl.logUnauthorizedAccessAttempt();  // break 없음 , 허가받지 않은 접근 
            case ASTRONAUT:
                cruiseControl.grantAccess(user);  // 허가받은 접근
                break;
            case COMMANDER:
                cruiseControl.grantAccess(user); // 허가받은 접근
                cruiseControl.grantAdminAccess(user);
                break;
        }
    }
```

- 개선코드

```java
    // 허가받은 접근과 허가받지 않은 접근을 서로 다른 코드블럭에 넣음.
    void authorize(User user) {
        Objects.requireNonNull(user);
        if (user.isUnknown()) {
            cruiseControl.logUnauthorizedAccessAttempt(); 
        }
        if (user.isAstronaut()) {
            cruiseControl.grantAccess(user);
        }
        if (user.isCommander()) {
            cruiseControl.grantAccess(user);
        }
        cruiseControl.grantAdminAccess(user); // SECURITY THREAT
    }
```
## 1.8 코드 대칭 이루기 

- 비슷한 관심사끼리 묶어준다 

- 개선 코드
 ```java   
    void authorize(User user) {
        Objects.requireNonNull(user);
        
        // 권한 부여 안하는 코드 혼자 if문 
        if (user.isUnknown()) {
            cruiseControl.logUnauthorizedAccessAttempt();
            return;
        }

        // 권한을 부여하는 코드끼리 if/else if로 묶어줌
        if (user.isAstronaut()) {
            cruiseControl.grantAccess(user); 
        } else if (user.isCommander()) {
            cruiseControl.grantAccess(user);
            cruiseControl.grantAdminAccess(user);
        }
    }
```

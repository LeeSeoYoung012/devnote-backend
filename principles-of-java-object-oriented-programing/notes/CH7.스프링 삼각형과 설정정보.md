# 스프링 삼각형과 설정 정보

## IOC/DI 제어의 역전/의존성 주입

**스프링 없이 의존성 주입하기 1 - 생성자를 통한 의존성 주입**

```java
Tire tire = new KoreaTire();
Car car = new Car(tire);
```
- 의존성 주입을 하면 확장성도 좋아진다. 
- 더떤 새로운 타이어 브랜드가 생겨도 각 타이어 브랜드들이 Tire 인터페이스를 구현한다면 Car.java 코드를 변경할 필요가 없다.

**스프링 없이 의존성 주입하기 2 - 속성을 통한 의존성 주입**

```java
Tire tire = new KoreaTire();
Car car = new Car();
car.setTire(tire);
```
- Car.java 파일에 생성자가 없어지고 tire 속성에 대한 접근자 및 설정자 메서드를 만든다. 

**스프링 없이 의존성 주입하기 3 - XML 파일 사용하기**

```java
package expert002;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.context.support.FileSystemXmlApplicationContext;

public class Driver {
	public static void main(String[] args) {
		//ApplicationContext context = new FileSystemXmlApplicationContext("src/main/java/expert002/expert002.xml"); // 입점된 상품이 들어있는 곳이 xml 파일
		//ApplicationContext context = new ClassPathXmlApplicationContext("expert002.xml", Driver.class);
		ApplicationContext context = new ClassPathXmlApplicationContext("expert002/expert002.xml");

		// Car car = (Car)context.getBean("car");
		Car car = context.getBean("car", Car.class);

		// Tire tire = (Tire)context.getBean("tire");
		Tire tire = context.getBean("tire", Tire.class);

		car.setTire(tire);

		System.out.println(car.getTireBrand());
	}
}
```
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
		http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="tire" class="expert002.KoreaTire"></bean> // 등록된 상픔 : KoreaTire

	<bean id="americaTire" class="expert002.AmericaTire"></bean> // 등록된 상품 :AmericaTire

	<bean id="car" class="expert002.Car"></bean> 

</beans>
```

- property 적용한 코드

```java
package expert003;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Driver {
	public static void main(String[] args) {
		//ApplicationContext context = new FileSystemXmlApplicationContext("src/main/java/expert003/expert003.xml");
		//ApplicationContext context = new ClassPathXmlApplicationContext("expert003.xml", Driver.class);
		ApplicationContext context = new ClassPathXmlApplicationContext("expert003/expert003.xml");

		//Car car = (Car)context.getBean("car");
		Car car = context.getBean("car", Car.class);   // Tire에 대한 getBean이 없어짐

		System.out.println(car.getTireBrand());
	}
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
		http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="koreaTire" class="expert003.KoreaTire"></bean>

	<bean id="americaTire" class="expert003.AmericaTire"></bean>

	<bean id="car" class="expert003.Car">
		<property name="tire" ref="koreaTire"></property>   // 타이어 속성을 여기서 정해준다. 
		<!--  
		<property name="tire" ref="americaTire"></property>
		-->
	</bean>
	
</beans>
```
**스프링 없이 의존성 주입하기 4 - @Autowired를 통한 속성 주입**

- @Autowired를 통해 car의 property를 자동으로 엮어줄 수 있어 생략이 가능하다. 

```java
package expert004;

import org.springframework.beans.factory.annotation.Autowired;

public class Car {
	@Autowired
	Tire tire;

	public String getTireBrand() {
		return "장착된 타이어: " + tire.getBrand();
	}
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.1.xsd">

	<context:annotation-config />

	<bean id="tire" class="expert004.KoreaTire"></bean>

	<bean id="wheel" class="expert004.AmericaTire"></bean>

	<bean id="car" class="expert004.Car"></bean>
</beans>
```
<1>
|파일|코드|
|-----|----------------------|
|Car.java|@Autowired Tire tire;|
|expert.xml | ```<bean id="tire" class="expert004.KoreaTire"></bean>```|

<2>
|파일|코드|
|-----|----------------------|
|Car.java|@Autowired Tire tire;|
|expert.xml | ``` <bean class="expert004.Tire"></bean> ```|

- <2>가 가능한 이유는 AmericaTire가 Tire을 상속 받기 때문.

**스프링을 통한 의존성 주입 @Resource를 통한 속성 주입**

```java
package expert005;

import javax.annotation.Resource;

public class Car {
	@Resource
	Tire tire;

	public String getTireBrand() {
		return "장착된 타이어: " + tire.getBrand();
	}
}
```
- Autowired는 type 과 id 가운데 매칭 우선순위가 type이 높지만 @Resource의 경우 type과 id 가운데 매칭 우선순위는 id가 높다. 
- Resource의 경우 id로 매칭할 빈을 찾지 못한 경우 type으로 빈을 찾게 된다. 

||@Autowired|@Resource|
|-----|-----|-----|
|출처|스프링 프레임워크|표준 자바|
|소속 패키지|org.springframework.beans.factory.annotation.Autowired|javax.annotation.Resource|
|빈 검색 방식|byType 먼저, 못 찾으면 byName|byName 먼저 못찾으면 byType|
|특이사항|@Qualifier("")협업|name 어트리뷰트|
|byName 강제하기|@Autowired @Qualifier("tire1")|@Resource(name="tire1")|



## AOP - Aspect? 관점? 핵심 관심사? 횡단 관심사?

- 횡단 관심사 : 공통적으로 나타나는 코드
- 핵심 관심사 : 공통적이지 않은 부분

- @Aspect : 이 클래스를 이제 AOP에서 사용하겠다. 
- @Before : 대상 메서드 실행 전에 이 메서드를 실행하겠다.

```java
package aop002;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;

@Aspect
public class MyAspect {
	@Before("execution(* runSomething())")
	public void before(JoinPoint joinPoint) { // JoinPoint는 Aspect 적용이 가능한 모든 지점을 말한다.
		System.out.println("얼굴 인식 확인: 문을 개방하라");
		// System.out.println("열쇠로 문을 열고 집에 들어간다.");
	}
}
```

```java
package aop002;

public interface Person {
	void runSomething();
}
```

```java
package aop002;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Start {
	public static void main(String[] args) {
		ApplicationContext context = new ClassPathXmlApplicationContext("aop002/aop002.xml");

		Person romeo = context.getBean("boy", Person.class);
		Person juliet = context.getBean("girl", Person.class);

		romeo.runSomething();
		juliet.runSomething();
	}
}
```
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans 
  xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:aop="http://www.springframework.org/schema/aop"

  xsi:schemaLocation="
    http://www.springframework.org/schema/aop
    http://www.springframework.org/schema/aop/spring-aop-3.1.xsd
    http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans.xsd">

	<aop:aspectj-autoproxy />
	<!--<bean class="org.springframework.aop.aspectj.annotation.AnnotationAwareAspectJAutoProxyCreator" /> -->

	<bean id="myAspect" class="aop002.MyAspect" />
	<bean id="boy" class="aop002.Boy" />
	<bean id="girl" class="aop002.Girl" />
</beans>
```
```java
package aop002;

public class Boy implements Person {
	public void runSomething() {
		System.out.println("컴퓨터로 게임을 한다.");
	}
}
```
```java
package aop002;

public class Girl implements Person {
	public void runSomething() {
		System.out.println("요리를 한다.");
	}
}
```
- MyAspect가 횡단 관심사를 처리하는 역할을 하게 된다. 
- AOP는 인터페이스 기반이다.
- AOP는 프록시 기반이다.
- AOP는 런타임 기반이다.
- JoinPoint는 Aspect적용이 가능한 모든 지점을 의미한다. 
- Advice는 Pointcut에 언제, 무엇을 적용할지 정의한 메서드.
- Aspect = Advice 들 + Pointcut들
- 어노테이션이 아닌 xml 기반으로도 만들 수 있다. 

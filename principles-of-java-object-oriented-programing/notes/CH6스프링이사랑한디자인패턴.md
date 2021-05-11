# CH6 스프링이 사랑한 디자인 패턴

## 1. 어댑터 패턴(Adapter Pattern)
- 어댑터는 개방 폐쇄 원칙(OCP)를 활용한 설계 패턴이라고 본다. 
- ServiceA.java
```java
package adapterPattern;

public class ServiceA {
	void runServiceA() {
		System.out.println("ServiceA");
	}
}
```
- ServiceB.java
```java
package adapterPattern;

public class ServiceB {
	void runServiceB() {
		System.out.println("ServiceB");
	}
}
```
- ClientWithNoAdapter.java
```java
package adapterPattern;

public class ClientWithNoAdapter {
	public static void main(String[] args) {
		ServiceA sa1 = new ServiceA();
		ServiceB sb1 = new ServiceB();

		sa1.runServiceA();
		sb1.runServiceB();
	}
}
```
- 위츼 코드를 보게 되면 sa1의 참조 변수와 sb1의 참조변수를 통해 호출하는 각 메서드가 비슷한 일을 하지만 메서드명이 다른 것을 확인 할 수 있다. 
- AdapterServiceA.java
```java
package adapterPattern;

public class AdapterServicA {
	ServiceA sa1 = new ServiceA();

	void runService() {
		sa1.runServiceA();
	}
}
```
- AdapterServiceB.java
```java
package adapterPattern;

public class AdapterServicB {
	ServiceB sb1 = new ServiceB();

	void runService() {
		sb1.runServiceB();
	}
}
```
- ClientWithAdapter.java
```java
package adapterPattern;

public class ClientWithAdapter {
	public static void main(String[] args) {
		AdapterServicA asa1 = new AdapterServicA();
		AdapterServicB asb1 = new AdapterServicB();

		asa1.runService();
		asb1.runService();
	}
}
```
- 위와 같이 어댑터 패턴을 사용하게 될 경우 동일한 메서드 명으로 두 객체의 메서드를 호출하는 것을 볼 수 있다.
- 호출당하는 쪽의 메서드를 호출하는 쪽의 코드에 대응하도록 중간에 변환기를 통해 호출하는 패턴이다.
- 즉, 서로 다른 두객체의 메서드가 같은 역할을 하지만 서로 다른 이름을 가졌을 때 `중간에 어댑터가 이를 같은 이름으로 통일` 시키는 역할을 한다.

## 2. 프록시 패턴(Proxy Pattern)

**<프록시 패턴을 적용하지 않은 경우>**
- Service.java
```java 
package proxyPattern;

public class Service {
	public String runSomething() {
		return "서비스 짱!!!";
	}
}
```
- ClientWithNoProxy.java
```java
package proxyPattern;

public class ClientWithNoProxy {
	public static void main(String[] args) {
		// 프록시를 이용하지 않은 호출
		Service service = new Service();
		System.out.println(service.runSomething());
	}
}
```
**<프록시를 적용한 경우>**
- IService.java
```java
package proxyPattern;

public interface IService {
	String runSomething();
}
```
- Service.java
```java
package proxyPattern;

public class Service implements IService {
	public String runSomething() {
		return "서비스 짱!!!";
	}
}
```
- Proxy.java
```java
package proxyPattern;

public class Proxy implements IService {
	IService service1; //실제서비스에 대한 참조변수

	public String runSomething() {
		System.out.println("호출에 대한 흐름 제어가 주목적, 반환 결과를 그대로 전달"); //실제 서비스 메서드 호출 전후에도 같은 이름을 가진 메서드를 호출하고 그 값을 클라이언트에게돌려줌

		service1 = new Service(); //실제 서비스와 같은 이름을 가진 메서드를 호출하고 그값을 클라이언트에게 돌려준다.
		return service1.runSomething();
	}
}
```
- ClientWithProxy.java
```java
package proxyPattern;

public class ClientWithProxy {
	public static void main(String[] args) {
		// 프록시를 이용한 호출
		IService proxy = new Proxy();
		System.out.println(proxy.runSomething());
	}
}
```
- 대리자(Proxy)는 실제 서비스와 같은 이름의 메서드를 구현한다. 이떄, 인터페이스를 사용한다. 
- 대리자는 실제 서비스에 대한 참조 변수를 갖는다. 
- 대리자는 실제 서비스와 같은 이름을 가진 메서드를 호출하고 그 값을 클라이언트에게 돌려준다.
- 대리자는 실제 서비스의 메서드 호출 전후에 별도의 로직을 수행할 수 있다. 
- 개방 폐쇄의 원칙(OCP)과 의존 역전의 원칙(DIP)가 적용이 되는 설계 패턴.

## 3. 데코레이터 패턴(Decorator Pattern)

- 데코레이터 패턴은 프록시 패턴과는 다르게 클라이언트가 받는 반환값에 장식을 더한다.
```java
package decoratorPattern;

public class Decoreator implements IService {
	IService service;

	public String runSomething() {
		System.out.println("호출에 대한 장식 주목적, 클라이언트에게 반환 결과에 장식을 더하여 전달");

		service = new Service();
		return "정말" + service.runSomething();
	}
}
```
```java
package decoratorPattern;

public class ClientWithDecolator  {
	public static void main(String[] args) {
		IService decoreator = new Decoreator();
	 	System.out.println(decoreator.runSomething());
	}
}
```
## 4. 싱글턴 패턴(Singleton Pattern)

- 인스턴스를 여러개 만들면 불필요한 자원을 사용하게 되고 프로그램이 예상치 못한 결과를 낳을 수 있다.
- new를 실행할 수 없도록 생성자에 private 접근 제어자를 지정한다. 
- 유일한 단일 객체를 반환할 수 있는 정적 메서드가 필요하다.
- 유일한 단일 객체를 참조할 정적 참조 변수가 필요하다. 

```java
package singletonPattern;

public class Singleton {
	static Singleton singletonObject; // 정적 참조 변수

	private Singleton() {
	}; // private 생성자

	// 객체 반환 정적 메서드
	public static Singleton getInstance() {
		if (singletonObject == null) {
			singletonObject = new Singleton();
		}

		return singletonObject;
	}
}
```
```java
package singletonPattern;

public class Client {
	public static void main(String[] args) {
		// private 생성자임으로 new 할 수 없다.
		// Singleton s = new Singleton();

		Singleton s1 = Singleton.getInstance();
		Singleton s2 = Singleton.getInstance();
		Singleton s3 = Singleton.getInstance();

		System.out.println(s1); //s1,s2,s3 는 모두 같은 곳을 가르키게 된다.
		System.out.println(s2);
		System.out.println(s3);

		s1 = null;
		s2 = null;
		s3 = null;
	}
}
```

## 5. 템플릿 메서드 패턴(Template Method Pattern)

**<템플릿 메서드 적용 >**
- Dog.java
```java
public class Dog {
	public void playWithOwner() {
		System.out.println("귀염둥이 이리온...");
		System.out.println("멍! 멍!");
		System.out.println("꼬리 살랑 살랑~");
		System.out.println("잘했어");
	}
}
```
- Cat.java
```java
public class Cat {
	public void playWithOwner() {
		System.out.println("귀염둥이 이리온...");
		System.out.println("야옹~ 야옹~");
		System.out.println("꼬리 살랑 살랑~");
		System.out.println("잘했어");
	}
}
```
- dog와 cat이 메서드안에 4번째 줄을 제외하고 모두 같은 것을 알 수 있다.

**<템플릿 메서드 적용 후>**

- Animal.java
```java
package templateMethodPattern;

public abstract class Animal {
	// 템플릿 메서드
	public void playWithOwner() {
		System.out.println("귀염둥이 이리온...");
		play();
		runSomething();
		System.out.println("잘했어");
	}

	// 추상 메서드
	abstract void play();

	// Hook(갈고리) 메서드
	void runSomething() {
		System.out.println("꼬리 살랑 살랑~");
	}
}
```

- Dog.java
```java
  
package templateMethodPattern;

public class Dog extends Animal {
	@Override
	// 추상 메서드 오버라이딩
	void play() {
		System.out.println("멍! 멍!");
	}

	@Override
	// Hook(갈고리) 메서드 오버라이딩
	void runSomething() {
		System.out.println("멍! 멍!~ 꼬리 살랑 살랑~");
	}
}
```
- Cat.java
```java
package templateMethodPattern;

public class Cat extends Animal {
	@Override
	// 추상 메서드 오버라이딩
	void play() {
		System.out.println("야옹~ 야옹~");
	}

	@Override
	// Hook(갈고리) 메서드 오버라이딩
	void runSomething() {
		System.out.println("야옹~ 야옹~ 꼬리 살랑 살랑~");
	}
}
```
- Driver.java
```java
package templateMethodPattern;

public class Driver {
	public static void main(String[] args) {
		Animal bolt = new Dog();
		Animal kitty = new Cat();

		bolt.playWithOwner();

		System.out.println();
		System.out.println();

		kitty.playWithOwner();
	}
}
```
- 하위 클래스인 Dog 와 Cat은 상위 클래스인 Animal에서 구현을 강제하고 있는 play() 추상메서드를 반드시 구현해야한다. 
- runSomething() 메서드는 선택적으로 오버라이딩 할 수 있다. 
- 이처럼 상위 클래스에 공통 로직을 수행하는 템플릿 메서드와 하위 클래스에 오버라이딩을 강제하는 추상메서드 또는 선택적으로 오버라이딩 할 수 있는 Hook 메서드를 두는 패턴을 템플릿 메서드 패턴이라한다.
- 의존 역전의 원칙(DIP)를 활용하고 있다.

## 6. 팩터리 메서드 패턴(Factory Method Pattern)
- Animal.java
```java
package factoryMethodPattern;

public abstract class Animal {
	// 추상 팩터리 메서드
	abstract AnimalToy getToy();
}
```
- AnimalToy.java
```java
package factoryMethodPattern;

// 팩터리 메서드가 생성할 객체의 상위 클래스
public abstract class AnimalToy {
	abstract void identify();
}
```
- Dog.java
```java
package factoryMethodPattern;

public class Dog extends Animal {
	// 추상 팩터리 메서드 오버라이딩
	@Override
	AnimalToy getToy() {
		return new DogToy();
	}
}
```
- DogToy.java
```java
package factoryMethodPattern;

//팩터리 메서드가 생성할 객체
public class DogToy extends AnimalToy {
	public void identify() {
		System.out.println("나는 테니스공! 강아지의 친구!");
	}
}
```
- Cat.java
```java
package factoryMethodPattern;

public class Cat extends Animal {
	// 추상 팩터리 메서드 오버라이딩
	@Override
	AnimalToy getToy() {
		return new CatToy();
	}
}
```
- CatToy.java
```java
package factoryMethodPattern;

//팩터리 메서드가 생성할 객체
public class CatToy extends AnimalToy {
	@Override
	public void identify() {
		System.out.println("나는 캣타워! 고양이의 친구!");
	}
}
```
- Driver.java
```java
package factoryMethodPattern;

public class Driver {
	public static void main(String[] args) {
		// 팩터리 메서드를 보유한 객체들 생성
		Animal bolt = new Dog();
		Animal kitty = new Cat();

		// 팩터리 메서드가 반환하는 객체들
		AnimalToy boltBall = bolt.getToy();
		AnimalToy kittyTower = kitty.getToy();

		// 팩터리 메서드가 반환한 객체들을 사용
		boltBall.identify();
		kittyTower.identify();
	}
  }
```
- 하위 클래스에서 팩터리 메서드를 오버라이딩해서 객체를 반환하게 하는 것을 의미한다. 
- 오버라이드 된 메서드가 객체를 반환하는 패턴이다. 
- 의존 역전 원칙(DIP)가 활용된다.

## 7. 전략 패턴(Strategy Pattern)

- 전략 메서드를 가진 객체 
- 전략 객체를 사용하는 컨텍스트(전략 객체의 사용자/소비자)
- 전략 객체를 생성해 컨텍스트에 주입하는 클라이언트(제3자, 전략 객체의 공급자)

## 8. 템플릿 콜백 패턴(Template Callback Pattern - 견본/회신 패턴)
## 9. 스프링이 사랑한 다른 패턴들

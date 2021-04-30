# Chapter5 객체지향 설계 5원칙

## 5.1 SRP - 단일 책임 원칙

- 어떤 클래스를 변경해야 하는 이유는 오직 하나 뿐이다. 

- 하나의 클래스에 역할가 책임이 너무 많으면 안된다. 그래서 책임에 따라 여러개의 클래스로 쪼개야한다. 

- 단일 책임의 원칙은 속성, 메서드, 패키지, 모듈, 컴포넌트, 그리고 프레임워크 등에도 적용될 수 있는 개념이다.

**<단일 책임의 원칙이 적용이 되지 않은 코드>**

```java
package srp.bad;

public class 강아지 {
	final static Boolean 숫컷 = true;
	final static Boolean 암컷 = false;
	Boolean 성별;

	void 소변보다() {
		if (this.성별 == 숫컷) {
			// 한쪽 다리를 들고 소변을 본다.
		} else {
			// 뒤다리 두 개로 앉은 자세로 소변을 본다.
		}
	}
```
- 수컷 강아지, 암컷 강아지를 책임지게 되면서 if 분기문을 씀 ( 분기문은 단일 책임 원칙이 지켜지지 않은 경우 나타나는 현상)

**<단일 책임의 원칙이 적용 된 코드>**

강아지.class
```java
  
package srp.good;

public abstract class 강아지 {
	abstract void 소변보다();
}
```
숫컷강아지.class
```java
  
package srp.good;

public class 숫컷강아지 extends 강아지 {
	void 소변보다() {
		// 한쪽 다리를 들고 소변을 본다.
	}
}
```
암컷강아지.class
```java
package srp.good;

public class 암컷강아지 extends 강아지 {
	void 소변보다() {
		// 뒤다리 두 개로 앉은 자세로 소변을 본다.
	}
}
```
- 공통점은 강아지 클래스에 두고 강아지 클래스를 상속해서 숫컷강아지와 암컷강아지가 차이점만 각자 구현을 하면 된다. 

## 5.2 OCP - 개방 폐쇄 원칙

- 자신의 확장에는 열려 있고 주변의 변화에 대해서는 닫혀있어야한다. 

**<개방폐쇄 원칙이 위배되는 경우>**

![image](https://user-images.githubusercontent.com/60209292/116554033-b29e1f00-a935-11eb-9d4f-1f11214ab0ad.png)

![image](https://user-images.githubusercontent.com/60209292/116554159-d5303800-a935-11eb-89b6-7963e7f50b3d.png)

- 운전자가 마티즈를 탔을 때는 인스턴스의 기어수동조작() 메서드를 사용했지만 쏘나타로 바꾸자마자 기어자동조작() 메서드를 사용하게 된다. 

**<개방폐쇄 원칙을 적용한 경우>**

![image](https://user-images.githubusercontent.com/60209292/116554448-293b1c80-a936-11eb-81c0-68a13d1a1f70.png)

- 상위 클래스 혹은 인터페이스를 중간에 둠으로써 다양한 자동차가 생긴다고 해도 객체 지향세계의 운전자는 영향을 받지 않음 

- 자신의 확장에는 개방돼 있는 것이고, 운전자 입장에서는 주변의 변화에 폐쇄돼 있는 것이다.

- 개방폐쇄 원칙이 적용되는 또다른 예시 : 

``JDBC``-오라클에서 mysql로 바뀌더라도 connection을 설정하는 부분 외에는 따로 수정할 필요가 없음

``JVM`` -개발자가 작성한 소스코드는 운영체제의 변화에 닫혀 있고, 각 운영체제별 JVM 은 확장에 열려 있는 구조가 되는 것이다.

``판매 인터페이스`` -손님의 구매라는 행위는 직원이 세부적으로 구매 담당자든, 보안 담당자든 심지어 남자에서 여자로 학생에서 노인으로 교대 한다 해도
전혀 영향 받지 않음. 

![image](https://user-images.githubusercontent.com/60209292/116555914-dbbfaf00-a937-11eb-833b-7d2b50283f34.png)


## 5.3 LCP - 리스코프 치환 원칙

- 서브 타입은 언제나 자신의 기반 타입으로 교체할 수 있어야 한다. 

- 계층도/조직도일 경우 논리에 맞지 않는다 (리스코프 치환 원칙 위반)

![image](https://user-images.githubusercontent.com/60209292/116556357-58528d80-a938-11eb-80d0-7d740ec2a877.png)

- 분류도일 경우 논리에 잘 맞는다. (리스코프 치환 원칙 성립)

![image](https://user-images.githubusercontent.com/60209292/116556490-73250200-a938-11eb-832e-640f0502ecf6.png)


## 5.4 ISP - 인터페이스 분리 원칙

![image](https://user-images.githubusercontent.com/60209292/116557598-897f8d80-a939-11eb-83e4-6b6901d0ae8d.png)

- 남자의 역할 별로 인터페이스를 분리한다.

- 인터페이스를 통해 메서드를 외부에 제공할 때는 최소한의 메서드만 제공한다. (인터페이스 최소주의 원칙)


![image](https://user-images.githubusercontent.com/60209292/116558157-1aeeff80-a93a-11eb-9387-e7a703599e9e.png)

- 상위클래스는 풍성할 수록 좋고 인터페이스는 작을 수록 좋다. 

**<빈약한 상위 클래스를 사용하는 경우>**

```java
package poorSuperClass;

import java.util.Date;

public class Driver {
	public static void main(String[] args) {
		사람 김학생 = new 학생("김학생", new Date(2000, 01, 01), "20000101-1234567",
				"20190001");
		사람 이군인 = new 군인("이군인", new Date(1998, 12, 31), "19981231-1234567",
				"19-12345678");

		System.out.println(김학생.이름);
		System.out.println(이군인.이름);

		// System.out.println(김학생.생일); // 사용불가
		// System.out.println(이군인.생일); // 사용불가

		System.out.println(((학생) 김학생).생일); // 캐스팅 필요
		System.out.println(((군인) 이군인).생일); // 캐스팅 필요

		// System.out.println(김학생.주민등록번호); // 사용불가
		// System.out.println(이군인.주민등록번호); // 사용불가

		System.out.println(((학생) 김학생).주민등록번호);
		// 캐스팅 필요
		System.out.println(((군인) 이군인).주민등록번호);
		// 캐스팅 필요

		김학생.먹다();
		이군인.먹다();

		// 김학생.자다(); // 사용불가
		// 이군인.자다(); // 사용불가

		((학생) 김학생).자다(); // 캐스팅 필요
		((군인) 이군인).자다(); // 캐스팅 필요

		// 김학생.소개하다(); // 사용불가
		// 이군인.소개하다(); // 사용불가

		((학생) 김학생).소개하다(); // 캐스팅 필요
		((군인) 이군인).소개하다(); // 캐스팅 필요

		((학생) 김학생).공부하다(); // 캐스팅 필요
		((군인) 이군인).훈련하다(); // 캐스팅 필요
	}
}
```
- 형변환이 여기저기 발생하면서 상속의 혜택을 보지 못하고 있다. 

**<풍성한 상위 클래스를 이용>**
```java
package richSuperClass;

import java.util.Date;

public class Driver {
	public static void main(String[] args) {
		사람 김학생 = new 학생("김학생", new Date(2000, 01, 01), "20000101-1234567",
				"20190001");
		사람 이군인 = new 군인("이군인", new Date(1998, 12, 31), "19981231-1234567",
				"19-12345678");

		System.out.println(김학생.이름);
		System.out.println(이군인.이름);

		System.out.println(김학생.생일);
		System.out.println(이군인.생일);

		System.out.println(김학생.주민등록번호);
		System.out.println(이군인.주민등록번호);

		// System.out.println(김학생.학번); // 사용불가
		// System.out.println(이군인.군번); // 사용불가

		System.out.println(((학생) 김학생).학번);
		// 캐스팅 필요
		System.out.println(((군인) 이군인).군번);
		// 캐스팅 필요

		김학생.먹다();
		이군인.먹다();

		김학생.자다();
		이군인.자다();

		김학생.소개하다();
		이군인.소개하다();

		// 김학생.공부하다(); // 사용불가
		// 이군인.훈련하다(); // 사용불가

		((학생) 김학생).공부하다(); // 캐스팅 필요
		((군인) 이군인).훈련하다(); // 캐스팅 필요
	}
}
```
- 캐스팅이 자주 일어나지 않는다. 

- 소개하다()는 사람 클래스에 공통적으로 있지만 이는 군인과 학생의 경우 다르게 실행이 되야한다. -> 이런 경우 추상메서드를 쓴다.

**<좋은 코드>**

```java
  
package richSuperClass;

import java.util.Date;

public abstract class 사람 {
	String 이름;  // 최대한 공통적인 변수들은 여기에 담는다.
	Date 생일;
	String 주민등록번호;
	
	void 먹다() {
		System.out.println(이름 + " 식사 중");
	}

	void 자다() {
		System.out.println(이름 + " 취침 중");
	}

	abstract void 소개하다(); // 추상메서드로 
}
```
```java
package richSuperClass;

import java.util.Date;

public class 군인 extends 사람 {
	String 군번;
	
	public 군인(String 이름, Date 생일, String 주민등록번호, String 군번) {
		this.이름 = 이름;
		this.생일 = 생일;
		this.주민등록번호 = 주민등록번호;
		this.군번 = 군번;
	}
	
	void 훈련하다() {
		System.out.println(이름 + " 훈련 중");
	}

	@Override
	void 소개하다() {
		String msg = "";
		
		msg += "이름: " + 이름;
		msg += "\n생일: " + 생일;
		msg += "\n주민등록번호: " + 주민등록번호;
		msg += "\n군번: " + 군번;
		
		System.out.println(msg);
	}
}
```
```java
package richSuperClass;

import java.util.Date;

public class 학생 extends 사람 {
	String 학번;

	public 학생(String 이름, Date 생일, String 주민등록번호, String 학번) {
		this.이름 = 이름;
		this.생일 = 생일;
		this.주민등록번호 = 주민등록번호;
		this.학번 = 학번;
	}

	void 공부하다() {
		System.out.println(이름 + " 공부 중");
	}

	@Override
	void 소개하다() {
		String msg = "";

		msg += "이름: " + 이름;
		msg += "\n생일: " + 생일;
		msg += "\n주민등록번호: " + 주민등록번호;
		msg += "\n학번: " + 학번;

		System.out.println(msg);
	}
}

```


## 5.5 DIP - 의존 역전 원칙

- 자신보다 변하기 쉬운 것에 의존하던 것을 추상화된 인터페이스나 상위 클래스를 두어 변하기 쉬운 것의 변화에 영향받지 않게 하는 것이 의존 역전 원칙이다.

**<의존 역전 원칙 적용 전>**

![image](https://user-images.githubusercontent.com/60209292/116561087-f21c3980-a93c-11eb-9f4d-0cd1cfda4fd1.png)

**<의존 역전 원칙 적용 후>**

![image](https://user-images.githubusercontent.com/60209292/116561196-10823500-a93d-11eb-937f-72a2821ada3e.png)

![image](https://user-images.githubusercontent.com/60209292/116717381-c9b33e80-aa13-11eb-82bb-a494753ffd4f.png)

- 여기서 KitchenLamp는 DIP 가 지켜지는 것. 추상화된 Button에 의존해서 변화에 영향을 받지 않지만 스스로 확장이 불가능해 OCP는 지켜지 않는다.
- 여기서 Button은 OCP 가 지켜지는 것 KitchenButton 외에도 다른 버튼으로 확장이 가능하고 변화에 영향을 받지 않는다.





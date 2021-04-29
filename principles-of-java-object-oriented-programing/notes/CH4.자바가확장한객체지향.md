# Chapter 4 자바가 확장한 객체 지향 

## 4.1 abstract 키워드 - 추상 메서드와 추상 클래스 

- 추상 메서드: 선언부는 있는데 구현부가 없는 메서드

- 추상 클래스는 객체를 만들 수 없다. 

- 추상메서드는 하위 클래스에게 메서드 구현을 강제한다. 오버라이딩 강제. 

- 추상메서드를 포함하는 클래스는 반드시 추상 클래스여야한다.

## 4.2 생성자 

```java

package constructor01;

public class 동물 {

}

```
- 위와 같이 생성자를 생성하지 않아도 기본생성자를 만들어 준다. 

```java

package constructor03;

public class 동물 {
	public 동물(String name) {
		System.out.println(name);
	}
}

```
- 이렇게 인자가 있는 생성자 하나라도 만들면 자바는 기본 생성자를 만들어 주지 않는다. 

## 4.3 클래스 생성 시의 실행 블록, static 블록

```java
package staticBlock;

public class 동물 {
	static {
		System.out.println("동물 클래스 레디 온!");
	}
}
```
- 동물에 대한 인스턴스를 만들어야만 static 블록이 실행된다. 

- 인스턴수 여러개를 만들어도 static block은 한번만 실행이 된다. 

- 클래스 정보는 해당 클래스가 코드에서 **맨 처음 사용될 때** T 메모리의 스태틱 영역에 로딩되며 이때 단 한번 해당 클래스의 static 블록이 실행된다 

- 클래스가 제일처음 사용할 때는 **클래스의 정적 속성을 사용할 때**, **클래스의 정적 메서드를 사용할때**, **클래스의 인스턴스를 최초로 만들 Eo

## 4.4 final 키워드 

- final과 클래스 : 상속을 허락하지 않아 하위 클래스를 만들 수 없다.

- final과 변수 : 정적 생성자에 해당하는 static 블록 내부에서 초기화가 가능하다. 

- final과 메서드 : 오버라이딩을 금지한다.

## 4.5 instanceof 연산자 

- instanceof 연산자는 만들어진 객체가 특정 클래스의 인스턴스 인지 물어보는 연산자이다. 

 ```java
 package instanceOf01;

class 동물 {

}

class 조류 extends 동물 {

}

class 펭귄 extends 조류 {

}

public class Driver {
	public static void main(String[] args) {
		동물 동물객체 = new 동물();
		조류 조류객체 = new 조류();
		펭귄 펭귄객체 = new 펭귄();

		System.out.println(동물객체 instanceof 동물);

		System.out.println(조류객체 instanceof 동물);
		System.out.println(조류객체 instanceof 조류);

		System.out.println(펭귄객체 instanceof 동물);
		System.out.println(펭귄객체 instanceof 조류);
		System.out.println(펭귄객체 instanceof 펭귄);

		System.out.println(펭귄객체 instanceof Object);
	}
}
 ```
 - 결과는 모두 true이다.

```java

package instanceOf02;

class 동물 {

}

class 조류 extends 동물 {

}

class 펭귄 extends 조류 {

}

public class Driver {
	public static void main(String[] args) {
		동물 동물객체 = new 동물();
		동물 조류객체 = new 조류();
		동물 펭귄객체 = new 펭귄();

		System.out.println(동물객체 instanceof 동물);

		System.out.println(조류객체 instanceof 동물);
		System.out.println(조류객체 instanceof 조류);

		System.out.println(펭귄객체 instanceof 동물);
		System.out.println(펭귄객체 instanceof 조류);
		System.out.println(펭귄객체 instanceof 펭귄);

		System.out.println(펭귄객체 instanceof Object);
	}
}
```
- 객체 참조 변수의 타입이 아닌 실제 객체의 타이에 의해 처리되기 때문에 동물 객체더라도 instanceof 조류 도 true가 된다. 
- instanceof는 클래스 상속관게 뿐만아니라 인터페이스 구현 관계에서도 동일하게 적용이된다.

## 4.6 package 키워드

- 고객사업부.Customer 와 마케팅사업부.Customer는 같은 Customer 클래스이지만 다른 패키지 이다. 

- 소유자가 패키지라고 보면된다.

## 4.7 interface 키워드와 implements 키워드

- 인터페이스는 public 추상메서드와 public 정적 상수만 가질 수 있다.
 
- 이때 static과 final을 붙이지 않아도 자동으로 자바가 알아서 붙여준다.

- 자바 8 부터는 인터페이스에 디폴트 메서드가 존재한다. 

## 4.8 this 키워드 

```java
package This;

class 펭귄 {
	int var = 10;

	void test() {
		int var = 20;

		System.out.println(var);
		System.out.println(this.var);
	}
}

public class Driver {
	public static void main(String[] args) {
		펭귄 뽀로로 = new 펭귄();
		뽀로로.test();
	}
}
```
- 지역 변수 속성(객체 변수, 정적 변수)의 이름이 같은 경우 지역 변수가 우선한다. 

- 객체 변수와 이름이 같은 지역 변수가 있는 경우 객체 변수를 사용하려면 this를 접두사로 사용한다. 

- 정적 변수와 이름이 같은 지역 변수가 있는 경우 정적변수를 사용하려면 클래스명을 접두사를 사용한다.


## 4.9 super 키워드 

```java
package Super;

class 동물 {
	void method() {
		System.out.println("동물");
	}
}

class 조류 extends 동물 {
	void method() {
		super.method();
		System.out.println("조류");
	}
}

class 펭귄 extends 조류 {
	void method() {
		super.method();
		System.out.println("펭귄");

		// Syntax error on token "super", Identifier expected
		// super.super.method();
	}
}

public class Driver {
	public static void main(String[] args) {
		펭귄 뽀로로 = new 펭귄();
		뽀로로.method();
	}
}
```
- 바로 위 상위 클래스의 인스턴스를 지칭하는 키워드이다. 

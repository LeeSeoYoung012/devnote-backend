# Chapter3 자바와 객체 지향 

## 3.1 객체 지향은 인간 지향이다. 

- 0과 1로 대변되는 기계에 맞춰 사고하던 방식을 버리고 현실세계를 인지하는 방식으로 프로그램을 만들고자 한게 객체 지향 방식, 따라서 직관적이다. 

## 3.2 객체 지향의 4대 특성 - 캡! 상추다

**캡 상추다** 

- 캡: 캡슐화 - 정보은닉 

- 상: 상속 - 재사용 

- 추: 추상화 - 모델링

- 다: 다형성 - 사용 편의

## 3.3 클래스 vs 객체 = 붕어빵틀 vs 붕어빵 ???

**클래스=붕어빵틀, 객체=붕어빵은 잘못된 개념이다**

- 붕어빵틀이 붕어빵을 찍어내서 클래스라고 한다면 같은 논리로 금형기계는 붕어빵틀을 찍어내는 클래스가 된다. 

- 금형기계 붕어빵틀 = new 금형기계()

- 이걸 인간적인 말로 해석하면 새로운 금형기계를 하나 만들었더니 붕어빵들이 되었다. 라는 이해가 안가는 예제가 되어버린다.

**클래스는 분류에 대한 개념이고 객체는 실체이다** 

- 클래스와 객체를 구분하는 간단한 방법은 나이를 물어보면 된다. 

- 사람의 나이? -> 말이 안됨 -> ``class``

- 김연아의 나이 -> 말이 됨 -> ``객체``

## 3.4 추상화: 모델링

**추상,객체,클래스란**

- 추상: 사물이나 개녕에서 ``공통되는 특성``이나 ``속성`` 따위를 추출하여 파악하는 작용
 
- 객체: 세상에 존재하는 ``유일무이한 사물``

- 클래스: 분류, 집합, 같은 속성을 가진 객체를 ``총칭``하는 개념

**추상화란 구체적인 것을 분해해서 관심 영역(application boundary)에 있는 특성만을 가지고 재조합하는 것 = 모델링**

- 프로그래머가 창조자 이면 클래스를 먼저 설계하게 된다. 클래스를 설계할 떄 먼저 사람의 공통된 특성을 찾게 된다. 

- 이 떄 사람의 모든 공통된 특성을 나열할 필요가없다. 

- 내가 만들고자 하는 애플리케이션은 어디서 사용될 것인가를 생각 하면서 ``어플리케이션 경계`` 에 따라 설계를 해야한다. 

**정리** 

- OOP의 추상화는 모델링이다. 
- 클래스:객체 = 펭귄:뽀로로
- 클래스 설계에서 추상화가 사용된다. 
- 클래스 설게를 위해서는 애플리케이션 경계부터 정해야 한다. 
- 객체 지향에서 추상화의 결과는 클래스이다. 

**추상화와 T메모리**

- Mouse class 
```java
  
package abstraction01;

public class Mouse {
	public String name;
	public int age;
	public int countOfTail;

	public void sing() {
		System.out.println(name + " 찍찍!!!");
	}
}
```
- MouseDriver class 
```java
package abstraction01;

public class MouseDriver {
	public static void main(String[] args) {
		Mouse mickey = new Mouse();

		mickey.name = "미키";
		mickey.age = 85;
		mickey.countOfTail = 1;

		mickey.sing();

		mickey = null;

		Mouse jerry = new Mouse();

		jerry.name = "제리";
		jerry.age = 73;
		jerry.countOfTail = 1;

		jerry.sing();
	}
}
```

< main()메서드 시작하기 전에 T메모리의 상태 > 

![image](https://user-images.githubusercontent.com/60209292/115692926-63d31100-a39a-11eb-8027-b0a50a02f2f3.png)

< main의 5번째 줄 실행 후 >

![image](https://user-images.githubusercontent.com/60209292/115693112-941aaf80-a39a-11eb-88ca-d4d81f77e4c2.png)

- Mouse mickey : Mouse 에 관한 객체 변수를 만들어 static에 저장

- new Mouse() : Mouse 클래스의 인스턴스를 하나 만들어 힙에 배치

- 대입문: Mouse 객체에 대한 주소(포인터)를 참조변수 mickey에 할당

< main 13번 째줄 까지 끝냈을 때 >

![image](https://user-images.githubusercontent.com/60209292/115693993-697d2680-a39b-11eb-8fbc-6d07ddd573e5.png)

< 가비지 컬렉터 이후 >

![image](https://user-images.githubusercontent.com/60209292/115694163-92052080-a39b-11eb-89d0-73769477a618.png)

- 가비지 컬랙터가 참조해 주지 않는 객체 Mouse를 쓰레기로 인지하고 수거해감

< 이후 또 다른 Mouse 객체 생성 >

![image](https://user-images.githubusercontent.com/60209292/115694520-e90af580-a39b-11eb-9263-b7e0c5a6e10b.png)

**클래스멤버 vs 객체멤버 = static 멤버 vs 인스턴스 멤버** 

- 모든 객체가 같은 값을 가지고 있는 속성에 대해서는 static으로 선언 해준다. 

- ex) Mouse 객체의 conntOfTail 은 무조건 1임

- 이렇게 하지 않으면 countOfTail이 Mouse 객체 수만큼 heap 메모리를 잡아 먹기 때문이다. 

<static 으로 고치기>

```java 
package abstraction02;

public class Mouse {
	public String name;
	public int age;
	public static int countOfTail = 1;
	// public final static int countOfTail = 1;

	public void sing() {
		System.out.println(name + " 찍찍!!!");
	}
}
```

- getter와 setter도 static 메서드로 선언한다.

- 스태틱 영역 : 클래스 속성, 정적 변수, 정적 속성

- 힙 영역 : 객체 속성, 객체 변수

- 스택 영역 : 지역 변수 


## 3.5 상속: 재사용 + 확장 

**상속이란?**

- 상속은 상위 클래스의 특성을 하위클래스에서 상속하고 거기에 더해 필요한 특성을 추가, 즉 확장해서 사용할 수 있다는 의미이다

![image](https://user-images.githubusercontent.com/60209292/115713152-a7387a00-a3b0-11eb-8eba-c8b7e40d0304.png)

- 상속이라는 키워드 대신 extends 가 존재

**상속의 강력함** 

< 예제1 >

```java

package inheritance01;

public class Driver01 {
	public static void main(String[] args) {
		동물 animal = new 동물();
		포유류 mammalia = new 포유류();
		조류 bird = new 조류();
		고래 whale = new 고래();
		박쥐 bat = new 박쥐();
		참새 sparrow = new 참새();
		펭귄 penguin = new 펭귄();

		animal.showMe();
		mammalia.showMe();
		bird.showMe();
		whale.showMe();
		bat.showMe();
		sparrow.showMe();
		penguin.showMe();
	}
}

```
- 상위 클래스에만 showMe()를 정의 했지만 하위 클래스에서도 사용할 수 있다. 


< 예제 2 >

```java
package inheritance01;

public class Driver02 {
	public static void main(String[] args) {
		동물 animal = new 동물();
		동물 mammalia = new 포유류();
		동물 bird = new 조류();
		동물 whale = new 고래();
		동물 bat = new 박쥐();
		동물 sparrow = new 참새();
		동물 penguin = new 펭귄();

		animal.showMe();
		mammalia.showMe();
		bird.showMe();
		whale.showMe();
		bat.showMe();
		sparrow.showMe();
		penguin.showMe();
	}
}

```
- 포유류를 동물이다 라고 정의 할 수 있음 

- 인간의 논리를 그대로 코드로 옮길 수 있다.

< 예제 3 >

```java

package inheritance01;

public class Driver03 {
	public static void main(String[] args) {
		동물[] animals = new 동물[7]; // 동물 배열에 모든 종류의 동물들이 들어감

		animals[0] = new 동물();
		animals[1] = new 포유류();
		animals[2] = new 조류();
		animals[3] = new 고래();
		animals[4] = new 박쥐();
		animals[5] = new 참새();
		animals[6] = new 펭귄();

		for (int index = 0; index < animals.length; index++) {
			animals[index].showMe();
		}
	}

```
- 반복문 하나면 모든 동물들이 자신을 들어 낼 수 있다.

**상속은 is a 관계?**

- 펭귄 is a 동물 , 하위클래스 is a 상위클래스 

- 이는 명확한 표현이 아니다. 

- 더 정확한 표현으로는 ``하위 클래스 is a kind of 상위 클래스`` 이다.

**정리**

- 객체 지향 상속은 상위 클래스의 특성을 재사용 하는 것이다. 

- 객체 지향의 상속은 상위 클래스의 특성을 확장하는 것이다. 

- 객체 지향의 상속은 is a kind of 관계를 만족해야 한다. 

**다중 상속과 자바** 

![image](https://user-images.githubusercontent.com/60209292/115715040-ba4c4980-a3b2-11eb-97fe-a365429eb535.png)

- 사람도 수영가능 , 물고기도 수영 가능 

- 인어에게 수영하라 했을 때, 사람처럼 수영을 해야할까 물고기 처럼 해야할까? 이를 다이아몬드 문제라 한다. 

**상속과 인터페이스**

- 상속은 ``하위클래스 is a kind of 상위클래스`` , 인터페이스는 ``구현클래스 is able to 인터페이스``

- 상위 클래스는 물려줄 특성이 풍성할 수록 좋고(리스코프 치환 원칙에 의해)

- 인터페이스는 구현을 강제할 메서드의 개수가 적을 수록 좋다. (인터페이스 분할 원칙에 의해)

**상속과 T메모리**

<Animal class>

```java
package inheritance03;

public class Animal {
	public String name;

	public void showName() {
		System.out.printf("안녕 나는 %s야. 반가워\n", name);
	}
}
```

<Penguin class>

```java
package inheritance03;

public class Penguin extends Animal {
	public String habitat;

	public void showHabitat() {
		System.out.printf("%s는 %s에 살아\n", name, habitat);
	}
}
```

<Driver class>

```java
package inheritance03;

public class Driver {
	public static void main(String[] args) {
		Penguin pororo = new Penguin();// 그림1

		pororo.name = "뽀로로";
		pororo.habitat = "남극"; 

		pororo.showName();
		pororo.showHabitat();  

		Animal pingu = new Penguin();// 그림2

		pingu.name = "핑구";
		// pingu.habitat = "EBS";

		pingu.showName();
		// pingu.showHabitat();

		// Penguin happyfeet = new Animal(); //실행 되지 않는 코드
	}
}
```

<그림 1>

![image](https://user-images.githubusercontent.com/60209292/115716943-b15c7780-a3b4-11eb-8d4a-eef5f9bb1eca.png)

- 하위 클래스의 인스턴스가 생성 될 때 상위 클래스의 인스턴스도 함께 생성된다. 

<그림 2>

![image](https://user-images.githubusercontent.com/60209292/115717315-07c9b600-a3b5-11eb-8c61-529fa668a652.png)

- Animal pingu = new Penguin() 일 경우 pingu 는 Animal을 가르킨다. 

- Penguin pororo = nw Penguin 일 경우 pororo는 Penguin을 가르킨다. 



## 3.6 다형성: 사용편의성 

- 오버라이딩 : 같은 메서드 이름 , 다른 인자 목록으로 상위 클래스의 메서드를 재정의

- 오버로딩 : 같은 메서드 이름, 다른 인자 목록으로 다수의 메서드를 중복 정의

- 제네릭 : 하나의 함수만 구현해도 다수의 함수를 구현한 효과를 낼 수 있다. 

## 3.7 캡슐화: 정보은닉

![image](https://user-images.githubusercontent.com/60209292/115718968-9be84d00-a3b6-11eb-8add-825fa857cc9c.png)

- call by reference : 저장하고 있는 값을 그 값 자체로 판단

![image](https://user-images.githubusercontent.com/60209292/115719347-f5e91280-a3b6-11eb-88fc-fa0d017a4670.png)

- call by value : 값을 주소 즉 포인터로 판단

![image](https://user-images.githubusercontent.com/60209292/115719468-131de100-a3b7-11eb-8104-dc3eccd6bad7.png)









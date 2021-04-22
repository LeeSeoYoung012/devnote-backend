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




## 3.5 상속: 재사용 + 확장 
## 3.6 다형성: 사용편의성 
## 3.7 캡슐화: 정보은닉

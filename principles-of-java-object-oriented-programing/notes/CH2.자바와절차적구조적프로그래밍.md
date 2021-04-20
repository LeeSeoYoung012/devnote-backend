# Chapter2 자바와 절차적/구조적 프로그래밍


## 2.1 자바프로그래밍의 개발과 구동

 **JDK, JRE, JVM**

- 자바 개발 도구인 `` JDK ``를 이용해 개발된 프로그램은 `` JRE ``에 의해 가상 컴퓨터인 ``JVM`` 상에서 구동된다. 
-  `` JDK `` : 자바 개발 도구 , 개발자는 JDK를 이용해 프로그램을 개발한다. 
-  `` JRE `` : 자바 실행 환경, 가상 컴퓨터인 JVM을 제어하는 운영체제 
-  `` JVM `` : 자바 가상 기계, 운영체제에 영향을 받지 않고 중재자로서 각 플랫폼에서 프로그램을 구동

**프로그램이 메모리를 사용하는 방식** 

- 메모리에는 코드 ``코드 실행 영역``, ``스태틱 영역``, ``스택 영역``, ``힙 영역``이 들어있음.



## 2.2 자바에 존재하는 절차적 /구조적 프로그래밍의 유산 

**절차적 프로그래밍이란 : goto 를 쓰지 말라는 것** 

- goto를 사용하게 되면 프로그램의 실행 순서가 인간이 이해하기에 너무 복잡해질 가능성이 있다. 

**구조적 프로그래밍이란 : 함수를 쓰라는 것** 

- 함수를 쓰면 중복 코드를 한 곳에서 관리할 수 있다 
- 논리를 함수 단위로 분리해서 이해하기 쉬운 코드를 작성할 수 있다. 
- 구조적 프로그래밍의 지침 중에는 전역변수 보다 지역 변수 쓰라는 것도 있음 ( 공유 사용의 문제로 인해 ) 



## 2.3 메서드 스택 프레임

- main() 메서드가 실행 될 때 메모리에는 어떤 일이 일어날까?

- **예제1**

```java
public class Start {
	public static void main(String[] args) {
		System.out.println("Hello OOP!!!");
	}
}
```
**<1단계>**

![image](https://user-images.githubusercontent.com/60209292/115447181-231cb000-a253-11eb-8e90-e6bf15ff5e50.png)


- JRE 가 프로그램 안에 main() 메서드가 있는지 확인을 한다 
- JRE 가 JVM 에 전원을 넣어 부팅하고 부팅된 JVM은 목적파일을 받아 목적파일 실행 
- ``스태틱 영역``에 ``java lang 패키지``, ``import 된 패키지``, 프로그램상의 ``모든 클래스``를 가져다 놓는다. 

**<2단계>**

![image](https://user-images.githubusercontent.com/60209292/115447298-4d6e6d80-a253-11eb-97de-9323a9e1c993.png)


- 중괄호가 열리는 순간 ``스택 프레임``을 ``스택 영역``에 할당한다. 
- 메서드의 인자 args를 저장할 변수 공간을 ``스택 프레임의 맨 밑에`` 확보한다. 즉 메서드 인자들의 ``변수 공간``을 할당 한다. 

**<3단계>** 

![image](https://user-images.githubusercontent.com/60209292/115447376-670fb500-a253-11eb-9a70-816bbbd986ff.png)


- println() 이 실행된 후 ``닫는 중괄호``로 ``스택프레임이 소멸``된다. 
- main() 메서드가 끝나면 JRE는 JVM을 종료하고 JRE 자체도 운영체제 상의 메모리에서 사라진다. 


## 2.4 변수와 메모리

- **예제2**

```java
public class Start2 {
	public static void main(String[] args) {
		int i;
		i = 10;

		double d = 20.0;
	}
}
```

**<변수할당 전>**

<img src = "https://user-images.githubusercontent.com/60209292/115445501-e354c900-a250-11eb-9b76-9b18067db80c.png" width="400px">

**<변수할당 후>**

<img src = "https://user-images.githubusercontent.com/60209292/115445640-172fee80-a251-11eb-9b56-73c3b6b4c615.png" width="400px">


- 메인메서드 ``스택 프레임`` 안에 변수들이 저장 된다.

## 2.5 블록 스택 프레임

- **예제3**

```java
public class Start3 {
	public static void main(String[] args) {
		int i = 10;
		int k = 20;

		if(i == 10) {
			int m = k + 5;
			k = m;
		} else {
			int p = k + 10;
			k = p;
		}

		//k = m + p;
	}
}
```
**<If 문 실행 중 메모리>**

![image](https://user-images.githubusercontent.com/60209292/115445799-4e060480-a251-11eb-9433-72d54e60e28d.png)
![image](https://user-images.githubusercontent.com/60209292/115445928-74c43b00-a251-11eb-8871-05ebe80c7182.png)

**<If 문 끝나고 중괄호 벗어 낫을 때>**

![image](https://user-images.githubusercontent.com/60209292/115446022-9291a000-a251-11eb-9e34-4d73989e8037.png)


- if 문의 여는 중괄호를 만나면 if(true) 스택 프레임이 만들어진다. 
- if 문이 끝나면 if(true) 스택프레임은 없어진다.
- 위에 주석 부분에 k는 if(true) 혹은 else 블록이 없어진 후므로 존재하지 않는다. 



## 2.6 지역 변수와 메모리

**변수는 어디에 있는 걸까** 

- 답은 ``스태틱 영역``, ``스택 영역``, ``힙 영역`` 모두이다. 
- stack 영역: 지역 변수 
- static 영역: 클래스 멤버 변수 
- heap 영역: 객체 멤버 변수

**변수 접근성**

- **예제4**
```java
public class Start3 {
	public static void main(String[] args) {
		int i = 10;
		int k = 20;

		if(i == 10) {
			int m = k + 5;
			k = m;
		} else {
			int p = k + 10;
			k = p;
		}

		//k = m + p;
	}
}
```
- ``외부 스택 프레임에서 내부 스택 프레임의 변수에 접근하는 것은 불가능 하나 그 역은 가능하다``
- 위의 코드에서 if 문 안에서 main에서 선언된 k는 접근 할 수 있지만 if 문 밖에서 if 문 안에 있는 k는 접근하지 못한다. 



## 2.7 메서드 호출과 메모리

- **예제5**
```java
public class Start4 {
	public static void main(String[] args) {
		int k = 5;
		int m;

		m = square(k);
	}

	private static int square(int k) {
		int result;
		
		k = 25;

		result = k;

		return result;
	}
}

```
- main() 메서드 스택프레임과 square() 메서드 스택프레임은 별도로 저장이 된다. 
- 그래서 메서드 사이에 서로의 변수를 참조할 수 없다.

 ![image](https://user-images.githubusercontent.com/60209292/115449350-ad661380-a255-11eb-9435-38b452bce81e.png)
 

## 2.8 전역 변수와 메모리

**전역 변수는 안쓰는게 좋다** 

- 여러 메서드에서 전역 변수의 값을 변경하면 전여 변수에 저장되어 있는 값을 파악하기가 어렵다. 
- 수만 줄이 넘는 코드에 다른 메서드에 의해 전역 변수에 다른 값이 저장될 경우 코드를 추적해 들어가야만 그 값과 그 값이 바뀐 이유를 알 수 있다.

## 2.9 멀티 스레드 / 멀티 프로세스의 이해

- 프로세스는 독립적인 메모리공간을 갖는다. 스레드는 스택을 제외한 모든 공간을 공유한다. 
- 서블릿은 요청당 스레드를 생성한다. 
- 멀티스레드 환경에서 전역 변수를 사용하게 되면 의도하지 않는 값을 얻을 수 있다. 

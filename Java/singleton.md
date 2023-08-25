# Java vs Spring Singleton

> 관련 자료
> 
1. jvm 구조
2.  static 사용 지양하는 이유

java와 spring 프레임워크에서 구현하는 signleton 패턴은 차이점이 있다.

java에서는 싱글톤 패턴을 구현했을 때 전역 상태로 이용할 수 있는 장점이 있지만, 안티패턴으로도 불린다.

```java
// java에서 싱글톤 패턴 구현
public class Person {
	private staitc Person instance;
	
	private Person() {
		throw new IllegalStateException("Private Constructor");
	}

	public static Person getInstance() {
		if (instance == null) {
			instance = new Person();
		}
		return instance;
	}
}
```

싱글톤 패턴을 구현하는 방법은 다양하지만, 각가의 패턴이 공통적으로 갖는 특징이 있음.

- private 생성자만을 정의해 외부 클래스로부터 인스턴스 생성을 차단함.
- 싱글톤을 구현하고자 하는 클래스 내부에 멤버 변수로 private static 객체 변수를 만듦.
- public static 메소드를 통해 외부에서 싱글톤 인스턴스에 접근할 수 있도록 접점을 제공

[[생성 패턴] 싱글톤(Singleton) 패턴을 구현하는 6가지 방법](https://readystory.tistory.com/116)

## private 생성자를 갖고 있어 상속이 불가능함.

### Java

상속을 통해 다형성을 적용하기 위해 다른 기본 생성자가 필요하므로 객체지향의 장점을 적용 불가.

static필드와 static 메소드를 사용해야 함.

### Spring

private 생성자가 필요 없이 spring container가 싱글톤을 생성하고, 관리하고 공급해주기 때문에 객체지향의 장점을 적용할 수 있다.

## 테스트하기 힘듦

### Java

싱글톤은 생성 방식이 제한적이므로 mock 객체로 대체하기가 어려우며 동적으로 객체를 주입하기도 힘들다.

### Spring

spring container가 동적으로 객체를 주입해주므로 테스트하기 쉽다.

## 서버 환경에서는 싱글톤이 1개만 생성됨을 보장 못함

### Java

서버에서 클래스 로더를 어떻게 구성하느냐에 따라 싱글톤 클래스임에도 불구하고 1개 이상의 객체가 만들어질 수 있다.

→ 유저 정의 클래스 로더가 여러개일 경우, 클래스를 로드할 때 여러 클래스 로더에서 로드될 수 있어 객체가 여러개 만들어질 수 있다.

 따라서 서버 환경에서 싱글톤이 꼭 보장된다고 볼 수 없다.

### Spring

spring container가 싱글톤 객체의 생명주기를 관리하고, 주입해주므로 1개의 객체만 생성될 수 있다.

## 전역 상태를 만들 수 있기 때문에 객체지향적이지 않음.

### Java

싱글톤의 static 메소드를 사용하면 언제든지 해당 객체를 자유롭게 접근하고 수정되며 공유되는 전역상태로 사용되기 쉽다.

이는 객체지향의 특징인 캡슐화를 위반하며, 캡슐화는 객체 접근 제한자로 구현되는데, 이는 객체의 결합도를 낮추고 응집도를 높이는 특징이다.

왜냐하면 기능을 구현하는 객체들을 분리하고, 사이드 이펙트를 없앤다. 따라서 서로 연관성이 없는 객체들끼리 분리를 시키므로 응집도를 높이고 결합도를 낮출 수 있다.

그러나 전역상태가 되어버리면, 그러한 특징들이 없어지므로 객체지향적이지 않다. 

### Spring

spring container가 전역상태가 아니더라도 캡슐화를 지키면서 스프링 빈으로 싱글톤 객체를 관리하므로, 객체지향적으로 코드를 개발할 수 있다.

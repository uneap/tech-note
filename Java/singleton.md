# Java vs Spring Singleton

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

## 테스트하기 힘듦

### Java

### Spring

## 서버 환경에서는 싱글톤이 1개만 생성됨을 보장 못함

### Java

### Spring

### 의존성 주입

어떤 객체를 사용하는 주체가 객체를 직접 생성하는 게 아닌,

```java
public class Store {

    private Pencil pencil;

}
```

```java
public class Store {

    private Pencil pencil;

    public Store() {
        this.pencil = new Pencil();
    }

}
```

 객체를 외부에서 생성해서 사용하려는 주체 객체에 주입해주는 방식.

```java
public interface Product {

}

public class Pencil implements Product {

}
```

```java
public class Store {

    private Product product;

		@Autowired
    public Store(Product product) {
        this.product = product;
    }

}
```

직접 객체를 생성하는 방식은,

- 두 클래스가 강하게 결합되게 된다.
- 객체들 간의 관계가 아닌 클래스 간의 관계가 맺어진다.

### 두 클래스가 강하게 결합됨

클래스가 강하게 결합될 경우, 각각의 다른 상품들을 판매하기 위해 생성자만 다르고 나머지는 중복되는 Store 클래스들이 파생될 수 있어, 확장하기가 어렵다.

그렇다고, 코드 재사용을 목적으로 하는 클래스 상속은 개방-폐쇄 원칙을 위배한다.

→ 부모의 코드를 수정하기 위해 자식의 코드도 확인해야하며, 알지 못하는 사이드 이펙트가 있을 수 있다.

그리고 기능을 추가하기 위해, 부모의 자식들과 비슷한 코드를 한묶음 추가해야하는 불상사가 있을 수 있다.

### 객체들 간의 관계가 아닌 클래스 간의 관계가 맺어짐

개방 폐쇄 원칙을 만족하지 못하고, 결합도가 높으며, 응집도가 낮아진다.

만일 객체들간의 관계가 맺어졌다면, 인터페이스 타입으로 사용한다.

 코드 재사용이 목적이 아닌, 객체 간의 계층 관계를 표현하는 데 중점을 둘 수 있다.

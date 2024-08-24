# FP(Functional Programming)와 OOP(Object Oriented Programming) 패러다임의 차이

## 주요 차이점

1. 상태 관리 및 은닉화(캡슐화)

- FP : 함수 내의 클로저를 사용해서 상태를 관리 및 은닉하며, 상태 값을 복사하여 새 값을 돌려주는 방식
- OOP : 함수 내의 멤버 필드를 통해 상태를 관리 및 은닉하며, Getter 를 통해 값에 접근 가능

```js
const createCounter = () => {
  let count = 0;
  return () => {
    count += 1;
    return count;
  };
};

// 클로저로 은닉되어 count 값에 접근하기 위해서는 별도의 메서드 필요
```

```java
public static class Counter {
  private int count = 0;

  public void increment() {
    count++;
  }

  public int getCount() {
    return count;
  }
}

// count 는 private 값이므로 getter 로만 접근 가능
Counter counter = new Counter();
System.out.println(counter.getCount());
```

2. 코드 구조

- FP : 함수를 통해 객체를 생성하고 메서드를 정의
- OOP : 클래스를 이용하여 객체를 생성하고 메서드를 클래스 내부에 정의

```js
const circle = (radius) => ({
  area: () => Math.PI * radius * radius,
});

const myCircle = circle(5);
console.log(myCircle.area());
```

```java
public interface Shape {
    public double area() {};
}

public class Circle implements Shape {
    private double radius;

    public Circle(double radius) {
        this.radius = radius;
    }

    @Override
    public double area() {
        return Math.PI * radius * radius;
    }
}

Circle myCircle = new Circle(5);
System.out.println(myCircle.area());
```

3. 재사용성

- FP : 함수의 합성을 통해 재사용성 확보
- OOP : 클래스의 상속 또는 인터페이스의 구현을 통해 재사용성 확보

```js
// 검증 함수
const validateEmail = (input) => input.includes('@');
const validateLength = (input) => input.length >= 5;

// 여러 검증을 수행하는 함수
const emailValidators =
  (...validators) =>
  (input) =>
    validators.every((validator) => validator(input));

// 검증 함수 결합
const validate = emailValidators(validateEmail, validateLength);

// 검증 수행
console.log(validate('test@example.com'));
console.log(validate('test'));
console.log(validate('invalid-email.com'));
```

```java
public interface Validator {
    boolean validate(String input);
}

// 인터페이스 구현을 통해 구현된 검증 클래스 2
public class EmailValidator implements Validator {
    @Override
    public boolean validate(String input) {
        return input.contains("@");
    }
}

// 인터페이스 구현을 통해 구현된 검증 클래스 2
public class LengthValidator implements Validator {
    private int minLength;

    public LengthValidator(int minLength) {
        this.minLength = minLength;
    }

    @Override
    public boolean validate(String input) {
        return input.length() >= minLength;
    }
}

// 여러 검증을 수행하는 클래스
public class CombinedValidator implements Validator {
  private Validator[] validators;

  public CombinedValidator(Validator... validators) {
    this.validators = validators;
  }

  @Override
  public boolean validate(String input) {
    for (Validator validator : validators) {
      if (!validator.validate(input)) {
        return false;
      }
    }

    return true;
  }
}

// 테스트
public class Main {
    public static void main(String[] args) {
        Validator emailValidator = new EmailValidator();
        Validator lengthValidator = new LengthValidator(5);

        // EmailValidator와 LengthValidator를 결합
        Validator combinedValidator = new CombinedValidator(emailValidator, lengthValidator);

        System.out.println(combinedValidator.validate("test@example.com"));
        System.out.println(combinedValidator.validate("test"));
        System.out.println(combinedValidator.validate("invalid-email.com"));
    }
}
```

4. 테스트 용이성

- FP : 메서드를 직접 테스트
- OOP : 클래스의 멤버 변수와 메서드를 테스트

```js
const add = (a, b) => a + b;

test('add function', () => {
  expect(add(2, 3)).toBe(5);
});
```

```java
public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
}

@Test
public void testAdd() {
  Calculator calc = new Calculator();
  assertEquals(5, calc.add(2, 3));
}
```

4. 도메인 모델링

- FP : JS 시절에는 상황에 맞는 객체를 생성하여 사용, TS에 들어서는 필요 Interface 와 Type 을 정의하여 사용
- OOP : 도메인과 일치하는 Entity 혹은 VO(Value Object)를 만들어서 사용, 필요한 경우에는 DTO(Data Transfer Object) 를 만들어 사용

```js
type Email = string;

const isValidEmail = (value: string): boolean =>
  value != null && value.includes('@');

const createEmail = (value: string): Email => {
  if (!isValidEmail(value)) {
    throw new Error('이메일 양식이 올바르지 않습니다');
  }
  return value;
};

// 생성 및 출력
const userEmail1 = createEmail('tetz@example.com');
console.log(userEmail1);
```

```java
public class EmailVO {
  private final String value;

  public EmailVO(String value) {
      if (!isValid(value)) {
        log.error("이메일 양식이 올바르지 않습니다");
      }
      this.value = value;
  }

  private static boolean isValid(String value) {
      return value != null && value.contains("@");
  }

  public String getValue() {
      return value;
  }

// 생성 및 출력
  public static void main(String[] args) {
    Email userEmail1 = new Email("tetz@example.com");
    System.out.println(userEmail1);
  }
}
```

> 결론 : 결국 하나의 패러다임만 고집 부리면 고생한다 예약이요 ^0^!!

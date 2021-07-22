## Optional과 Null

NullPointerException은 자바를 이용해 개발을 하는 과정에서 많은 개발자들이 겪어본 예외 이다. 이러한 Null로 인해 발생하는 Null 문제점들을 개선하고자 만들어진 클래스다.

### Null의 문제점

```
 public String getCarInsuranceName(Person person) {
   return person.getCar().getInsurance().getName();
}
```

Null처리에 대한 설명으로 많이 사용되는 예제이다. 해당 코드에서는 NullPointerException이 발생하는 가능성을 가진 경우가 있다.

- 자동차를 소유하지 않은 사람
- 자동차는 소유했지만 보험이 없는 사람

### 1. 보수적인 자세로 NullPointerexception 줄이기

가장 간단하게 해당 코드에서 NullPointerException을 피하는 방법은 if문을 통해 처리하는 방식이다.

```
public String getCarInsuranceName(Person person) {
	if (person != null) {
		Car car = person.getCar();
		if (car != null) {
			Insurance insurance = car.getInsurance();
			if (insurance != null) {
				return insurance.getName();
			}
		}
	}
	return "Unknown";
}

```

하지만 위와 같은 코드는 좋은 구조를 갖지못하고 가독성이 떨어지게 된다.

```
public String getCarInsuranceName(Person person) {
	if (person != null) {
		return "Unknown";
	}

	Car car = person.getCar();
	if (car != null) {
		return "Unknown";
	}

	Insurance insurance = car.getInsurance();
	if (insurance != null) {
		return "Unknown";
	}

	return insurance.getName();
}

```

위 코드의 경우는 중첩 if 블록을 없애서 가독성 개선했으나 코드에 반복이 많아지는 문제가 있다.

### 2. Optional 패턴

- 빈 Optional

```
  // Optional.emplty로 빈 Optional 객체를 얻을 수 있다.
  Optional<Car> optCar = Optional.empty();
```

- null이 아닌 값으로 Optional 만들기

```
  // 이제 car가 null이라면 즉시 NullPointerException이 발생한다
  // (Optional을 사용하지 않았다면 car의 프로퍼티에 접근하려 할 때 에러가 발생한다.)
  Optional<Car> optCar = Optional.of(car);
```

- null값으로 Optional 만들기

```
  // car가 null이면 빈 Optional 객체가 반환된다.
  Optional<Car> optCar = Optional.ofNullable(car);
```

```
public class Person {
    private Car car;
    public Optional<Car> getCarAsOptional() {
        return Optional.ofNullable(car);
    }
}

public class Car {
    private Insurance insurance;
    public Optional<Insurance> getInsuranceAsOptional() {
        return Optional.ofNullable(insurance);
    }
}

public class Insurance {
    private String name;
    public String getName() {
        return name;
    }
}

public String getCarInsuranceName(Person person) {
    return Optional.of(person)
            .flatMap(Person::getCarAsOptional)
            .flatMap(Car::getInsuranceAsOptional)
            .map(Insurance::getName)
            .orElse("other value");
}
```

## 주의사항

IF문을 모두 Optional 바꾸는 경우 오히려 코드 가독성이 떨어지고 성능이 저하되는 경우가 발생할 수 있기 때문에 이를 적절하게 사용하는것이 가장 중요하다  
[Optional에 대한 성능이슈](https://stackoverflow.com/questions/34696884/performance-of-java-optional) => https://stackoverflow.com/questions/34696884/performance-of-java-optional

# 점진적인 리팩토링

## TDD, 리팩토링 연습 과정
### AS-IS
1. 학습 테스트 
    - JUnit 사용법 및 단위 테스트 연습
2. 단위 테스트 
    - 내가 구현한 코드에 대한 단위 테스트
3. TDD 
    - TDD 사이클이 익숙해질 때까지 같은 미션으로 반복 연습

### TO-BE - 점진적으로 리팩토링하기
#### 레거시 코드 리팩토링
> - 레거시 코드 리팩토링의 핵심은 기존 기능을 깨트리지 않으면서 리팩토링하는 것이다.

#### 점진적인 리팩토링이란?
- 기존의 Test Case가 깨지지 않는 상태로 리팩토링하기
- 컴파일 에러 발생을 최소화하면서 리팩토링하기

#### 기존 리팩토링 방식
- 우리가 마주하는 상황 1
  - 메소드에 인자가 추가되었다.
  - 메소드를 사용하는 모든 곳에서 컴파일 에러가 발생한다.
  - 테스트 코드 포함 컴파일 에러를 해결한다.
  - 테스트를 실행했더니 테스트가 깨지는 Test Case가 발생한다.
  - 디버깅을 한다.
- 우리가 마주하는 상황 2
  - 인스턴스 변수의 타입을 String에서 Integer로 변경했다.
  - 변수를 사용하는 모든 곳에서 컴파일 에러가 발생한다.
  - 테스트 코드 포함 컴파일 에러를 해결한다.
  - 테스트를 실행했더니 테스트가 깨지는 Test Case가 발생한다.
  - 디버깅을 한다.
- 위 방식으로 리팩토링을 하면 테스트에 대한 긍정적인 측면보다는 오히려 부정적인 측면이 생길 수 있다.
  - A 메서드를 수정하면, 사용하는 곳에서 side effect 가 생기는지 모두 확인해봐야 한다.
    - 테스트 코드에서도 해당 메서드를 호출하기 때문에 컴파일 에러의 수가 더 많아진다.
  - 시간이 너무 오래 걸린다.
  - 수정을 포기. 롤백. 인수에 저장해놨다가 까먹는다.
- 리팩토링을 생활화 하기 위해선?
  - 컴파일 에러 발생하는 시간이 적어야 한다.
  - 리팩토링한 코드가 바로 배포될 수 있어야 한다.
  - 리팩토링 시간이 짧게 걸려야 한다.

#### 우리가 연습해야할 리팩토링은?
1. 기존 코드에 컴파일 에러가 발생하지 않도록 리팩토링하는 연습을 해야 한다.
2. 리팩토링 과정에서 테스트 코드가 깨지지 않도록 리팩토링하는 연습을 해야 한다.

#### 악행을 저질러야 한다. (과도기 단계)
- 위 두 가지 원칙을 지키면서 리팩토링을 하려면 **과도기적인 중간 단계**가 있어야 한다.

1. 메소드에 인자가 추가되는 경우
    - 변경하려는 메소드를 그대로 복사해 메소드 이름을 임시로 변경한다.
    - 예를 들어 match() 메소드라면 match2()와 같이 임시로 구현다.
    - match() 메소드를 사용하는 모든 코드가 match2()를 사용하도록 변경한다.
      - 한 번에 하나씩 이동한다. (하나에 30분)
      - 서로 중복된 코드인데, 모두 동작하는 코드이므로 실서비스에 당장 배포해도 된다.
    - match() 메소드를 사용하는 코드가 더 이상 존재하지 않으면 match()를 제거한다.
    - match2() 메소드를 match()로 이름을 변경한다.
2. 인스턴스 변수의 타입이 변경되는 경우
    - 새로운 타입의 인스턴스 변수를 추가한다.
    - 예를 들어, String에서 Integer로 변경한다면 일시적으로 두 개의 타입을 동시에 가진다.
      - DB 리팩토링에서도 동일하게 쓸 수 있다.
      - 데이터를 2중으로 쌓는다.
    - 두 개의 변수에 동시에 데이터를 할당하고 변경하도록 변경한다.

- 각 리팩토링 성격에 따라 컴파일 에러를 발생시키지 않고, Test Case를 실패하지 않으면서 리팩토링하는 방법을 찾아야 한다.

## 로또 - 점진적인 리팩토링
### 점진적인 리팩토링 - TDD에 대한 감이 생길 때 한번씩 도전
#### 1. 1단계 - 메소드를 다른 클래스로 이동
```java
public class LottoGame {
  public static int match(List<Integer> userLotto, List<Integer> winningLotto, int bonusNo) {
    ...
    int matchCount = match(userLotto, winningLotto);
    boolean matchBonus = userLotto.contains(bonusNo);

    return rank(matchCount, matchBonus);
  }

  private static int rank(int matchCount, boolean matchBonus) {
    if (matchCount == 6) {
      return 1;
    }
    if (matchCount == 5 && matchBonus) {
      return 2;
    }
    if (matchCount > 2) {
      return 6 - matchCount + 2;
    }
    return 0;
  }
  ...
}
```
- 리팩토링 단계
  1. TDD로 새로운 클래스에 이동할 메소드를 구현한다. e.g. `match2()`
  2. Test 코드를 작성한다. e.g. 1등에 대한 테스트 코드 먼저 작성
  3. Test 코드가 통과할 수 있는 프로덕션 코드를 작성한다. e.g. `Rank` 클래스
  4. 2~6 등까지 2번, 3번을 반복해서 구현한다.
      - 메서드를 이동하다 보면 중복된 테스트 코드들이 생긴다.
      - 그럼 이 중 몇개를 테스트 할지에 대해 생각해봐야 한다.
  5. 레거시 코드에서 TDD로 구현한 메소드를 호출한다.
      - `match()`를 사용하던 곳을 찾아 `match2()`로 하나씩 수정하면서 테스트 코드를 수행해본다.
      - 테스트 케이스는 내가 불안감을 없앨 수 있는 정도까지만 관리하는 것이 좋다.
  6. 사용하지 않게 된 메소드를 삭제하고 리팩토링을 진행한 메소드의 이름을 원래의 이름으로 변경한다.

#### 2. 2단계 - 메소드에 인자 타입이나 수, 반환 값이 변경되는 경우
- 규칙 3: 모든 원시값과 문자열을 포장한다.
  ```java
  public class LottoGame {
    public static int match(List<Integer> userLotto, List<Integer> winningLotto, LottoNumber bonusNo) {
      ...
    }
  }
  ```
- 메소드 인자의 수를 줄인다.
  ```java
  public class LottoGame {
    public static int match(Lotto userLotto, Lotto winningLotto, LottoNo bonusNo) {
      ...
    }
  }
  ```
  ```java
  public class LottoGame {
    public static int match(Lotto userLotto, WinningLotto winningLotto) {
      ...
    }
  }
  ```

#### 3. 3단계 - 인스턴스 변수(필드)의 타입이 변경되는 경우 (가장 높은 난이도)
```java
public class Lotto {
  private static final int LOTTO_SIZE = 6;
  private final List<Integer> numbers;

  public Lotto(List<Integer> numbers) {
    ...
  }
  ...
}
public class Lotto {
  private static final int LOTTO_SIZE = 6;
  private final List<LottoNumber> numbers;

  public Lotto(List<Integer> numbers) { // 이 생성자를 쓰는 곳에서는 문제가 발생하지 않는다.
  ...
  }
  ...
}
```
```java
public class Lotto {
  private static final int LOTTO_SIZE = 6;

  private final List<Integer> numbers;
  private final List<LottoNumber> numbers2;

  public Lotto(List<Integer> numbers) { 
    ...
    this.numbers = numbers;
    this.numbers2 = numbers.stream()
          .map(n -> new LottoNumber(n))
          .collect(Collectors.toList()); // 점진적으로 리팩토링 진행
  }
  ...
}
```
- 점진적으로 리팩토링 하는 역량을 꾸준히 쌓는 것이 지금까지 학습한 TDD, 리팩토링, 클린코드, OOP가 현장의 애플리케이션 개발에 의미를 가지는 유일한 길이다. 
  - 인자, 메서드의 중복을 이용하여 점진적으로 리팩토링을 한다.
- 시간이 날 때마다 해야 하는 것이다.


---

### :game_die: [Lotto Home](https://github.com/gmlwjd9405/tdd-refactoring-clean-code-8/tree/master/study/java-lotto)

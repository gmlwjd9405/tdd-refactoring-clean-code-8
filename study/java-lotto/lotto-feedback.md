# java-lotto 피드백

## TDD, 리팩토링 연습 단계
### AS-IS
1. 학습 테스트 
- JUnit 사용법 및 단위 테스트 연습
2. 단위 테스트 
- 내가 구현한 코드에 대한 단위 테스트
3. TDD 
- TDD 사이클을 맛보기 단계

### TO-BE
- TDD 
  - TDD 사이클이 익숙해질 때까지 같은 미션으로 반복 연습
  - 사이클이 익숙해지기 전에 새로운 미션을 하면 요구사항부터 클래스 설계부터 새로 해야되기 때문에 TDD 사이클이 깨지기 쉽다.

## Lotto 피드백
### 기능 요구사항
- 로또 구입 금액을 입력하면 구입 금액에 해당하는 로또를 발급해야 한다.
- 로또 1장의 가격은 1000원이다.
```
구입금액을 입력해 주세요.
14000
14개를 구매했습니다.
[8, 21, 23, 41, 42, 43]
[...]
[3, 8, 27, 30, 35, 44]
지난 주 당첨 번호를 입력해 주세요.
1, 2, 3, 4, 5, 6
보너스 볼을 입력해 주세요.
7
당첨 통계
---------
3개 일치 (5000원)- 1개
4개 일치 (50000원)- 0개
5개 일치 (1500000원)- 0개
5개 일치, 보너스 볼 일치(30000000원) - 0개
6개 일치 (2000000000원)- 0개
총 수익률은 0.35입니다.(기준이 1이기 때문에 결과적으로 손해라는 의미임)
```

### 시작하기
- 요구사항 분석을 통한 기능 목록 작성
- 객체설계를 통해 어느 부분부터 구현을 시작할 것인지 결정
  - 가장 안쪽의 **작은 객체**로 TDD를 시작하는 것이 쉽다.

### 기능 목록
- 구매할 Lotto의 매수 구하기
  - 1000 -> 1, 1500 -> 1, 500 -> error
- 한장의 Lotto 생성
- 당첨 번호 생성
  - 정상적인 당첨번호 입력
  - 유효하지 않은 당첨번호
- 한장의 Lotto에 대한 당첨 결과 구하기
- n장의 Lotto에 대한 당첨 결과 구하기
- Lotto 결과에 따른 수익률 구하기
- ...

### TDD로 구현할 기능 찾기
- 구현 중간 부분을 자르는 연습을 해야 한다.
  - 로또 구매 금액을 전달하면 구매할 수 있는 로또의 장수를 반환한다.
  - 구매한 로또와 당첨 번호를 넣으면 당첨 결과를 반환한다.
  - 당첨 결과를 입력하면 당첨금 총액을 반환한다.
  - 당첨 금액과 구매 금액을 넣으면 수익률을 반환한다.
- 구현 중간 부분을 자른다는 것은 로또 구현에 필요한 메소드를 찾는 과정이다.

### # TDD 연습 예시) 한장의 Lotto에 대한 당첨 결과 구하기
- 오늘 강의는
  - **객체 설계 경험이 부족한 주니어 개발자**이거나, **객체 설계가 부족한 레거시 코드가 존재**하는 상황을 가정한 내용이다.

- 시작하기
  - 객체 설계를 어떻게 해야할지 모르겠다면 시작은 클래스 메소드 구현으로 시작한 후 지속적인 리팩토링
  - 리팩토링할 때는 객체지향 생활체조 원칙, 클린코드 원칙을 참고해 리팩토링

```java
public class LottoGame {
  public static int match(List<Integer> userLotto, List<Integer> winningLotto, int bonusNo) {
    // TODO 인자에 대한 유효성 체크
    // * 로또 한 장은 6개의 번호여야 한다.
    // * 6개의 각 숫자는 1에서 45 사이의 값이어야 한다.
    // * 6개의 값은 중복된 값이 있으면 안된다.
    // * 당첨 번호의 경우는 보너스 번호가 1에서 45 사이의 값이어야 하여, 보너스 번호는 당첨 번호 6개의 값이 아니어야 한다.
    int matchCount = match(userLotto, winningLotto);
    if (matchCount == 6) {
      return 1;
    }
    boolean matchBonus = userLotto.contains(bonusNo);
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

### # 리팩토링. 어디서 어떻게 시작할 것인가?
#### 메소드 분리
1. 들여쓰기(indent)를 줄이는 방법
  - 규칙 1: 한 메서드에 오직 한 단계의 들여쓰기(indent)만 한다.
  - 규칙 2: else 예약어를 쓰지 않는다.
  - 메소드를 분리한다.
2. 함수(또는 메소드)가 한 가지 일만 잘 하도록 구현한다.
  - 정량적인 기준을 만들어 연습한다.
  - 함수(또는 메소드)의 길이가 15라인을 넘어가지 않도록 구현한다.
  - 메서드 네이밍 

#### 클래스 분리
1. 규칙 3: 모든 원시값과 문자열을 포장한다.
  ```java
  public class LottoGame {
    public static int match(List<Integer> userLotto, List<Integer> winningLotto, LottoNumber bonusNo) {
      ... // LottoNumber 는 1~45의 값을 갖는다.
    }
  }
  ```
  ```java
  public class LottoGame {
    public static int match(List<LottoNumber> userLotto, List<LottoNumber> winningLotto, LottoNumber bonusNo) {
      ...
    }
  }
  ```
2. 규칙 7: 3개 이상의 인스턴스 변수를 가진 클래스를 쓰지 않는다.
3. 규칙 8: 일급 콜렉션을 쓴다.
  ```java
  public class LottoGame {
    public static int match(Lotto userLotto, Lotto winningLotto, LottoNumber bonusNo) {
      ... // 유효한지 체크를 Lotto로 이동: 중복없는 6개의 값(Lotto가 생성될 때)
    }
  }
  ```
4. 메소드(함수)에 인자 수를 최소화한다.
  - 메소드(함수)에서 이상적인 인자 개수는 0개(무항)이다. 다음은 1개이고, 다음은 2개이다.
  - 3개는 가능한 피하는 편이 좋다. 4개 이상은 특별한 이유가 있어도 사용하면 안된다.
  ```java
  public class LottoGame {
    public static int match(Lotto userLotto, WinningLotto winningLotto) {
      ... // 관련성 있는 인자를 묶어 하나의 클래스로 만든다.
    }
  }
  ```
5. **private method를 분리해서 단위 테스트해야 하는 것인 아닌가?** 라는 의구심
  - private에 해당하는 메서드를 하나의 클래스를 만들어 처리할 수 있는지 생각
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
  ```java
  public class LottoGame {
    // Rank enum 의 값만 반환
    public static Rank match(List<Integer> userLotto, List<Integer> winningLotto, int bonusNo) {
    ...
    int matchCount = match(userLotto, winningLotto);
      boolean matchBonus = userLotto.contains(bonusNo);
      return Rank.of(matchCount, matchBonus);
    }
    ...
  }
  ```

### # 클래스간의 의존관계 연결
- 클래스를 분리할 때 또는 클리스를 분리한 후
  - **고민해야할 부분은 클래스간의 의존관계를 어떻게 연결할 것인가?**이다.

#### 상속(is-a 관계)과 조합(has-a 관계)
- 일급 collection을 구현할 때 접근 방법으로 상속과 조합 방법으로 구현할 수 있다.
- 객체의 중복(Lotto와 WinningLotto)을 제거할 때 상속과 조합 방법으로 구현할 수 있다.

- e.g. **첫 번째 예제**
  - 상속(is-a)을 통한 구현
    ```java
    import java.util.ArrayList;
    import lotto.dto.LottoResult;
    public class Lottos extends ArrayList<Lotto> { 
      public LottoResults match(Lotto winningLotto) {
        LottoResults lottoResults = new LottoResults();
        this.stream()
          .map(lotto -> new LottoResult(lotto.getCorrectCount(winningLotto.getNumbers())))
              .forEach(lottoResults::add);
        return lottoResults;
      }
    }
    ```
    - Lottos 가 제공하는 API는 ArrayList 가 제공하는 모든 API 까지 외부에 공개하게 된다. (불필요한 api 까지 모두 제공)
  - 조합(has-a)을 통한 구현
    ```java
    import java.util.List;
    import lotto.dto.LottoResult;
    public class Lottos {
      private List<Lotto> lottos; // has-a

      public Lottos(List<Lotto> lottos) {
        this.lottos = lottos;
      }
      public LottoResults match(Lotto winningLotto) {
        LottoResults lottoResults = new LottoResults();
        lottos.stream()
          .map(lotto -> new LottoResult(lotto.getCorrectCount(winningLotto.getNumbers())))
            .forEach(lottoResults::add);
        return lottoResults;
      }
    }
    ```
    - 코드가 조금 더 많아지는 단점이 있다.

- e.g. **두 번째 예제**
  - 상속(is-a)을 통한 구현
    ```java
    public class WinningLotto extends Lotto {
      private LottoNumber bonusNumber;

      public WinningLotto(List<Integer> numbers, int bonusNumber) {
        super(numbers);
        this.bonusNumber = new LottoNumber(bonusNumber);
      }
    }
    ```

  - 조합(has-a)을 통한 구현
    ```java
    public class WinningLotto {
      private Lotto winningLotto;
      private LottoNumber bonusNumber; // has-a

        public WinningLotto(Lotto winningLotto, int bonusNumber) {
          this.winningLotto = winningLotto;
          this.bonusNumber = new LottoNumber(bonusNumber);
      }
    }
    ```

#### 상속(is-a 관계)과 조합(has-a 관계) 중 어느 방법으로 구현하는 것이 더 좋을까?
- [Favor Object Composition over Class Inheritance from Design Patterns]() 책에서 코드의 재사용성(Reusability) 측면에서는 상속이 유리하지만 유연성(Flexiblity) 측면에서는 조합이 더 유리하다.
  - 인터페이스 상속은 추천!
  - 클래스 상속의 경우, 
    - 상속: 부모가 변화가 생기면 자식 클래스에서도 영향을 받는다.
    - 조합: **변화에 빠르게 대응하는 것**이 점점 더 중요해지고 있는 현재는 재사용성보다 유연성(조합)이 훨씬 더 중요하다.

### # 추가 리팩토링
#### 1. 생성자 대신 정적 팩토리 메서드를 사용하라.
- 생성자가 2개 이상인 경우는 정적 팩토리 메서드를 사용하라.
  - 생성자를 private 로 변경
  - 여러가지 형태의 데이터를 전달해 인스턴스를 생성할 수 있도록 정적 팩토리 메서드를 제공하면 api를 사용하는 측면에서 좋다.
- e.g. 규칙 3: 모든 원시값과 문자열을 포장한다.
  ```java
  public class LottoNumber {
    public static final int LOTTO_NUMBER_MIN = 1;
    public static final int LOTTO_NUMBER_MAX = 45;

    private final int number;
      public LottoNumber(String text) {
      this(Integer.parseInt(text));
    }
    public LottoNumber(int number) {
      if (number < LOTTO_NUMBER_MIN || number > LOTTO_NUMBER_MAX) {
        throw new IllegalArgumentException("로또 번호의 범위를 벗어났습니다.");
      }
      this.number = number;
    }
    ... // equals, hashCode, toString 메소드
  }
  ```

- 정적 팩토리 메서드 장점
  - 이름을 가짐으로써 객체 생성의 의도를 드러낼 수 있다.
  - 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다. - singleton 패턴 적용할 경우
  - 반환 타입의 하위 타입 객체를 반환할 수 있다.
  - 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.
- 정적 팩토리 메소드 명명 규칙 - [Effective Java]()에서는 다음과 같이 제시
  - `valueOf(), of()`: 인자로 전달되는 값에 해당하는 인스턴스를 반환. type-conversion method
  - `getInstance(), newInstance()`: 보통 자신과 같은 Type의 인스턴스를 반환. singleton 패턴에서 사용
  - `getType(), newType()`: 자신과 다른 Type의 인스턴스를 반환

#### 2. mutable object(가변 객체)보다 immutable object(불변 객체) 기반으로 구현하라.
- 버그 발생 가능성이 적어진다.
- **mutable object(가변 객체) vs immutable object(불변 객체)**
  - 가변 객체: 초기화 후 변경될 수 있는 객체 
    - List Collection을 가지면 가변 객체가 될 확률이 높아진다.
- 다음 객체는 불변 객체인가?
  ```java
  public class Positive {
    private int number;

    public Positive(int number) {
      if (number < 0) {
        throw new RuntimeException();
      }
      this.number = number;
    }
    public Positive add(Positive positive) { // 변경 가능
      this.number += positive.number;
      return this;
    }
  }
  ```
  ```java
  public class WinningLotto {
    private final Lotto lotto; // Lotto도 불변객체가 맞는지 확인해봐야 한다.
    private final LottoNumber bonusNo;

    public WinningLotto(Lotto lotto, int no) {
      this.bonusNo = LottoNumber.of(no);
      if (lotto.contains(bonusNo)) {
        throw new IllegalArgumentException();
      }
      this.lotto = lotto;
    }
    public Rank match(Lotto userLotto) {
      int matchCount = lotto.match(userLotto);
      boolean matchBonus = userLotto.contains(bonusNo);
      return Rank.valueOf(matchCount, matchBonus);
    }
  }
  ```
- 불변 객체 만들기
  - 객체의 상태를 변경하는 메소드를 제공하지 않는다.
  - 클래스를 확장할 수 없도록 한다.(public final class)
  - 모든 필드를 final로 선언한다.
    - Collection 이 있는 경우에는 final 로 선언해도 불변 객체를 보장하는 것은 아니다.
  - 모든 필드를 private으로 선언한다.
- 불변 객체의 단점
  - immutable object 적용과 관련해 종종 듣는 질문
    - immutable object(불변 객체)가 좋은 것은 알겠는데 인스턴스가 너무 많이 생성되어 성능이 떨어지는 것은 아닌가?
  - **캐싱**을 적용해 인스턴스 생성을 최소화할 수 있는 방법이 있는지 검토한다.
    ```java
    public class LottoNumber {
      public static final int LOTTO_NUMBER_MIN = 1;
      public static final int LOTTO_NUMBER_MAX = 45;

      private static final Map<Integer, LottoNumber> lottoNos = new HashMap<>(); // 캐싱
      static {
        for (int i = LOTTO_NUMBER_MIN; i <= LOTTO_NUMBER_MAX; i++) {
          lottoNos.put(i, new LottoNumber(i));
        }
      }
      private final int no;

      private LottoNumber(int no) {
        this.no = no;
      }
      static LottoNumber of(int number) {
        return Optional.ofNullable(lottoNos.get(number))
          .orElseThrow(IllegalArgumentException::new);
      }
    }
    ```
    ```java
    public class LottoNumberTest {
      @Test
      public void create() {
        assertThat(of("8")).isEqualTo(of(8)); // 서로 다른 인스턴스여도 값이 동일
        assertThat(of("8") == of(8)).isTrue(); // 메모리 상의 주소까지 동일
      }
    }
    ```
    - 일정값을 캐싱하여 재사용한다.
      - e.g. Integer 클래스 

#### 3. 성능 고려
- 성능을 고려하면서 구현하는 습관은 좋은 습관이다.
  - 하지만 성능만 고려하는 것은 아닌지 한번쯤 의구심을 가져봐야 한다.
- 성능 외에도
  - **유지보수하기 좋은 코드**, 
  - **읽기 좋은 코드**, 
  - **유연한 코드**, 
  - **버그 발생 가능성이 낮은 코드**
  - 와 같이 다양한 측면을 고려하면서 프로그래밍하는 습관을 가지는 것이 더 중요하다.


---

### :game_die: [Lotto Home](https://github.com/gmlwjd9405/tdd-refactoring-clean-code-8/tree/master/study/java-lotto)

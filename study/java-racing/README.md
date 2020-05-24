## java-racing 학습 내용

### :heavy_check_mark: 목표
- Github 기반으로 온라인 코드 리뷰하는 경험
- JUnit 사용법을 익혀 단위 테스트하는 경험
- 자바 code convention을 지키면서 프로그래밍하는 경험
- 메소드를 분리하는 리팩토링 경험

### :heavy_check_mark: 강의
- 의식적인 연습과 학습 테스트
  - [내용 정리](./conscious-practice-and-learning-test.md)
- 자동차 경주 게임 피드백 
  - [내용 정리](./racingcar-feedback.md)
- TDD 로 자동차 경주 게임 구현 
  - [내용 정리](../java-lotto/tdd-racingcar.md)

### :heavy_check_mark: 리뷰 및 학습 내용 
- equals, hashCode 
  - equals 메소드: 논리적 동치성(logical equality)를 비교해야할 때 재정의
    - equals는 일반 규약을 지켜 재정의하라
    - 두 객체의 내용이 같은지, 즉 객체 내 값이 같은지 비교
    - Map의 **키**나 Set의 **요소**로 해당 객체를 저장하려고 사용하려면 같은 값이 객체가 이미 있는지 비교해야 하기 때문에 재정의가 필요하다.
    - equals 를 재정의 해야 하는 이유?
        - Object의 equals 메소드는 두 객체의 참조만을 비교하고 있기 때문 (== 비교 연산자 사용)
  - hashCode 메소드: 두 객체가 같은 객체인지, 동일성(identity)를 비교해야할 때 재정의
      - 참조하는 메모리 주소가 같은지 
- equals를 재정의하려거든 hashCode도 재정의하라
  - c.f. equals와 hashCode는 모두 VO(Value Object)에서만 사용하는 것을 권장
    - https://nesoy.github.io/articles/2018-06/Java-equals-hashcode
    - https://jojoldu.tistory.com/134
    - https://aroundck.tistory.com/244
    - https://ssoco.tistory.com/62
    - http://egloos.zum.com/hahaha333/v/3906967
- assertThat 활용
  - isSameAs == assertSame
  - isEqualTo == assertEqual
  - isSameAs VS isEqualTo
    - https://stackoverflow.com/questions/5535777/what-is-the-difference-between-issameas-and-isequalto-in-fest
  - isZero
  - isTrue
  - containsExactly
  - containsAnyOf
  - hasSize
- assertThatCode
  - doesNotThrowAnyException
- 예외 처리 테스트 코드 
  - assertThatThrownBy
  - assertThatExceptionOfType
  - assertThatIllegalArgumentException

- `public List<Integer> getLottoNumbers() { return new ArrayList<>(this.lottoNumbers.toList()) };`
  - lottoNumbers의 불변성을 유지

- Q. TDD 를 진행하면서 (test -> feat -> refactor) 중 리팩토링 과정에서 private method 가 새로운 클래스의 public method로 빠질 때가 있습니다. 그럼 이 경우엔 public method로 빼고 테스트 코드를 작성하게 되는 것인데, (feat -> test)(?) 순서라고 생각됩니다. TDD 를 진행하면서 이런 순서로 작업하게 되는 경우가 자연스러운 일인가요??
  - e.g. StringUtil.isEmpty()



---

### :house: [Go Home](https://github.com/gmlwjd9405/tdd-refactoring-clean-code-8)
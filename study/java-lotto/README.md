## java-lotto 학습 내용

### :heavy_check_mark: 목표
- TDD 기반으로 프로그래밍하는 경험
- 메소드 분리 + 클래스를 분리하는 리팩토링 경험
  - 로또 구현을 통해 단위 테스트, 리팩토링, 객체 지향 프로그래밍 연습을 한다.
  - 클래스, 메서드 이름을 통해 의도를 드러내 다른 사람이 읽기 좋은 코드를 구현하는 경험을 한다.
- 점진적으로 리팩토링하는 경험 

### :heavy_check_mark: 강의
- Clean Code 가이드 
  - [내용 정리](./clean-code-guide.md)
- TDD 로 자동차 경주 게임 구현 
  - [내용 정리](./tdd-racingcar.md)
- 로또 피드백 
  - [내용 정리](./lotto-feedback.md)
- 점진적인 리팩토링 
  - [내용 정리](./progressive-refactoring.md)

### :heavy_check_mark: 리뷰 및 학습 내용 
- private static final Pattern STRING_TOKEN_PATTERN = Pattern.compile("//(.)\\n(.*)");
  - 만드는데 메모리나 시간이 오래 걸리는 객체 즉 "비싼 객체"를 반복적으로 만들어야 한다면 캐시해두고 재사용할 수 있는지 고려하는 것이 좋다.
  - https://github.com/keesun/study/blob/master/effective-java/item6.md
- find, matches 차이
- unmodifiableList

---

### :house: [Go Home](https://github.com/gmlwjd9405/tdd-refactoring-clean-code-8)
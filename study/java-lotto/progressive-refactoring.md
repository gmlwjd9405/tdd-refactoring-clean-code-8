# 점진적인 리팩토링

TDD, 리팩토링 연습 과정
AS-IS
학습 테스트 - JUnit 사용법 및 단위 테스트 연습
단위 테스트 - 내가 구현한 코드에 대한 단위 테스트
TDD - TDD 사이클이 익숙해질 때까지 같은 미션으로 반복 연습
TO-BE - 점진적으로 리팩토링하기
레거시 코드 리팩토링
레거시 코드 리팩토링의 핵심은 기존 기능을 깨트리지 않으면서 리팩토링하는 것이다.
점진적인 리팩토링이란?
기존의 Test Case가 깨지지 않는 상태로 리팩토링하기
컴파일 에러 발생을 최소화하면서 리팩토링하기
우리가 마주하는 상황 1
메소드에 인자가 추가되었다.
메소드를 사용하는 모든 곳에서 컴파일 에러가 발생한다.
테스트 코드 포함 컴파일 에러를 해결한다.
테스트를 실행했더니 테스트가 깨지는 Test Case가 발생한다.
디버깅을 한다.
우리가 마주하는 상황 2
인스턴스 변수의 타입을 String에서 Integer로 변경했다.
변수를 사용하는 모든 곳에서 컴파일 에러가 발생한다.
테스트 코드 포함 컴파일 에러를 해결한다.
테스트를 실행했더니 테스트가 깨지는 Test Case가 발생한다.
디버깅을 한다.
위 방식으로 리팩토링을 하면 테스트에 대한 긍정적인 측면보다는 오히려 부정적인 측면이 생길 수 있
다.
우리가 연습해야할 리팩토링은?
기존 코드에 컴파일 에러가 발생하지 않도록 리팩토링하는 연습을 해야 한다.
리팩토링 과정에서 테스트 코드가 깨지지 않도록 리팩토링하는 연습을 해야 한다.
위 두 가지 원칙을 지키면서 리팩토링을 하려면 과도기적인 중간 단계가 있어야 한다.
메소드에 인자가 추가되는 경우
변경하려는 메소드를 그대로 복사해 메소드 이름을 임시로 변경한다.
예를 들어 match() 메소드라면 match2()와 같이 임시로 구현다.
match() 메소드를 사용하는 모든 코드가 match2()를 사용하도록 변경한다.
match() 메소드를 사용하는 코드가 더 이상 존재하지 않으면 match()를 제거한다.
match2() 메소드를 match()로 이름을 변경한다.
인스턴스 변수의 타입이 변경되는 경우
새로운 타입의 인스턴스 변수를 추가한다.
예를 들어 String에서 Integer로 변경한다면 일시적으로 두 개의 타입을 동시에 가진다.
두 개의 변수에 동시에 데이터를 할당하고 변경하도록 변경한다.
각 리팩토링 성격에 따라 컴파일 에러를 발생시키지 않고,
Test Case를 실패하지 않으면서 리팩토링하는 방법을 찾아야 한다.
로또 - 점진적인 리팩토링
점진적인 리팩토링 - TDD에 대한 감이 생길 때 한번씩 도전
1단계 - 메소드를 다른 클래스로 이동
2단계 - 메소드에 인자 타입이나 수, 반환 값이 변경되는 경우
3단계 - 인스턴스 변수(필드)의 타입이 변경되는 경우
1단계 - 메소드를 다른 클래스로 이동
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
리팩토링 단계
TDD로 새로운 클래스에 이동할 메소드를 구현한다.
레거시 코드에서 TDD로 구현한 메소드를 호출한다.
2단계 - 메소드에 인자 타입이나 수, 반환 값이 변경되는 경우
규칙 3: 모든 원시값과 문자열을 포장한다.
public class LottoGame {
public static int match(List<Integer> userLotto, List<Integer> winningLotto, LottoNumber bonusNo) {
...
}
}
메소드 인자의 수를 줄인다.
public class LottoGame {
public static int match(Lotto userLotto, Lotto winningLotto, LottoNo bonusNo) {
...
}
}
public class LottoGame {
public static int match(Lotto userLotto, WinningLotto winningLotto) {
...
}
}
3단계 - 인스턴스 변수(필드)의 타입이 변경되는 경우
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
public Lotto(List<Integer> numbers) {
...
}
...
}
점진적으로 리팩토링 하는 역량을 꾸준히 쌓는 것이
지금까지 학습한 TDD, 리팩토링, 클린코드, OOP가
현장의 애플리케이션 개발에 의미를 가지는 유일한 길이다.

---

### :game_die: [Lotto Home](https://github.com/gmlwjd9405/tdd-refactoring-clean-code-8/tree/master/study/java-lotto)

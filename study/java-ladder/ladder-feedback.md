# java-ladder 피드백

사다리타기 TDD로 구현
Out -> In 접근 방식 vs In -> Out 접근 방식
Out -> In 접근 방식은 도메인 지식이 없거나 요구사항 복잡도가 높은 경우 적합
In -> Out 접근 방식은 도메인 지식이 있거나 요구사항이 단순한 경우 적합
Out -> In 접근 방식으로 구현
요구사항 분석 후 도메인 객체 설계
Ladder와 Line 중 어디부터 구현을 시작하는 것이 좋을까?
위 다이어그램에서 Out의 시작점은 어느 곳을 의미할까?
Out부터 In으로 구현
Ladder를 생성하는 부분과 생성한 Ladder를 실행(사다리 타기)하는 부분을 분리
TDD 구현시 특정 상태를 분리해 시작하는 것이 중요
Ladder를 생성하는 부분은 무시하고, Ladder를 실행(설계에서 move)하는 부분에서 시작
테스트하기 어려움. 바로 포기
기존 코드를 삭제하고 Line move()에서 다시 시작
도메인 지식을 쌓은 후 더 작은 단위로 분리할 객체는 없을까?
객체 설계 사이클
In부터 Out으로 구현
가장 작은 단위의 객체(In)부터 기능을 추가하면서 Out 방식으로 완성해 나간다.
시작 단계에서 가장 작은 단위의 객체를 찾는 것이 쉽지 않다.
애플리케이션 개발 단계에서 Out -> In, In -> Out 방식 중 하나만 사용되지 않는다.
두 방식이 핑퐁처럼 주고 받으며 개발이 진행된다.
Out -> In으로 시작
도메인 지식으로 설계할 수 있는 부분까지 설계
도메인 설계에서 Object Graph 중
가장 마지막 노드(의존성을 가지는 객체가 하나도 없는 객체)부터 구현을 시작하는 것 이
TDD로 접근하기 수월하다.
class Line {
private final List<Boolean> points;
Line(List<Boolean> points) {
// 유효한 값인지 체크하는 로직
this.points = points;
}
public int move(int position) {
// 각 라인별로 이동하는 로직 구현
}
}
도메인 지식을 쌓은 후 더 작은 단위로 객체 분리
class Point {
private final int index;
private final boolean left;
private final boolean current;
Point(int index, boolean left, boolean current) {
if (left && current) {
throw new IllegalArgumentException();
}
this.index = index;
this.left = left;
this.current = current;
}
int move() {
if (this.left) {
return index - 1;
}
if (this.current) {
return index + 1;
}
return index;
}
...
}
도메인 지식을 쌓은 후 더 작은 단위로 객체 분리
class Direction {
private final boolean left;
private final boolean current;
private Direction(final boolean left, final boolean current) {
if (left && current) {
throw new IllegalArgumentException();
}
this.left = left;
this.current = current;
}
int move() {
if (this.left) {
return -1;
}
if (this.current) {
return 1;
}
return 0;
}
...
}
더 이상 객체를 분리할 수 없는 가장 작은 단계까지 객체를 분리한 후
In -> Out 방식으로 구현
class Point {
private final int index;
private final Direction direction;
Point(int index, Direction direction) {
this.index = index;
this.direction = direction;
}
int move() {
return index + direction.move();
}
...
}
class Line {
private final List<Point> points;
Line(List<Point> points) {
this.points = points;
}
public int move(int position) {
return points.get(position).move();
}
...
}
Out -> In, In -> Out 방식은 일방적이지 않다.
현재 컨텍스트(도메인 지식 수준, 객체 설계 역량 등)에 따라 접근 방식은 달라질 수 있다.
책임주도설계로 사다리타기 재설계
TDD로 만족하는 수준까지 기능 구현을 완료한 후
책임주도설계로 다시 구현해 보는 것도 좋은 연습이다.
책임주도설계 == 인터페이스 주도 설계(interface driven development)
사다리 초기화하기(생성하기)
사다리 실행해 결과 얻기
사다리 최종 결과 구하기
사다리 핵심 로직에 대한 인터페이스 설계
사다리 생성
public interface LadderCreator {
Ladder create(int countOfPersion, int height);
}
사다리 실행 결과 얻기
public interface Ladder {
PlayResults play();
PlayResult play(int source);
}

public class PlayResult {
  private Position source;
  private Position target;
}

사다리 한 라인의 결과 얻기
public interface Line {
int move(int position);
}
사다리 최종 결과 구하기
public class ResultProcessor {
private List<Person> people;
private List<Result> result;
LottoResults toResults(PlayResults results) {
...
}
}
패키지 설계 및 의존 관계

인터페이스와 구현체 의존관계 연결
import ladder.engine.LadderCreator;
import ladder.engine.LineCreator;
import ladder.nextstep.NextStepLadderCreator;
import ladder.nextstep.NextStepLineCreator;
public class LadderFactoryBean {
public static LadderCreator createLadderFactory() {
LineCreator lineCreator = new NextStepLineCreator();
return new NextStepLadderCreator(lineCreator);
}
}

패키지 사이의 의존관계는 단방향이 되도록 해야 한다.
Cyclic Dependencies 발생하지 않도록 지속적으로 감시하고 리팩토링해야 한다.
sonarqube와 같은 정적 분석 도구를 활용해 Cyclic Dependencies를 찾는 것이 가장 좋다.
책임주도설계로 구현한 자바지기 코드는 java-ladder 저장소 javajigi 브랜치에서 참고할 수 있다.

---

### :triangular_ruler: [Ladder Home](https://github.com/gmlwjd9405/tdd-refactoring-clean-code-8/tree/master/study/java-ladder)

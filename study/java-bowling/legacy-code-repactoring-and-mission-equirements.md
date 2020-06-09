# 레거시 코드 리팩토링 및 미션 요구사항

TDD, 리팩토링 연습 과정
AS-IS
학습 테스트 - JUnit 사용법 및 단위 테스트 연습
단위 테스트 - 내가 구현한 코드에 대한 단위 테스트
TDD - 익숙해질 때까지 같은 미션으로 반복 연습
점진적으로 리팩토링하기 - 레거시 코드 리팩토링
기존의 Test Case가 깨지지 않는 상태로 리팩토링하기
컴파일 에러 발생을 최소화하면서 리팩토링하기
TO-BE - 한 단계 더 나아가기
ATDD(Acceptance Test Driven Development) 기반으로 응용 애플리케이션 개발하기
NextStep에서 진행하는 ATDD와 함께 클린 API로 가는 길 과정 추천
레거시 애플리케이션에 테스트 코드 추가해 리팩토링하기
레거시 코드 리팩토링하기
다음과 같은 layered architecture 기반 하에서 핵심 비지니스 로직은 어디에 구현하는 것이 맞을까?
응용 애플리케이션(웹, 모바일 애플리케이션)을 개발할 때 이 과정에서 배운 TDD, OOP를 적용하려
면 핵심 비지니스 로직을 도메인 객체가 담당하도록 구현하는 것이다.
즉, 테스트하기 쉬운 부분과 테스트하기 어려운 부분을 분리해 테스트하기 쉬운 부분에 대한 단위테스
트를 구현하고, 지속적인 리팩토링을 한다.
레거시 코드 리팩토링의 1단계는
Service Layer에 단위 테스트를 추가한 후 비지니스 로직을 도메인 객체로 이동하는 리팩토링
Acceptance Test를 추가한 후 리팩토링
이번 클린코드 과정의 목표는
Service Layer에 단위 테스트를 추가한 후 비지니스 로직을 도메인 객체로 이동하는 리팩토링 이다.
레거시 코드 리팩토링 미션 요구사항
질문 삭제하기 요구사항
질문 데이터를 완전히 삭제하는 것이 아니라 데이터의 상태를 삭제 상태(deleted - boolean
type)로 변경한다.
로그인 사용자와 질문한 사람이 같은 경우 삭제 가능하다.
답변이 없는 경우 삭제가 가능하다.
질문자와답변글의모든답변자같은경우삭제가가능하다.
질문을 삭제할 때 답변 또한 삭제해야 하며, 답변의 삭제 또한 삭제 상태(deleted)를 변경
한다.
질문자와답변자가다른경우답변을삭제할수없다.
질문과 답변 삭제 이력에 대한 정보를 DeleteHistory를 활용해 남긴다.
프로그래밍 요구사항
qna.service.QnaService의 deleteQuestion()는 앞의 질문 삭제 기능을 구현한 코드이다. 이
메소드는 단위 테스트하기 어려운 코드와 단위 테스트 가능한 코드가 섞여 있다.
단위 테스트하기 어려운 코드와 단위 테스트 가능한 코드를 분리해 단위 테스트 가능한 코드 에
대해 단위 테스트를 구현한다.
public class QnAService {
public void deleteQuestion(User loginUser, long questionId) throws CannotDeleteException {
Question question = findQuestionById(questionId);
if (!question.isOwner(loginUser)) {
throw new CannotDeleteException("질문을 삭제할 권한이 없습니다.");
}
List<Answer> answers = question.getAnswers();
for (Answer answer : answers) {
if (!answer.isOwner(loginUser)) {
throw new CannotDeleteException("다른 사람이 쓴 답변이 있어 삭제할 수 없습니다.");
}
}
List<DeleteHistory> deleteHistories = new ArrayList<>();
question.setDeleted(true);
deleteHistories.add(new DeleteHistory(ContentType.QUESTION, questionId, question.getWriter(), LocalDateTime.now()));
for (Answer answer : answers) {
answer.setDeleted(true);
deleteHistories.add(new DeleteHistory(ContentType.ANSWER, answer.getId(), answer.getWriter(), LocalDateTime.now()));
}
deleteHistoryService.saveAll(deleteHistories);
}
}
힌트1
객체의 상태 데이터를 꺼내지(get)말고 메시지를 보낸다.
규칙 8: 일급 콜렉션을 쓴다.
Question의 List<Answer>를 일급 콜렉션으로 구현해 본다.
규칙 7: 3개 이상의 인스턴스 변수를 가진 클래스를 쓰지 않는다.
인스턴스 변수의 수를 줄이기 위해 도전한다.
힌트2
테스트하기 쉬운 부분과 테스트하기 어려운 부분을 분리해 테스트 가능한 부분만 단위테스트한
다.




---

### :bowling: [Bowling Home](https://github.com/gmlwjd9405/tdd-refactoring-clean-code-8/tree/master/study/java-bowling)

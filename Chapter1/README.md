## Chapter1 내용 분석 

#관심사란?
- 여기서 관심사란, 프로그램 코드에 영향을 주는 정보의 집합이라고 설명한다. 즉, 기능과 같은 결과를 내보내는 함수 등을 말하는 것이다.
- 그럼 우리는 관심사를 왜 분리하여야만 하는가?
   - 유지보수, 재사용성 등 당장만 해도 여러 이유가 떠오르지만 가장 중요한 것은 협업자에게 해당 기능에 대한 이해도를 높여줄 수 있다는 것이다.
- MVC, MVP, MVVM 등도 여기에 속한다고 생각할 수 있겠다.

#결합도(의존도)란?
- 결합도는 의존도라고 이해하는 것이 더 편할 것이다. 말 그대로 다른 기능에 의존하는 정도라고 생각하면 된다.
- 예시를 들어주자면, 자동차가 엔진에 의존하는 것을 생각하면 될 것이다.
- 하지만 여기서 주의할 점이 있다. 만약 MVC에서 V가 M에 아주 강하게 의존한다고 생각해보자. 그럼 우리는 M을 수정할 때마다 V를 수정해야할 것이고 V를 수정하면 C를 또 수정하는 굴레에 빠지게 된다.
- 이렇기에 결합도는 낮은 게 유지보수 및 변경 등에 좋다
  - 그러면 결합은 안 하는 게 낫지 않나?
    - 소프트웨어는 여러 개로 나뉘어 있고 서로 협력해서 동작해야 해요.
    - 예시로, MVC는 C에서 action을 받아서 V에 전달후 필요한 것을 M에 요청후 V에 보내듯이 어느정도 순환적으로 돌아간다.

#책임이란?
- 객체가 수행해야 할 역할이나 기능으로, 코드의 응집도를 높이고, 결합도를 낮추는 역할을 합니다.
- 책임이 어떻게 결합도를 낮추는가?
  - 책임이 명확하다면 다른 모듈에 의존하지 않고 자신이 해야 할 일만 처리
  - 책임이 잘 나눠지면 구체적인 구현이 아닌 추상적인 역할만 의존하면 되기 때문

#Implementation
1. BubbleSort.cs (버블 정렬)
2. InsertionSort.cs (삽입 정렬)
3. SelectionSort.cs (선택 정렬)
- 세가지 정렬 모두 한가지 목적 (관심사)를 가지고 있다. => 정렬이라는 한가지 관심사 속에서 한 파일에 정리해둔 것이다.

#ArrayExtensions.cs
1. 배열을 관리하는 메서드의 집합
2. 배열의 내용을 문자열로 보기 쉽게 변환
3. 특정 위치 혹은 전체 배열이 정렬됐는지 비교기로 판단

즉, 배열이 제기능을 하는지 확인하는 부분으로 정렬을 검증하는 책임을 가지고 있다.

#SortOrder.cs
1. 오름차순 내림차순으로 정렬하는 역할로 책임을 가진다.

#IntRandomArrayGenerator.cs
1. 무작위 난수 생성 및 출력 역할로 책임을 가진다.

#Program.cs
1. 사용자와 상호작용 하며 있는 정렬 알고리즘 등을 생성하여 제공하여 애플리케이션을 실행하는 책임을 가진다. 

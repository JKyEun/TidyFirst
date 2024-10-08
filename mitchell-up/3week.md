1. 소프트웨어 설계란?

- `요소`들을 `유익하게` `관계 맺는` 일
- 설계를 구성하는 요소들과 그들의 관계, 그리고 그 관계에서 파생되는 이점
- 변화를 위한 준비, 즉 동작 변경에 대한 준비
- 요소: 토큰 -> 식 -> 문 -> 함수 -> 모듈
- 관계맺기: 호출, 발행, 대기, 참조
- 유익하게: 중간 요소를 통한 서로에게 도움주기

2. 소프트웨어 설계와 개발, 운영 비용의 관계

   - 선택가능성을 유지하고 확장하기 위해 구조에 투자하더라도, 실제로 투자했는지 알 수 없다. (그 효과가 확실한지 알 수 없다.)
   - 먼저 벌고 나중에 쓴다: 코드 정리를 먼저하기 보다는 나중에 하는 것을 권장
   - 물건이 아닌 옵션을 만들어야한다: 코드정리를 선행하는 것을 권장
   - 둥작을 구현하기 전에 "다음에 어떤 동작을 구현할 수 있을까"를 스스로 질문하기
   - 잠재적인 동작 변경요소가 많을 때, 개발 기간이 길 때 설계로 인해 효율적 개발이 가능
   -

3. 소프트웨어 구조에 투자할 때와 하지 않을 때의 장단점
4. 소프트웨어 구조 변경 의사결정 시 사용할 원칙

- 코드정리를 선행해야 하는 경우
  ```
  비용(코드정리) + 비용(정리 후 동작변경) < 비용(바로 동작 변경)
  ```
- 경제적 인센티브에 반하는 행동을 하고 있는지를 인식하기

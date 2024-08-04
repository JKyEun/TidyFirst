#### 07. 선언과 초기화를 함께 옮기기

```javascript
  /** A번 섹션 계산 */
  
  // A 섹션 변수 선언
  const AFa = result?.Arr[0].real_A ?? 0;
  const APyeong = +numberToKoreanArea(realFa)?.toFixed(2) ?? 0;

  // A 섹션 변수가 사용되는 로직
  const ACost =
    +A_Meta?.A * realFaPyeong;
  const BCost = +A_Meta?.BCost * APyeong;
  const CCost = +A_Meta?.CCost * APyeong;
  const DCost = +A_Meta?.DCost * APyeong;
  const ECost = +A_Meta?.ECost * APyeong;
 
  const A_Section_Total =
    ACost +
    BCost +
    CCost +
    DCost +
    ECost +

  /** B번 섹션 계산 */
  // B번 섹션 변수 선언
  const BArea = result?.BArea;
  const BPrice = Number(
    ((parcel?.at(-1)?.price ?? 0) / 1000).toFixed(1)
  );
  const BTax =
    (BPrice * BArea * +BMeta?.B_Rate) / 100;
     
  // B 섹션 변수가 사용되는 로직
  const B_ACost =
    +B_Meta?.B * BArea;
  const B_BCost = +B_Meta?.BCost * APyeong;
  const B_CCost = +B_Meta?.CCost * APyeong;
  const B_DCost = +B_Meta?.DCost * APyeong;
  const B_ECost = +B_Meta?.ECost * APyeong;
 
  const B_Section_Total =
    B_ACost +
    B_BCost +
    B_CCost +
    B_DCost +
    B_ECost +

```

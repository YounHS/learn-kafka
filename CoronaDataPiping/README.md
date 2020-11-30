# CoronaDataPiping (CDP)
1. 개요

   코로나로 인해 팬데믹이 선언된 이후, 도무지 현 상황이 나아질 기미가 보이지 않아, 국내의 코로나 관련 다양한 관측값, 더 나아가 전세계의 코로나 관련 다양한 관측값을 추출하여 데이터를 시각화해보기 위해 시작한다. 향후, 동시간대의 타 데이터와 머징하여 다양한 변수들 간의 상관관계를 도출하여 유의미한 분석까지 하는 것을 본 프로젝트의 목표로 한다.



![CDP_EcoSystem](https://github.com/YounHS/Study_DataEngineering/blob/main/CoronaDataPiping/Picture/EcoSys.png)



2. 관련 스터디

   2.1 ELK 환경 구축

   (자세한 내용은 지속적으로 추가할 예정)

   [링크 클릭 후, ELK_install.md 파일 참고](https://github.com/YounHS/Study_DataEngineering/tree/main/code)



​		2.2 Kafka 환경 구축

​		(자세한 내용은 지속적으로 추가할 예정)

​		[링크 클릭 후, README.md 파일 참고](https://github.com/YounHS/Study_DataEngineering/tree/main/code)



3. 실험에 사용한 자료

   3.1 CoronaBoard (유용히 잘 사용하겠습니다.)

   [실시간 코로나 상황](https://coronaboard.kr/)

   수집하는 데이터는 하단과 같다.

   - 전세계

     - 확진자
     - 사망자
     - 격리해제
     - 치명률
     - 발생국

     

   - 대한민국

     - 확진자
     - 사망자
     - 격리해제
     - 치명률
     - 총검사자
     - 검사중
     - 결과음성



​		3.2 Selenium

​		웹 크롤링이 필요한데, 지금까지 썼던 bs4의 경우, 어떠한 방법을 써도 동적으로 변화하는 데이터를 랜더링하는 웹 데이터를 크롤링할 수 없어서 애를 먹고 있었다. Selunium의 경우 Chrome driver를 통해 time.sleep() 같은 함수나 명시적/암묵적 함수를 설정할 수 있어, 이러한 동적 데이터를 랜더링하는 웹의 경우에도 데이터를 수집할 수 있다는 장점이 있어서 Selenium을 사용해보았다.

# What a Kafka!?
> Apache Kafka는 LinkedIn에서 개발한 분산 메시징 시스템이며 2011년 오픈소스로 공개되었다. 대용량 실시간 로그 처리에 특화된 아키텍처 설계를 통하여 기존 메시징 시스템보다 우수한 TPS를 보여주고 있다.

#### Kafka

- 분산 스트리밍 플랫폼

- 데이터 파이프 라인 구성 시, 주로 사용하는 오픈소스 솔루션

- 대용량의 실시간 로그 처리에 특화되어 있는 솔루션

- 데이터 손실없이 안전하게 전달하는 것이 주목적인 메시징 시스템

- 클러스터링이 가능하여, **Fault-Tolerant**한 안정적인 아키텍처와 빠른 퍼포먼스로 데이터 처리

- 수평적으로 서버의 **Scale-Out**이 가능

- Pub-Sub모델의 메세지 큐

  > Fault-Tolerant: 시스템 내, Fault(장애)가 발생하더라도 시스템에 지장을 주지 않도록 설계된 컴퓨터 시스템
  >
  > Scale-Out: 서버의 대수를 늘려서 성능을 향상시키는 방법



1. Pub-Sub 모델

   - Publish - Subscribe 모델은 메세지를 특정 수신자에게 직접적으로 보내주는 시스템이 아님

   - publisher는 메세지를 topic을 통해 카테고리화함

   - 분류된 메세지를 수신하길 원하는 receiver는 해당 topic을 subscribe함으로써 메세지 읽기 가능

   - publisher는 topic에 대한 정보만 알고 있고, subscriber도 topic만 바라봄

   - 데이터를 관리하는 Kafka 서버를 사이에 끼고 데이터를 넣는 publisher와 데이터를 읽는 subscriber는 각자의 업무만 Kafka 서버를 통해 수행하므로 서로 모르는 상태

   - Kafka 클러스터를 중심으로 producer가 데이터를 push하고 consumer가 데이터를 pull하는 구조




2. 디스크 순차 저장 및 처리

   - 메세지를 메모리에 저장하는 기존 메시징 시스템과 달리 메세지를 파일 시스템에 저장
   
   
- 파일 시스템에 메세지를 저장하기 때문에 별도의 설정을 하지 않아도 데이터의 영속성 보장 가능
   
   
   - 디스크가 순차적으로 저장되어 있으므로 디스크 I/O가 줄어들어 속도가 빠름



3. Kafka 구조 및 구성요소

   ![Kafka_archi](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fd8cokW%2FbtqEvYj5sEi%2F3V2KFeLaAkpAoV6XFhXTK0%2Fimg.jpg)

   (이미지 출처: http://blog.mmlac.com/log-transport-with-apache-kafka/)

   - Kafka Broker는 Kafka 서버, Zookeeper는 Kafka Cluster를 구성할 수 있도록 분산 코디네이션 시스템 역할을 함


   - Kafka Broker와 Zookeeper로 구성된 것들을 각각 Kafka Cluster라고 명명


   - Kafka Broker와 Zookeeper는 다른 서버에 설치하여 운영하는 것을 추천하며, 이는 만약 Zookeeper에서 시스템 오류가 발생한 경우, Kafka Broker에 영향을 미치지 않게 하기 위해서임


   - Kafka의 topic은 partition이라는 단위로 쪼개어져 Kafka Cluster의 각 서버들에 분산되어 저장됨


   - partition에는 데이터들이 순차적으로 저장되며, Kafka 서버는 해당 데이터를 설정 시간에 따라 보관

   

   ![Anatomy_of_a_Topic](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcSpDzW%2FbtqEwJmfAK6%2FI4MGF6YN8lzvIJdIsjSQ2k%2Fimg.png)

   (이미지 출처: http://kafka.apache.org/081/documentation.html)

   - 각 partition은 0부터 1씩 증가하는 offset 값을 메세지에 부여하며, 각 partition의 메세지를 식별하는 ID로 사용
   - offset 값은 partition마다 별도로 관리되므로 topic 내에서 메세지를 식별 시, partition 번호와 offset 값을 함께 사용 -> 위 그림에서 최근 쓰여진 메세지들의 ex) (partition_no, offset_no) = (0, 12) / (1, 9) / (2, 12)
   - 이러한 구조로 쓰여진 메세지는 해당 topic을 구독한 consumer가 소비. consumer가 가져다가 사용하는 구조이기 때문에 consumer 스스로가 소비하는 속도 조절 가능
   - consumer들의 묶음을 consumer group이라고 하며, 특정 partition에는 consumer group당 오로지 하나의 consumer만 접근이 가능. 즉, 동일한 consumer group에 속하는 consumer끼리는 동일한 partition에 접근할 수 없음. 이 때, 특정 partition에 접근하는 consumer를 partition owner라고 명명
   - consumer가 message의 offset 번호를 기억하고 있다가, 다음 메세지를 읽어올 때 offset 번호를 이용해서 읽어오기 가능. 한 consumer group 내부의 consumer 끼리는 각자 접근하고 있는 partition의 메세지 offset 값을 공유하고 있기에, 어떤 consumer에서 장애 발생 시, 다른 consumer가 장애가 발생한 consumer의 offset 값을 알고 있기 때문에 해당 consumer 대신 메세지 소비
   - 한 번 정해진 partition owner는 Kafka broker나 consumer 구성의 변동이 있지 않는 한 계속 유지
   - consumer와 partition의 대칭 비율은 1:N이며, consumer가 partition의 수보다 많은 경우는 사용되지 않는 consumer가 생기고, partition의 수가 consumer의 수보다 많은 경우는 하나의 consumer가 2개 이상의 partition에 접근 가능. 따라서 partition과 consumer의 수 조절할 필요 존재



------

해야할 일

> Kafka Cluster 정리
are u ready? 
응 아니야

# redis 란?
- redis는 REmote DIctionary Server의 약자이다. C언어로 작성됨

- In-Memory 데이터베이스이며, 다른 제품들(ex. Memcached)과 다르게 다양한 자료구조를 지원
- String, Bitmap, Hash, List, Set, Sorted Set, Geospatial Index, Hyperloglog, Stream


영속성(Persistence) 보장을 위해 보관과 백업 기능이 있다. (4번 참조)

(Persistence 로서 redis 를 사용하는 건 많은 노력이 소요되므로 추천하진 않음. 원래 cache 용도로 작성된 거라)


메인쓰레드(이벤트루프)는 싱글스레드로 운용된다.

장점 : atomic 보장, race condition 회피
단점 : 오래걸리는 명령을 실행하면 다른 명령에 영향을 줌

O(N) 명령어는 사용 자제 : KEYS, FLUSHALL, FLUSHDB, DEL 등




버전 6.x부터는 아래와 같은 부분에만 멀티스레드가 도입되었다.

클라이언트에서 전송한 명령을 읽고 파싱하는 부분
명령어 처리 결과를 클라이언트에게 전송하는 부분
(주의) 명령어 실행 자체는 위의 메인스레드에서 수행하므로 여전히 싱글스레드라고 봐야함


![](https://velog.velcdn.com/images/noahshin__11/post/8e73d738-4412-4332-9692-5142a6889e5b/image.png)


redis 는 key-value 형태로 데이터를 저장한다. 여기서 value 에는 아래와 같은 타입들이 올 수 있다.
key 는 문자열이며, 최대 512MB 까지 가능

key 를 가독성있게 잘 설계하는 것이 중요


key 와 관련된 커맨드

EXISTS (key) : 해당 key 가 존재하는지 확인
DEL (key1) (key2) ... : key 삭제
TYPE (key) : key의 value 가 어떤 데이터타입을 사용하고 있는지 반환
SCAN (cursor) [match pattern] [count] : key의 목록을 커서단위로 얻어옴


# String


















출처: https://sjh836.tistory.com/178 [빨간색코딩:티스토리]
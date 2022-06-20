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





모든 종류의 문자열을 저장할 수 있다.

이진 데이터도 포함되니, 이미지 같은 것도 저장 가능
커맨드

저장 : 단건 SET (key) (value) 복수 MSET (key1) (value1) (key2) (value2) ...

SETNX (key) (value) : value 가 존재하지 않으면 key 를 생성


읽기 : 단건 GET (key) 복수 MGET (key1) (key2) ...




숫자라면 원자적인 증감도 가능하다.

커맨드

1 증가 : INCR (key)
N 증가 : INCRBY (key) (number)




값이 최대 사이즈는 512MB 이다.

2-2. List

Linked List 와 같은 형태이며, head와 tail 에 요소를 추가할 때 동일한 시간이 소요된다.
pub-sub, job queue 으로도 활용할 수 있다.

생산자가 아이템을 만들어서 list에 넣으면 소비자가 꺼내와서 액션을 수행
데이터가 없다면 null 을 반환하는데, BRPOP 커맨드를 사용하면 데이터가 들어올 때까지 block 하므로 큐를 구현할때 유용 (polling 이 불필요해져서)


커맨드

앞 push : LPUSH (key) (Element1) (Element2) ...
뒤 push : RPUSH (key) (Element1) (Element2) ...
앞 pop : LPOP (Element)
뒤 pop : RPOP (Element)
BLPOP (Element) : 누가 push 할때까지 대기



2-3. Set

중복되지 않고 정렬되지 않은 문자열의 모음이다.
데이터가 있는지 중복체크용이나, 관계표현(ex. 유저의 구독정보)을 할때 주로 활용된다.
커맨드

SADD (key) (value1) (value2) ... : 이미 value 가 있다면 추가되지 않는다.
SMEMBERS (key) : 모든 value 를 반환
SISMEMBER (key) (value) : value 가 존재하면 1, 없으면 0



2-4. Sorted Set

위의 set 과 비슷하지만 key, value 에서 score 까지 함께 저장하며, 이 score 값으로 정렬된다.

score 값이 같다면 사전순으로 정렬된다.


인덱스를 이용하여 빠르게 조회할 때 주로 사용된다.
커맨드

ZADD (key) (score) (value) : 이미 value 가 있다면 score 값만 변경한다.
ZRANGE (key) (startIndex) (endIndex) : 해당 index 범위의 모든 value를 반환

0 -1 로 하면 전체


ZRANGEBYSCORE (key) (startScore) (endScore) : 해당 score 범위의 모든 value 를 반환

endScore 가 +inf 이면 끝까지




이 때, score 는 double 이기 때문에, 부동소수점에 주의해야한다.

2-5. Hash

field - value 쌍의 hash 형태로 저장한다.
key 의 field(=subKey) 갯수에는 제한이 없다.
커맨드

조회 : 단건 HGET (key) (field) 복수 HMGET (key) (field1) (field2) ...
저장 : 단건 HSET (key) (field) (value) 복수 HMSET (key) (field1) (value1) (field2) (value2) ...
HGETALL (key) : 해당 key 의 모든 field - value 를 조회



2-6. Hyperloglog(hll)

Hyperloglog 라는 타입은 2007년 Philippe Flajolet 발표한 알고리즘이다.
시간복잡도 O(1), 최적화된 공간을 사용하는 unique count 기능이다. 카디널리티(원소의 수)를 구하는데 주로 사용된다.

확률적 자료구조를 이용한 추정 원리 : https://d2.naver.com/helloworld/711301
요약

AS-IS : Set 같은 곳에 전부 add 한 후 count 를 세야함. 규모가 커질 수록 공간복잡도와 시간복잡도가 매우 높음
TO-BE : 매우 적은 메모리로 집합의 원소 개수를 추정 가능. 근사값을 추정하는 형태이며, 오차는 0.81%




count 만 셀 뿐이기 때문에 value 는 저장하지 않는다. (내부 데이터 확인 불가능)

내부적으론 string 타입 활용


커맨드

추가 : PFADD (key) (Element1) (Element2) ...
PFCOUNT (key) : key 에 저장된 unique count 조회
PFMERGE (destkey) (sourcekey1) (sourcekey2) ... : destkey 에 sourcekey 들을 머지한다



2-7. 이외

Bitmap : SETBIT, GETBIT 커맨드로 비트연산 지원, 비트맵을 사용하여 공간절약 가능
Geospatial Index : 경도, 위도에 활용되는 자료구조, 내부적으로는 Sorted Set 을 활용함
Stream : 주로 로그같은 걸 처리하기 위해 최적화된 데이터 타입

3. Expire

메모리는 한정적이니 대부분 key 에 Expire 를 설정할 것을 권장
동일한 key 가 다시 들어오면 timeout 이 재설정됨
Expire 는 개별 Collection 의 item key 단위(ex. hash key의 field 들)로는 설정할 수 없다.
커맨드

EXPIRE (key) (ttl) : ttl 은 초단위
PERSIST (key) : ttl 설정을 제거하고 영구 보관



3-1. Expire는 언제되는가?

key 에 대한 접근이 발생할 때
(내부 스케줄러) activeExpireCycle 에 의한 삭제

코드 : https://github.com/antirez/redis/blob/unstable/src/expire.c#L123


command 처리 전에 memory가 부족할 때 메모리 정책에 따라서 삭제

4. Persistence

redis 는 데이터를 메모리에 저장한다. shutdown 되면 모든 데이터가 날아가는 것이다. 이런 문제를 해결하고 영속성을 위하여 크게 2가지 방식을 지원한다.

4-1. AOF(Append Only File) = 보관

명령이 실행될 때 마다 파일(ex. appendonly.aof)에 기록한다.

조회는 해당되지 않으며, 입력/수정/삭제만


AOF를 계속 수행하면 기록되는 파일의 size 가 커진다. 너무 커져서 기록이 중단된다 하더라도, rewrite 하면 size 가 작아진다.

CUD 에 대해 최신 로그만 기록되므로
ex. INCR 1000번 로그가 SET key 1000 으로 바뀜.


aof 파일을 통해 영속성 보장

4-2. RDB(snapshot) = 백업

특정한 간격으로 메모리에 잇는 레디스 데이터 전체를 disk 에 바이너리 형태로 기록
AOF 보다 size 가 작으며, 로딩속도도 빠르다.









출처: https://sjh836.tistory.com/178 [빨간색코딩:티스토리]
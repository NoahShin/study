# AWS Route 53 


![](https://velog.velcdn.com/images/noahshin__11/post/de6c884e-ed3f-4a12-8421-4ee8fa456183/image.png)

Route53 은 도메인 등록 대행을 해주는 역할도 하는데

등록 대행에세 example.com 을 등록하고 싶다고 하면

대행이 등록소에 가서, IP 는 안 알려주고 등록함

그러면 등록소는 등록소서버에 example.com 이라는 도메인은 
a.iana 라는 등록대행 서버에 있다 ~ 라고 저장해놓음

여기까지가 도메인 등록하는 간략한 과정.

이제 유저가 (클라이언트) 크롬 브라우저에 example.com 을 엔터 치면 어떻게 되는지 봅시다.

일단 유저 노트북에 와이파이를 연결하거나 랜선을 꽂는 순간
그 컴터만의 DNS 서버가 생기는데 (어떻게 생기는지는 모름, 생활코딩형이 그냥 마법이라고 그랬음)

example.com 적고 엔터치면 아주 빠른속도로 이 노트북의 DNS 서버가 
루트네임서버한테 가서 
"야, example.com  ip 뭐임?"
루트네임서버: "음... 뒤에 .com 으로 끝나네, 그럼 a.gtld-servers.net 으로 가봐

이번엔 해당 등록소 서버에 가서 "야, example.com  ip 뭐임?"
등록소서버: "엥 ip는 모르는데, example.com 은 
a.iana-server.net 쪽에서 등록해달라고 했음"

이번엔 등록대행 서버에가서 
"야, example.com  ip 뭐임?"
등록대행서버: 93.184.216.34 

그제야 우리의 부지런한 DNS server 는 client 에게 IP 넘버를 갖다주고, 클라이언트는 그 IP 로 접속할 수 있게 된다.

Route53의 두가지 역할
- 등록대행
- 네임서버를 임대해주는 역할
![](https://velog.velcdn.com/images/noahshin__11/post/e4092364-685b-4e01-8607-6bb00d973077/image.png)



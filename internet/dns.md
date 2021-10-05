# DNS (Domain Name System)
- 호스트 도메인 이름을 호스트의 네트워크 주소(IP 주소)로 바꾸거나 그 반대의 변환을 수행할 수 있도록 하기 위해 개발되었다.


## DNS의 동작원리

1. 웹 브라우저에 www.naver.com을 입력하면 먼저 hosts파일에 해당 도메인이 등록되어 있는지 확인한다.
![image](https://user-images.githubusercontent.com/18106839/136116176-a6ebdbd9-8e3b-46f0-afb4-9080d37e05fb.png)

- 우리가 localhost라고 입력했을 때 127.0.0.1로 연결되는 이유이다.

2. hosts파일에 도메인이 등록되어 있지않으면 로컬 DNS서버로 요청을 보낸다. 
로컬 DNS서버는 기기마다 가지고 있다. 네트워크 설정에 가보면 로컬 DNS 설정 정보를 찾을 수 있다.

![image](https://user-images.githubusercontent.com/18106839/136115880-8756a6dc-fac7-45b0-9240-e0219bd8781c.png)

3. 로컬 DNS서버에서 해당 도메인에 대한 IP주소를 찾을 수 없다면 Root DNS 서버로 요청을 전달한다. 모든 DNS서버는 루트 도메인 서버의 주소를 알고 있다.
4. 루트 도메인 서버로 부터 ".com"을 관리하는 Top-Level Domain(TLD) 서버 정보를 받는다.
5. TLD에서 "naver.com"을 관리하는 DNS 정보를 전달
6. "naver.com" 도메인을 관리하는 DNS 서버에 "www.naver.com" 호스트네임에 대한 IP주소를 요청
7. 로컬 DNS 서버에게 "www.naver.com"의 IP주소를 응답한다.
8. 로컬 DNS 서버는 클라이언트에게 "www.naver.com"의 IP주소를 전달한다.

![image](https://user-images.githubusercontent.com/18106839/136116905-715503ec-0656-4464-9e80-1f041efdf9fd.png)

### REF
- https://velog.io/@goban/DNS%EC%99%80-%EC%9E%91%EB%8F%99%EC%9B%90%EB%A6%AC

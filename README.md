# VNF 병렬도에 따른 지연시간 측정 실험 설계

---
## 1. 실험 개요

- **실험 제목**: 컨테이너 기반 RAW-Socket LB + iptables NAT 병렬화에 따른 HTTP 요청 지연 측정
- **작성자** : 정재홍
- **날짜**: 2025-04-28
- **목표 (Objective)**:
  - NAT VNF 인스턴스 수(N)별 HTTP 요청-응답 지연(RTT) 변화 측정
- **가설 (Hypothesis)**:
  - "NAT 인스턴스가 증가할수록 각 VNF 당 부하가 감소하여 평균 RTT가 감소하다가, 
    병렬화 오버헤드로 인해 평균 RTT 가 증가하는 추세를 보인다."

---
## 2. 네트워크 토폴로지

![[./images/ex.drawio.svg]]

---
## 3. 상세 설명

### 3.1 컨테이너

| 이름             | 역할              |
| :------------- | :-------------- |
| client         | HTTP 요청 발생      |
| LB Container   | RAW Socket LB   |
| NAT Container  | iptable 기반 NAT  |
| HTTP Container | HTTP 에코 서버      |

### 3.2 호스트 네트워크 설정

- **Docker**
	- `--network none `옵션을 통해 네트워크 수동 설정
- **브릿지, 인터페이스**
	- veth pair 와 큐 길이 등 설정

### 3.3 VNF 설정

- **LB Container**
	- RAW socket 프로그래밍을 통해 IP, Port 를 해시하여 세션 스티키니스 보장
- **VNF Container**
	-  iptables 설정을 통해 NAT 구현

---
## 4. 실험 절차

1. 환경 초기화 (컨테이너, 브릿지 삭제)
2. 브릿지 및 veth 생성, 연결
3. 컨테이너 생성 (`--network none`)
4. veth 인터페이스 컨테이너에 연결
5. LB Container 배포 및 RAW 소켓 로직 실행
6. NAT VNF 수 조정 (N=1,2,4,8 등)
7. Web Server 실행
8. 부하 생성 및 RTT 측정
   - wrk2 사용:     ```
   - hping3 사용:
9. 데이터 수집 및 분석

---
## 5. 측정 지표 및 데이터

- 지연시간 (P50, P95, P99)
- 처리량 (requests/sec)
- CPU 사용률 (docker stats)

---


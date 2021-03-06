# 서버 준비

## 사전 준비 사항

 — 모든 노드는 CentOs/Rhel 7.2 ~ 7.3 OS 에서 구동됩니다.  
 - 내부망 UDP 뚤려있을것: UDP 가 뚤려있지 않으면 53 포트 domain resolve 가 작동하지 않는다. (운영시)
 - 내부망 TCP 포트는 모두 뚤려있을것 (운영시)
 - 외부망 인바운드 :  퍼블릭 에이전트 머신의 80 포트와 443 포트 (운영시)
 - 외부망 아웃바운드 : 모두 뚤려있을것 (설치시)
 - 네임서버 :  *.<server>.<domain>  형식으로, A MASK 가  *  로 설정된 도메인을 보유하고있을것. (운영시)
 - 모든 서버는 동일한 pem 파일로 ssh 가 가능할 것. (운영시)
 - 모든 서버의 /etc/ssh/ssh_config 는 PermitRootLogin (설치시) 을 허용할 것.
 
### 서버 준비

- 다음의 서버들을 준비해야 합니다. (사양은 추천사항입니다.)
- 마스터 노드는 홀수개의 서버로 준비하도록 합니다. (1,3,5)
- 프라이빗 에이전트 노드는 최소 한개를 구성해야 합니다.
- 퍼블릭 에이전트 노드는 최소 한개를 구성해야 합니다.
- 퍼블릭 에이전트 노드의 80 포트와 443 포트, 그리고 9000 - 60000 (허용 최대치) 포트는 외부에서 접속이 가능해야 합니다.
- 마스터 노드중 한개의 80 포트는 외부에서 접속이 가능해야 합니다.
- 깃랩 서버의 80 포트와 5000 포트는 외부에서 접속이 가능해야 합니다.
- 깃랩 서버, 퍼블릭 에이전트 노드, 그리고 한개의 마스터 노드는 인터넷 환경에서 접속 가능한 퍼블릭 아이피를 가지고 있어야 합니다.

#### 서버 예시

| 역할 / 호스트네임 | 사양                     | IP 주소      | 퍼블릭 IP 주소 | 외부 포트바인딩     |
|-------------------|--------------------------|--------------|----------------|---------------------|
| bootstrap         | 2 CPU /2 GB/100 GB Disk  | 172.31.8.143 |                |                     |
| master1           | 2 CPU /4 GB/100 GB Disk  | 172.31.12.143 | 52.79.125.242  | 80                  |
| master2           | 2 CPU /4 GB/100 GB Disk  | 172.31.4.125 |                |                     |
| master3           | 2 CPU /4 GB/100 GB Disk  | 172.31.1.198  |                |                     |
| public-agent      | 4 CPU /8 GB/100 GB Disk  | 172.31.5.136 | 52.79.51.79    | 80,443,9000 - 60000 |
| agent1            | 4 CPU /8 GB/100 GB Disk  | 172.31.6.35 |                |                     |
| agent2            | 4 CPU /8 GB/100 GB Disk  | 172.31.1.235 |                |                     |
| agent3            | 4 CPU /8 GB/100 GB Disk  | 172.31.5.245 |                |                     |
| agent4            | 4 CPU /8 GB/100 GB Disk  | 172.31.14.247 |                |                     |
| agent5            | 4 CPU /8 GB/100 GB Disk  | 172.31.7.160 |                |                     |
| agent6            | 4 CPU /8 GB/100 GB Disk  | 172.31.11.70 |                |                     |
| agent7            | 4 CPU /8 GB/100 GB Disk  | 172.31.0.164 |                |                     |
| gitlab            | 4 CPU /32 GB/300 GB Disk | 172.31.15.249 | 52.78.60.43    | 80,5000             |
| ci                | 1 CPU /1 GB/100 GB Disk  | 172.31.3.61 |                |                     |


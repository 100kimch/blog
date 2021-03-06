# 네트워크(Network)

- Written in Nov. 12. 2021
- Authored by Jaycol Kim <jaycol@gmail.com>

## 프록시 서버 (Proxy Server)

냉장고가 고장이 났을 때 우리는 냉장고의 정확한 구조를 이해하고 있는 엔지니어에게 직접 전화를 하지 않고 고객센터를 통해 수리 전문가를 호출한다. 서버와 클라이언트 간 통신에 있어서도 직접적으로 서버가 클라이언트의 요청과 응답을 받아 처리하는 것 보다 중간에서 대리해주는 서버가 있는 것이 유리할 때가 있다. 우리는 이것을 **프록시 서버**라고 부르기로 했다.
클라이언트인 우리가 google.com 웹사이트 정보를 서버에 요청할 때, 프록시 서버가 있다면 프록시 서버는 다음과 같은 역할을 한다:

1. 우리의 요청사항을 수렴
1. 요청사항을 웹사이트 정보를 담고 있는 실 서버에게 요청
1. 실서버의 응답 데이터를 수신
1. 수신된 데이터를 우리에게 전송

왜 번잡하게 중간에 끼어드는 서버를 두는 것일까.
첫번째, **캐시 데이터**를 사용하기 위함이다. 고객 센터에서 반복적인 작업들은 직접 상담사와 통화하기 전 1번, 2번 등을 눌러 관련 설명들을 듣게 강제하는 것처럼 실서버의 부하 없이 캐시에 저장된 내용들을 돌려줄 수 있다.
두번째, **보안 목적**이다. 수리 전문가의 개인정보인 이름, 전화번호를 사전에 제공하지 않고도 고객은 고장난 냉장고를 수리할 수 있을 것이다. 실서버의 IP 주소를 숨길 수 있기에 프록시 서버를 방화벽으로 사용할 수도 있다.
세번째, **접속 우회**이다. 한국에서 접속이 안되는 사이트가 있다면 미국의 프록시 서버를 통해 해당 사이트에 접근한다면 한국 지역을 제한하는 웹 서버는 미국에 있는 프록시 서버의 요청을 거부할 이유가 없어진다.

### 포워드 프록시(Forward Proxy)

만약, 위에서 언급한 세가지 역할: 캐시데이터를 사용하고 싶다거나, 보안 목적으로, 또는 접속 우회의 목적으로 프록시 서버를 두고 싶다면 공유기 내부 네트워크에 프록시 서버를 두 될 것이고, 이를 **포워드 프록시**라고 부른다.

홈 라우터를 기준으로 공인 IP 주소와 사설 IP 주소로 나뉜다는 것은 기본적으로 홈 라우터인 공유기가 우리가 사용하는 기기의 IP 주소를 숨겨주는 역할을 하는 것이다. 그래서 위에서 언급한 보안 목적으로 프록시 서버를 두는 것이 이해가 되지 않을 수 있다. 간단하게 이야기하면 이는 NAT와 프록시의 차이점이라 할 수 있고 프록시 서버는 필터링 규칙 지정, 요청의 유효성 검사 등 더 세밀한 작업을 진행할 수 있다.

## 리버스 프록시(Reverse Proxy)

클라이언트 단에서 공유기 내부에 프록시 서버를 두는 경우도 있지만, 웹사이트의 데이터를 보내주는 서버 측의 사설 네트워크에서 프록시 서버를 붙이기도 한다. (아니, 거의 대부분 붙인다.) 이를 **리버스 프록시** 라고 부르는데, 앞서 언급한 프록시 서버의 3가지 역할 뿐만 아니라 리버스 프록시는 다음과 같은 효과를 준다:

1. 로드밸런싱 (Load Balancing)

    - 웹사이트 데이터를 전달하는 웹서버 입장에서 수많은 클라이언트 요청을 받는다. 필자의 블로그를 서비스해주는 서버는 일대일로 대응이 가능하겠지만, 큰 규모의 사이트는 하나의 서버가 모든 요청에 대응하기 힘들 것이다. 리버스 프록시는 들어오는 데이터 트래칙을 분산시켜 줌으로써 오버로드되는 단일 서버의 가능성을 줄여준다.

2. HTTP 요청 내용에 따른 시스템의 동작 제어

    - 클라이언트의 IP 주소를 인식해 특정 IP 주소만 접근할 수 있다거나, 특정 포트로의 접근만을 가능하게 할 수 있다.
    - 클라이언트 요청 패킷 헤더의 User-Agent 같은 것을 보고 특별한 웹서버로 접속되도록 유도할 수 있다. 사람이 아닌 봇의 요청의 경우 캐시서버로 전달되게 한다는 등의 특별한 제어 기능을 심을 수 있다는 뜻이다.

3. 해커의 공격으로부터 방어

   - 무차별적으로 하나의 서버에 온갖 요구를 다해 서버가 미쳐버리게 하는 디도스 어택(DDoS attack)으로부터 방어선을 구축할 수 있다. 스로틀링 (Throttling)을 걸든 특정 IP 주소에 대한 접근 제한을 걸 수 있는 것이다.

4. GSLB 설정

   - Global Server Load Balancing(GSLB )를 둠으로서 전세계 몇몇개의 서버를 통해 웹사이트 배포를 분산시킬 수 있으며, 이를 통해 로드 시간, travel 횟수를 줄이는 등의 거리를 좁힐 수 있다.

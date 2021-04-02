---
title: "Azure 리소스 모니터링 서버 만들기 (2)"
date: 2020-12-09T20:46:49+09:00
#draft: true
categories:
  - "Monitoring"
tags:
  - "Grafana"
  - "Azure"
---

## 모니터링 환경 구성하기
----
앞서 포스트에선 Grafana와 Azure Monitor 기능을 이용하여 Azure 리소스를 모니터링 할 때의 장/단점과 특징들을 살펴보았습니다. 이번 포스트 부터 Grafana 모니터링 시스템을 구축하도록 하겠습니다.  
　<br>

### Azure AD App 만들기
----
우선 사용자를 대신하여 백그라운드에서 Azure API의 인증과 권한을 획득하여 Metric API의 데이터를 읽어올 수 있는 Azure AD App을 추가합니다. Azure AD App은 Azure AD의 앱등록 에서 만들 수 있습니다. Azure AD App을 만들기 전에 아래 사진과 같이 사용자 계정이 Azure AD 내에서 애플리케이션 개발자 이상의 역할을 가지고 있어야 합니다.

{{< figure src="/img/grafana/chpt2/adapp1.png" title="" >}}

충분한 권한을 가진 것을 확인 했다면 아래 사진의 순서대로 Azure AD App을 추가합니다.

{{< figure src="/img/grafana/chpt2/adapp2.png" title="" >}}

표시되는 옵션들은 모두 O'Auth와 관련된 옵션 입니다. 지원되는 계정 유형은 Azure Ad App과같은 Azure AD 내의 계정들만 로그인을 허용하는 옵션이고, 리다이렉션 URL은 Microsoft login을 통과한 후 다시 돌아갈 웹페이지를 지정하는 공간 입니다. Grafana에서 Azure Metric을 읽어오는 작업은 O'Auth와 관련이 없으므로 기본값으로 작성하고 넘어갑니다.  

{{< figure src="/img/grafana/chpt2/adapp3.png" title="" >}}

앱 추가가 완료되었으니 해당 app 이름을 검색하여 찾습니다.
{{< figure src="/img/grafana/chpt2/adapp4.png" title="" >}}

아래 두 가지 정보는 Grafana 설정시 필요하므로 복사하여 메모장에 옮겨 적습니다.
{{< figure src="/img/grafana/chpt2/adapp5.png" title="" >}}

마지막으로 앱의 Grafana 설정에 필요한 암호를 생성합니다.
{{< figure src="/img/grafana/chpt2/adapp6.png" title="" >}}

암호는 창을 새로고침하면 사라지므로 복사하여 메모장에 옮겨 적습니다.
{{< figure src="/img/grafana/chpt2/adapp7.png" title="" >}}

마지막으로 AD App이 구독의 리소스를 읽어올 수 있도록 구독에 독자 역할을 할당 합니다.
{{< figure src="/img/grafana/chpt2/adapp8.png" title="" >}}

　<br>

### Grafana 설치하기
----
Grafana 설치는 [공식 홈페이지](https://grafana.com/grafana/download)에서 제공하는 패키지 파일을 다운로드 받아서 설치합니다. CentOS에 설치를 할 예정 이므로 아래 빨간 박스의 설명을 사용합니다.
{{< figure src="/img/grafana/chpt2/grafanainstall1.png" title="" >}}

링크를 복사하여 터미널에서 실행합니다.
{{< figure src="/img/grafana/chpt2/grafanainstall2.png" title="" >}}

Grafana 자동 시작 설정, 서비스 시작 그리고 상태를 확인 합니다.
{{< figure src="/img/grafana/chpt2/grafanainstall3.png" title="" >}}

설치가 완료됐다면 브라우저를 열고 Grafana가 설치된 호스트에 접속 합니다. 현재 사용중인 PC에 설치했다면 localhost:3000으로 접속합니다. 다른 PC에 설치했다면 <<your-pcIP>>:3000 에 접속합니다.
기본 ID/PW는 모두 admin이며 한번 로그인 하면 암호를 바꾸라고 알려주면 암호를 바꿔줍니다. 그대로 유지할 수도 있습니다.
{{< figure src="/img/grafana/chpt2/grafanainstall4.png" title="" >}}


　<br>
                              
### Azure 데이터 소스 추가하기
----
이제부터 Azure 데이터 소스 플러그인을 사용하여 Metric API와 연결을 만들어봅니다. 우선 아래 메뉴로 들어갑니다.
{{< figure src="/img/grafana/chpt2/add-datas1.png" title="" >}}

azure로 검색하면 데이터소스 플러그인을 찾을 수 있습니다. select 버튼을 클릭합니다.
{{< figure src="/img/grafana/chpt2/add-datas2.png" title="" >}}

위에서 저장한 테넌트, 앱 ID, 앱 비밀번호 를 아래 사진처럼 기입하고 Load Subscription 버튼을 클릭 합니다. 구독이 불러와지면 기본으로 사용할 구독을 선택합니다.
{{< figure src="/img/grafana/chpt2/add-datas3.png" title="" >}}

그리고 아래쪽 Log Analytics는 아직 사용하지 않으므로 아무것도 기입하지 않고 Save & Test 버튼을 클릭합니다. 아래 사진의 에러는 Metric API 인증에는 성공 했고 Log Analytics는 없으므로 API 인증에 실패했다는 메시지 입니다. Log Analytics는 아직 사용하지 않으므로 Back을 클릭합니다.
{{< figure src="/img/grafana/chpt2/add-datas4.png" title="" >}}

이제 Azure 리소스 모니터링을 위한 기본 준비가 끝났습니다. 다음 포스트에선 Azure 플러그인을 사용하여 여러 메트릭을 조회하고 대시보드를 만들어 보겠습니다.
---
title: "Azure 리소스 모니터링 서버 만들기 (3)"
date: 2021-01-04T22:12:20+09:00
categories:
  - "Monitoring"
tags:
  - "Grafana"
  - "Azure"
---

## 가상머신 CPU 대시보드 만들기
----
앞서 포스트에선 Grafana 설치와 Azure 플러그인을 사용하여 데이터소스를 연결하는 방법을 알아보았습니다. 이번 포스트에선 Azure VM을 모니터링하는 간단한 대시보드를 만들어 보겠습니다. 그리고 Azure Plugin 에서 사용할 수 있는 빌트인 변수와, 그 변수를 사용하여 동적으로 메트릭이 추가되는 패널을 만들어보도록 하겠습니다.
　<br>

### 대시보드 추가
----
먼저 좌측메뉴에서 Create - Dashboard를 선택하여 대시보드를 만듭니다.

{{< figure src="/img/grafana/chpt3/1-create-dashboard.png" title="" >}}

아래 순서대로 현재 대시보드의 이름을 지정하고 저장합니다.

{{< figure src="/img/grafana/chpt3/2-save-dashboard.png" title="" >}}

다음으로 데이터를 표현할 패널을 만듭니다. 패널은 메트릭 뿐만 아니라 로그, 테이블 등 다양한 데이터를 표현할 수 있습니다.

{{< figure src="/img/grafana/chpt3/3-add-panel.png" title="" >}}


　<br>

### 패널 조작하기
----
초기 패널을 추가했으면 [+Add new panel]버튼을 클릭하여 패널 에디트 모드로 들어갑니다. 그러면 아래와 같은 초기 화면을 확인할 수 있습니다. 왼쪽 창은 불러올 데이터를 조작할 수 있으며 오른쪽 창은 패널 디자인을 변경할 수 있습니다. 패널 디자인은 추후 다른 포스트에서 다루도록 하겠습니다.

{{< figure src="/img/grafana/chpt3/4-query-data-default.png" title="" >}}

먼저 데이터를 쿼리할 데이터 소스를 선택합니다.

{{< figure src="/img/grafana/chpt3/4-query-data-source.png" title="" >}}

다음으로 리소스그룹과 가상머신을 선택합니다.

{{< figure src="/img/grafana/chpt3/4-query-metrics.png" title="" >}}

Apply 버튼을 클릭하여 대시보드로 돌아갑니다.

{{< figure src="/img/grafana/chpt3/4-query-save.png" title="" >}}


　<br>
                              
## 여러 데이터 추가하기 // 패널
----
여러 데이터를 추가하는 방법으로는 패널 하나에 여러 개의 쿼리를 집어넣어 여러개의 그래프를 보는 방법, 대시보드 하나에 여러개의 패널을 추가하는 방법이 있습니다. 패널 하나에 여러 개의 쿼리를 집어넣는 방법은 Grafana에 조금 더 익숙해진 이후 Template 조작을 통해 쉽게 만들 수 있습니다. 이번 포스트에서는 대시보드 하나에 여러개의 패널을 동적으로 추가할 수 있는 방법을 알아보겠습니다.


　<br>

### Azure 플러그인 빌트인 변수 추가
----
Azure 플러그인을 사용하면 Grafana 변수에 Azure 데이터값을 동적으로 추가할 수 있습니다. 자세한 내용은 공식 홈페이지의 [링크](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/#create-template-variables-for-azure-monitor)를 참고할 수 있습니다. 그럼 지금부터 변수 추가를 시작해보겠습니다.  

먼저 현재 대시보드 우측 상단의 설정으로 들어갑니다.

{{< figure src="/img/grafana/chpt3/5-variable-1.png" title="" >}}

좌측 리스트의 변수로 들어가 변수 추가를 클릭합니다.

{{< figure src="/img/grafana/chpt3/5-variable-2.png" title="" >}}

먼저 구독을 불러올 수 있는 변수를 추가합니다. 변수 이름을 적절하게 입력하고 변수 쿼리문을 입력합니다. 아래 사진과 같이 옵션을 지정하고 하단의 Add 혹은 Update 버튼을 클릭합니다.

{{< figure src="/img/grafana/chpt3/5-variable-3.png" title="" >}}

추가된 변수를 확인하고 New 버튼을 클릭하여 리소스 그룹을 위한 변수 추가를 진행합니다. 

{{< figure src="/img/grafana/chpt3/5-variable-4.png" title="" >}}

적절한 변수 이름을 입력하고 Query 부분을 유심히 봐주시기 바랍니다. $변수이름 규칙을 사용하여 이전에 만든 변수를 현재 쿼리에서 사용할 수 있습니다. 아래 사진과 같이 옵션을 지정하고 Add 혹은 Update 버튼을 클릭합니다.

{{< figure src="/img/grafana/chpt3/5-variable-5.png" title="" >}}

마지막으로 가상머신 변수를 추가하기 전 대시보드로 돌아와 가상머신이 존재하고 있는 리소스 그룹을 미리 선택합니다. 변수 $ResourceGroup을 사용하여 가상머신 목록을 불러올 때, 해당 리소스 그룹에 가상머신이 없다면 변수의 값이 None로 표기되기 때문에 미리 손을 써 두는 행동 입니다.
{{< figure src="/img/grafana/chpt3/5-variable-6.png" title="" >}}

가상머신 변수의 이름과 쿼리를 적절하게 입력합니다. 모든 가상 머신을 확인하고 싶기때문에 모두 포함 옵션을 키고 Add 혹은 Update 버튼을 클릭합니다.
{{< figure src="/img/grafana/chpt3/5-variable-7.png" title="" >}}

이제 다시 대시보드로 돌아오면 추가된 변수들을 확인할 수 있습니다.
{{< figure src="/img/grafana/chpt3/5-variable-8.png" title="" >}}


　<br>

### 패널에 변수 적용하기
----
변수 추가가 완료됐으므로 이제 패널에 변수를 적용할 차례 입니다. 패널에 변수값을 적용하는 방법은 매우 간단합니다. $변수이름 규칙만 사용하면 됩니다. 아래 그림과 같이 패널 편집 모드로 들어갑니다.

{{< figure src="/img/grafana/chpt3/6-edit-panel-var-1.png" title="" >}}

아래와 같은 방법으로 변수를 지정합니다. 패널에 어떤 데이터가 표시되는지 보여주는 범례 형식(Legend)는 [공식 문서](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/#format-legend-keys-with-aliases-for-azure-monitor)를 참조하여 작성합니다.

{{< figure src="/img/grafana/chpt3/6-edit-panel-var-2.png" title="" >}}

Apply 버튼을 클릭하여 다시 대시보드로 돌아와 변수가 잘 작동하는지 확인합니다. 확인하는 도중 여기서 문제가 생깁니다. 하나의 가상 머신을 선택하면 패널이 잘 그려지지만 여러개의 가상 머신을 선택하면 패널 오류가 발생합니다.  
맞습니다. 쿼리 하나당 한 종류의 데이터만 보여줄 수 있는 Azure 플러그인의 한계 때문에 여러개의 변수를 선택했을 경우 API 호출 에러가 발생합니다. 이를 해결하려면 반복 옵션을 사용하거나 Azure Log Analytics를 사용하여 패널을 만드는 방법이 있습니다.

{{< figure src="/img/grafana/chpt3/6-edit-panel-var-3.png" title="" >}}

{{< figure src="/img/grafana/chpt3/6-edit-panel-var-4.png" title="" >}}


　<br>

### 동적으로 패널 추가하기
----
여러개의 가상 머신을 선택할 때 에러가 발생하므로 패널의 반복 옵션을 사용하여 동적으로 가상머신 패널들을 나열할 차례 입니다.  

다시 패널 편집모드로 들어간 후 좌측 메뉴의 스크롤을 아래로 내려 반복옵션 기능을 활성화 합니다. 어떤 기준으로 반복을 할지 선택하는 물음에 VmName 변수를 사용하여 반복을 하겠다고 작성합니다. 그리고 수직, 수평 옵션은 사용자 입맛대로 선택 하고 Apply를 클릭합니다.

{{< figure src="/img/grafana/chpt3/7-repeat-1.png" title="" >}}

다시 대시보드로 돌아와 모든 가상 머신을 선택하면 동적으로 패널이 추가됨을 확인할 수 있습니다.

{{< figure src="/img/grafana/chpt3/7-repeat-2.png" title="" >}}


----
이번 포스트에선 Azure 플러그인의 빌트인 변수를 사용하여 동적으로 대시보드를 구성하는 방법에 대하여 알아보았습니다. 지금 보여드린 LAB은 Azure Monitor with Grafana의 극히 일부분에 불과합니다. 순수 Azure Monitor, Log Analytics API를 사용하여 모니터링 할 수 없는 경우도 있으며, 이때는 가상 머신에 직접 에이전트를 심거나, 웹 페이지 소스에 코드를 삽입하여 데이터를 직접 Influx DB 혹은 Log Analytics에 데이터를 전송하여 인프라와 애플리케이션을 모니터링 해야합니다.  

앞으로 기회가 되면 Grafana를 사용한 여러가지 모니터링 방법을 포스팅할 계획입니다.  
다음 포스트로는 Grafana의 사용자 보안, Azure AD를 사용한 사용자 인증으로 돌아오겠습니다.  
Azure AD를 사용한 사용자 인증은 O'auth 2.0과 Nginx 웹서버를 사용할 예정 입니다.
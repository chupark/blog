---
title: "Azure Workbook 모니터링 소식!"
date: 2021-04-07T20:07:13+09:00
# draft: true
categories:
  - "Monitoring"
tags:
  - "Workbook"
  - "Azure"
---

# Workbook 소식
----
Workbook을 활용한 Azure 모니터링 방법을 어떻게 써야할지 준비하고 있습니다. 다양한 기능들이 숨어있어 하나하나 정리가 필요한데 감이 안 오네요. 그래도 사용하면서 느낀 점이 있는데, 무료로 제공되는 서비스이기 때문에 제대로 사용 한다면 상용 솔루션을 넘어서는 퍼포먼스를 보여줄 것 같습니다. 아쉬운 점이 있다면... Microsoft의 공식 문서가 매우 불친절 합니다. 아마 본인들이 느끼기에도 기능 설정이 너무 복잡하여 쓰다가 지친것 같습니다.  
포스트 준비를 하면서 Microsoft [모니터링 커뮤니티](https://github.com/microsoft/AzureMonitorCommunity)에 템플릿 기여 활동을 하려고 합니다. 오늘 첫 번째 템플릿인 NSG Flow Log Analysis의 Pull Request를 보낸 상태 입니다. Grafana 모니터링 대시보드를 만들면서 개발한 쿼리가 아주 많기 때문에 다양한 Workbook을 만들 수 있을 것 같습니다. Azure를 사용하고 있지만 별도 모니터링 솔루션을 도입하기엔 부담이 되는 분들이 적극적으로 Workbook을 사용하게 되면 좋겠네요. 조만간 백업 리포트, 가상머신 주요 메트릭 이 두가지를 추가로 업로드 할 것 같고, 앞으로 아래 내용처럼 사용자 요청이 많은 템플릿 위주로 작업을 할 것 같습니다. 일단... 빨간 박스 3가지를 자주 접하므로 먼저 작업이 이루어질것 같고 나머지는 어떻게 될지 모르겠네요.
{{< figure src="/img/workbooks/4.Top-Asked.png" title="" >}}  

Github에 업로드 되는 템플릿은 누구나 쉽게 설치하고 사용해야 할 수 있기 때문에 설정이 덜 복잡한 범용적인 템플릿만 업로드할 예정 입니다. 특정 환경에 적합한 Workbook을 만들고 싶을 경우 제 템플릿을 참고 하시거나 메일 부탁 드립니다. 포스트로 설명하기엔 템플리 설정이 복잡하고 어려운게 많아서 한계가 있습니다. 블로그 방문자가 그렇게 많지는 않지만, 그래도 문의를 취합하여 따로 포스트를 쓰거나 어떠한 방법으로든 안내를 드리겠습니다.  
　  

## 템플릿 설치 방법
----
템플릿 설치 방법은 매우 간단합니다. 제 [깃허브](https://github.com/chupark/AzureMonitorCommunity/blob/master/Azure%20Services/Network%20Watcher/Workbooks/NSG%20Flow%20Log/NSG%20Flow%20Log%20Analysis.workbook)에서 템플릿을 복사 합니다. Raw를 클릭하세요!
{{< figure src="/img/workbooks/1.installation.png" title="" >}}

새로운 페이지가 열리면 템플릿이 보입니다. 해당 템플릿을 전부 복사합니다.
{{< figure src="/img/workbooks/2.installation.png" title="" >}}

Azure Monitor - 통합문서(Workbook) - 고급 편집 - 템플릿 붙여넣기 - 적용 을 순서대로 수행합니다.
{{< figure src="/img/workbooks/3.installation.png" title="" >}}  

　  
## 상세 설정
----
해당 템플릿은 NSG Flow Log를 분석하는 간단한 템플릿 입니다. 상세한 설정 방법은 별도 [프로젝트](https://github.com/chupark/AzureMonitorCommunity/tree/master/Azure%20Services/Network%20Watcher/Workbooks/NSG%20Flow%20Log)에 첨부했으므로 해당 페이지 참고 부탁 드립니다.
{{< figure src="/img/workbooks/nsg/3.functions.gif" title="" >}}  
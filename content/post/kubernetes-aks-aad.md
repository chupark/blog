---
title: "AKS와 Azure AD RBAC 통합"
date: 2020-09-04T16:33:25+09:00
categories:
  - "kubernetes RBAC"
tags:
  - "kubernetes"
  - "AKS"
comments: true
draft: true
---

# AAD와 AKS의 RBAC
Kubernetes 클러스터 보안을 위해 Kubernetes 리소스를 컨트롤 할 수 있는 사용자를 제한할 수 있다. 예를 들어 클러스터 관리자를 한 명 선정하고 그 관리자 에게만 포드, 서비스등 리소스를 배포할 수 있는 권한을 부여할 수 있다. AKS를 사용하면 손쉽게 Azure AD의 원하는 사용자, 그룹에게 해당 작업을 수행할 수 있다.   
이를 위해선 클러스터의 RBAC 플러그인을 활성화하고, AKS관리형 Azure AD 기능이 "사용" 상태로 배포됐어야한다.
{{< figure src="/img/rbac/aks-role2.png" title="" >}}

　
<br>

## 기본
----
{{< figure src="/img/rbac/aks-role1.png" title="" >}}

Kubernetes는 사용자 정보를 클러스터 내부에 저장하지 않는다. 대신 RBAC 플러그인과 Azure AD등 클러스터 외부의 사용자 정보 시스템과 상호작용을 통해 해당 유저가 작업을 수행하기 위한 권한이 있는지 체크한다. 이를 위해 Kubernetes에는 롤(Role), 롤바인딩(RoleBinding) 리소스가 존재한다. 따라서 권한을 만들기 위해 Kubernetes 네임스페이스에 롤 리소스를 만들고, Azure AD의 사용자나 그룹을 해당 롤에 매핑시키는 롤바인딩 리소스를 만들어 사용자의 인증과 권한을 동시에 관리할 수 있다. 특정 네임스페이스 뿐만 아니라 클러스터 레벨 롤과 롤 바인딩도 존재한다.  

<br>

> **_NOTE :_**  사용자 계정을 저장하지 않는대신 클러스터엔 서비스어카운트 라는 계정을 생성할 수 있는데, 사용자 대신 Azure 리소를 만들고 조회하는 Azure AD App과 같이 Kubernetes 클러스터의 리소스와 상호작용하는 서비스계정이다.

　

### 클러스터 어드민 조회
----
AKS 와 Azure AD 를 통합하여 배포하면 모든 권한을 가진 클러스터 어드민 롤과 롤바인딩 리소스가 자동으로 생성된다.

- 클러스터 롤
{{< figure src="/img/rbac/aks-role4.png" title="" >}}
{{< figure src="/img/rbac/aks-role4-1.png" title="" >}}
　
- 클러스터 롤바인딩
{{< figure src="/img/rbac/aks-role5.png" title="" >}}
{{< figure src="/img/rbac/aks-role6.png" title="" >}}
클러스터 롤바인딩의 Kind가 Group으로 되어있고 그룹의 ID를 Name 필드에서 확인할 수 있다. 해당 그룹을 Azure AD에서 조회하면 그룹 ID가 동일함을 확인할 수 있다.
{{< figure src="/img/rbac/aks-role7.png" title="" >}}
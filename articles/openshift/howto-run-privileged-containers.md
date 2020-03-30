---
title: Azure Red Hat 오픈시프트 클러스터에서 권한 있는 컨테이너 실행 | 마이크로 소프트 문서
description: 권한 있는 컨테이너를 실행하여 보안 및 규정 준수를 모니터링합니다.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 12/05/2019
keywords: aro, 오픈 시프트, 아쿠아섹, 트위스트록, 레드 햇
ms.openlocfilehash: e1c1dd9f27a207f78dd22e271f6b070c7f92f622
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271369"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>Azure Red Hat OpenShift 클러스터에서 권한 있는 컨테이너 실행

Azure Red Hat OpenShift 클러스터에서는 임의권한 컨테이너를 실행할 수 없습니다.
두 개의 보안 모니터링 및 규정 준수 솔루션이 ARO 클러스터에서 실행할 수 있습니다.
이 문서에서는 보안 제품 공급업체의 일반 OpenShift 배포 설명서의 차이점에 대해 설명합니다.


공급업체의 지침을 따르기 전에 이 지침을 읽어보십시오.
아래 제품별 단계의 섹션 제목은 공급업체 설명서의 섹션 제목을 직접 참조합니다.

## <a name="before-you-begin"></a>시작하기 전에

대부분의 보안 제품의 설명서에는 클러스터 관리자 권한이 있다고 가정합니다.
고객 관리자는 Azure Red Hat OpenShift에 모든 권한이 없습니다. 클러스터 전체 리소스를 수정하는 데 필요한 권한은 제한되어 있습니다.

먼저 을 실행하여 `oc get scc`사용자가 고객 관리자로 클러스터에 로그인되어 있는지 확인합니다. 고객 관리자 그룹의 구성원인 모든 사용자에게는 클러스터에서 SCC(보안 컨텍스트 제약 조건)를 볼 수 있는 권한이 있습니다.

그런 다음 이진 버전이 `oc` `3.11.154`있는지 확인합니다.
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>아쿠아 시큐리티를 위한 제품별 단계
수정할 기본 지침은 [Aqua Security 배포 설명서에서](https://docs.aquasec.com/docs/openshift-red-hat)찾을 수 있습니다. 여기서 단계는 Aqua 배포 설명서와 함께 실행됩니다.

첫 번째 단계는 업데이트될 필요한 SCC에 추가하는 것입니다. 이러한 주석은 클러스터의 동기화 포드가 이러한 SSC에 대한 변경 내용을 되돌리지 못하게 합니다.

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>1단계: 전제 조건 준비
클러스터 관리자 역할 대신 ARO 고객 관리자로 클러스터에 로그인해야 합니다.

프로젝트 및 서비스 계정을 만듭니다.
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

클러스터 리더 역할을 할당하는 대신 다음 명령을 사용하여 고객-관리자-클러스터 역할을 aqua-account에 할당합니다.
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

1단계의 나머지 지침을 계속 따르십시오.  이러한 지침은 Aqua 레지스트리의 비밀 설정에 대해 설명합니다.

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>2단계: 아쿠아 서버, 데이터베이스 및 게이트웨이 배포
아쿠아 콘솔.yaml 설치를 위해 아쿠아 설명서에 제공된 단계를 따르십시오.

제공된 을 `aqua-console.yaml`수정합니다.  레이블이 지정된 상위 `kind: ClusterRole` 두 `kind: ClusterRoleBinding`개체를 제거하고 .  이러한 리소스는 현재 고객 관리자가 수정 `ClusterRole` 및 `ClusterRoleBinding` 개체에 대한 권한이 없기 때문에 만들어지지 않습니다.

두 번째 수정 사항은 `kind: Route` `aqua-console.yaml`의 일부에 대한 것입니다. 파일의 개체에 대해 `kind: Route` 다음 yaml을 바꿉습니다. `aqua-console.yaml`
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: aqua-web
  name: aqua-web
  namespace: aqua-security
spec:
  port:
    targetPort: aqua-web
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: aqua-web
    weight: 100
  wildcardPolicy: None
```

나머지 지침을 따르십시오.

### <a name="step-3-login-to-the-aqua-server"></a>3단계: 아쿠아 서버에 로그인
이 섹션은 어떤 방식으로도 수정되지 않습니다.  아쿠아 설명서를 따르십시오.

다음 명령을 사용하여 아쿠아 콘솔 주소를 가져옵니다.
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>4단계: 아쿠아 집행자 배치
집행자를 배포할 때 다음 필드를 설정합니다.

| 필드          | 값         |
| -------------- | ------------- |
| 오케스트레이터   | OpenShift     |
| ServiceAccount | 아쿠아 계정  |
| Project        | 아쿠아 보안 |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>프리스마 클라우드 / 트위스트록을 위한 제품별 단계

수정할 기본 지침은 [Prisma 클라우드 배포 설명서에서](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html) 확인할 수 있습니다.

"프리스마 `twistcli` 클라우드 설치" 및 "프리스마 클라우드 소프트웨어 다운로드" 섹션에 설명된 대로 도구를 설치하여 시작합니다.

새 오픈시프트 프로젝트 만들기
```
oc new-project twistlock
```

"프리스마 클라우드 이미지를 개인 레지스트리로 푸시"(개인 레지스트리로 푸시) 섹션(선택 사항)을 건너뜁니다. Azure Red Hat 오픈시프트에서 작동하지 않습니다. 대신 온라인 레지스트리를 사용합니다.

아래에 설명된 수정 을 적용하는 동안 공식 문서를 따를 수 있습니다.
"콘솔 설치" 섹션으로 시작합니다.

### <a name="install-console"></a>콘솔 설치

`oc create -f twistlock_console.yaml` 2단계에서 네임스페이스를 만들 때 오류가 발생합니다.
당신은 안전하게 무시할 수 있습니다, 네임 스페이스는 `oc new-project` 명령으로 이전에 만들어졌습니다.

저장소 `azure-disk` 유형에 사용 합니다.

### <a name="create-an-external-route-to-console"></a>콘솔에 대한 외부 경로 만들기

oc 명령을 선호하는 경우 설명서 또는 아래 지침을 따를 수 있습니다.
다음 경로 정의를 컴퓨터의 twistlock_route.yaml라는 파일에 복사합니다.
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    name: console
  name: twistlock-console
  namespace: twistlock
spec:
  port:
    targetPort: mgmt-http
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: twistlock-console
    weight: 100
  wildcardPolicy: None
```
그런 다음 실행합니다.
```
oc create -f twistlock_route.yaml
```

이 명령을 사용하여 Twistlock 콘솔에 할당된 URL을 얻을 수 있습니다.`oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>콘솔 구성

트위스트록 설명서를 따르십시오.

### <a name="install-defender"></a>수비수 설치

`oc create -f defender.yaml` 2단계에서는 클러스터 역할 및 클러스터 역할 바인딩을 만들 때 오류가 발생합니다.
이런 예외는 무시할 수 있습니다.

방어는 계산 노드에만 배포됩니다. 노드 선택기로 제한할 필요가 없습니다.

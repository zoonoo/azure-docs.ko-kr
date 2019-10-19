---
title: Azure Monitor에서 실시간으로 컨테이너 로그 보기 | Microsoft Docs
description: 이 문서에서는 컨테이너의 Azure Monitor with kubectl를 사용 하지 않고 컨테이너 로그 (stdout/stderr) 및 이벤트의 실시간 보기에 대해 설명 합니다.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/12/2019
ms.openlocfilehash: 25cfe10ec192f874d050bca22ce1b85c2d1afbb4
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554092"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>실시간으로 로그 및 이벤트를 보는 방법 (미리 보기)
컨테이너에 대 한 Azure Monitor는 현재 미리 보기로 제공 되는 기능을 포함 하 여 kubectl 명령을 실행할 필요 없이 AKS (Azure Kubernetes Service) 컨테이너 로그 (stdout/stderr) 및 이벤트에 대 한 실시간 보기를 제공 합니다. 두 옵션 중 하나를 선택 하면 **노드**, **컨트롤러**및 **컨테이너** 보기의 성능 데이터 테이블 아래에 새 창이 표시 됩니다. 실시간으로 문제 해결에 도움이 되는 컨테이너 엔진에 의해 생성 된 라이브 로깅 및 이벤트를 보여 줍니다.

>[!NOTE]
>이 기능은 Azure 중국을 비롯 한 모든 Azure 지역에서 사용할 수 있습니다. 현재는 Azure 미국 정부에서 사용할 수 없습니다.

>[!NOTE]
>이 기능을 사용 하려면 **Azure Kubernetes Service 클러스터 사용자 역할** 에 클러스터 리소스에 대 한 액세스 권한이 있어야 합니다. [Azure Kubernetes Cluster 사용자 역할에 대해 자세히 알아보세요](https://docs.microsoft.com/en-us/azure/aks/control-kubeconfig-access#available-cluster-roles-permissions).

라이브 로그는 로그에 대 한 액세스를 제어 하는 세 가지 방법을 지원 합니다.

1. Kubernetes RBAC 권한 부여를 사용하도록 설정되지 않은 AKS
2. Kubernetes RBAC 권한 부여를 사용하도록 설정된 AKS
3. AD (Azure Active Directory) SAML 기반 single sign-on을 사용 하는 AKS

## <a name="kubernetes-cluster-without-rbac-enabled"></a>RBAC를 사용하도록 설정되지 않은 Kubernets 클러스터
 
Kubernetes RBAC 인증으로 구성되지 않았거나 Azure AD Single Sign-On과 통합된 Kubernetes 클러스터가 있는 경우 다음 단계를 수행할 필요가 없습니다. Kubernetes 권한 부여에서 kube-api를 사용하므로 읽기 전용 권한이 필요합니다.

## <a name="kubernetes-rbac-authorization"></a>Kubernetes RBAC 권한 부여
Kubernetes RBAC 권한 부여를 사용하도록 설정한 경우 클러스터 역할 바인딩을 적용해야 합니다. 다음 예제 단계에서는 이 yaml 구성 템플릿에서 클러스터 역할 바인딩을 구성하는 방법을 보여 줍니다. 

1. yaml 파일을 복사하여 붙여넣고, LogReaderRBAC.yaml로 저장합니다.  

    ```
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRole 
    metadata: 
       name: containerHealth-log-reader 
    rules: 
       - apiGroups: [""] 
         resources: ["pods/log", "events"] 
         verbs: ["get", "list"]  
    --- 
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRoleBinding 
    metadata: 
       name: containerHealth-read-logs-global 
    roleRef: 
        kind: ClusterRole 
        name: containerHealth-log-reader 
        apiGroup: rbac.authorization.k8s.io 
    subjects: 
       - kind: User 
         name: clusterUser 
         apiGroup: rbac.authorization.k8s.io
    ```

2. 처음으로 구성 하는 경우 다음 명령을 실행 하 여 클러스터 규칙 바인딩을 적용 합니다. `kubectl create -f LogReaderRBAC.yaml`. 라이브 이벤트 로그를 도입 하기 전에 이전에 실시간 로그 미리 보기 지원을 사용 하도록 설정한 경우 구성을 업데이트 하려면 다음 명령을 실행 합니다. `kubectl apply -f LogReaderRBAC.yaml`.

## <a name="configure-aks-with-azure-active-directory"></a>Azure Active Directory를 사용하여 AKS 구성

사용자 인증에 Azure AD(Active Directory)를 사용하도록 AKS를 구성할 수 있습니다. 처음으로 구성 하는 경우 [Azure Kubernetes 서비스와 Azure Active Directory 통합](../../aks/azure-ad-integration.md)을 참조 하세요. [클라이언트 응용 프로그램](../../aks/azure-ad-integration.md#create-the-client-application)을 만드는 단계에서 다음을 지정 합니다.

-  **리디렉션 URI**: 두 개의 **웹** 응용 프로그램 유형을 만들어야 합니다. 첫 번째 기준 URL 값을 `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` 해야 하 고 두 번째 기준 URL 값을 `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` 해야 합니다.
- 응용 프로그램을 등록 한 후 **개요** 페이지의 왼쪽 창에서 **인증** 을 선택 합니다. **인증** 페이지의 **고급 설정** 아래에서 암시적으로 **액세스 토큰** 및 **ID 토큰** 을 부여 하 고 변경 내용을 저장 합니다.

>[!NOTE]
>Azure 중국 지역에서이 기능을 사용 하는 경우 첫 번째 기준 URL 값을 `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` 하 고 두 번째 기준 URL 값을 `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` 해야 합니다.

>[!NOTE]
>Single sign-on에 대 한 Azure Active Directory를 사용 하 여 인증을 구성 하는 것은 새 AKS 클러스터의 초기 배포 중에만 수행할 수 있습니다. 이미 배포된 AKS 클러스터에는 Single Sign-On을 구성할 수 없습니다.
  
>[!IMPORTANT]
>업데이트 된 URI를 사용 하 여 사용자 인증을 위해 Azure AD를 다시 구성한 경우 브라우저의 캐시를 지워 업데이트 된 인증 토큰이 다운로드 되어 적용 되는지 확인 합니다.   

## <a name="view-live-logs-and-events"></a>라이브 로그 및 이벤트 보기

**노드**, **컨트롤러**및 **컨테이너** 뷰에서 컨테이너 엔진에 의해 생성 된 실시간 로그 이벤트를 볼 수 있습니다. 속성 창에서 **라이브 데이터 보기 (미리 보기)** 옵션을 선택 하면 연속 스트림에서 로그 및 이벤트를 볼 수 있는 성능 데이터 테이블 아래에 창이 표시 됩니다.

![노드 속성 창 라이브 로그 보기 옵션](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

로그 및 이벤트 메시지는 뷰에서 선택한 리소스 유형에 따라 제한 됩니다.

| 보기 | 리소스 종류 | 로그 또는 이벤트 | 제공 된 데이터 |
|------|---------------|--------------|----------------|
| 노드 | 노드 | 행사 | 노드가 선택 되 면 이벤트가 필터링 되지 않고 클러스터 차원의 Kubernetes 이벤트가 표시 됩니다. 창 제목에는 클러스터의 이름이 표시 됩니다. |
| 노드 | Pod | 행사 | Pod를 선택 하면 이벤트가 해당 네임 스페이스로 필터링 됩니다. 창 제목에 pod의 네임 스페이스가 표시 됩니다. | 
| Controllers | Pod | 행사 | Pod를 선택 하면 이벤트가 해당 네임 스페이스로 필터링 됩니다. 창 제목에 pod의 네임 스페이스가 표시 됩니다. |
| Controllers | Controller | 행사 | 컨트롤러를 선택 하면 이벤트가 해당 네임 스페이스로 필터링 됩니다. 창 제목에는 컨트롤러의 네임 스페이스가 표시 됩니다. |
| 노드/컨트롤러/컨테이너 | 컨테이너 | 로그 | 창 제목은 컨테이너를 그룹화 할 pod의 이름을 표시 합니다. |

AAD를 사용하여 AKS 클러스터를 SSO로 구성하면 해당 브라우저 세션 중에 처음 사용할 때 인증을 요구하는 메시지가 표시됩니다. 계정을 선택하고 Azure를 통해 인증을 수행합니다.  

성공적으로 인증되면 라이브 로그 창이 가운데 창의 아래쪽 섹션에 표시됩니다. 가져오기 상태 표시기에서 창의 오른쪽 끝에 있는 녹색 확인 표시를 보여 주면 데이터를 검색할 수 있음을 의미합니다.
    
  ![데이터가 검색되는 라이브 로그 창](./media/container-insights-live-logs/live-logs-pane-01.png)  

검색 창에서 키 단어를 기준으로 필터링 하 여 로그 또는 이벤트에서 해당 텍스트를 강조 표시 하 고 맨 오른쪽의 검색 표시줄에서 필터와 일치 하는 결과의 수를 확인할 수 있습니다.

  ![라이브 로그 창의 필터링 예](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

이벤트를 보는 동안 검색 표시줄의 오른쪽에 있는 **필터** 약을 사용 하 여 결과를 추가로 제한할 수 있습니다. 선택한 리소스에 따라 약에는 선택한 pod, 네임 스페이스 또는 클러스터가 나열 됩니다.  

자동 스크롤를 일시 중단 하 고 창의 동작을 제어 하 고 읽기를 통해 새 데이터를 수동으로 스크롤할 수 있도록 하려면 **스크롤** 옵션을 클릭 합니다. 자동 스크롤를 다시 사용 하도록 설정 하려면 **스크롤** 옵션을 다시 클릭 하면 됩니다. **일시 중지** 옵션을 클릭 하 고 다시 시작할 준비가 되 면 **재생**을 클릭 하 여 로그 나 이벤트 데이터의 검색을 일시 중지할 수도 있습니다.  

![라이브 로그 창의 라이브 보기 일시 중지](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

**분석의**드롭다운 목록 뷰에서 **컨테이너 로그 보기** 를 선택 하 여 기록 컨테이너 로그를 보려면 Azure Monitor 로그로 이동할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure Monitor를 사용하고 AKS 클러스터의 다른 측면을 모니터링하는 방법을 계속 알아보려면 [Azure Kubernetes 서비스 상태](container-insights-analyze.md)를 참조하세요.

- [로그 쿼리 예](container-insights-log-search.md#search-logs-to-analyze-data) 를 확인 하 여 미리 정의 된 쿼리 및 예제를 확인 하거나 사용자 지정 하 여 클러스터에 대 한 경고, 시각화 또는 분석을 평가 하거나 사용자 지정 합니다.

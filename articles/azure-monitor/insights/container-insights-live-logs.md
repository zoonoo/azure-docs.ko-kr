---
title: Azure Monitor에서 실시간으로 컨테이너 로그 보기 | Microsoft Docs
description: 이 문서에서는 컨테이너에 대 한 Azure Monitor를 사용 하 여 kubectl을 사용 하지 않고 컨테이너 로그 (stdout/stderr) 및 이벤트의 실시간 보기를 설명 합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: magoedte
ms.openlocfilehash: 8d4cc5e46066ad2f18d596d0484f62f478b4cc23
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514319"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>실시간 (미리 보기) 로그 및 이벤트를 보는 방법
컨테이너에 대 한 azure Monitor는 현재 미리 보기로 kubectl 명령을 실행 하지 않고도 Azure Kubernetes Service (AKS) 컨테이너 로그 (stdout/stderr) 및 이벤트에 실시간 보기를 제공 하는 기능을 포함 합니다. 두 옵션 중 하나를 선택 하면 새 창이 표 아래에 표시 성능 데이터에는 **노드**를 **컨트롤러**, 및 **컨테이너** 보기. 실시간 로깅 및 더 많은 도움을 실시간으로 문제를 해결 컨테이너 엔진에 의해 생성 된 이벤트를 보여 줍니다. 

>[!NOTE]
>**참가자** 클러스터 리소스에 대 한 액세스는이 기능이 작동 하기 위해 필요 합니다.
>

실시간 로그는 로그에 대 한 액세스를 제어 하는 세 가지 방법을 지원 합니다.

1. Kubernetes RBAC 권한 부여를 사용하도록 설정되지 않은 AKS 
2. Kubernetes RBAC 권한 부여를 사용하도록 설정된 AKS
3. AKS에서 사용 하 여 Azure AD (Active Directory) SAML 기반 single sign을 사용 하도록 설정 

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

2. 다음 명령을 실행 하 여 클러스터 규칙 바인딩을 만든 경우 처음으로 구성 됩니다. `kubectl create -f LogReaderRBAC.yaml`합니다. 실시간 로그에 구성을 업데이트 하려면 라이브 이벤트 로그를 도입 했습니다 전에 미리 보기에 대 한 지원 하도록 이전에 설정한 경우 다음 명령을 실행: `kubectl apply -f LogReaderRBAC.yml`합니다. 

## <a name="configure-aks-with-azure-active-directory"></a>Azure Active Directory를 사용하여 AKS 구성
사용자 인증에 Azure AD(Active Directory)를 사용하도록 AKS를 구성할 수 있습니다. 처음으로 구성 된 경우 참조 [통합 Azure Active Directory와 Azure Kubernetes Service](../../aks/azure-ad-integration.md)합니다. 만드는 단계는 [클라이언트 응용 프로그램](../../aks/azure-ad-integration.md#create-client-application)를 두 개를 지정 해야 **리디렉션 URI** 항목입니다. 두 Uri는 다음과 같습니다.

- https://ininprodeusuxbase.microsoft.com/*
- https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html  

>[!NOTE]
>Single Sign-On을 위해 Azure Active Directory를 사용하여 인증을 구성하는 작업은 새 AKS 클러스터의 초기 배포 중에만 수행할 수 있습니다. 이미 배포된 AKS 클러스터에는 Single Sign-On을 구성할 수 없습니다. 인증을 구성 해야 **앱 등록 (레거시)** 옵션 및 uri에서 와일드 카드 사용을 지원 하기 위해 Azure ad에서로 등록 하 여 목록에 추가 된 **네이티브** 앱.
> 

## <a name="view-live-logs-and-events"></a>실시간 로그 보기 및 이벤트

컨테이너 엔진에서 생성 될 때 실시간 로그 이벤트를 볼 수 있습니다 합니다 **노드가**, **컨트롤러**, 및 **컨테이너** 보기. 속성 창에서 선택 **라이브 데이터 (미리 보기)를 확인** 창과 옵션 연속 스트림으로 로그 및 이벤트를 볼 수는 있는 성능 데이터 표 아래에 표시 됩니다. 

![노드 속성 창 보기 실시간 로그 옵션](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

로그 및 이벤트 메시지 보기에서 선택 된 리소스 종류에 따라 제한 됩니다.

| 보기 | 리소스 종류 | 로그 또는 이벤트 | 표시 되는 데이터 |
|------|---------------|--------------|----------------|
| 노드 | 노드 | 행사 | 노드가 선택 될 때 이벤트 필터링 되지 않음 및 클러스터 전체 Kubernetes 이벤트를 표시 합니다. 창 제목에는 클러스터의 이름이 표시 됩니다. |
| 노드 | Pod | 행사 | Pod를 선택 하면 해당 네임 스페이스에 이벤트 필터링 됩니다. 창 제목 pod의 네임 스페이스를 보여 줍니다. | 
| Controllers | Pod | 행사 | Pod를 선택 하면 해당 네임 스페이스에 이벤트 필터링 됩니다. 창 제목 pod의 네임 스페이스를 보여 줍니다. |
| Controllers | Controller | 행사 | 컨트롤러를 선택 하면 해당 네임 스페이스에 이벤트 필터링 됩니다. 창 제목 컨트롤러의 네임 스페이스를 보여 줍니다. |
| 노드/컨트롤러/컨테이너 | 컨테이너 | 로그 | 창 제목 컨테이너 pod의 이름을 사용 하 여 그룹화를 보여 줍니다. |

AAD를 사용하여 AKS 클러스터를 SSO로 구성하면 해당 브라우저 세션 중에 처음 사용할 때 인증을 요구하는 메시지가 표시됩니다. 계정을 선택하고 Azure를 통해 인증을 수행합니다.  

성공적으로 인증되면 라이브 로그 창이 가운데 창의 아래쪽 섹션에 표시됩니다. 가져오기 상태 표시기에서 창의 오른쪽 끝에 있는 녹색 확인 표시를 보여 주면 데이터를 검색할 수 있음을 의미합니다.
    
  ![데이터가 검색되는 라이브 로그 창](./media/container-insights-live-logs/live-logs-pane-01.png)  

검색 창에서 맨 오른쪽의 검색 표시줄에 로그 또는 이벤트에 해당 텍스트를 강조 표시 키 word에서 필터링 할 수 있습니다, 그리고 필터 아웃 일치 하는 얼마나 많은 결과가 표시 됩니다.   

  ![라이브 로그 창의 필터링 예](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

자동 스크롤을 일시 중단 하 고 창의 동작을 제어 수동으로 새 데이터 읽기를 스크롤할 수를 클릭 합니다 **스크롤** 옵션입니다. 자동 스크롤을 다시 활성화 하려면 클릭할 합니다 **스크롤** 다시 옵션입니다. 클릭 하 여 로그 또는 이벤트 데이터를 검색할을 일시 중지할 수도 있습니다는 **일시 중지** 옵션을 다시 시작할 준비가 때 클릭할 **재생**합니다.  

![라이브 로그 창의 라이브 보기 일시 중지](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

선택 하 여 기록 컨테이너 로그를 확인 하려면 Azure Monitor 로그로 이동할 수 있습니다 **컨테이너 로그 보기** 드롭 다운 목록에서 **analytics에서 보기**합니다.

## <a name="next-steps"></a>다음 단계
- Azure Monitor를 사용하고 AKS 클러스터의 다른 측면을 모니터링하는 방법을 계속 알아보려면 [Azure Kubernetes 서비스 상태](container-insights-analyze.md)를 참조하세요.
- 뷰 [쿼리 예제 로그](container-insights-log-search.md#search-logs-to-analyze-data) 에 미리 정의 된 쿼리 및 평가 하거나 경고를 시각화, 분석 하 여 클러스터에 대 한 사용자 지정 하는 예제를 참조 하세요.

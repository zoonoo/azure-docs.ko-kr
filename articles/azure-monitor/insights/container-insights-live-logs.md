---
title: Azure Monitor에서 실시간으로 컨테이너 로그 보기 | Microsoft Docs
description: 이 문서에서는 컨테이너에 대한 Azure Monitor에서 kubectl을 사용하지 않고 컨테이너 로그(stdout/stderr)를 실시간으로 보는 방법을 설명합니다.
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
ms.date: 01/09/2019
ms.author: magoedte
ms.openlocfilehash: 6fe8cccf60e60ada34e3b7847964958cf6e03c4a
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904749"
---
# <a name="how-to-view-container-logs-real-time-with-azure-monitor-for-containers-preview"></a>Azure Monitor에서 컨테이너 로그를 실시간으로 보는 방법(미리 보기)
현재 미리 보기로 있는 이 기능은 Kubectl 명령을 실행하지 않고도 AKS(Azure Kubernetes Service) 컨테이너 로그(stdout/stderr)에 대한 실시간 보기를 제공합니다. 이 옵션을 선택하면 **컨테이너** 보기에서 컨테이너 성능 데이터 테이블 아래에 새 창이 나타납니다.  여기에는 실시간으로 문제를 해결하는 데 더 많은 도움을 주기 위해 컨테이너 엔진에서 생성하는 라이브 로깅이 표시됩니다. **참가자** 클러스터 리소스에 대 한 액세스는이 기능이 작동 하기 위해 필요 합니다.

로그에 대한 액세스를 제어하기 위해 라이브 로그에서 지원하는 세 가지 방법은 다음과 같습니다.

1. Kubernetes RBAC 권한 부여를 사용하도록 설정되지 않은 AKS 
2. Kubernetes RBAC 권한 부여를 사용하도록 설정된 AKS
3. Azure AD(Active Directory) SAML 기반 Single Sign-On을 사용하도록 설정된 AKS 

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
        resources: ["pods/log"] 
        verbs: ["get"] 
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

2. `kubectl create -f LogReaderRBAC.yaml` 명령을 실행하여 클러스터 규칙 바인딩을 만듭니다. 

## <a name="configure-aks-with-azure-active-directory"></a>Azure Active Directory를 사용하여 AKS 구성
사용자 인증에 Azure AD(Active Directory)를 사용하도록 AKS를 구성할 수 있습니다. 처음 구성하는 경우 [Azure Kubernetes Service와 Azure Active Directory 통합](../../aks/aad-integration.md)을 참조하세요. [클라이언트 애플리케이션](../../aks/aad-integration.md#create-client-application)을 만들고 **리디렉션 URI**를 지정하는 단계를 수행하는 동안 `https://ininprodeusuxbase.microsoft.com/*` 목록에 다른 URI를 추가해야 합니다.  

>[!NOTE]
>Single Sign-On을 위해 Azure Active Directory를 사용하여 인증을 구성하는 작업은 새 AKS 클러스터의 초기 배포 중에만 수행할 수 있습니다. 이미 배포된 AKS 클러스터에는 Single Sign-On을 구성할 수 없습니다.  
> 

## <a name="view-live-logs"></a>라이브 로그 보기
**컨테이너** 를 볼 때 **컨테이너 로그 보기** 또는 **컨테이너 라이브 로그 보기**를 사용할 수 있습니다.  **컨테이너 라이브 로그 보기**를 선택하면 컨테이너 성능 데이터 테이블 아래에 새 창이 표시됩니다. 여기서는 문제를 실시간으로 해결하는 데 도움이 되도록 컨테이너 엔진에서 생성된 라이브 로깅을 보여 줍니다.  
1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. **Microsoft Azure** 메뉴에서 **모니터**를 선택한 다음, **컨테이너**를 선택합니다.  
3. **모니터링되는 컨테이너** 보기 아래의 목록에서 컨테이너를 선택합니다.  
4. **컨테이너** 보기를 선택하고 선택한 컨테이너에 대한 속성 패널에서 **컨테이너 라이브 로그 보기** 링크가 나열됩니다.  
5. AAD를 사용하여 AKS 클러스터를 SSO로 구성하면 해당 브라우저 세션 중에 처음 사용할 때 인증을 요구하는 메시지가 표시됩니다. 계정을 선택하고 Azure를 통해 인증을 수행합니다.  

성공적으로 인증되면 라이브 로그 창이 가운데 창의 아래쪽 섹션에 표시됩니다. 가져오기 상태 표시기에서 창의 오른쪽 끝에 있는 녹색 확인 표시를 보여 주면 데이터를 검색할 수 있음을 의미합니다.
    
  ![데이터가 검색되는 라이브 로그 창](./media/container-insights-live-logs/live-logs-pane-01.png)  

검색 창에서 키워드로 필터링하여 로그의 해당 텍스트를 강조 표시할 수 있습니다.   

  ![라이브 로그 창의 필터링 예](./media/container-insights-live-logs/live-logs-pane-filter-01.png)

자동 스크롤을 일시 중단하고, 창의 동작을 제어하고, 새 로그 데이터 읽기를 수동으로 스크롤할 수 있게 하려면 **스크롤** 옵션을 클릭합니다.  자동 스크롤을 사용하도록 다시 설정하려면 **스크롤** 옵션을 다시 클릭하면 됩니다.  또한 **일시 중지** 옵션을 클릭하여 로그 데이터 검색을 일시 중지할 수 있으며, 다시 시작할 준비가 되면 **재생**을 클릭하면 됩니다.  

![라이브 로그 창의 라이브 보기 일시 중지](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

## <a name="next-steps"></a>다음 단계
Azure Monitor를 사용하고 AKS 클러스터의 다른 측면을 모니터링하는 방법을 계속 알아보려면 [Azure Kubernetes 서비스 상태](container-insights-analyze.md)를 참조하세요.

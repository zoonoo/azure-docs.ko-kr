---
title: 컨테이너 라이브 데이터(미리 보기)에 대한 Azure 모니터 설정 | 마이크로 소프트 문서
description: 이 문서에서는 컨테이너에 대 한 Azure 모니터와 kubectl을 사용 하지 않고 컨테이너 로그 (stdout/stderr) 및 이벤트의 실시간 보기를 설정 하는 방법에 대해 설명 합니다.
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: f19071ca642cd229cbd7d49b4eab90c970672eee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275374"
---
# <a name="how-to-set-up-the-live-data-preview-feature"></a>라이브 데이터(미리 보기) 기능을 설정하는 방법

AKS(Azure Kubernetes) 클러스터의 컨테이너에 대한 Azure 모니터를 사용하여 라이브 데이터(미리 보기)를 보려면 Kubernetes 데이터에 대한 액세스 권한을 부여하도록 인증을 구성해야 합니다. 이 보안 구성을 사용하면 Azure 포털에서 직접 Kubernetes API를 통해 데이터에 실시간으로 액세스할 수 있습니다.

이 기능은 로그, 이벤트 및 메트릭에 대한 액세스를 제어하는 다음 방법을 지원합니다.

- Kubernetes RBAC 권한 부여를 사용하도록 설정되지 않은 AKS
- Kubernetes RBAC 권한 부여를 사용하도록 설정된 AKS
    - 클러스터 역할 바인딩 클러스터로 구성된 ** [AKS모니터링사용자](https://docs.microsoft.com/rest/api/aks/managedclusters/listclustermonitoringusercredentials?view=azurermps-5.2.0)**
- Azure Active Directory(AD) SAML 기반 단일 사인온으로 AKS 사용 가능

이러한 지침에는 Kubernetes 클러스터에 대한 관리 액세스와 사용자 인증에 AD(Azure Active Directory)를 사용하도록 구성하는 경우 Azure AD에 대한 관리 액세스가 모두 필요합니다.  

이 문서에서는 클러스터의 라이브 데이터(미리 보기) 기능에 대한 액세스를 제어하도록 인증을 구성하는 방법을 설명합니다.

- RBAC(역할 기반 액세스 제어) 지원 AKS 클러스터
- Azure Active Directory 통합 AKS 클러스터입니다. 

>[!NOTE]
>개인 클러스터로 활성화된 AKS [클러스터는](https://azure.microsoft.com/updates/aks-private-cluster/) 이 기능에서 지원되지 않습니다. 이 기능은 브라우저에서 프록시 서버를 통해 Kubernetes API에 직접 액세스하는 데 의존합니다. 네트워킹 보안을 사용하여 이 프록시에서 Kubernetes API를 차단하면 이 트래픽이 차단됩니다. 

>[!NOTE]
>이 기능은 Azure China를 포함한 모든 Azure 지역에서 사용할 수 있습니다. 현재 Azure 미국 정부에서는 사용할 수 없습니다.

## <a name="authentication-model"></a>인증 모델

라이브 데이터(미리 보기) 기능은 명령줄 도구와 동일한 `kubectl` Kubernetes API를 사용합니다. Kubernetes API 끝점은 브라우저에서 유효성을 검사할 수 없는 자체 서명된 인증서를 활용합니다. 이 기능은 내부 프록시를 사용하여 AKS 서비스를 사용하여 인증서의 유효성을 검사하여 트래픽을 신뢰할 수 있도록 합니다.

Azure 포털은 Azure Active Directory 클러스터에 대한 로그인 자격 증명의 유효성을 검사하고 클러스터 를 만드는 동안 클라이언트 등록 설정으로 리디렉션하고 이 문서에서 다시 구성하라는 메시지를 표시합니다. 이 동작은 `kubectl`에 필요한 인증 프로세스와 유사합니다. 

>[!NOTE]
>클러스터에 대한 권한 부여는 Kubernetes에 의해 관리되며 클러스터가 구성된 보안 모델입니다. 이 기능에 액세스하는 사용자는 실행과 `az aks get-credentials -n {your cluster name} -g {your resource group}`유사한 Kubernetes 구성(kubeconfig)을 다운로드할 수 있는 권한이 필요합니다.*kubeconfig* 이 구성 파일에는 **AZURE Kubernetes 서비스 클러스터 사용자 역할에**대한 권한 부여 및 인증 토큰이 포함되어 있습니다. 여기에는 AD(Azure Active Directory) SAML 기반 단일 사인온을 통해 AKS를 사용하도록 설정하면 Azure AD 및 클라이언트 등록 세부 정보가 포함되어 있습니다.

>[!IMPORTANT]
>이 기능을 `kubeconfig` 다운로드하고 사용하려면 Azure [Kubernetes 클러스터 사용자 역할이](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions) 클러스터에 필요합니다. 사용자는 이 기능을 활용하기 위해 클러스터에 대한 기여자 액세스가 필요하지 **않습니다.** 

## <a name="using-clustermonitoringuser-with-rbac-enabled-clusters"></a>RBAC 지원 클러스터를 사용하는 클러스터 모니터링사용자 사용

RBAC 권한 부여를 [사용하도록 설정한](#configure-kubernetes-rbac-authorization) 후 Kubernetes 사용자 역할 바인딩 **클러스터 사용자** 액세스 권한을 허용하기 위해 추가 구성 변경 사항을 적용할 필요가 없으므로 AKS는 **clusterMonitoringUser라는**새 Kubernetes 클러스터 역할 바인딩을 추가했습니다. 이 클러스터 역할 바인딩에는 Kubernetes API에 액세스하는 데 필요한 모든 권한이 있으며 라이브 데이터(미리 보기) 기능을 활용하기 위한 끝점입니다.

이 새 사용자와 함께 라이브 데이터(미리 보기) 기능을 활용하려면 AKS 클러스터 리소스에서 [기여자](../../role-based-access-control/built-in-roles.md#contributor) 역할의 구성원이 어야 합니다. 컨테이너에 대한 Azure Monitor를 사용하도록 설정하면 기본적으로 이 사용자를 사용하여 인증하도록 구성됩니다. 클러스터MonitoringUser 역할 바인딩이 클러스터에 없는 경우 **클러스터User** 대신 인증에 사용 됩니다.

AKS는 2020년 1월에 이 새 역할 바인딩을 릴리스했기 때문에 2020년 1월 이전에 생성된 클러스터에는 해당 역할 바인딩이 없습니다. 2020년 1월 이전에 만들어진 클러스터가 있는 경우 새 **클러스터MonitoringUser는** 클러스터에서 PUT 작업을 수행하거나 클러스터에서 다른 작업을 수행하여 클러스터 버전 업데이트와 같은 클러스터에서 PUT 작업을 수행하여 기존 클러스터에 추가할 수 있습니다.

## <a name="kubernetes-cluster-without-rbac-enabled"></a>RBAC를 사용하도록 설정되지 않은 Kubernets 클러스터

Kubernetes RBAC 인증으로 구성되지 않았거나 Azure AD Single Sign-On과 통합된 Kubernetes 클러스터가 있는 경우 다음 단계를 수행할 필요가 없습니다. 이는 RBAC가 아닌 구성에서 기본적으로 관리 권한이 있기 때문입니다.

## <a name="configure-kubernetes-rbac-authorization"></a>Kubernetes RBAC 권한 구성

Kubernetes RBAC 권한 부여를 사용하도록 설정하면 두 명의 **사용자(clusterUser** 및 **clusterAdmin)가** 사용되어 Kubernetes API에 액세스합니다. 이는 관리 옵션 `az aks get-credentials -n {cluster_name} -g {rg_name}` 없이 실행하는 것과 유사합니다. 즉, **클러스터User** Kubernetes API의 끝점에 대 한 액세스 권한을 부여 해야 합니다.

다음 예제 단계에서는 이 yaml 구성 템플릿에서 클러스터 역할 바인딩을 구성하는 방법을 보여 줍니다.

1. yaml 파일을 복사하여 붙여넣고, LogReaderRBAC.yaml로 저장합니다.  

    ```
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRole 
    metadata: 
       name: containerHealth-log-reader 
    rules: 
        - apiGroups: ["", "metrics.k8s.io", "extensions", "apps"] 
          resources: 
             - "pods/log" 
             - "events" 
             - "nodes" 
             - "pods" 
             - "deployments" 
             - "replicasets" 
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

2. 구성을 업데이트하려면 다음 명령을 `kubectl apply -f LogReaderRBAC.yaml`실행합니다.

>[!NOTE] 
> 이전 버전의 `LogReaderRBAC.yaml` 파일을 클러스터에 적용한 경우 위의 1단계에 표시된 새 코드를 복사하여 붙여넣은 다음 2단계에 표시된 명령을 실행하여 클러스터에 적용합니다.

## <a name="configure-ad-integrated-authentication"></a>AD 통합 인증 구성 

사용자 인증을 위해 Azure Active Directory(AD)를 사용하도록 구성된 AKS 클러스터는 이 기능에 액세스하는 사용자의 로그인 자격 증명을 사용합니다. 이 구성에서는 Azure AD 인증 토큰을 사용하여 AKS 클러스터에 로그인할 수 있습니다.

Azure AD 클라이언트 등록을 다시 구성하여 Azure Portal이 권한 부여 페이지를 신뢰할 수 있는 리디렉션 URL로 리디렉션할 수 있도록 해야 합니다. 그러면 Azure AD의 사용자는 **ClusterRoles** 및 **ClusterRoleBindings를**통해 동일한 Kubernetes API 끝점에 직접 액세스할 수 있습니다. 

Kubernetes의 고급 보안 설정에 대한 자세한 내용은 [Kubernetes 설명서를](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)참조하십시오. 

>[!NOTE]
>새 RBAC 지원 클러스터를 만드는 경우 [Azure Active Directory와 Azure Kubernetes 서비스 통합을](../../aks/azure-ad-integration.md) 참조하고 단계에 따라 Azure AD 인증을 구성합니다. 클라이언트 응용 프로그램을 만드는 단계에서 는 아래 3단계에서 지정된 컨테이너와 일치하는 컨테이너에 대해 Azure Monitor용으로 만들어야 하는 두 개의 리디렉션 URL을 강조 표시됩니다.

### <a name="client-registration-reconfiguration"></a>클라이언트 등록 재구성

1. Azure **포털의 Azure Active Directory > 앱 등록에서** Azure AD에서 Kubernetes 클러스터에 대한 클라이언트 등록을 찾습니다.

2. 왼쪽 창에서 **인증을** 선택합니다. 

3. 이 목록에 두 개의 리디렉션 URL을 **웹** 응용 프로그램 유형으로 추가합니다. 첫 번째 기본 URL `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` 값이어야 하며 두 `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`번째 기본 URL 값이어야 합니다.

    >[!NOTE]
    >Azure China에서 이 기능을 사용하는 경우 첫 번째 기본 `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` URL 값이어야 하며 `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`두 번째 기본 URL 값이어야 합니다. 
    
4. 리디렉션 URL을 등록한 후 **암시적 권한 부여에서** **액세스 토큰** 및 **ID 토큰** 옵션을 선택한 다음 변경 내용을 저장합니다.

>[!NOTE]
>단일 로그온에 대한 Azure Active Directory를 사용 하 고 인증을 구성 하는 것은 새 AKS 클러스터의 초기 배포 하는 동안에만 수행할 수 있습니다. 이미 배포된 AKS 클러스터에는 Single Sign-On을 구성할 수 없습니다.
  
>[!IMPORTANT]
>업데이트된 URI를 사용하여 사용자 인증을 위해 Azure AD를 다시 구성한 경우 브라우저의 캐시를 지우고 업데이트된 인증 토큰이 다운로드되고 적용되도록 합니다.

## <a name="grant-permission"></a>사용 권한 부여

라이브 데이터(미리 보기) 기능에 액세스하려면 각 Azure AD 계정에 Kubernetes의 적절한 API에 대한 사용 권한이 부여되어야 합니다. Azure Active Directory 계정을 부여하는 단계는 [Kubernetes RBAC 인증](#configure-kubernetes-rbac-authorization) 섹션에 설명된 단계와 유사합니다. yaml 구성 템플릿을 클러스터에 적용하기 전에 **ClusterRoleBinding** 아래의 **클러스터사용자를** 원하는 사용자로 바꿉습니다. 

>[!IMPORTANT]
>RBAC 바인딩을 부여하는 사용자가 동일한 Azure AD 테넌트에 있는 경우 userPrincipalName에 따라 사용 권한을 할당합니다. 사용자가 다른 Azure AD 테넌트에 있는 경우 objectId 속성을 쿼리하고 사용합니다.

AKS 클러스터 **클러스터롤바인딩을**구성하는 추가 도움말은 [RBAC 바인딩 만들기](../../aks/azure-ad-integration-cli.md#create-rbac-binding)를 참조하십시오.

## <a name="next-steps"></a>다음 단계

이제 설치 인증이 있으므로 클러스터에서 [메트릭,](container-insights-livedata-metrics.md) [배포](container-insights-livedata-deployments.md)및 [이벤트 및 로그를](container-insights-livedata-overview.md) 실시간으로 볼 수 있습니다.

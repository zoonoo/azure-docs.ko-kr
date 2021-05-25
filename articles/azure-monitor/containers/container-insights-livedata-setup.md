---
title: 컨테이너 인사이트 라이브 데이터 설정(미리 보기) | Microsoft Docs
description: 이 문서에서는 kubectl을 사용하지 않고 컨테이너 인사이트를 사용하여 컨테이너 로그(stdout/stderr) 및 이벤트의 실시간 보기를 설정하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 01/08/2020
ms.custom: references_regions
ms.openlocfilehash: 4302bdbb3d71c890f7fb0cfb82ab5f8d5aecbd43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101713782"
---
# <a name="how-to-set-up-the-live-data-preview-feature"></a>라이브 데이터(미리 보기) 기능을 설정하는 방법

AKS(Azure Kubernetes Service) 클러스터의 컨테이너 인사이트를 사용하여 라이브 데이터(미리 보기)를 보려면 Kubernetes 데이터에 대한 액세스 권한을 부여하도록 인증을 구성해야 합니다. 이 보안 구성을 통해 Azure Portal에서 직접 Kubernetes API를 통해 데이터에 실시간으로 액세스할 수 있습니다.

이 기능은 로그, 이벤트 및 메트릭에 대한 액세스를 제어하는 다음과 같은 방법을 지원합니다.

- Kubernetes RBAC 권한 부여를 사용하도록 설정되지 않은 AKS
- Kubernetes RBAC 권한 부여를 사용하도록 설정된 AKS
    - 클러스터 역할 바인딩 **[clusterMonitoringUser](/rest/api/aks/managedclusters/listclustermonitoringusercredentials)** 로 구성된 AKS
- Azure AD(Active Directory) SAML 기반 Single Sign-On을 사용하도록 설정된 AKS

이러한 지침을 따르려면 Kubernetes 클러스터에 대한 관리 액세스 권한이 필요하고, 사용자 인증에 AD(Azure Active Directory)를 사용하도록 구성하는 경우 Azure AD에 대한 관리 액세스 권한도 필요합니다.

이 문서에서는 클러스터에서 라이브 데이터(미리 보기) 기능에 대한 액세스를 제어하도록 인증을 구성하는 방법을 설명합니다.

- Kubernetes RBAC(Kubernetes 역할 기반 액세스 제어)가 설정된 AKS 클러스터
- Azure Active Directory가 통합된 AKS 클러스터입니다.


## <a name="authentication-model"></a>인증 모델

라이브 데이터(미리 보기) 기능은 `kubectl` 명령줄 도구와 동일한 Kubernetes API를 활용합니다. Kubernetes API 엔드포인트는 브라우저에서 유효성을 검사할 수 없는 자체 서명된 인증서를 활용합니다. 이 기능은 내부 프록시를 사용하여 AKS 서비스에 대한 인증서의 유효성을 검사하고 트래픽을 신뢰할 수 있는지 확인합니다.

Azure Portal은 Azure Active Directory 클러스터에 대한 로그인 자격 증명의 유효성을 검사하라는 메시지를 표시하고, 클러스터를 만드는 동안 클라이언트 등록 설정으로 리디렉션합니다(이 문서에서는 다시 구성됨). 이 동작은 `kubectl`에서 요구하는 인증 프로세스와 유사합니다.

>[!NOTE]
>클러스터에 대한 권한 부여는 Kubernetes 및 구성된 보안 모델을 통해 관리됩니다. 이 기능에 액세스하는 사용자는 `az aks get-credentials -n {your cluster name} -g {your resource group}`을 실행할 때와 비슷하게 Kubernetes 구성(*kubeconfig*)을 다운로드할 권한이 있어야 합니다. 이 구성 파일에는 **Azure Kubernetes Service 클러스터 사용자 역할**(Azure RBAC가 설정된 경우) 및 Kubernetes RBAC 권한 부여가 설정되지 않은 AKS 클러스터에 대한 권한 부여 및 인증 토큰이 포함되어 있습니다. AKS에 AD(Azure Active Directory) SAML 기반 Single Sign-On이 설정된 경우, 이 파일에 Azure AD에 대한 정보와 클라이언트 등록 세부 정보가 포함됩니다.

>[!IMPORTANT]
>이 기능의 사용자가 `kubeconfig`를 다운로드하고 이 기능을 사용하려면 클러스터에 대한 [Azure Kubernetes 클러스터 사용자 역할](../../role-based-access-control/built-in-roles.md)이 있어야 합니다. 사용자에게 클러스터에 대한 기여자 액세스 권한이 **없어도** 이 기능을 활용할 수 있습니다.

## <a name="using-clustermonitoringuser-with-kubernetes-rbac-enabled-clusters"></a>Kubernetes RBAC가 설정된 클러스터와 함께 clusterMonitoringUser 사용

[Kubernetes RBAC](#configure-kubernetes-rbac-authorization) 권한 부여를 사용하도록 설정한 후, Kubernetes 클러스터 역할 바인딩 **clusterUser** 에 라이브 데이터(미리 보기) 기능에 대한 액세스를 허용하는 추가 구성 변경을 적용할 필요가 없도록 AKS가 **clusterMonitoringUser** 라는 새 Kubernetes 클러스터 역할 바인딩을 추가했습니다. 이 클러스터 역할 바인딩에는 라이브 데이터(미리 보기) 기능을 활용하기 위해 Kubernetes API 및 엔드포인트에 액세스하는 데 필요한 모든 권한이 기본 제공됩니다.

이 새 사용자와 함께 라이브 데이터(미리 보기) 기능을 활용하려면 AKS 클러스터 리소스에 대한 [Azure Kubernetes Service 클러스터 사용자](../../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) 또는 [기여자](../../role-based-access-control/built-in-roles.md#contributor) 역할의 구성원이어야 합니다. 컨테이너 인사이트를 사용하도록 설정하면 기본적으로 clusterMonitoringUser를 사용하여 인증하도록 구성됩니다. clusterMonitoringUser 역할 바인딩이 클러스터에 존재하지 않는 경우 **clusterUser** 가 대신 인증에 사용됩니다. 기여자는 clusterMonitoringUser(있는 경우)에 대한 액세스 권한을 제공하고, Azure Kuberenetes Service 클러스터 사용자는 clusterUser에 대한 액세스 권한을 제공합니다. 이러한 두 역할은 모두 이 기능을 사용할 수 있는 충분한 액세스 권한을 제공합니다.

AKS는 2020년 1월에 이 새 역할 바인딩을 릴리스했으므로, 2020년 1월 이전에 만든 클러스터에는 이 역할이 포함되지 않습니다. 2020년 1월 이전에 만든 클러스터가 있는 경우, 클러스터에서 PUT 작업을 수행하거나 클러스터에 대한 PUT 작업을 수행하는 다른 모든 작업(예: 클러스터 버전 업데이트)을 클러스터에서 수행하여 새 **clusterMonitoringUser** 를 기존 클러스터에 추가할 수 있습니다.

## <a name="kubernetes-cluster-without-kubernetes-rbac-enabled"></a>Kubernetes RBAC가 설정되지 않은 Kubernets 클러스터

Kubernetes RBAC 인증으로 구성되지 않았거나 Azure AD Single Sign-On과 통합된 Kubernetes 클러스터가 있는 경우 다음 단계를 수행할 필요가 없습니다. 이는 기본적으로 RBAC가 아닌 구성에서 관리 권한을 갖고 있기 때문입니다.

## <a name="configure-kubernetes-rbac-authorization"></a>Kubernetes RBAC 권한 부여 구성

Kubernetes RBAC 권한 부여를 사용하도록 설정하면 두 명의 사용자(**clusterUser** 및 **clusterAdmin**)가 Kubernetes API에 액세스하는 데 사용됩니다. 이는 관리 옵션 없이 `az aks get-credentials -n {cluster_name} -g {rg_name}`을 실행하는 것과 유사합니다. 즉, **clusterUser** 에 Kubernetes API의 엔드포인트에 대한 액세스 권한을 부여해야 합니다.

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

2. 구성을 업데이트하려면 `kubectl apply -f LogReaderRBAC.yaml` 명령을 실행합니다.

>[!NOTE]
> 이전 버전의 `LogReaderRBAC.yaml` 파일을 클러스터에 적용한 경우 위의 1단계에 표시된 새 코드를 복사하고 붙여넣은 다음, 2단계에 나와 있는 명령을 실행하여 클러스터에 적용합니다.

## <a name="configure-ad-integrated-authentication"></a>AD 통합 인증 구성

사용자 인증에 AD(Azure Active Directory)를 사용하도록 구성된 AKS 클러스터는 이 기능에 액세스하는 사용자의 로그인 자격 증명을 활용합니다. 이 구성에서 Azure AD 인증 토큰을 사용하여 AKS 클러스터에 로그인할 수 있습니다.

Azure Portal에서 권한 부여 페이지를 신뢰할 수 있는 리디렉션 URL로 리디렉션할 수 있도록 Azure AD 클라이언트 등록을 다시 구성해야 합니다. 그러면 Azure AD의 사용자에게 **ClusterRoles** 및 **ClusterRoleBindings** 를 통해 동일한 Kubernetes API 엔드포인트에 대한 액세스 권한이 직접 부여됩니다.

Kubernetes의 고급 보안 설정에 대한 자세한 내용은 [Kubernetes 설명서](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)를 참조하세요.

>[!NOTE]
>새 Kubernetes RBAC 지원 클러스터를 만드는 경우 [Azure Kubernetes Service와 Azure Active Directory 통합](../../aks/azure-ad-integration-cli.md)을 참조하고 Azure AD 인증을 구성하는 단계를 따르세요. 클라이언트 애플리케이션을 만드는 단계에서 해당 섹션의 참고 사항에는 아래 3단계에서 지정한 것과 일치하는 컨테이너 인사이트를 위해 만들어야 하는 두 개의 리디렉션 URL이 요약되어 있습니다.

### <a name="client-registration-reconfiguration"></a>클라이언트 등록 재구성

1. Azure Portal의 **Azure Active Directory > 앱 등록** 에서 Azure AD의 Kubernetes 클러스터에 대한 클라이언트 등록을 찾습니다.

2. 왼쪽 창에서 **인증** 을 선택합니다.

3. 이 목록에 두 개의 리디렉션 URL을 **웹** 애플리케이션 유형으로 추가합니다. 첫 번째 기준 URL 값은 `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`이어야 하고, 두 번째 기준 URL 값은 `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`이어야 합니다.

    >[!NOTE]
    >Azure 중국에서 이 기능을 사용하는 경우 첫 번째 기준 URL 값이 `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`이어야 하며 두 번째 기준 URL 값은 `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`이어야 합니다.

4. 리디렉션 URL을 등록한 후 **암시적 권한 부여** 에서 **액세스 토큰** 및 **ID 토큰** 에 대한 옵션을 선택한 후 변경 내용을 저장합니다.

>[!NOTE]
>Single Sign-On을 위해 Azure Active Directory를 사용하여 인증을 구성하는 작업은 새 AKS 클러스터의 초기 배포 중에만 수행할 수 있습니다. 이미 배포된 AKS 클러스터에는 Single Sign-On을 구성할 수 없습니다.

>[!IMPORTANT]
>사용자 인증을 위해 업데이트된 URI를 사용하여 Azure AD를 다시 구성한 경우 브라우저의 캐시를 지워서 업데이트된 인증 토큰이 다운로드되고 적용되도록 해야 합니다.

## <a name="grant-permission"></a>사용 권한 부여

라이브 데이터(미리 보기) 기능에 액세스하려면 각 Azure AD 계정에 Kubernetes의 적절한 API에 대한 권한을 부여해야 합니다. Azure Active Directory 계정에 권한을 부여하는 단계는 [Kubernetes RBAC 인증](#configure-kubernetes-rbac-authorization) 섹션에 설명된 단계와 유사합니다. yaml 구성 템플릿을 클러스터에 적용하기 전에 **ClusterRoleBinding** 의 **clusterUser** 를 원하는 사용자로 바꿉니다.

>[!IMPORTANT]
>Kubernetes RBAC 바인딩 권한을 부여한 사용자가 동일한 Azure AD 테넌트에 있는 경우 userPrincipalName에 따라 권한을 할당합니다. 사용자가 다른 Azure AD 테넌트에 있는 경우에는 objectId 속성을 쿼리하고 사용합니다.

AKS 클러스터 **ClusterRoleBinding** 을 구성하는 방법에 대한 추가 도움말은 [Kubernetes RBAC 바인딩 만들기](../../aks/azure-ad-integration-cli.md#create-kubernetes-rbac-binding)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 인증이 설정되었으므로 클러스터에서 실시간으로 [메트릭](container-insights-livedata-metrics.md), [배포](container-insights-livedata-deployments.md), [이벤트 및 로그](container-insights-livedata-overview.md)를 볼 수 있습니다.

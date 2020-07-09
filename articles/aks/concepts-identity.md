---
title: 개념 - AKS(Azure Kubernetes Service)의 액세스 및 ID
description: Azure Active Directory 통합, Kubernetes RBAC(역할 기반 액세스 제어), 역할 및 바인딩을 포함하여 AKS(Azure Kubernetes Service)의 액세스 및 ID에 대해 알아봅니다.
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: 55fd27d473bd47fd3321bdb2e730e4ef2d35352f
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86106177"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)의 액세스 및 ID 옵션

여러 가지 방법으로 인증 하 고, 액세스/권한 부여 하 고, Kubernetes 클러스터를 보호할 수 있습니다. Kubernetes RBAC (역할 기반 액세스 제어)를 사용 하 여 사용자, 그룹 및 서비스 계정에 필요한 리소스에만 액세스 권한을 부여할 수 있습니다. AKS (Azure Kubernetes Service)를 사용 하 여 Azure Active Directory 및 Azure RBAC를 사용 하 여 보안 및 사용 권한 구조를 추가로 향상 시킬 수 있습니다. 이러한 접근 방식은 클러스터 액세스를 보호 하 고 개발자 및 운영자에 게 필요한 최소 권한만 제공 하는 데 도움이 됩니다.

이 문서에서는 AKS에서 권한을 인증하고 할당하는 데 도움이 되는 핵심 개념을 소개합니다.

- [Kubernetes 역할 기반 액세스 제어 (RBAC)](#kubernetes-role-based-access-controls-rbac)
  - [Roles 및 ClusterRoles](#roles-and-clusterroles)
  - [RoleBindings 및 ClusterRoleBindings](#rolebindings-and-clusterrolebindings) 
  - [Kubernetes 서비스 계정](#kubernetes-service-accounts)
- [Azure Active Directory 통합](#azure-active-directory-integration)
- [Azure RBAC](#azure-role-based-access-controls-rbac)
  - [Azure RBAC를 사용 하 여 AKS 리소스에 대 한 액세스 권한 부여](#azure-rbac-to-authorize-access-to-the-aks-resource)
  - [Kubernetes 권한 부여에 대 한 Azure RBAC (미리 보기)](#azure-rbac-for-kubernetes-authorization-preview)


## <a name="kubernetes-role-based-access-controls-rbac"></a>Kubernetes 역할 기반 액세스 제어 (RBAC)

사용자가 수행할 수 있는 작업에 대 한 세부적인 필터링을 제공 하기 위해 Kubernetes는 RBAC (역할 기반 액세스 제어)를 사용 합니다. 이 제어 메커니즘을 통해 리소스 만들기 또는 수정, 실행 중인 애플리케이션 워크로드에서 로그 보기 등의 작업을 수행할 수 있는 권한을 사용자 또는 사용자 그룹에 할당할 수 있습니다. 이러한 권한은 단일 네임스페이스로 범위가 지정되거나 전체 AKS 클러스터에서 부여할 수 있습니다. Kubernetes RBAC를 사용하여 권한을 정의하는 *역할*을 만든 다음, *역할 바인딩*을 통해 해당 역할을 사용자에게 할당합니다.

자세한 내용은 [RBAC 권한 부여 사용][kubernetes-rbac]을 참조하세요.


### <a name="roles-and-clusterroles"></a>Roles 및 ClusterRoles

Kubernetes RBAC를 사용하여 사용자에게 권한을 할당하기 전에 먼저 해당 권한을 *Role*로 정의합니다. Kubernetes 역할은 권한을 *부여*합니다. *Deny* 권한의 개념은 없습니다.

역할은 네임스페이스 내에서 권한을 부여하는 데 사용됩니다. 전체 클러스터 또는 지정된 네임스페이스 외부의 클러스터 리소스에 권한을 부여해야 하는 경우 *ClusterRoles*를 대신 사용할 수 있습니다.

ClusterRole은 리소스에 권한을 부여하는 것과 동일한 방식으로 작동하지만 특정 네임스페이스가 아닌 전체 클러스터의 리소스에 적용할 수 있습니다.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings 및 ClusterRoleBindings

리소스에 권한을 부여하는 역할이 정의되면 *RoleBinding*을 사용하여 해당 Kubernetes RBAC 권한을 할당합니다. AKS 클러스터가 [Azure Active Directory와 통합](#azure-active-directory-integration)되는 경우 바인딩은 이러한 Azure AD 사용자에 게 클러스터 내에서 작업을 수행할 수 있는 권한을 부여 하는 방법입니다. [역할 기반 액세스 제어 및 Azure Active Directory id를 사용 하 여 클러스터 리소스에 대 한 액세스를 제어](azure-ad-rbac.md)하는 방법을 참조 하세요.

역할 바인딩은 지정된 네임스페이스에 대한 역할을 할당하는 데 사용됩니다. 이 방법을 사용하면 사용자가 할당된 네임스페이스의 애플리케이션 리소스에만 액세스할 수 있는 단일 AKS 클러스터를 논리적으로 구분할 수 있습니다. 전체 클러스터 또는 네임스페이스 외부의 리소스에 역할을 바인딩해야 하는 경우 *ClusterRoleBindings*를 대신 사용할 수 있습니다.

ClusterRoleBinding은 사용자에게 역할을 바인딩하는 것과 동일한 방식으로 작동하지만 특정 네임스페이스가 아닌 전체 클러스터의 리소스에 적용할 수 있습니다. 이 방법을 통해 관리자 또는 지원 엔지니어에게 AKS 클러스터의 모든 리소스에 대한 액세스 권한을 부여할 수 있습니다.


> [!NOTE]
> Microsoft/AKS에서 수행 하는 모든 클러스터 작업은 기본 제공 Kubernetes 역할 `aks-service` 및 기본 제공 역할 바인딩에서 사용자 동의를 사용 하 여 수행 됩니다 `aks-service-rolebinding` . 이 역할을 통해 AKS는 클러스터 문제를 해결 하 고 진단할 수 있지만 권한을 수정 하거나 역할 또는 역할 바인딩 또는 기타 높은 권한 작업을 만들 수 없습니다. 역할 액세스는 JIT (just-in-time) 액세스를 사용 하는 활성 지원 티켓 에서만 사용 하도록 설정 됩니다. [AKS 지원 정책](support-policies.md)에 대해 자세히 알아보세요.


### <a name="kubernetes-service-accounts"></a>Kubernetes 서비스 계정

Kubernetes의 기본 사용자 유형 중 하나는 *서비스 계정*입니다. 서비스 계정은 Kubernetes API에서 보유하고 관리합니다. 서비스 계정에 대한 자격 증명은 Kubernetes 비밀로 저장되어 권한 있는 Pod에서 API 서버와 통신하는 데 사용할 수 있습니다. 대부분의 API 요청은 서비스 계정 또는 일반 사용자 계정에 대한 인증 토큰을 제공합니다.

일반 사용자 계정을 사용 하면 서비스 및 프로세스 뿐만 아니라 사용자 관리자 또는 개발자에 게 더 많은 기존 액세스를 사용할 수 있습니다. Kubernetes 자체는 일반 사용자 계정 및 암호를 저장 하는 id 관리 솔루션을 제공 하지 않습니다. 대신, 외부 ID 솔루션이 Kubernetes에 통합될 수 있습니다. AKS 클러스터의 경우 이 통합 ID 솔루션은 Azure Active Directory입니다.

Kubernetes의 ID 옵션에 대한 자세한 내용은 [Kubernetes 인증][kubernetes-authentication]을 참조하세요.

## <a name="azure-active-directory-integration"></a>Azure Active Directory 통합

AKS 클러스터의 보안은 Azure AD(Active Directory) 통합으로 강화될 수 있습니다. 수십 년간의 엔터프라이즈 ID 관리를 기반으로 하여 구축된 Azure AD는 핵심 디렉터리 서비스, 애플리케이션 액세스 관리 및 ID 보호를 결합한 다중 테넌트, 클라우드 기반 디렉터리 및 ID 관리 서비스입니다. Azure AD를 사용하면 온-프레미스 ID를 AKS 클러스터에 통합하여 계정 관리 및 보안을 위한 단일 원본을 제공할 수 있습니다.

![AKS 클러스터와 Azure Active Directory 통합](media/concepts-identity/aad-integration.png)

Azure AD 통합 AKS 클러스터를 사용하면 네임스페이스 내에서 또는 클러스터 전체에서 Kubernetes 리소스에 대한 액세스 권한을 사용자 또는 그룹에 부여할 수 있습니다. `kubectl` 구성 컨텍스트를 가져오려면 사용자가 [az aks get-credentials][az-aks-get-credentials] 명령을 실행할 수 있습니다. 그런 다음 사용자가를 사용 하 여 AKS 클러스터와 상호 작용 하면 `kubectl` 해당 AZURE AD 자격 증명을 사용 하 여 로그인 하 라는 메시지가 표시 됩니다. 이 방법은 사용자 계정 관리 및 암호 자격 증명을 위한 단일 원본을 제공합니다. 사용자는 클러스터 관리자가 정의한 리소스에만 액세스할 수 있습니다.

OpenID Connect와 함께 AKS 클러스터에 Azure AD 인증이 제공됩니다. OpenID Connect는 OAuth 2.0 프로토콜을 기반으로 하는 ID 계층입니다. Openid connect Connect에 대 한 자세한 내용은 [Open ID connect 설명서] [열기-id-연결]를 참조 하세요. Kubernetes 클러스터 내부에서 웹 후크 [토큰 인증][webhook-token-docs] 을 사용 하 여 인증 토큰을 확인 합니다. Webhook 토큰 인증은 AKS 클러스터의 일부로 구성 및 관리됩니다.

Kubernetes 클러스터 내부에서 인증 토큰을 확인하는 데 Webhook 토큰 인증이 사용됩니다. Webhook 토큰 인증은 AKS 클러스터의 일부로 구성 및 관리됩니다.

### <a name="webhook-and-api-server"></a>웹 후크 및 API 서버

![웹 후크 및 API 서버 인증 흐름](media/concepts-identity/auth-flow.png)

위의 그림에 표시 된 것 처럼 API 서버는 AKS webhook 서버를 호출 하 고 다음 단계를 수행 합니다.

1. Azure AD 클라이언트 응용 프로그램은 kubectl에서 [OAuth 2.0 장치 권한 부여 흐름](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code)을 사용 하 여 사용자를 로그인 하는 데 사용 됩니다.
2. Azure AD는 access_token, id_token 및 refresh_token를 제공 합니다.
3. 사용자가 kubeconfig의 access_token를 사용 하 여 kubectl에 대 한 요청을 만듭니다.
4. Kubectl는 APIServer에 access_token를 보냅니다.
5. API 서버는 유효성 검사를 수행 하기 위해 Auth WebHook 서버를 사용 하 여 구성 됩니다.
6. 인증 webhook 서버는 Azure AD 공개 서명 키를 확인 하 여 JSON Web Token 서명이 유효한 지 확인 합니다.
7. 서버 응용 프로그램은 사용자 제공 자격 증명을 사용 하 여 MS Graph API에서 로그인 한 사용자의 그룹 멤버 자격을 쿼리 합니다.
8. 응답은 액세스 토큰의 UPN (사용자 계정 이름) 클레임 및 개체 ID를 기반으로 하는 사용자의 그룹 멤버 자격과 같은 사용자 정보를 사용 하 여 APIServer에 전송 됩니다.
9. API는 Kubernetes Role/RoleBinding에 따라 권한 부여 결정을 수행 합니다.
10. 권한이 부여 되 면 API 서버는 kubectl에 대 한 응답을 반환 합니다.
11. Kubectl 사용자에 게 피드백을 제공 합니다.
 
**[여기](managed-aad.md)에서 AAD와 AKS를 통합 하는 방법에 대해 알아봅니다.**

## <a name="azure-role-based-access-controls-rbac"></a>Azure RBAC(역할 기반 액세스 제어)

Azure RBAC는 Azure 리소스에 대한 액세스를 세밀하게 관리할 수 있는 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 기반의 권한 부여 시스템입니다.

 Azure RBAC는 Azure 구독 내에서 리소스에 대해 작동 하도록 설계 되었지만 Kubernetes RBAC는 AKS 클러스터 내의 Kubernetes 리소스에서 작동 하도록 설계 되었습니다. 

Azure RBAC를 사용하여 적용할 권한을 설명하는 *역할 정의*를 만듭니다. 그런 다음 특정 *범위*에 대 한 역할 할당을 통해 사용자 또는 그룹에이 역할 정의가 할당 됩니다 .이 역할은 개별 리소스, 리소스 그룹 또는 구독에 대 한 *역할 할당* 을 통해 할당 됩니다.

자세한 내용은 [Azure RBAC란?][azure-rbac]을 참조하세요.

AKS 클러스터를 완벽 하 게 운영 하는 데 필요한 액세스 수준에는 다음 두 가지가 있습니다. 
1. [Azure 구독에서 AKS 리소스에 액세스](#azure-rbac-to-authorize-access-to-the-aks-resource)합니다. 이 프로세스를 통해 AKS Api를 사용 하 여 클러스터를 확장 하거나 업그레이드 하는 작업을 제어 하 고 kubeconfig를 끌어올 수 있습니다.
2. Kubernetes API에 대 한 액세스. 이 액세스는 [KUBERNETES RBAC](#kubernetes-role-based-access-controls-rbac) (일반적으로) 또는 [Kubernetes 권한 부여를 위해 AKS와 Azure RBAC를 통합](#azure-rbac-for-kubernetes-authorization-preview) 하 여 제어 됩니다.

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Azure RBAC를 사용 하 여 AKS 리소스에 대 한 액세스 권한 부여

Azure RBAC를 사용 하면 하나 이상의 구독에서 AKS 리소스에 대 한 세분화 된 액세스 권한을 사용자에 게 제공할 수 있습니다. 예를 들어 클러스터를 확장 하 고 업그레이드 하는 등의 작업을 수행할 수 있도록 하는 [Azure Kubernetes Service 참가자 역할이](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) 있을 수 있습니다. 다른 사용자에 게는 Admin kubeconfig를 끌어올 수 있는 권한만 부여 하는 [Azure Kubernetes Service 클러스터 관리자 역할이](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) 있을 수 있습니다.

또는 사용자에 게 일반적인 [참가자](../role-based-access-control/built-in-roles.md#contributor) 역할을 제공할 수 있습니다 .이 역할에는 권한 자체 관리를 제외 하 고 AKS 리소스에서 가능한 모든 작업 및 위의 사용 권한이 포함 됩니다.

Azure RBAC를 사용 하 여 Kubernetes API에 대 한 액세스를 제공 하는 kubeconfig 파일에 대 한 액세스를 보호 하는 방법에 대해서는 [여기](control-kubeconfig-access.md)를 참조 하세요.

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Kubernetes 권한 부여에 대 한 Azure RBAC (미리 보기)

Azure RBAC 통합을 사용 하면 AKS는 Kubernetes 권한 부여 webhook 서버를 사용 하 여 Azure RBAC 역할 정의 및 역할 할당을 사용 하 여 Azure AD 통합 K8s 클러스터 리소스의 권한 및 할당을 관리할 수 있습니다.

![Kubernetes 권한 부여 흐름에 대 한 Azure RBAC](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

위의 다이어그램에 표시 된 것 처럼 Azure RBAC 통합을 사용 하는 경우 Kubernetes API에 대 한 모든 요청은 [Azure Active integration 섹션](#azure-active-directory-integration)에 설명 된 것과 동일한 인증 흐름을 따릅니다. 

그러나 그 후에는 권한 부여를 위해 Kubernetes RBAC를 단독으로 활용 하는 대신 요청이 AAD에 존재 하는 한 요청은 실제로 Azure에서 권한을 부여 받습니다. Id가 AAD에 존재 하지 않는 경우 (예: Kubernetes service 계정), Azure RBAC는 시작 되지 않으며 정상적인 Kubernetes RBAC가 됩니다.

이 시나리오에서는 사용자에 게 네 가지 기본 제공 역할 중 하나를 제공 하거나, Kubernetes 역할을 수행 하는 것 처럼 사용자 지정 역할을 만들 수 있습니다. 그러나이 경우에는 Azure RBAC 메커니즘과 Api를 사용 합니다. 

예를 들어이 기능을 사용 하면 구독에 대해 사용자에 게 AKS 리소스에 대 한 사용 권한을 부여 하 고 설정 하 여 Kubernetes API에 대 한 액세스를 제어 하는 각 클러스터 내에서 부여할 역할 및 권한을 부여할 수 있습니다. 예를 들어 `Azure Kubernetes Service RBAC Viewer` 구독 범위에서 역할을 부여 하 고 해당 수신자가 모든 클러스터에서 모든 Kubernetes 개체를 나열 하 고 가져올 수 있지만 수정할 수는 없습니다.


#### <a name="built-in-roles"></a>기본 제공 역할

AKS는 다음과 같은 네 가지 기본 제공 역할을 제공 합니다. [Kubernetes 기본 제공 역할과](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) 비슷하지만 crds 지원과 같은 몇 가지 차이점이 있습니다. 각 기본 제공 역할에서 허용 하는 작업의 전체 목록을 보려면 [여기](../role-based-access-control/built-in-roles.md)를 참조 하세요.

| 역할                                | Description  |
|-------------------------------------|--------------|
| Azure Kubernetes 서비스 RBAC 뷰어  | 읽기 전용 액세스를 허용 하 여 네임 스페이스의 대부분의 개체를 표시 합니다. 역할 또는 역할 바인딩을 볼 수 없습니다. 암호의 내용을 읽으면 네임 스페이스의 자격 증명에 액세스할 수 있으므로이 역할은 보기를 허용 하지 않습니다 .이 `Secrets` `ServiceAccount` 는 네임 스페이스에서 API 액세스를 허용 합니다 `ServiceAccount` (권한 상승 형태).  |
| Azure Kubernetes 서비스 RBAC 기록기 | 네임 스페이스의 대부분의 개체에 대 한 읽기/쓰기 액세스를 허용 합니다. 이 역할은 역할이 나 역할 바인딩을 보거나 수정할 수 없습니다. 그러나이 역할을 사용 하 여 `Secrets` 네임 스페이스의 ServiceAccount로 pod를 액세스 하 고 실행할 수 있으므로 네임 스페이스에 있는 모든 ServiceAccount의 API 액세스 수준을 얻는 데 사용할 수 있습니다. |
| Azure Kubernetes 서비스 RBAC 관리자  | 네임 스페이스 내에서 부여할 수 있는 관리 액세스를 허용 합니다. 네임 스페이스 내에서 역할 및 역할 바인딩을 만드는 기능을 포함 하 여 네임 스페이스 (또는 클러스터 범위)에서 대부분의 리소스에 대 한 읽기/쓰기 액세스를 허용 합니다. 이 역할은 리소스 할당량 또는 네임 스페이스 자체에 대 한 쓰기 액세스를 허용 하지 않습니다. |
| Azure Kubernetes 서비스 RBAC 클러스터 관리자  | 슈퍼 사용자 액세스를 허용 하 여 모든 리소스에 대 한 작업을 수행할 수 있습니다. 클러스터의 모든 리소스와 모든 네임 스페이스에 대 한 모든 권한을 부여 합니다. |

**[여기](manage-azure-rbac.md)에서 Kubernetes 권한 부여에 대 한 Azure RBAC를 활용 하는 방법을 참조 하세요.**

## <a name="next-steps"></a>다음 단계

- Azure AD 및 Kubernetes RBAC를 시작하려면 [AKS와 Azure Active Directory 통합][aks-aad]을 참조하세요.
- 관련 모범 사례는 [AKS에서 인증 및 권한 부여에 대 한 모범 사례][operator-best-practices-identity]를 참조 하세요.
- Kubernetes 권한 부여에 대 한 Azure RBAC를 시작 하려면 [AKS (Azure Kubernetes Service) 클러스터 내에서 AZURE rbac를 사용 하 여 액세스 권한 부여](manage-azure-rbac.md)를 참조 하세요.
- Kubeconfig 파일의 보안을 시작 하려면 [클러스터 구성 파일에 대 한 액세스 제한](control-kubeconfig-access.md) 을 참조 하세요.

Kubernetes 및 AKS 핵심 개념에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Kubernetes/AKS 클러스터 및 워크로드][aks-concepts-clusters-workloads]
- [Kubernetes/AKS 보안][aks-concepts-security]
- [Kubernetes/AKS 가상 네트워크][aks-concepts-network]
- [Kubernetes/AKS 스토리지][aks-concepts-storage]
- [Kubernetes/AKS 크기 조정][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md

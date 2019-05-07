---
title: Azure Kubernetes Service와 Azure Active Directory 통합
description: Azure Active Directory 사용 AKS(Azure Kubernetes Service) 클러스터를 만드는 방법
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: iainfou
ms.openlocfilehash: 026c0eefc0c4fe31e72ecad91a4a7b558f367487
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192119"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Azure Kubernetes Service와 Azure Active Directory 통합

사용자 인증을 위해 Azure AD(Active Directory)를 사용하도록 AKS(Azure Kubernetes Service)를 구성할 수 있습니다. 이 구성에서는 Azure Active Directory 인증 토큰을 사용 하 여 AKS 클러스터에 서명할 수 있습니다. 또한 클러스터 관리자는 사용자의 id 또는 directory 그룹 멤버 자격에 따라 Kubernetes 역할 기반 액세스 제어 (RBAC)를 구성할 수 있습니다.

이 문서에서는 AKS 및 Azure AD에 대 한 필수 구성 요소를 배포 하는 방법을 다음 Azure AD 사용이 가능한 클러스터를 배포 하 고 Azure portal을 사용 하 여 AKS 클러스터에서 기본 RBAC 역할을 만드는 방법을 보여 줍니다. 할 수도 있습니다 [Azure CLI를 사용 하 여 이러한 단계를 완료할][azure-ad-cli]합니다.

다음 제한 사항이 적용됩니다.

- Azure AD는 RBAC 사용 클러스터를 새로 만들 때만 사용하도록 설정할 수 있습니다. 기존 AKS 클러스터에서는 Azure AD를 사용하도록 설정할 수 없습니다.
- *게스트* 사용자가 Azure AD에서 이러한 다른 디렉터리에서 페더레이션된 로그인을 사용 하는 것 처럼 사용할 수 없습니다.

## <a name="authentication-details"></a>인증 세부 정보

OpenID Connect와 함께 AKS 클러스터에 Azure AD 인증이 제공됩니다. OpenID Connect는 OAuth 2.0 프로토콜을 기반으로 하는 ID 계층입니다. OpenID Connect에 대한 자세한 내용은 [Open ID 연결 설명서][open-id-connect]를 참조하세요.

Kubernetes 클러스터 내부에서 인증 토큰을 확인하는 데 Webhook 토큰 인증이 사용됩니다. Webhook 토큰 인증은 AKS 클러스터의 일부로 구성 및 관리됩니다. Webhook 토큰 인증에 대한 자세한 내용은 [webhook 인증 설명서][kubernetes-webhook]를 참조하세요.

AKS 클러스터에 대 한 Azure AD 인증을 제공 하려면 두 개의 Azure AD 응용 프로그램 생성 됩니다. 첫 번째 응용 프로그램은 사용자 인증을 제공 하는 서버 구성 요소입니다. 두 번째 응용 프로그램은 인증에 대 한 CLI에서 메시지가 표시 되 면 사용 되는 클라이언트 구성 요소입니다. 이 클라이언트 응용 프로그램 클라이언트에서 제공한 자격 증명의 실제 인증을 위한 서버 응용 프로그램을 사용 합니다.

> [!NOTE]
> AKS 인증을 위해 Azure AD를 구성할 때 두 개의 Azure AD 애플리케이션이 구성됩니다. 각 응용 프로그램에 대 한 권한을 위임 하는 단계는 Azure 테 넌 트 관리자가 완료 되어야 합니다.

## <a name="create-server-application"></a>서버 애플리케이션 만들기

첫 번째 Azure AD 애플리케이션은 사용자 Azure AD 그룹 구성원 자격을 가져오는 데 사용됩니다. Azure portal에서이 응용 프로그램을 만듭니다.

1. 선택 **Azure Active Directory** > **앱 등록** > **새 등록**합니다.

    * 와 같은 응용 프로그램에 이름을 지정 *AKSAzureADServer*합니다.
    * 에 대 한 **지원 되는 계정 유형**, 선택 *계정에만이 조직 directory*합니다.
    * 선택할 *웹* 에 대 한 합니다 **리디렉션 URI** 형식과 같은 형식이 지정 된 URI 값을 입력할 *https://aksazureadserver*합니다.
    * 선택 **등록** 완료 합니다.

1. **매니페스트**를 선택하고 `groupMembershipClaims` 값을 `"All"`로 편집합니다.

    ![그룹 멤버 자격을 모두로 업데이트](media/aad-integration/edit-manifest.png)

    **저장** 완료 되 면 업데이트 합니다.

1. Azure AD 응용 프로그램의 왼쪽 탐색에서 선택 **인증서 및 비밀**합니다.

    * 선택할 **+ 새 클라이언트 암호**합니다.
    * 같은 키 설명을 추가한 *AKS Azure AD 서버*합니다. 만료 시간을 선택 하 고 선택 **추가**합니다.
    * 키 값을 적어둡니다. 이 초기 시간에만 표시에 해당 합니다. Azure AD 사용 AKS 클러스터를 배포할 때이 값 이라고는 `Server application secret`합니다.

1. Azure AD 응용 프로그램의 왼쪽 탐색에서 선택 **API 사용 권한**를 선택한 **권한을 추가 +** 합니다.

    * 아래 **Microsoft Api**, 선택 *Microsoft Graph*합니다.
    * 선택 **위임 된 권한**, 다음 확인란 옆에 **디렉터리 > (디렉터리 데이터 읽기), Directory.Read.All**합니다.
        * 기본값에 대 한 권한을 위임 하는 경우 **사용자 > User.Read (로그인 및 사용자 프로필 읽기)** 확인란을 선택이 사용 권한을 존재 하지 않는 합니다.
    * 선택 **응용 프로그램 사용 권한**, 다음 확인란 옆에 **디렉터리 > (디렉터리 데이터 읽기), Directory.Read.All**합니다.

        ![그래프 사용 권한 설정](media/aad-integration/graph-permissions.png)

    * 선택할 **권한 추가** 에 업데이트를 저장 합니다.

    * 아래는 **동의 부여할** 섹션을 선택 합니다 **관리자 동의 부여**합니다. 이 단추는 회색 및 현재 계정이 테 넌 트 관리자 아닌 경우에 사용할 수 없는

        권한을 성공적으로 부여 받으면 포털에서 다음 알림이 표시됩니다.

        ![성공적인 권한 부여의 알림](media/aad-integration/permissions-granted.png)

1. Azure AD 응용 프로그램의 왼쪽 탐색에서 선택 **API를 노출**를 선택한 **범위를 추가 하는 +** 합니다.
    
    * 설정을 *범위 이름*를 *관리자 동의 표시 이름*, 및 *관리자 동의 설명*, 같은 *AKSAzureADServer*합니다.
    * 있는지 확인 합니다 **상태** 로 설정 된 *Enabled*합니다.

        ![서버 앱을 사용 하 여 다른 서비스에 대 한 API로 노출](media/aad-integration/expose-api.png)

    * 선택할 **범위 추가**합니다.

1. 응용 프로그램에 반환 **개요** 페이지 및 메모를 **응용 프로그램 (클라이언트) ID**합니다. Azure AD 사용 AKS 클러스터를 배포할 때이 값 이라고는 `Server application ID`합니다.

   ![애플리케이션 ID 가져오기](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>클라이언트 애플리케이션 만들기

두 번째 Azure AD 응용 프로그램을 Kubernetes CLI에 로그인 할 때 사용 됩니다 (`kubectl`).

1. 선택 **Azure Active Directory** > **앱 등록** > **새 등록**합니다.

    * 와 같은 응용 프로그램에 이름을 지정 *AKSAzureADClient*합니다.
    * 에 대 한 **지원 되는 계정 유형**, 선택 *계정에만이 조직 directory*합니다.
    * 선택할 *웹* 에 대 한 합니다 **리디렉션 URI** 형식과 같은 형식이 지정 된 URI 값을 입력할 *https://aksazureadclient*합니다.
    * 선택 **등록** 완료 합니다.

1. Azure AD 응용 프로그램의 왼쪽 탐색에서 선택 **API 사용 권한**를 선택한 **권한을 추가 +** 합니다.

    * 선택 **내 Api**를 선택한 다음 이전 단계에서 만든 Azure AD 서버 응용 프로그램 *AKSAzureADServer*합니다.
    * 선택할 **위임 된 권한**, 그런 다음 Azure AD 서버 앱 옆에 확인 표시를 배치 합니다.

        ![애플리케이션 사용 권한 구성](media/aad-integration/select-api.png)

    * 선택 **권한 추가**합니다.

    * 아래는 **동의 부여할** 섹션을 선택 합니다 **관리자 동의 부여**합니다. 이 단추는 회색 및 현재 계정이 테 넌 트 관리자 아닌 경우에 사용할 수 없는

        권한을 성공적으로 부여 받으면 포털에서 다음 알림이 표시됩니다.

        ![성공적인 권한 부여의 알림](media/aad-integration/permissions-granted.png)

1. Azure AD 응용 프로그램의 왼쪽 탐색에서 메모를 **응용 프로그램 ID**합니다. Azure AD 사용 AKS 클러스터를 배포할 때 이 값은 `Client application ID`이라고 합니다.

   ![애플리케이션 ID 가져오기](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>테넌트 ID 가져오기

마지막으로 Azure 테넌트의 ID를 가져옵니다. 이 값은 AKS 클러스터를 만들 때 사용 됩니다.

Azure Portal에서 **Azure Active Directory** > **속성**을 선택하고 **디렉터리 ID**를 기록해 둡니다. Azure AD 사용 AKS 클러스터를 만든 경우이 값 이라고는 `Tenant ID`합니다.

![Azure 테넌트 ID 가져오기](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>클러스터 배포

[az group create][az-group-create] 명령을 사용하여 AKS 클러스터에 대한 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

[az aks create][az-aks-create] 명령을 사용하여 클러스터를 배포합니다. 서버 앱 ID 및 암호, 클라이언트 앱 ID 및 테 넌 트 ID에 대 한 Azure AD 응용 프로그램을 만들 때 수집 된 값을 사용 하 여 샘플 명령은 아래에 있는 값을 바꿉니다.

```azurecli
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --generate-ssh-keys \
  --aad-server-app-id b1536b67-29ab-4b63-b60f-9444d0c15df1 \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 8aaf8bd5-1bdd-4822-99ad-02bfaa63eea7 \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

AKS 클러스터를 만드는 데 몇 분 정도 걸립니다.

## <a name="create-rbac-binding"></a>RBAC 바인딩 만들기

Azure Active Directory 계정을 AKS 클러스터와 함께 사용하려면 역할 바인딩 또는 클러스터 역할 바인딩을 만들어야 합니다. *역할*은 부여할 사용 권한을 정의하고, *바인딩*은 원하는 사용자에게 역할을 적용합니다. 이러한 할당은 주어진 네임스페이스 또는 전체 클러스터에 적용될 수 있습니다. 자세한 내용은 [RBAC 권한 부여 사용][rbac-authorization]을 참조하세요.

먼저 사용 하 여는 [az aks 자격 증명 가져오기] [ az-aks-get-credentials] 명령과 `--admin` 인수에 대 한 관리자 액세스를 사용 하 여 클러스터에 로그인 합니다.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

다음으로, AKS 클러스터에 대 한 액세스 권한을 부여 하려는 Azure AD 계정의 ClusterRoleBinding를 만듭니다. 다음 예제에서는 클러스터의 모든 네임 스페이스에는 계정 전체 액세스를 제공합니다.

- 에 대 한 RBAC 바인딩을 동일한 Azure AD 테 넌 트에 부여 하면 사용자, 사용자 계정 이름 (UPN)에 따라 사용 권한을 할당 합니다. 이동할 단계로 ClusterRuleBinding에 대 한 YAML 매니페스트를 만듭니다.

- 사용자가 테 넌 트의 다른 Azure AD에 대 한 쿼리 및 사용 합니다 *objectId* 속성 대신 합니다. 필요한 경우는 *objectId* 필요한 사용자의 계정을 사용 하 여 합니다 [az ad 사용자 표시] [ az-ad-user-show] 명령입니다. 필요한 계정의 사용자 계정 이름 (UPN)을 제공 합니다.

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

*rbac-aad-user.yaml*과 같은 파일을 만들고 다음 내용을 붙여 넣습니다. 마지막 줄으로 바꿉니다 *userPrincipalName_or_objectId* 경우에 따라 UPN 또는 개체 ID를 사용 하 여 사용자가 동일한 Azure AD 테 넌 트 여부.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

다음 예제와 같이 [kubectl apply][kubectl-apply] 명령을 사용하여 바인딩을 적용합니다.

```console
kubectl apply -f rbac-aad-user.yaml
```

Azure AD 그룹의 모든 구성원에 대해 역할 바인딩을 만들 수도 있습니다. 다음 예제에서와 같이 Azure AD 그룹은 그룹 개체 ID를 사용하여 지정됩니다. *rbac-aad-group.yaml*과 같은 파일을 만들고 다음 내용을 붙여 넣습니다. Azure AD 테넌트의 그룹 개체 ID로 업데이트합니다.

 ```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
   kind: Group
   name: "894656e1-39f8-4bfe-b16a-510f61af6f41"
```

다음 예제와 같이 [kubectl apply][kubectl-apply] 명령을 사용하여 바인딩을 적용합니다.

```console
kubectl apply -f rbac-aad-group.yaml
```

RBAC를 사용하여 Kubernetes 클러스터 보호에 대한 자세한 내용은 [RBAC 권한 부여 사용][rbac-authorization]을 참조하세요.

## <a name="access-cluster-with-azure-ad"></a>Azure AD를 사용하여 클러스터에 액세스

다음으로 [az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 비 관리자 사용자에 대한 컨텍스트를 끌어 옵니다.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

실행 한 후을 `kubectl` 명령, Azure로 인증 하 라는 메시지가 표시 됩니다. 수행 된 화면의 다음 예제에서와 같이 프로세스를 완료 하기 위한 지침:

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

완료 되 면 인증 토큰 캐시 됩니다. 만 경우 토큰이 만료 되었는지 또는 다시 생성 하는 Kubernetes 구성 파일에 로그인 reprompted는 있습니다.

성공적으로 로그인한 후에 권한 부여 오류 메시지가 표시되는 경우 다음 사항에 해당하는지 여부를 확인합니다.
1. 사용자 로그인 하는 그대로 (이 시나리오는 경우 다른 디렉터리에서 페더레이션된 계정을 사용 하는 경우) Azure AD 인스턴스에서 게스트 없습니다.
2. 사용자는 200개가 넘는 그룹의 멤버가 아닙니다.
3. 서버에 대 한 응용 프로그램 등록에서 정의 하는 암호-aad 서버-앱 암호를 사용 하 여 구성 값이 일치 하지 않습니다.

```console
error: You must be logged in to the server (Unauthorized)
```

## <a name="next-steps"></a>다음 단계

Azure AD 사용자 및 그룹을 사용 하 여 클러스터 리소스에 대 한 액세스를 제어 하려면를 참조 하세요 [역할 기반 access control 및 Azure AD id를 사용 하 여 AKS에서 클러스터 리소스에 대 한 액세스 제어][azure-ad-rbac]합니다.

Kubernetes 클러스터를 보호 하는 방법에 대 한 자세한 내용은 참조 하십시오 [AKS에 대 한 액세스 및 id 옵션)][rbac-authorization]합니다.

Id 및 리소스 제어에 대 한 모범 사례를 참조 하세요 [인증 및 권한 부여 AKS에 대 한 유용한][operator-best-practices-identity]합니다.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md

---
title: Azure Kubernetes Service와 Azure Active Directory 통합
description: Azure Active Directory 지원 Azure Kubernetes Service (AKS) 클러스터를 만드는 방법
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: mlearned
ms.openlocfilehash: 80137023643630e8472a70fcca6cb656aeba7123
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616380"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Azure Kubernetes Service와 Azure Active Directory 통합

Azure Kubernetes Service (AKS)는 사용자 인증을 위해 Azure Active Directory (Azure AD)를 사용 하도록 구성할 수 있습니다. 이 구성에서는 서명할 수 있습니다 AKS 클러스터에 Azure AD 인증 토큰을 사용 하 여.

클러스터 관리자는 사용자의 id 또는 directory 그룹 멤버 자격에 따라 Kubernetes 역할 기반 액세스 제어 (RBAC)를 구성할 수 있습니다.

이 문서에서는 다음 작업을 수행하는 방법을 설명합니다.

- AKS 및 Azure AD에 대 한 필수 구성 요소를 배포 합니다.
- Azure AD 사용이 가능한 클러스터를 배포 합니다.
- Azure portal을 사용 하 여 AKS 클러스터에서 기본 RBAC 역할을 만듭니다.

사용 하 여 이러한 단계를 완료할 수도 있습니다는 [Azure CLI][azure-ad-cli]합니다.

> [!NOTE]
> Azure AD는 새 RBAC 지원 클러스터를 만들 때에 사용할 수 있습니다. 기존 AKS 클러스터에서는 Azure AD를 사용하도록 설정할 수 없습니다.

## <a name="authentication-details"></a>인증 세부 정보

Azure AD 인증은 OpenID Connect는 AKS 클러스터에 제공 됩니다. OpenID Connect는 OAuth 2.0 프로토콜을 기반으로 하는 ID 계층입니다.

OpenID Connect에 대 한 자세한 내용은 참조 하세요. [OpenID Connect 및 Azure AD를 사용 하 여 웹 응용 프로그램에 대 한 액세스 권한을 부여][open-id-connect]합니다.

Kubernetes 클러스터 내에서 웹 후크 토큰 인증이 인증 토큰에 사용 됩니다. Webhook 토큰 인증은 AKS 클러스터의 일부로 구성 및 관리됩니다.

웹 후크 토큰 인증에 대 한 자세한 내용은 참조는 [웹 후크 토큰 인증][kubernetes-webhook] Kubernetes 설명서의 섹션입니다.

AKS 클러스터에 대 한 Azure AD 인증을 제공 하려면 두 개의 Azure AD 응용 프로그램 생성 됩니다. 첫 번째 응용 프로그램은 사용자 인증을 제공 하는 서버 구성 요소입니다. 두 번째 응용 프로그램은 인증에 대 한 CLI에서 메시지가 사용 되는 클라이언트 구성 요소입니다. 이 클라이언트 응용 프로그램 클라이언트에서 제공한 자격 증명의 실제 인증을 위한 서버 응용 프로그램을 사용 합니다.

> [!NOTE]
> AKS 인증을 위해 Azure AD를 구성할 때 두 개의 Azure AD 응용 프로그램 구성 됩니다. 각 응용 프로그램에 대 한 권한을 위임 하는 단계는 Azure 테 넌 트 관리자가 완료 되어야 합니다.

## <a name="create-the-server-application"></a>서버 응용 프로그램 만들기

첫 번째 Azure AD 응용 프로그램은 사용자의 Azure AD 그룹 멤버 자격을 적용 합니다. Azure portal에서이 응용 프로그램을 만들려면:

1. 선택 **Azure Active Directory** > **앱 등록** > **새 등록**합니다.

    a. 와 같은 응용 프로그램에 이름을 지정 *AKSAzureADServer*합니다.

    b. 에 대 한 **지원 되는 계정 유형**를 선택 **만이 조직 디렉터리의 계정**합니다.
    
    c. 선택할 **웹** 리디렉션 uri를 입력 한 후 모든 URI 형식의 값을 같은 입력 *https://aksazureadserver* 합니다.

    d. 선택 **등록** 과정을 완료 합니다.

2. 선택 **매니페스트**를 선택한 다음 편집 합니다 **groupMembershipClaims:** 값 **모든**합니다. 업데이트를 사용 하 여 완료 되 면, 선택 **저장할**합니다.

    ![그룹 멤버 자격을 모두로 업데이트](media/aad-integration/edit-manifest.png)

3. Azure AD 응용 프로그램의 왼쪽된 창에서 선택 **인증서 및 비밀**합니다.

    a. 선택 **+ 새 클라이언트 암호**합니다.

    b. 같은 키 설명을 추가한 *AKS Azure AD 서버*합니다. 만료 시간을 선택 하 고 선택한 **추가**합니다.

    c. 이 이번에만 표시 되는 키 값을 note 합니다. Azure AD 사용 AKS 클러스터를 배포할 때이 값은 서버 응용 프로그램 암호를 라고 합니다.

4. Azure AD 응용 프로그램의 왼쪽된 창에서 선택 **API 사용 권한**를 선택한 후 **권한을 추가 +** 합니다.

    a. 아래 **Microsoft Api**를 선택 **Microsoft Graph**합니다.

    b. 선택 **위임 된 권한**를 선택한 다음 옆에 확인란 **디렉터리 > (디렉터리 데이터 읽기), Directory.Read.All**합니다.

    c. 기본값에 대 한 권한을 위임 하는 경우 **사용자 > User.Read (로그인 및 사용자 프로필 읽기)** 존재 하지 않습니다, 옆에 있는 확인란을 선택 합니다.

    d. 선택 **응용 프로그램 사용 권한**를 선택한 다음 옆에 확인란 **디렉터리 > (디렉터리 데이터 읽기), Directory.Read.All**합니다.

    ![그래프 사용 권한 설정](media/aad-integration/graph-permissions.png)

    e. 선택 **권한 추가** 에 업데이트를 저장 합니다.

    f. 아래 **동의 부여할**를 선택 **관리자 동의 부여**합니다. 이 단추 계정이 든 현재 계정 테 넌 트 관리자 없는 경우에 사용할 수 없습니다.

    성공적으로 권한을 부여 하는 경우 포털에서 다음 알림이 표시 됩니다.

   ![성공적인 권한 부여의 알림](media/aad-integration/permissions-granted.png)

5. Azure AD 응용 프로그램의 왼쪽된 창에서 선택 **API를 노출**를 선택한 후 **범위를 추가 하는 +** 합니다.
    
    a. 입력을 **범위 이름**, **관리자 동의 표시 이름**를 차례로 **관리자 동의 설명** 와 같은 *AKSAzureADServer*합니다.

    b. 했는지 **상태** 로 설정 된 **Enabled**합니다.

    ![서버 앱을 사용 하 여 다른 서비스에 대 한 API로 노출](media/aad-integration/expose-api.png)

    c. 선택 **범위 추가**합니다.

6. 응용 프로그램에 반환 **개요** 페이지 및 참고 합니다 **응용 프로그램 (클라이언트) ID**합니다. Azure AD 사용 AKS 클러스터를 배포한 경우이 값 서버 응용 프로그램 id입니다. 라고

    ![애플리케이션 ID 가져오기](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>클라이언트 응용 프로그램 만들기

두 번째 Azure AD 응용 프로그램은 Kubernetes CLI (kubectl)에 로그인 할 때 사용 됩니다.

1. 선택 **Azure Active Directory** > **앱 등록** > **새 등록**합니다.

    a. 와 같은 응용 프로그램에 이름을 지정 *AKSAzureADClient*합니다.

    b. 에 대 한 **지원 되는 계정 유형**를 선택 **만이 조직 디렉터리의 계정**합니다.

    c. 선택 **웹** 리디렉션 uri를 입력 한 후 URI 형식의 값을 같은 입력 *https://aksazureadclient* 합니다.

    d. 선택 **등록** 과정을 완료 합니다.

2. Azure AD 응용 프로그램의 왼쪽된 창에서 선택 **API 사용 권한**를 선택한 후 **권한을 추가 +** 합니다.

    a. 선택 **내 Api**를 선택한 후 이전 단계에서 만든 Azure AD 서버 응용 프로그램 *AKSAzureADServer*합니다.

    b. 선택 **위임 된 권한**, 한 다음 Azure AD 서버 앱 옆의 확인란을 선택 합니다.

    ![애플리케이션 사용 권한 구성](media/aad-integration/select-api.png)

    c. 선택 **권한 추가**합니다.

    d. 아래 **동의 부여할**를 선택 **관리자 동의 부여**합니다. 이 단추 계정이 든 현재 계정 테 넌 트 관리자 없는 경우에 사용할 수 없습니다. 권한을 부여 하는 경우 포털에서 다음 알림이 표시 됩니다.

    ![성공적인 권한 부여의 알림](media/aad-integration/permissions-granted.png)

3. Azure AD 응용 프로그램의 왼쪽된 창에서 선택 **인증**합니다.

    - 아래 **기본 클라이언트 형식**를 선택 **예** 하 **공용 클라이언트로 클라이언트를 처리**합니다.

5. Azure AD 응용 프로그램의 왼쪽된 창에서 응용 프로그램 ID를 note 이 값을 클라이언트 응용 프로그램 ID 라고 Azure AD 사용 AKS 클러스터를 배포 하면

   ![애플리케이션 ID 가져오기](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>테 넌 트 ID를 가져옵니다.

다음으로, Azure 테 넌 트의 ID를 가져옵니다. 이 값은 AKS 클러스터를 만들 때 사용 됩니다.

Azure portal에서 선택 **Azure Active Directory** > **속성** 확인 합니다 **디렉터리 ID**합니다. 이 값을 테 넌 트 ID 라고 Azure AD 사용 AKS 클러스터를 만든 경우

![Azure 테넌트 ID 가져오기](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>AKS 클러스터 배포

사용 된 [az 그룹 만들기][az-group-create] AKS 클러스터에 대 한 리소스 그룹을 만드는 명령입니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

사용 된 [az aks 만들기][az-aks-create] AKS 클러스터를 배포 하기 위해 명령을 합니다. 다음으로, 다음 샘플 명령은에 있는 값을 대체 합니다. 서버 앱 ID, 앱 비밀, 클라이언트 앱 ID 및 테 넌 트 ID에 대 한 Azure AD 응용 프로그램을 만들 때 수집한 값 사용

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

AKS 클러스터를 만들려면 몇 분이 걸립니다.

## <a name="create-an-rbac-binding"></a>RBAC 바인딩 만들기

를 AKS 클러스터와 Azure Active Directory 계정을 사용 하기 전에 역할 바인딩 또는 바인딩 역할 클러스터를 만들어야 합니다. 역할에 권한을 부여 하려면 권한을 정의 하 고 바인딩을 원하는 사용자에 게 적용할 메시지를 표시 합니다. 이러한 할당은 주어진 네임스페이스 또는 전체 클러스터에 적용될 수 있습니다. 자세한 내용은 [RBAC를 사용 하 여 권한 부여][rbac-authorization]합니다.

먼저 사용 하 여는 [az aks 자격 증명 가져오기][az-aks-get-credentials] 명령과 `--admin` 인수에 대 한 관리자 액세스를 사용 하 여 클러스터에 로그인 합니다.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

다음으로 ClusterRoleBinding AKS 클러스터에 대 한 액세스 권한을 부여 하려는 Azure AD 계정을 만듭니다. 다음 예제는 클러스터의 모든 네임 스페이스에 계정 전체 액세스를 제공합니다.

- 에 대 한 RBAC 바인딩을 동일한 Azure AD 테 넌 트에 부여 하면 사용자, 사용자 계정 이름 (UPN)에 따라 사용 권한을 할당 합니다. ClusterRoleBinding에 대 한 YAML 매니페스트를 만드는 단계로 넘어갑니다.

- 사용자가 테 넌 트의 다른 Azure AD에 대 한 쿼리 및 사용 합니다 **objectId** 속성 대신 합니다. 필요한 경우 사용 하 여 필요한 사용자 계정의 objectId 가져오기 합니다 [az ad 사용자 표시][az-ad-user-show] 명령입니다. 필요한 계정의 사용자 계정 이름 (UPN)을 제공 합니다.

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

와 같은 파일을 만듭니다 *rbac-aad-user.yaml*, 다음 내용을 붙여넣습니다. 마지막 줄으로 바꿉니다 **userPrincipalName_or_objectId** UPN 또는 개체 id 선택한은 사용자가 동일한 Azure AD 테 넌 트 인지 여부에 따라 달라 집니다.

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

바인딩을 사용 하 여 적용 된 [kubectl 적용][kubectl-apply] 다음 예와에서 같이 명령:

```console
kubectl apply -f rbac-aad-user.yaml
```

Azure AD 그룹의 모든 구성원에 대해 역할 바인딩을 만들 수도 있습니다. 다음 예제에서와 같이 azure AD 그룹은 그룹 개체 ID를 사용 하 여 지정 됩니다.

와 같은 파일을 만듭니다 *rbac-aad-group.yaml*, 다음 내용을 붙여넣습니다. Azure AD 테넌트의 그룹 개체 ID로 업데이트합니다.

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

바인딩을 사용 하 여 적용 된 [kubectl 적용][kubectl-apply] 다음 예와에서 같이 명령:

```console
kubectl apply -f rbac-aad-group.yaml
```

RBAC 사용 하 여 Kubernetes 클러스터를 보호 하는 방법은 참조 하세요 [RBAC 권한 부여를 사용 하 여][rbac-authorization]입니다.

## <a name="access-the-cluster-with-azure-ad"></a>Azure AD 사용 하 여 클러스터에 액세스

관리자가 아닌 사용자에 대 한 컨텍스트를 사용 하 여 끌어오기 합니다 [az aks 자격 증명 가져오기][az-aks-get-credentials] 명령입니다.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

실행 한 후의 `kubectl` 명령, Azure를 사용 하 여 인증 하 라는 메시지가 나타납니다. 수행 된 화면의 다음 예제에서와 같이 프로세스를 완료 하는 지침은:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

프로세스가 완료 되 면 인증 토큰 캐시 됩니다. 만 묻는 토큰이 만료 되거나 Kubernetes 구성 파일을이 다시 만들어질 때 다시 로그인 합니다.

성공적으로 로그인 한 후에 권한 부여 오류 메시지를 표시 하는 경우에 다음 조건을 확인 합니다.

```console
error: You must be logged in to the server (Unauthorized)
```


- 적절 한 개체 ID 또는 UPN이 사용자 계정은 동일한 Azure AD 테 넌 트에이 경우에 따라 정의 했습니다.
- 사용자는 200 개가 넘는 그룹의 구성원이 아닙니다.
- 사용 하 여 구성 된 값과 일치 하는 서버에 대 한 응용 프로그램 등록에 정의 된 비밀 `--aad-server-app-secret`합니다.

## <a name="next-steps"></a>다음 단계

Azure AD 사용자 및 그룹을 사용 하 여 클러스터 리소스에 대 한 액세스를 제어 하려면를 참조 하세요 [역할 기반 access control 및 Azure AD id를 사용 하 여 AKS에서 클러스터 리소스에 대 한 액세스를 제어][azure-ad-rbac]입니다.

Kubernetes 클러스터를 보호 하는 방법에 대 한 자세한 내용은 참조 하세요. [AKS에 대 한 액세스 및 id 옵션][rbac-authorization]합니다.

Id 및 리소스 제어에 대 한 자세한 내용은 참조 하세요 [인증 및 권한 부여 AKS에 대 한 유용한][operator-best-practices-identity]합니다.

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

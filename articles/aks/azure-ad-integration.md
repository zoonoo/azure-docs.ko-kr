---
title: Azure Kubernetes Service와 Azure Active Directory 통합
description: Azure Active Directory 지원 AZURE Kubernetes 서비스(AKS) 클러스터를 만드는 방법
services: container-service
ms.topic: article
ms.date: 02/02/2019
ms.openlocfilehash: de57a46f92fab2486aa7722daf8745a01be1f4f6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617591"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Azure Kubernetes Service와 Azure Active Directory 통합

AZURE Kubernetes 서비스(AKS)는 사용자 인증을 위해 Azure Active Directory(Azure AD)를 사용하도록 구성할 수 있습니다. 이 구성에서는 Azure AD 인증 토큰을 사용하여 AKS 클러스터에 로그인할 수 있습니다.

클러스터 관리자는 사용자의 ID 또는 디렉터리 그룹 구성원 자격에 따라 Kubernetes 역할 기반 액세스 제어(RBAC)를 구성할 수 있습니다.

이 문서에서는 다음 방법을 설명합니다.

- AKS 및 Azure AD에 대한 필수 구성 조건을 배포합니다.
- Azure AD 지원 클러스터를 배포합니다.
- Azure 포털을 사용하여 AKS 클러스터에서 기본 RBAC 역할을 만듭니다.

[Azure CLI][azure-ad-cli]를 사용하여 이러한 단계를 완료할 수도 있습니다.

> [!NOTE]
> Azure AD는 새 RBAC 지원 클러스터를 만들 때만 사용할 수 있습니다. 기존 AKS 클러스터에서는 Azure AD를 사용하도록 설정할 수 없습니다.

## <a name="authentication-details"></a>인증 세부 정보

Azure AD 인증은 OpenID Connect가 있는 AKS 클러스터에 제공됩니다. OpenID Connect는 OAuth 2.0 프로토콜을 기반으로 하는 ID 계층입니다.

OpenID Connect에 대한 자세한 내용은 [OpenID Connect 및 Azure AD를 사용하여 웹 응용 프로그램에 대한 액세스 권한 부여를][open-id-connect]참조하세요.

Kubernetes 클러스터 내에서 웹후크 토큰 인증은 인증 토큰에 사용됩니다. Webhook 토큰 인증은 AKS 클러스터의 일부로 구성 및 관리됩니다.

웹후크 토큰 인증에 대한 자세한 내용은 Kubernetes 설명서의 [Webhook 토큰 인증][kubernetes-webhook] 섹션을 참조하십시오.

AKS 클러스터에 대한 Azure AD 인증을 제공하기 위해 두 개의 Azure AD 응용 프로그램이 만들어집니다. 첫 번째 응용 프로그램은 사용자 인증을 제공하는 서버 구성 요소입니다. 두 번째 응용 프로그램은 인증을 위해 CLI에 의해 메시지가 표시될 때 사용되는 클라이언트 구성 요소입니다. 이 클라이언트 응용 프로그램은 클라이언트에서 제공하는 자격 증명의 실제 인증을 위해 서버 응용 프로그램을 사용합니다.

> [!NOTE]
> AKS 인증을 위해 Azure AD를 구성하면 두 개의 Azure AD 응용 프로그램이 구성됩니다. 각 응용 프로그램에 대한 권한을 위임하는 단계는 Azure 테넌트 관리자가 완료해야 합니다.

## <a name="create-the-server-application"></a>서버 응용 프로그램 만들기

첫 번째 Azure AD 응용 프로그램이 사용자의 Azure AD 그룹 구성원 자격을 얻기 위해 적용됩니다. Azure 포털에서 이 응용 프로그램을 만들려면 다음을 수행합니다.

1. **Azure Active Directory** > **앱 등록** > **새 등록을**선택합니다.

    a. 응용 프로그램에 *AKSAzureADServer*와 같은 이름을 지정합니다.

    b. **지원되는 계정 유형의**경우 **이 조직 디렉터리에서만 계정을**선택합니다.
    
    다. 리디렉션 URI 유형에 대해 **웹을** 선택한 다음 와 같은 *https://aksazureadserver*URI 형식의 값을 입력합니다.

    d. 완료되면 **등록을** 선택합니다.

2. **[매니페스트]** 를 선택한 다음 **그룹멤버클레임:** 값을 **모두로**편집합니다. 업데이트가 완료되면 **저장을**선택합니다.

    ![그룹 멤버 자격을 모두로 업데이트](media/aad-integration/edit-manifest.png)

3. Azure AD 응용 프로그램의 왼쪽 창에서 **인증서 & 비밀을 선택합니다.**

    a. 선택 **+ 새 클라이언트 보안**.

    b. *AKS Azure AD 서버와*같은 키 설명을 추가합니다. 만료 시간을 선택한 다음 추가 **를**선택합니다.

    다. 현재 만 표시되는 키 값을 기록합니다. Azure AD 지원 AKS 클러스터를 배포할 때 이 값을 서버 응용 프로그램 보안이라고 합니다.

4. Azure AD 응용 프로그램의 왼쪽 창에서 API 권한을 선택한 다음 **+ 사용 권한 추가를** **선택합니다.**

    a. **마이크로소프트 API에서** **마이크로소프트 그래프를**선택합니다.

    b. **위임된 권한을**선택한 다음 **디렉터리 > 디렉터리.Read.All(디렉터리 데이터 읽기)** 옆의 확인란을 선택합니다.

    다. **사용자 > User.Read(로그인 및 사용자 프로필 읽기)에** 대한 기본 위임된 권한이 없는 경우 옆에 있는 확인란을 선택합니다.

    d. **응용 프로그램 권한을**선택한 다음 **디렉터리 > Directory.Read.All(디렉터리 데이터 읽기)** 옆의 확인란을 선택합니다.

    ![그래프 권한 설정](media/aad-integration/graph-permissions.png)

    e. 업데이트를 저장할 **권한 추가를 선택합니다.**

    f. **교부금 동의하에** **권한 부여 관리자 동의를 선택합니다.** 이 단추는 사용 중인 현재 계정이 테넌트 관리자로 나열되지 않은 상태로 사용할 수 없습니다.

    사용 권한이 성공적으로 부여되면 포털에 다음 알림이 표시됩니다.

   ![성공적인 권한 부여의 알림](media/aad-integration/permissions-granted.png)

5. Azure AD 응용 프로그램의 왼쪽 창에서 **API 노출을**선택한 다음 **+ 범위 추가를**선택합니다.
    
    a. 범위 **이름,** **관리자 동의 표시 이름**및 *AKSAzureADServer*와 같은 **관리자 동의 설명을** 입력합니다.

    b. **상태가** **사용 설정으로**설정되어 있는지 확인합니다.

    ![서버 앱을 다른 서비스와 함께 사용할 API로 노출](media/aad-integration/expose-api.png)

    다. **범위 추가를**선택합니다.

6. 응용 프로그램 **개요** 페이지로 돌아가서 **응용 프로그램(클라이언트) ID를**기록합니다. Azure AD 지원 AKS 클러스터를 배포할 때 이 값을 서버 응용 프로그램 ID라고 합니다.

    ![애플리케이션 ID 가져오기](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>클라이언트 애플리케이션 만들기

두 번째 Azure AD 응용 프로그램은 Kubernetes CLI(kubectl)로 로그인할 때 사용됩니다.

1. **Azure Active Directory** > **앱 등록** > **새 등록을**선택합니다.

    a. 응용 프로그램에 *AKSAzureADClient와*같은 이름을 지정합니다.

    b. **지원되는 계정 유형의**경우 **이 조직 디렉터리에서만 계정을**선택합니다.

    다. 리디렉션 URI 유형에 대한 **웹을** 선택한 다음 와 같은 *https://aksazureadclient*URI 형식의 값을 입력합니다.

    >[!NOTE]
    >컨테이너에 대한 Azure Monitor를 지원하는 새 RBAC 지원 클러스터를 만드는 경우 다음 두 개의 리디렉션 URL을 이 목록에 **웹** 응용 프로그램 유형으로 추가합니다. 첫 번째 기본 URL `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` 값이어야 하며 두 번째 기본 URL 값은`https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`
    >
    >Azure China에서 이 기능을 사용하는 경우 첫 번째 기본 `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` URL 값이어야 하며 두 번째 기본 URL 값은`https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`
    >
    >자세한 내용은 컨테이너에 대한 Azure [Monitor의 라이브 데이터(미리 보기) 기능을 설정하는 방법과](../azure-monitor/insights/container-insights-livedata-setup.md) [AD 통합 인증 구성](../azure-monitor/insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication) 섹션에서 인증을 구성하는 단계를 참조하세요.

    d. 완료되면 **등록을** 선택합니다.

2. Azure AD 응용 프로그램의 왼쪽 창에서 API 권한을 선택한 다음 **+ 사용 권한 추가를** **선택합니다.**

    a. **내 API를**선택한 다음 *AKSAzureADServer*와 같은 이전 단계에서 만든 Azure AD 서버 응용 프로그램을 선택합니다.

    b. **위임된 권한을**선택한 다음 Azure AD 서버 앱 옆에 있는 확인란을 선택합니다.

    ![애플리케이션 사용 권한 구성](media/aad-integration/select-api.png)

    다. **권한 추가**를 선택합니다.

    d. **교부금 동의하에** **권한 부여 관리자 동의를 선택합니다.** 현재 계정이 테넌트 관리자가 아닌 경우 이 단추를 사용할 수 없습니다. 사용 권한이 부여되면 포털에 다음 알림이 표시됩니다.

    ![성공적인 권한 부여의 알림](media/aad-integration/permissions-granted.png)

3. Azure AD 응용 프로그램의 왼쪽 창에서 **인증을**선택합니다.

    - **기본 클라이언트 유형에서** **예를** 선택하여 **클라이언트를 공용 클라이언트로 처리합니다.**

5. Azure AD 응용 프로그램의 왼쪽 창에서 응용 프로그램 ID를 기록합니다. Azure AD 지원 AKS 클러스터를 배포할 때 이 값을 클라이언트 응용 프로그램 ID라고 합니다.

   ![애플리케이션 ID 가져오기](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>테넌트 ID 받기

다음으로 Azure 테넌트의 ID를 가져옵니다. 이 값은 AKS 클러스터를 만들 때 사용됩니다.

Azure 포털에서 **Azure Active 디렉터리** > **속성을** 선택하고 **디렉터리 ID를**기록합니다. Azure AD 지원 AKS 클러스터를 만들 때 이 값을 테넌트 ID라고 합니다.

![Azure 테넌트 ID 가져오기](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>AKS 클러스터 배포

[az group create][az-group-create] 명령을 사용하여 AKS 클러스터에 대한 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

AZ [aks create][az-aks-create] 명령을 사용하여 AKS 클러스터를 배포합니다. 다음으로 다음 샘플 명령의 값을 바꿉습니다. 서버 앱 ID, 앱 보안 검색, 클라이언트 앱 ID 및 테넌트 ID에 대해 Azure AD 응용 프로그램을 만들 때 수집된 값을 사용합니다.

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

## <a name="create-an-rbac-binding"></a>RBAC 바인딩 만들기

> [!NOTE]
> 클러스터 역할 바인딩 이름은 대/소문자를 지정합니다.

AKS 클러스터에서 Azure Active Directory 계정을 사용하기 전에 역할 바인딩 또는 클러스터 역할 바인딩을 만들어야 합니다. 역할은 부여할 사용 권한을 정의하고, 바인딩은 원하는 사용자에게 역할을 적용합니다. 이러한 할당은 주어진 네임스페이스 또는 전체 클러스터에 적용될 수 있습니다. 자세한 내용은 [RBAC 권한 부여 사용][rbac-authorization]을 참조하세요.

먼저 `--admin` 인수와 함께 [az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 관리자 액세스 권한이 있는 클러스터에 로그인합니다.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

그런 다음 AKS 클러스터에 대한 액세스 권한을 부여하려는 Azure AD 계정에 대해 ClusterRoleBinding을 만듭니다. 다음 예제에서는 계정에 클러스터의 모든 네임스페이스에 대한 전체 액세스 권한을 부여합니다.

- RBAC 바인딩을 부여하는 사용자가 동일한 Azure AD 테넌트에 있는 경우 사용자 주체 이름(UPN)을 기반으로 권한을 할당합니다. 단계로 이동하여 ClusterRoleBinding에 대한 YAML 매니페스트를 만듭니다.

- 사용자가 다른 Azure AD 테넌트에 있는 경우 **대신 objectId** 속성을 쿼리하고 사용합니다. 필요한 경우 [az 광고 사용자 표시][az-ad-user-show] 명령을 사용하여 필요한 사용자 계정의 objectId를 가져옵니다. 필수 계정의 사용자 주체 이름(UPN)을 제공합니다.

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

*rbac-aad-user.yaml과*같은 파일을 만든 다음 다음 내용을 붙여넣습니다. 마지막 줄에서 **userPrincipalName_or_objectId** UPN 또는 개체 ID로 바꿉습니다. 선택은 사용자가 동일한 Azure AD 테넌트인지 여부에 따라 달라집니다.

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

Azure AD 그룹의 모든 구성원에 대해 역할 바인딩을 만들 수도 있습니다. Azure AD 그룹은 다음 예제와 같이 그룹 개체 ID를 사용하여 지정됩니다.

*rbac-aad-group.yaml과*같은 파일을 만든 다음 다음 내용을 붙여넣습니다. Azure AD 테넌트의 그룹 개체 ID로 업데이트합니다.

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

## <a name="access-the-cluster-with-azure-ad"></a>Azure AD를 통해 클러스터에 액세스

[az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 비관리자 사용자의 컨텍스트를 가져옵니다.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

`kubectl` 명령을 실행한 후 Azure를 사용하여 인증하라는 메시지가 표시됩니다. 다음 예제와 같이 화면의 지침에 따라 프로세스를 완료합니다.

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

프로세스가 완료되면 인증 토큰이 캐시됩니다. 토큰이 만료되거나 Kubernetes 구성 파일이 다시 생성될 때만 다시 로그인하라는 메시지가 표시됩니다.

성공적으로 로그인한 후 권한 부여 오류 메시지가 표시되면 다음 조건을 확인하십시오.

```console
error: You must be logged in to the server (Unauthorized)
```


- 사용자 계정이 동일한 Azure AD 테넌트에 있는지 여부에 따라 적절한 개체 ID 또는 UPN을 정의했습니다.
- 사용자는 200개 이상의 그룹의 구성원이 아닙니다.
- 서버에 대한 응용 프로그램 등록에 정의된 비밀은 `--aad-server-app-secret`을 사용하여 구성된 값과 일치합니다.

## <a name="next-steps"></a>다음 단계

Azure AD 사용자 및 그룹을 사용하여 클러스터 리소스에 대한 액세스를 제어하려면 [AKS의 역할 기반 액세스 제어 및 Azure AD ID를 사용하여 클러스터 리소스에][azure-ad-rbac]대한 액세스 제어를 참조하세요.

Kubernetes 클러스터를 보호하는 방법에 대한 자세한 내용은 [AKS에 대한 액세스 및 ID 옵션을][rbac-authorization]참조하십시오.

ID 및 리소스 제어에 대한 자세한 내용은 [AKS의 인증 및 권한 부여에 대한 모범 사례를][operator-best-practices-identity]참조하십시오.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md

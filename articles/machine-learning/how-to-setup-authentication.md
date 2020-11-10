---
title: 인증 설정
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 다양한 리소스 및 워크플로에 대한 인증을 설정하고 구성하는 방법을 알아봅니다.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 11/05/2020
ms.topic: conceptual
ms.custom: how-to, has-adal-ref, devx-track-js, devx-track-azurecli, contperfq2
ms.openlocfilehash: adc0547e36e9cf996a87c2683b4830541b8cd360
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442109"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Azure Machine Learning 리소스 및 워크플로에 대한 인증 설정


Azure Machine Learning 작업 영역에 대 한 인증을 설정 하는 방법을 알아봅니다. Azure Machine Learning 작업 영역에 대 한 인증은 대부분의 작업에 대해 __Azure Active Directory__ (Azure AD)를 기반으로 합니다. 일반적으로 작업 영역에 연결할 때 사용할 수 있는 인증 워크플로는 세 가지가 있습니다.

* __대화형__ : Azure Active Directory에서 계정을 사용 하 여 직접 인증 하거나 인증에 사용 되는 토큰을 가져옵니다. 대화형 인증은 _실험 및 반복적인 개발_ 중에 사용 됩니다. 대화형 인증을 사용 하면 사용자 별로 리소스 (예: 웹 서비스)에 대 한 액세스를 제어할 수 있습니다.

* __서비스 사용자__ : Azure Active Directory에서 서비스 사용자 계정을 만들고이 계정을 사용 하 여 인증 하거나 토큰을 가져옵니다. 서비스 주체는 사용자 상호 작용 없이 서비스에 _인증 하는 자동화 된 프로세스가_ 필요한 경우에 사용 됩니다. 예를 들어 학습 코드가 변경 될 때마다 모델을 학습 하 고 테스트 하는 연속 통합 및 배포 스크립트입니다.

* __관리 id__ : _azure 가상 머신에서_ Azure Machine Learning SDK를 사용 하는 경우 azure에 대 한 관리 id를 사용할 수 있습니다. 이 워크플로를 사용 하면 VM이 관리 id를 사용 하 여 Python 코드에 자격 증명을 저장 하거나 사용자에 게 인증 하 라는 메시지를 표시 하지 않고 작업 영역에 연결할 수 있습니다. _모델 학습_ 시 관리 되는 id를 사용 하 여 작업 영역에 액세스 하도록 계산 클러스터 Azure Machine Learning 구성할 수도 있습니다.

> [!IMPORTANT]
> 사용 되는 인증 워크플로와 상관 없이, azure RBAC (역할 기반 액세스 제어)를 사용 하 여 리소스에 허용 되는 액세스 수준 (권한 부여)의 범위를 지정할 수 있습니다. 예를 들어 관리 또는 자동화 프로세스는 계산 인스턴스를 만들 수 있지만 사용 하지는 않지만, 데이터 과학자는 사용할 수 있지만 삭제 하거나 만들 수는 없습니다. 자세한 내용은 [Azure Machine Learning 작업 영역에 대 한 액세스 관리](how-to-assign-roles.md)를 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)을 만듭니다.
* Azure Machine Learning SDK를 설치 하거나 SDK가 이미 설치 된 [Azure Machine Learning 계산 인스턴스](concept-azure-machine-learning-architecture.md#compute-instance) 를 사용 하도록 [개발 환경을 구성](how-to-configure-environment.md) 합니다.

## <a name="azure-active-directory"></a>Azure Active Directory

작업 영역에 대 한 모든 인증 워크플로는 Azure Active Directory을 사용 합니다. 사용자가 개별 계정을 사용 하 여 인증 하도록 하려면 Azure AD에 계정이 있어야 합니다. 서비스 주체를 사용 하려는 경우 Azure AD에 존재 해야 합니다. 관리 id는 Azure AD의 기능 이기도 합니다. 

Azure AD에 대 한 자세한 내용은 [Azure Active Directory 인증 이란?](..//active-directory/authentication/overview-authentication.md)을 참조 하세요.

Azure AD 계정을 만들었으면 Azure Machine Learning의 작업 영역 및 기타 작업에 대 한 액세스 권한을 부여 하는 방법에 대 한 자세한 내용은 [Azure Machine Learning 작업 영역에 대 한 액세스 관리](how-to-assign-roles.md) 를 참조 하세요.

## <a name="configure-a-service-principal"></a>서비스 주체

SP (서비스 주체)를 사용 하려면 먼저 SP를 만들고 작업 영역에 대 한 액세스 권한을 부여 해야 합니다. 앞서 설명한 것 처럼 Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 액세스를 제어 하므로 SP를 부여할 액세스 권한도 결정 해야 합니다.

> [!IMPORTANT]
> 서비스 주체를 사용 하는 경우 사용 되는 __작업에 필요한 최소한의 액세스__ 권한을 부여 합니다. 예를 들어,에 사용 되는 모든 사용자가 웹 배포에 대 한 액세스 토큰을 읽는 경우 서비스 사용자 소유자 또는 참가자 액세스 권한을 부여 하지 않습니다.
>
> 최소한의 액세스 권한을 부여 하는 이유는 서비스 사용자가 암호를 사용 하 여 인증 하 고 암호가 자동화 스크립트의 일부로 저장 될 수 있기 때문입니다. 암호가 유출 되는 경우 특정 작업에 필요한 최소한의 액세스 권한이 있으면 SP의 악성 사용이 최소화 됩니다.

SP를 만들고 작업 영역에 대 한 액세스 권한을 부여 하는 가장 쉬운 방법은 [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)를 사용 하는 것입니다. 서비스 사용자를 만들고 작업 영역에 대 한 액세스 권한을 부여 하려면 다음 단계를 사용 합니다.

> [!NOTE]
> 이러한 모든 단계를 수행 하려면 구독에 대 한 관리자 여야 합니다.

1. Azure 구독을 인증합니다.

    ```azurecli-interactive
    az login
    ```

    CLI가 기본 브라우저를 열 수 있는 경우, 그렇게 하고 로그인 페이지를 로드합니다. CLI가 브라우저를 열 수 없는 경우에는 사용자가 브라우저를 열고 명령줄의 지침을 따릅니다. 지침에 따라 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)으로 이동하고 인증 코드를 입력합니다.

    여러 Azure 구독이 있는 경우 명령을 사용 하 여 구독을 `az account set -s <subscription name or ID>` 설정할 수 있습니다. 자세한 내용은 [여러 Azure 구독 사용](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)을 참조하세요.

    다른 인증 방법은 [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli?preserve-view=true&view=azure-cli-latest)을 참조하세요.

1. Azure Machine Learning 확장을 설치 합니다.

    ```azurecli-interactive
    az extension add -n azure-cli-ml
    ```

1. 서비스 주체를 만듭니다. 다음 예제에서는 ml 이라는 SP **인증** 을 만듭니다.

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth --name ml-auth
    ```

    출력은 다음과 유사한 JSON입니다. 이 문서의 다른 단계에 필요하므로 `clientId`, `clientSecret` 및 `tenantId` 필드를 기록해 둡니다.

    ```json
    {
        "clientId": "your-client-id",
        "clientSecret": "your-client-secret",
        "subscriptionId": "your-sub-id",
        "tenantId": "your-tenant-id",
        "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
        "resourceManagerEndpointUrl": "https://management.azure.com",
        "activeDirectoryGraphResourceId": "https://graph.windows.net",
        "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
        "galleryEndpointUrl": "https://gallery.azure.com/",
        "managementEndpointUrl": "https://management.core.windows.net"
    }
    ```

1. 이전 단계에서 반환 된 값을 사용 하 여 서비스 주체에 대 한 세부 정보를 검색 합니다 `clientId` .

    ```azurecli-interactive
    az ad sp show --id your-client-id
    ```

    다음 JSON은 명령의 출력에 대 한 간단한 예입니다. 다음 단계에서 해당 값이 필요하므로 `objectId` 필드를 기록해 둡니다.

    ```json
    {
        "accountEnabled": "True",
        "addIns": [],
        "appDisplayName": "ml-auth",
        ...
        ...
        ...
        "objectId": "your-sp-object-id",
        "objectType": "ServicePrincipal"
    }
    ```

1. SP가 Azure Machine Learning 작업 영역에 액세스할 수 있도록 허용 합니다. 작업 영역 이름과 `-w` 및 `-g` 매개 변수에 대한 리소스 그룹 이름이 각각 필요합니다. `--user` 매개 변수의 경우 이전 단계의 `objectId` 값을 사용합니다. `--role`매개 변수를 사용 하 여 서비스 사용자에 대 한 액세스 역할을 설정할 수 있습니다. 다음 예제에서는 SP가 **소유자** 역할에 할당 됩니다. 

    > [!IMPORTANT]
    > 소유자 액세스를 사용 하면 서비스 주체가 작업 영역에서 거의 모든 작업을 수행할 수 있습니다. 액세스 권한을 부여 하는 방법을 설명 하기 위해이 문서에서 사용 됩니다. 프로덕션 환경에서는 사용자가 의도 한 역할을 수행 하는 데 필요한 최소한의 액세스 권한을 서비스 주체에 부여 하는 것이 좋습니다. 시나리오에 필요한 액세스 권한으로 사용자 지정 역할을 만드는 방법에 대 한 자세한 내용은 [Azure Machine Learning 작업 영역에 대 한 액세스 관리](how-to-assign-roles.md)를 참조 하세요.

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    이 호출은 성공 시 출력을 생성 하지 않습니다.

## <a name="configure-a-managed-identity"></a>관리 ID 구성

> [!IMPORTANT]
> 관리 id는 Azure 가상 컴퓨터 또는 Azure Machine Learning 계산 클러스터에서 Azure Machine Learning SDK를 사용 하는 경우에만 지원 됩니다. 관리 id를 계산 클러스터와 함께 사용 하는 것은 현재 미리 보기 상태입니다.

### <a name="managed-identity-with-a-vm"></a>VM을 사용 하는 관리 id

1. [VM에서 Azure 리소스에 대 한 시스템 할당 관리 id](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)를 사용 하도록 설정 합니다.

1. [Azure Portal](https://portal.azure.com)에서 작업 영역을 선택한 다음 __Access Control (IAM)__ 을 선택 하 고 __, 역할 할당을 추가__ 하 고, __액세스 권한 할당__ 드롭다운에서 __가상 컴퓨터__ 를 선택 합니다. 마지막으로 VM의 id를 선택 합니다.

1. 이 id에 할당할 역할을 선택 합니다. 예를 들어 참가자 또는 사용자 지정 역할이 있습니다. 자세한 내용은 [리소스에 대 한 액세스 제어](how-to-assign-roles.md)를 참조 하세요.

### <a name="managed-identity-with-compute-cluster"></a>계산 클러스터가 포함 된 관리 되는 id

자세한 내용은 [계산 클러스터에 대 한 관리 Id 설정](how-to-create-attach-compute-cluster.md#managed-identity)을 참조 하세요.

<a id="interactive-authentication"></a>

## <a name="use-interactive-authentication"></a>대화형 인증 사용

> [!IMPORTANT]
> 대화형 인증은 브라우저를 사용 하며 쿠키 (타사 쿠키 포함)가 필요 합니다. 쿠키를 사용 하지 않도록 설정한 경우 "로그인 할 수 없습니다."와 같은 오류가 표시 될 수 있습니다. 이 오류는 [Azure multi-factor authentication](../active-directory/authentication/concept-mfa-howitworks.md)을 사용 하도록 설정한 경우에도 발생할 수 있습니다.

설명서 및 샘플에서 대부분의 예제는 대화형 인증을 사용 합니다. 예를 들어 SDK를 사용 하는 경우 UI 기반 인증 흐름을 자동으로 표시 하는 두 개의 함수 호출이 있습니다.

* `from_config()` 함수를 호출하면 프롬프트가 표시됩니다.

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

    `from_config()` 함수는 작업 영역 연결 정보를 포함하는 JSON 파일을 찾습니다.

* 생성자를 사용 하 여 `Workspace` 구독, 리소스 그룹 및 작업 영역 정보를 제공 하는 경우에도 대화형 인증을 요청 합니다.

    ```python
    ws = Workspace(subscription_id="your-sub-id",
                  resource_group="your-resource-group-id",
                  workspace_name="your-workspace-name"
                  )
    ```

> [!TIP]
> 여러 테넌트에 액세스할 수 있는 경우 해당 클래스를 가져오고 대상 테넌트를 명시적으로 정의해야 합니다. `InteractiveLoginAuthentication`에 대한 생성자를 호출하면 위의 호출과 비슷하게 로그인하라는 메시지가 표시됩니다.
>
> ```python
> from azureml.core.authentication import InteractiveLoginAuthentication
> interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
> ```

Azure CLI 사용 하는 경우 `az login` 명령은 CLI 세션을 인증 하는 데 사용 됩니다. 자세한 내용은 [Azure CLI 시작](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)을 참조 하세요.

> [!TIP]
> 이전에 Azure CLI를 사용 하 여 대화형으로 인증 한 환경에서 SDK를 사용 하는 경우 클래스를 사용 하 여 `AzureCliAuthentication` CLI에서 캐시 된 자격 증명을 사용 하 여 작업 영역에 인증할 수 있습니다.
>
> ```python
> from azureml.core.authentication import AzureCliAuthentication
> cli_auth = AzureCliAuthentication()
> ws = Workspace(subscription_id="your-sub-id",
>                resource_group="your-resource-group-id",
>                workspace_name="your-workspace-name",
>                auth=cli_auth
>                )
> ```

<a id="service-principal-authentication"></a>

## <a name="use-service-principal-authentication"></a>서비스 주체 인증 사용

서비스 주체를 사용 하 여 SDK에서 작업 영역에 인증 하려면 `ServicePrincipalAuthentication` 클래스 생성자를 사용 합니다. 매개 변수로 서비스 공급자를 만들 때 얻은 값을 사용 합니다. `tenant_id` 매개 변수는 위의 `tenantId`에 매핑되고, `service_principal_id`는 `clientId`에 매핑되고, `service_principal_password`는 `clientSecret`에 매핑됩니다.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

이제 `sp` 변수는 SDK에서 직접 사용하는 인증 개체를 포함합니다. 일반적으로 다음 코드와 같이 환경 변수에서 위의 사용된 ID/암호를 저장하는 것이 좋습니다. 환경 변수에 저장 하면 정보가 실수로 GitHub 리포지토리로 체크 인 되지 않습니다.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Python에서 실행되고 SDK를 주로 사용하는 자동화 워크플로의 경우 대부분의 경우 인증에 이 개체를 그대로 사용할 수 있습니다. 다음 코드는 사용자가 만든 auth 개체를 사용 하 여 작업 영역에 인증 합니다.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

### <a name="use-a-service-principal-from-the-azure-cli"></a>Azure CLI에서 서비스 주체를 사용 합니다.

Azure CLI 명령에 대해 서비스 주체를 사용할 수 있습니다. 자세한 내용은 [서비스 주체를 사용 하 여 로그인](/cli/azure/create-an-azure-service-principal-azure-cli?preserve-view=true&view=azure-cli-latest#sign-in-using-a-service-principal)을 참조 하세요.

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>REST API에서 서비스 주체 사용 (미리 보기)

서비스 주체를 사용 하 여 Azure Machine Learning [REST API](/rest/api/azureml/) (미리 보기)에 인증할 수도 있습니다. 자동화 워크플로에서 헤드리스 인증에 대한 서비스 간 호출을 허용하는 Azure Active Directory [클라이언트 자격 증명 권한 부여 흐름](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)을 사용합니다. 이 예제는 Python 및 Node.js 모두에서 [ADAL 라이브러리](../active-directory/azuread-dev/active-directory-authentication-libraries.md)를 사용하여 구현되지만 OpenID Connect 1.0을 지원하는 오픈 소스 라이브러리를 사용할 수도 있습니다.

> [!NOTE]
> MSAL.js는 ADAL보다 최신 라이브러리이지만 MSAL.js에서 클라이언트 자격 증명을 사용하여 서비스 간 인증을 수행할 수 없습니다. 주로 특정 사용자와 연결된 대화형/UI 인증을 위한 클라이언트 쪽 라이브러리이기 때문입니다. REST API를 사용하여 자동화 워크플로를 빌드하려면 아래와 같이 ADAL을 사용하는 것이 좋습니다.

#### <a name="nodejs"></a>Node.js

Node.js를 사용하여 인증 토큰을 생성하려면 다음 단계를 사용합니다. 사용자 환경에서 `npm install adal-node`를 실행합니다. 그런 다음, 위의 단계에서 만든 서비스 주체의 `tenantId`, `clientId` 및 `clientSecret`를 다음 스크립트에서 일치하는 변수의 값으로 사용합니다.

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

변수 `tokenResponse`는 토큰 및 관련 메타데이터(예: 만료 시간)를 포함하는 개체입니다. 토큰은 1시간 동안 유효하며, 동일하게 다시 호출하고 새 토큰을 검색하여 새로 고칠 수 있습니다. 다음 코드 조각은 샘플 응답입니다.

```javascript
{
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id'
}
```

`accessToken` 속성을 사용하여 인증 토큰을 페치합니다. 토큰을 사용하여 API 호출을 수행하는 방법에 대한 예제는 [REST API 설명서](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API)를 참조하세요.

#### <a name="python"></a>Python

Python을 사용하여 인증 토큰을 생성하려면 다음 단계를 사용합니다. 사용자 환경에서 `pip install adal`를 실행합니다. 그런 다음, 위의 단계에서 만든 서비스 주체의 `tenantId`, `clientId` 및 `clientSecret`를 다음 스크립트에서 해당 변수의 값으로 사용합니다.

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

변수 `token_response`는 토큰 및 관련 메타데이터(예: 만료 시간)를 포함하는 사전입니다. 토큰은 1시간 동안 유효하며, 동일하게 다시 호출하고 새 토큰을 검색하여 새로 고칠 수 있습니다. 다음 코드 조각은 샘플 응답입니다.

```python
{
    'tokenType': 'Bearer',
    'expiresIn': 3599,
    'expiresOn': '2019-12-17 19:47:15.150205',
    'resource': 'https://management.azure.com/',
    'accessToken': 'random-oauth-token',
    'isMRRT': True,
    '_clientId': 'your-client-id',
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

`token_response["accessToken"]`를 사용하여 인증 토큰을 페치합니다. 토큰을 사용하여 API 호출을 수행하는 방법에 대한 예제는 [REST API 설명서](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API)를 참조하세요.

#### <a name="java"></a>Java

Java에서 표준 REST 호출을 사용 하 여 전달자 토큰을 검색 합니다.

```java
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();
HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
if (response.statusCode == 200)
{
     body = gson.fromJson(body, AuthenticationBody.class);

    // ... etc ... 
}
// ... etc ...

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl){
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create(authUrl))
            .POST(HttpRequest.BodyPublishers.ofString(bodyString))
            .build();
    return request;
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

위의 코드는 이외의 예외 및 상태 코드를 처리 해야 `200 OK` 하지만 패턴을 보여 줍니다. 

- 클라이언트 ID 및 암호를 사용 하 여 프로그램에 액세스 권한이 있어야 하는지 확인 합니다.
- 테 넌 트 ID를 사용 하 여 찾을 위치를 지정 `login.microsoftonline.com` 합니다.
- 권한 부여 토큰의 원본으로 Azure Resource Manager 사용

## <a name="use-managed-identity-authentication"></a>관리 id 인증 사용

관리 id로 구성 된 VM 또는 계산 클러스터에서 작업 영역을 인증 하려면 클래스를 사용 `MsiAuthentication` 합니다. 다음 예제에서는이 클래스를 사용 하 여 작업 영역에 인증 하는 방법을 보여 줍니다.

```python
from azureml.core.authentication import MsiAuthentication

msi_auth = MsiAuthentication()

ws = Workspace(subscription_id="your-sub-id",
                resource_group="your-resource-group-id",
                workspace_name="your-workspace-name",
                auth=msi_auth
                )
```

## <a name="next-steps"></a>다음 단계

* [학습에서 비밀을 사용 하는 방법](how-to-use-secrets-in-runs.md)
* [웹 서비스로 배포 된 모델에 대 한 인증을 구성 하는 방법](how-to-authenticate-web-service.md)입니다.
* [웹 서비스로 배포된 Azure Machine Learning 모델 사용](how-to-consume-web-service.md)
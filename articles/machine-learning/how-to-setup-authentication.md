---
title: 인증 설정
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 다양한 리소스 및 워크플로에 대한 인증을 설정하고 구성하는 방법을 알아봅니다. 개발 또는 테스트 목적의 간단한 UI 기반 인증부터 전체 Azure Active Directory 서비스 주체 인증에 이르기까지 서비스 내에서 인증을 구성하고 사용하는 방법에는 여러 가지가 있습니다.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: has-adal-ref
ms.openlocfilehash: 6b2cfa85ea412a5ef8bda47a7ff6e99970ba6b0e
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611843"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Azure Machine Learning 리소스 및 워크플로에 대한 인증 설정
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning에서 다양한 리소스 및 워크플로에 대한 인증을 설정하고 구성하는 방법을 알아봅니다. 개발 또는 테스트 목적의 간단한 UI 기반 인증부터 전체 Azure Active Directory 서비스 주체 인증에 이르기까지 서비스에서 인증을 받는 방법에는 여러 가지가 있습니다. 또한 이 문서에서는 웹 서비스 인증의 작동 방식에 대한 차이점과 Azure Machine Learning REST API에서 인증을 받는 방법을 설명합니다.

이 문서에서는 다음 태스크를 수행하는 방법을 보여 줍니다.

* 테스트/개발에 대화형 UI 인증 사용
* 서비스 주체 인증 설정
* 작업 영역에서 인증 받기
* Azure Machine Learning REST API에 대한 OAuth 2.0 전달자 형식 토큰 가져오기
* 웹 서비스 인증 이해

Azure Machine Learning 내의 보안 및 인증에 대한 일반적인 개요는 [개념 문서](concept-enterprise-security.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)을 만듭니다.
* Azure Machine Learning SDK를 설치하거나 SDK가 이미 설치되어 있는 [Azure Machine Learning Notebook VM](concept-azure-machine-learning-architecture.md#compute-instance)을 사용하도록 [개발 환경을 구성](how-to-configure-environment.md)합니다.

## <a name="interactive-authentication"></a>대화형 인증

이 서비스에 대한 설명서에 나오는 대부분의 예제에서는 간단한 테스트 및 시연 방법으로 Jupyter Notebook에서 대화형 인증을 사용합니다. 이것은 빌드하는 항목을 테스트하는 간단한 방법입니다. UI 기반 인증 흐름을 자동으로 확인하는 두 개의 함수 호출이 있습니다.

`from_config()` 함수를 호출하면 프롬프트가 표시됩니다.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

`from_config()` 함수는 작업 영역 연결 정보를 포함하는 JSON 파일을 찾습니다. 또한 `Workspace` 생성자를 사용하여 연결 정보를 명시적으로 지정할 수 있으며, 이 경우 대화형 인증도 요구됩니다. 두 호출은 모두 동일합니다.

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

여러 테넌트에 액세스할 수 있는 경우 해당 클래스를 가져오고 대상 테넌트를 명시적으로 정의해야 합니다. `InteractiveLoginAuthentication`에 대한 생성자를 호출하면 위의 호출과 비슷하게 로그인하라는 메시지가 표시됩니다.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

대화형 인증은 테스트 및 학습에 유용하지만 자동화 또는 헤드리스 워크플로를 만드는 데는 도움이 되지 않습니다. SDK를 사용하는 자동화 프로세스의 경우 서비스 주체 인증을 설정하는 것이 가장 좋은 방법입니다.

## <a name="set-up-service-principal-authentication"></a>서비스 주체 인증 설정

이 프로세스는 특정 사용자 로그인에서 분리된 인증을 사용하도록 설정하여 자동화 워크플로에서 Azure Machine Learning Python SDK에 인증할 수 있도록 하는 데 필요합니다. 또한 서비스 주체 인증을 사용하여 [REST API에서 인증을 받을 수도 있습니다](#azure-machine-learning-rest-api-auth).

서비스 주체 인증을 설정하려면 먼저 Azure Active Directory에서 앱 등록을 만든 다음, ML 작업 영역에 대해 앱 역할 기반 액세스 권한을 부여합니다. 이 설치를 완료하는 가장 쉬운 방법은 Azure Portal의 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)을 사용하는 것입니다. 포털에 로그인한 후 페이지의 오른쪽 위, 이름 옆에 있는 `>_` 아이콘을 클릭하여 셸을 엽니다.

Azure 계정에서 이전에 cloud shell을 사용해 본 적이 없으면 기록된 모든 파일을 저장하기 위한 스토리지 계정 리소스를 만들어야 합니다. 일반적으로 이 스토리지 계정에는 약간의 월별 비용이 발생합니다. 또한 이전에 다음 명령을 통해 사용해 본 적이 없으면 기계 학습 확장을 설치합니다.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> 다음 단계를 수행하려면 구독에 대한 관리자여야 합니다.

그런 후 다음 명령을 실행하여 서비스 주체를 만듭니다. 이름을 지정합니다(이 경우에는 **ml-auth**).

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

이제 위의 `clientId` 값을 `--id` 매개 변수에 대한 입력으로 사용하고 다음 명령을 실행하여 방금 만든 서비스 주체에 대한 세부 정보를 가져옵니다.

```azurecli-interactive
az ad sp show --id your-client-id
```

다음은 명령의 JSON 출력에 대한 간단한 예제입니다. 다음 단계에서 해당 값이 필요하므로 `objectId` 필드를 기록해 둡니다.

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

그런 후 다음 명령을 사용하여 서비스 주체에 기계 학습 작업 영역에 대한 액세스 권한을 할당합니다. 작업 영역 이름과 `-w` 및 `-g` 매개 변수에 대한 리소스 그룹 이름이 각각 필요합니다. `--user` 매개 변수의 경우 이전 단계의 `objectId` 값을 사용합니다. `--role` 매개 변수를 사용하여 서비스 주체에 대한 액세스 역할을 설정할 수 있으며, 일반적으로 **소유자** 또는 **기여자**를 사용합니다. 둘 다 컴퓨팅 클러스터 및 데이터 저장소와 같은 기존 리소스에 대한 쓰기 권한이 있지만 **소유자**만 이러한 리소스를 프로비저닝할 수 있습니다.

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

이 호출은 출력을 생성하지 않지만 이제 작업 영역에 대해 서비스 주체 인증이 설정되었습니다.

## <a name="authenticate-to-your-workspace"></a>작업 영역에서 인증 받기

이제 서비스 주체 인증을 사용하도록 설정했으므로 사용자로 물리적으로 로그인하지 않고도 SDK에서 작업 영역의 인증을 받을 수 있습니다. `ServicePrincipalAuthentication` 클래스 생성자를 사용하고 이전 단계에서 가져온 값을 매개 변수로 사용합니다. `tenant_id` 매개 변수는 위의 `tenantId`에 매핑되고, `service_principal_id`는 `clientId`에 매핑되고, `service_principal_password`는 `clientSecret`에 매핑됩니다.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

이제 `sp` 변수는 SDK에서 직접 사용하는 인증 개체를 포함합니다. 일반적으로 다음 코드와 같이 환경 변수에서 위의 사용된 ID/암호를 저장하는 것이 좋습니다.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Python에서 실행되고 SDK를 주로 사용하는 자동화 워크플로의 경우 대부분의 경우 인증에 이 개체를 그대로 사용할 수 있습니다. 다음 코드는 방금 만든 인증 개체를 사용하여 작업 영역에서 인증을 합니다.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Azure Machine Learning REST API 인증

위의 단계에서 만든 서비스 주체는 Azure Machine Learning [REST API](https://docs.microsoft.com/rest/api/azureml/)에서 인증을 받는 데도 사용할 수 있습니다. 자동화 워크플로에서 헤드리스 인증에 대한 서비스 간 호출을 허용하는 Azure Active Directory [클라이언트 자격 증명 권한 부여 흐름](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)을 사용합니다. 이 예제는 Python 및 Node.js 모두에서 [ADAL 라이브러리](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)를 사용하여 구현되지만 OpenID Connect 1.0을 지원하는 오픈 소스 라이브러리를 사용할 수도 있습니다.

> [!NOTE]
> MSAL.js는 ADAL보다 최신 라이브러리이지만 MSAL.js에서 클라이언트 자격 증명을 사용하여 서비스 간 인증을 수행할 수 없습니다. 주로 특정 사용자와 연결된 대화형/UI 인증을 위한 클라이언트 쪽 라이브러리이기 때문입니다. REST API를 사용하여 자동화 워크플로를 빌드하려면 아래와 같이 ADAL을 사용하는 것이 좋습니다.

### <a name="nodejs"></a>Node.js

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

변수 `tokenResponse`는 토큰 및 관련 메타데이터(예: 만료 시간)를 포함하는 개체입니다. 토큰은 1시간 동안 유효하며, 동일하게 다시 호출하고 새 토큰을 검색하여 새로 고칠 수 있습니다. 다음은 샘플 응답입니다.

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

### <a name="python"></a>Python

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

변수 `token_response`는 토큰 및 관련 메타데이터(예: 만료 시간)를 포함하는 사전입니다. 토큰은 1시간 동안 유효하며, 동일하게 다시 호출하고 새 토큰을 검색하여 새로 고칠 수 있습니다. 다음은 샘플 응답입니다.

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

## <a name="web-service-authentication"></a>웹 서비스 인증

Azure Machine Learning의 웹 서비스는 위에서 설명한 것과 다른 인증 패턴을 사용합니다. 배포된 웹 서비스에서 인증을 받는 가장 쉬운 방법은 **키 기반 인증**을 사용하는 것입니다. 이를 통해 새로 고칠 필요가 없는 정적 전달자 형식의 인증 키를 생성할 수 있습니다. 배포된 웹 서비스에서만 인증을 받으면 될 경우 위에 표시된 대로 서비스 주체 인증을 설정할 필요가 없습니다.

Azure Kubernetes Service에 배포된 웹 서비스에는 기본적으로 키 기반 인증이 *사용하도록 설정*되어 있습니다. Azure Container Instances 배포 서비스에는 기본적으로 키 기반 인증이 *사용하지 않도록 설정*되어 있지만 ACI 웹 서비스를 만들 때 `auth_enabled=True`를 설정하여 사용하도록 설정할 수 있습니다. 다음은 키 기반 인증이 사용하도록 설정된 ACI 배포 구성을 만드는 예입니다.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

그런 다음, `Model` 클래스를 사용하여 배포에서 사용자 지정 ACI 구성을 사용할 수 있습니다.

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

인증 키를 페치하려면 `aci_service.get_keys()`를 사용합니다. 키를 다시 생성하려면 `regen_key()` 함수를 사용하고 **Primary** 또는 **Secondary**를 전달합니다.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

웹 서비스는 토큰 기반 인증도 지원하지만 Azure Kubernetes Service 배포에 대해서만 지원합니다. 인증에 대한 자세한 내용은 웹 서비스 사용에 대한 [방법](how-to-consume-web-service.md)을 참조하세요.

### <a name="token-based-web-service-authentication"></a>토큰 기반 웹 서비스 인증

웹 서비스에 대한 토큰 인증을 사용하도록 설정하는 경우 사용자는 액세스할 수 있도록 웹 서비스에 대한 Azure Machine Learning JSON 웹 토큰을 제공해야 합니다. 토큰은 지정된 시간 프레임 후에 만료되므로 계속 호출하려면 새로 고쳐야 합니다.

* Azure Kubernetes Service에 배포하는 경우 토큰 인증이 **기본적으로 사용하지 않도록 설정**됩니다.
* 토큰 인증은 Azure Container Instances에 배포할 때 **지원되지 않습니다**.

토큰 인증을 제어하려면 배포를 만들거나 업데이트할 때 `token_auth_enabled` 매개 변수를 사용합니다.

토큰 인증을 사용하도록 설정하면 `get_token` 메서드를 사용하여 JWT(JSON 웹 토큰) 및 해당 토큰의 만료 시간을 검색할 수 있습니다.

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> 토큰의 `refresh_by` 시간 이후 새 토큰을 요청해야 합니다. Python SDK 외부에서 토큰을 새로 고쳐야 하는 경우 한 가지 옵션은 앞에서 설명한 대로 서비스 주체 인증에서 REST API를 사용하여 `service.get_token()`을 주기적으로 호출하는 것입니다.
>
> Azure Kubernetes Service 클러스터와 동일한 지역에 Azure Machine Learning 작업 영역을 만드는 것이 좋습니다.
>
> 토큰으로 인증하기 위해 웹 서비스는 Azure Machine Learning 작업 영역이 생성되는 지역을 호출합니다. 작업 영역의 지역을 사용할 수 없는 경우에는 클러스터가 작업 영역의 다른 지역에 있는 경우에도 웹 서비스에 대한 토큰을 페치할 수 없습니다. 결과적으로 작업 영역을 다시 사용할 수 있을 때까지 Azure AD 인증을 사용할 수 없습니다.
>
> 또한 클러스터 지역과 작업 영역의 지역 간 거리가 클수록 토큰을 페치하는 데 시간이 오래 걸립니다.

## <a name="next-steps"></a>다음 단계

* [이미지 분류 모델 학습 및 배포](tutorial-train-models-with-aml.md)
* [웹 서비스로 배포된 Azure Machine Learning 모델 사용](how-to-consume-web-service.md)

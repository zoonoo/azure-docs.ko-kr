---
title: 인증 설정
titleSuffix: Azure Machine Learning
description: Azure 기계 학습에서 다양한 리소스 및 워크플로에 대한 인증을 설정하고 구성하는 방법을 알아봅니다. 개발 또는 테스트 목적으로 간단한 UI 기반 인증부터 전체 Azure Active Directory 서비스 보안 주체 인증에 이르기까지 서비스 내에서 인증을 구성하고 사용하는 방법에는 여러 가지가 있습니다.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: fcaa7a0c44851d6b48b40b01af4c8ec992c330b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283538"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Azure 기계 학습 리소스 및 워크플로에 대한 인증 설정
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning에서 다양한 리소스 및 워크플로에 대한 인증을 설정하고 구성하는 방법을 알아봅니다. 개발 또는 테스트 목적으로 간단한 UI 기반 인증부터 전체 Azure Active Directory 서비스 보안 주체 인증에 이르기까지 서비스에 인증하는 방법에는 여러 가지가 있습니다. 이 문서에서는 웹 서비스 인증의 작동 방식과 Azure 기계 학습 REST API를 인증하는 방법의 차이점에 대해서도 설명합니다.

이 방법은 다음 작업을 수행하는 방법을 보여 주며 다음과 같은 작업을 수행합니다.

* 테스트/개발에 대화형 UI 인증 사용
* 서비스 주체 인증 설정
* 작업 영역에 대한 인증
* Azure 기계 학습 REST API에 대한 OAuth2.0 베어러 형 토큰 받기
* 웹 서비스 인증 이해

Azure 기계 학습 내에서 보안 및 인증에 대한 일반적인 개요는 [개념 문서를](concept-enterprise-security.md) 참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

* Azure [기계 학습 작업 영역을](how-to-manage-workspace.md)만듭니다.
* [개발 환경을 구성하여](how-to-configure-environment.md) Azure 기계 학습 SDK를 설치하거나 SDK가 이미 설치된 [Azure 기계 학습 노트북 VM을](concept-azure-machine-learning-architecture.md#compute-instance) 사용합니다.

## <a name="interactive-authentication"></a>대화형 인증

이 서비스에 대한 설명서의 대부분의 예제는 Jupyter 노트북에서 테스트 및 데모를 위한 간단한 방법으로 대화형 인증을 사용합니다. 이것은 당신이 구축하는 것을 테스트하는 간단한 방법입니다. UI 기반 인증 흐름을 자동으로 묻는 두 가지 함수 호출이 있습니다.

함수를 `from_config()` 호출하면 프롬프트가 발행됩니다.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

이 `from_config()` 함수는 작업 영역 연결 정보가 포함된 JSON 파일을 찾습니다. `Workspace` 생성자(s)를 사용하여 연결 세부 정보를 명시적으로 지정할 수도 있으며, 이 생성자는 대화형 인증을 묻는 메시지가 표시됩니다. 두 호출은 모두 동일합니다.

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

여러 테넌트에 액세스할 수 있는 경우 클래스를 가져오고 대상으로 하는 테넌트를 명시적으로 정의해야 할 수 있습니다. 생성자 `InteractiveLoginAuthentication` 호출을 호출하면 위의 호출과 유사한 로그인하라는 메시지가 표시됩니다.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

테스트 및 학습에 유용하지만 대화형 인증은 자동화된 또는 헤드리스 워크플로를 구축하는 데 도움이 되지 않습니다. 서비스 주체 인증 설정은 SDK를 사용하는 자동화된 프로세스에 가장 적합한 방법입니다.

## <a name="set-up-service-principal-authentication"></a>서비스 주체 인증 설정

이 프로세스는 자동화된 워크플로에서 Azure 기계 학습 파이썬 SDK에 인증할 수 있는 특정 사용자 로그인에서 분리된 인증을 활성화하는 데 필요합니다. 서비스 주체 인증을 통해 [REST API를 인증할](#azure-machine-learning-rest-api-auth)수도 있습니다.

서비스 주체 인증을 설정하려면 먼저 Azure Active Directory에서 앱 등록을 만든 다음 ML 작업 영역에 대한 앱 역할 기반 액세스 권한을 부여합니다. 이 설정을 완료하는 가장 쉬운 방법은 Azure 포털의 [Azure 클라우드 셸을](https://azure.microsoft.com/features/cloud-shell/) 사용하는 것입니다. 포털에 로그인한 후 이름 `>_` 근처 페이지 오른쪽 상단에 있는 아이콘을 클릭하여 셸을 엽니다.

Azure 계정에서 이전에 클라우드 셸을 사용하지 않은 경우 작성된 파일을 저장하기 위한 저장소 계정 리소스를 만들어야 합니다. 일반적으로 이 저장소 계정에는 무시할 수 있는 월별 비용이 발생합니다. 또한 다음 명령으로 이전에 사용하지 않은 경우 기계 학습 확장을 설치합니다.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> 다음 단계를 수행하려면 구독의 관리자여야 합니다.

그런 다음 다음 명령을 실행하여 서비스 주체를 만듭니다. 이 경우 **ml-auth에**이름을 지정합니다.

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth --name ml-auth
```

출력은 다음과 유사한 JSON이 됩니다. 이 문서의 `clientId` `clientSecret`다른 `tenantId` 단계에 필요한 경우 는 에서 와 서 및 필드를 기록해 둡을 기록합니다.

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

그런 다음 다음 명령을 실행하여 방금 만든 서비스 주체에 `clientId` 대한 세부 정보를 매개 `--id` 변수에 대한 입력으로 위의 값을 사용합니다.

```azurecli-interactive
az ad sp show --id your-client-id
```

다음은 명령에서 JSON 출력의 간단한 예입니다. 다음 단계에 `objectId` 대한 값이 필요하므로 필드를 기록해 둡을 기록합니다.

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

다음으로 다음 명령을 사용하여 서비스 주체가 기계 학습 작업 영역에 액세스할 수 있도록 할당합니다. 작업 영역 이름과 해당 리소스 그룹 이름이 각각 `-w` `-g` 및 매개 변수에 대해 필요합니다. 매개 `--user` 변수의 경우 `objectId` 이전 단계의 값을 사용합니다. 매개 `--role` 변수를 사용하면 서비스 주체에 대한 액세스 역할을 설정할 수 있으며 일반적으로 **소유자** 또는 **참여자**중 하나를 사용합니다. 둘 다 계산 클러스터 및 데이터 스토어와 같은 기존 리소스에 대한 쓰기 액세스 권한을 가지지만 **소유자만** 이러한 리소스를 프로비전할 수 있습니다. 

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

이 호출은 출력을 생성하지 않지만 이제 작업 영역에 대한 서비스 주체 인증을 설정했습니다.

## <a name="authenticate-to-your-workspace"></a>작업 영역에 대한 인증

이제 서비스 주 체급을 사용하도록 설정되었으므로 사용자로 물리적으로 로그인하지 않고도 SDK의 작업 영역에 인증할 수 있습니다. `ServicePrincipalAuthentication` 클래스 생성자 사용 하 고 매개 변수로 이전 단계에서 얻은 값을 사용 합니다. 매개 `tenant_id` 변수는 `tenantId` 위에서 `service_principal_id` 매핑하고 `clientId`에 `service_principal_password` 매핑하고 에 매핑합니다. `clientSecret`

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

이제 `sp` 변수에 SDK에서 직접 사용하는 인증 개체가 있습니다. 일반적으로 다음 코드와 같이 위에 사용된 ids/암호를 환경 변수에 저장하는 것이 좋습니다.

```python
import os 

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Python에서 실행되고 주로 SDK를 사용하는 자동화된 워크플로의 경우 대부분의 경우 인증에 이 개체를 있는 것처럼 사용할 수 있습니다. 다음 코드는 방금 만든 인증 개체를 사용하여 작업 영역에 인증합니다.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example", 
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Azure 기계 학습 REST API 인증

위의 단계에서 만든 서비스 주체는 Azure 기계 학습 [REST API를](https://docs.microsoft.com/rest/api/azureml/)인증하는 데 사용할 수도 있습니다. Azure Active Directory [클라이언트 자격 증명 부여 흐름을](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)사용 하 여 자동화 된 워크플로에서 서비스 간 호출에 대 한 헤드 리스 인증을 허용 합니다. 예제는 파이썬과 Node.js 모두에서 [ADAL 라이브러리로](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) 구현되지만 OpenID Connect 1.0을 지원하는 모든 오픈 소스 라이브러리를 사용할 수도 있습니다. 

> [!NOTE]
> MSAL.js는 ADAL보다 최신 라이브러리이지만 MSAL.js를 사용하여 서비스 간 인증을 수행할 수 없습니다. REST API를 사용하여 자동화된 워크플로를 빌드하려면 아래와 같이 ADAL을 사용하는 것이 좋습니다.

### <a name="nodejs"></a>Node.js

다음 단계를 사용하여 Node.js를 사용하여 인증 토큰을 생성합니다. 사용자 환경에서 를 `npm install adal-node`실행합니다. 그런 다음 `tenantId`에서 `clientId`를 `clientSecret` 사용하고 위의 단계에서 만든 서비스 주체에서 다음 스크립트의 일치 하는 변수에 대 한 값으로 합니다.

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

변수는 `tokenResponse` 토큰 및 만료 시간과 같은 관련 메타데이터를 포함하는 개체입니다. 토큰은 1시간 동안 유효하며 동일한 호출을 다시 실행하여 새 토큰을 검색하여 새로 고칠 수 있습니다. 다음은 샘플 응답입니다.

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

속성을 `accessToken` 사용하여 인증 토큰을 가져옵니다. 토큰을 사용하여 API를 호출하는 방법에 대한 예제는 [REST API 설명서를](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) 참조하십시오.

### <a name="python"></a>Python 

다음 단계를 사용하여 파이썬을 사용하여 인증 토큰을 생성합니다. 사용자 환경에서 를 `pip install adal`실행합니다. 그런 다음 `tenantId`에서 `clientId`를 `clientSecret` 사용하고 위의 단계에서 만든 서비스 주체에서 다음 스크립트의 적절한 변수에 대한 값으로 사용합니다.

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

변수는 `token_response` 토큰 및 만료 시간과 같은 관련 메타데이터를 포함하는 사전입니다. 토큰은 1시간 동안 유효하며 동일한 호출을 다시 실행하여 새 토큰을 검색하여 새로 고칠 수 있습니다. 다음은 샘플 응답입니다.

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

인증 `token_response["accessToken"]` 토큰을 가져오는 데 사용합니다. 토큰을 사용하여 API를 호출하는 방법에 대한 예제는 [REST API 설명서를](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) 참조하십시오.

## <a name="web-service-authentication"></a>웹 서비스 인증

Azure 기계 학습의 웹 서비스는 위에서 설명한 것과 다른 인증 패턴을 사용합니다. 배포된 웹 서비스를 인증하는 가장 쉬운 방법은 새로 고칠 필요가 없는 정적 베어러 유형 인증 키를 생성하는 **키 기반**인증을 사용하는 것입니다. 배포된 웹 서비스에 대해서만 인증해야 하는 경우 위와 같이 서비스 원칙 인증을 설정할 필요가 없습니다.

Azure Kubernetes 서비스에 배포된 웹 서비스는 기본적으로 키 기반 *인증이 활성화되어 있습니다.* Azure 컨테이너 인스턴스 배포된 서비스에는 기본적으로 키 기반 *인증이 비활성화되어* 있지만 ACI 웹 서비스를 만들 때 설정하여 `auth_enabled=True`사용하도록 설정할 수 있습니다. 다음은 키 기반 인증이 활성화된 ACI 배포 구성을 만드는 예입니다.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

그런 다음 클래스를 사용하여 배포에서 사용자 `Model` 지정 ACI 구성을 사용할 수 있습니다.

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

인증 키를 가져오려면 `aci_service.get_keys()`을 사용합니다. 키를 다시 생성하려면 `regen_key()` 함수를 사용하고 **기본** 또는 **보조**를 전달합니다.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

웹 서비스는 토큰 기반 인증을 지원하지만 Azure Kubernetes 서비스 배포에대해서만 지원합니다. 인증에 대한 자세한 내용은 웹 서비스 사용 [방법을](how-to-consume-web-service.md) 참조하세요.

### <a name="token-based-web-service-authentication"></a>토큰 기반 웹 서비스 인증

웹 서비스에 대한 토큰 인증을 사용하도록 설정하면 사용자는 Azure 기계 학습 JSON 웹 토큰을 웹 서비스에 표시하여 액세스해야 합니다. 토큰은 지정된 기간 이후에 만료되며 통화를 계속하려면 새로 고쳐야 합니다.

* Azure Kubernetes 서비스에 배포할 때 기본적으로 토큰 인증이 **비활성화됩니다.**
* Azure 컨테이너 인스턴스에 배포할 때 토큰 **인증이 지원되지 않습니다.**

토큰 인증을 제어하려면 `token_auth_enabled` 배포를 만들거나 업데이트할 때 매개 변수를 사용합니다.

토큰 인증이 활성화된 경우 `get_token` 이 메서드를 사용하여 JSON 웹 토큰(JWT) 및 해당 토큰의 만료 시간을 검색할 수 있습니다.

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> 토큰의 시간 이후에 새 토큰을 `refresh_by` 요청해야 합니다. Python SDK 외부에서 토큰을 새로 고쳐야 하는 경우 한 가지 옵션은 앞에서 설명한 것처럼 `service.get_token()` REST API를 서비스 주체 인증과 함께 사용하여 주기적으로 호출하는 것입니다.
>
> Azure Kubernetes 서비스 클러스터와 동일한 지역에 Azure 기계 학습 작업 영역을 만드는 것이 좋습니다. 
>
> 토큰으로 인증하기 위해 웹 서비스는 Azure Machine Learning 작업 영역이 만들어지는 지역을 호출합니다. 작업 영역을 사용할 수 없는 경우 클러스터가 작업 영역과 다른 지역에 있더라도 웹 서비스에 대한 토큰을 가져올 수 없습니다. 그 결과 작업 영역을 다시 사용할 수 있게 될 때까지 Azure AD 인증을 사용할 수 없습니다. 
>
> 또한 클러스터의 영역과 작업 영역 간의 거리가 클수록 토큰을 가져오는 데 더 오래 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [이미지 분류 모델을 학습하고 배포합니다.](tutorial-train-models-with-aml.md)
* [웹 서비스로 배포된 Azure 기계 학습 모델을 사용합니다.](how-to-consume-web-service.md)

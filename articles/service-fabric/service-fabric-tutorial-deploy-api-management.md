---
title: "API Management와 Azure Service Fabric 통합 | Microsoft Docs"
description: "Azure API Management 및 Service Fabric을 빠르게 시작하는 방법을 알아봅니다."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/13/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 1cadb543660b636ce7f0285973e6fb4141841b99
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="deploy-api-management-with-service-fabric"></a>Service Fabric을 사용하여 API Management 배포
이 자습서는 시리즈의 2부입니다. 이 자습서에서는 Service Fabric에서 [Azure API Management](../api-management/api-management-key-concepts.md)를 설정하여 백 엔드 서비스에 트래픽을 라우팅하는 방법을 보여줍니다.  작업을 완료한 경우 VNET에 API Management가 배포되고, 백 엔드 상태 비저장 서비스에 트래픽을 전송하도록 API 작업이 구성됩니다. Service Fabric을 사용하는 Azure API Management 시나리오에 대해 자세히 알아보려면 [개요](service-fabric-api-management-overview.md) 문서를 참조하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * API Management 배포
> * API Management 구성
> * API 작업 만들기
> * 백 엔드 정책 구성
> * 제품에 API 추가

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * 템플릿을 사용하여 Azure에서 보안 [Windows 클러스터](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 또는 [Linux 클러스터](service-fabric-tutorial-create-vnet-and-linux-cluster.md) 만들기
> * Service Fabric을 사용하여 API Management 배포

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에:
- Azure 구독이 없는 경우 [평가판 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- [Azure PowerShell 모듈 버전 4.1 이상](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) 또는 [Azure CLI 2.0](/cli/azure/install-azure-cli)을 설치합니다.
- Azure에서 보안 [Windows 클러스터](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 또는 [Linux 클러스터](service-fabric-tutorial-create-vnet-and-linux-cluster.md) 만들기

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Azure에 로그인 및 구독 선택
이 자습서에서는 [Azure PowerShell][azure-powershell]을 사용합니다. 새로 PowerShell 세션을 시작하려면 Azure 계정에 로그인한 후 Azure 명령을 실행하기 전에 구독을 선택합니다.
 
Azure 계정에 로그인하고 구독을 선택합니다.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-api-management"></a>API Management 배포
일반적으로 클라우드 응용 프로그램에는 사용자, 장치 또는 기타 응용 프로그램 수신을 위한 단일 지점을 제공하는 프런트 엔드 게이트웨이가 필요합니다. Service Fabric에서 게이트웨이는 ASP.NET Core 응용 프로그램과 같은 상태 비저장 서비스 또는 트래픽 수신을 위해 설계된 기타 서비스(예: Event Hubs, IoT Hub, Azure API Management)일 수 있습니다. 이 자습서에서는 Service Fabric 응용 프로그램에 대한 게이트웨이로 Azure API Management를 사용하는 방법을 소개합니다. API Management는 Service Fabric과 직접 통합되므로 다양한 라우팅 규칙 집합을 사용하여 백 엔드 Service Fabric 서비스에 API를 게시할 수 있습니다. 

이제 Azure에 보안 [Windows 클러스터](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 또는 [Linux 클러스터](service-fabric-tutorial-create-vnet-and-linux-cluster.md)가 있으므로, 서브넷의 VNET(가상 네트워크) 및 API Management에 지정된 NSG에 API Management를 배포합니다. 이 자습서에서는 API Management Resource Manager 템플릿이 이전 [Windows 클러스터 자습서](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 또는 [Linux 클러스터 자습서](service-fabric-tutorial-create-vnet-and-linux-cluster.md)에서 설정한 VNET, 서브넷 및 NSG의 이름을 사용하도록 미리 구성되어 있습니다. 

다음 Resource Manager 템플릿 및 매개 변수 파일을 다운로드합니다.
 
- [apim.json][apim-arm]
- [apim.parameters.json][apim-parameters-arm]

배포를 위해 `apim.parameters.json`에서 빈 매개 변수를 채웁니다.

다음 스크립트를 사용하여 API Management에 대해 Resource Manager 템플릿 및 매개 변수 파일을 배포합니다.

```powershell
$ResourceGroupName = "tutorialgroup"
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
```

```azurecli
ResourceGroupName="tutorialgroup"
az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json 
```

## <a name="configure-api-management"></a>API Management 구성

API Management 및 Service Fabric 클러스터가 배포되면 API Management에서 보안 설정 및 Service Fabric 백 엔드를 구성할 수 있습니다. 이렇게 하면 Service Fabric 클러스터로 트래픽을 전송하는 백 엔드 서비스 정책을 만들 수 있습니다.

### <a name="configure-api-management-security"></a>API Management 보안 구성

Service Fabric 백 엔드를 구성하려면 먼저 API Management 보안 설정을 구성해야 합니다. 보안 설정을 구성하려면 Azure Portal에서 API Management 서비스로 이동합니다.

#### <a name="enable-the-api-management-rest-api"></a>API Management REST API 사용

API Management REST API가 현재 백 엔드 서비스를 구성하는 유일한 방법입니다. 첫 번째 단계는 API Management REST API를 사용하도록 설정하고 보안하는 것입니다.

 1. API Management 서비스의 **보안**에서 **관리 API**를 선택합니다.
 2. **API Management REST API 사용** 확인란을 선택합니다.
 3. 나중에 Service Fabric 백 엔드를 설정하는 데 사용할 **관리 API URL**을 적어둡니다.
 4. 만료 날짜 및 키를 선택하여 **액세스 토큰**을 생성하고 페이지 아래쪽에서 **생성** 단추를 클릭합니다.
 5. **액세스 토큰**을 복사하고 저장합니다.  다음 단계에서 액세스 토큰을 사용합니다. 액세스 토큰은 기본 키 및 보조 키와 다릅니다.

#### <a name="upload-a-service-fabric-client-certificate"></a>Service Fabric 클라이언트 인증서 업로드

API Management는 서비스 검색을 위해 클러스터에 대한 액세스 권한이 있는 클라이언트 인증서를 사용하여 Service Fabric 클러스터에 인증해야 합니다. 편의성을 높이기 위해 이 자습서에서는 기본적으로 클러스터 액세스에 사용할 수 있는 [Windows 클러스터](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor) 또는 [Linux 클러스터](service-fabric-tutorial-create-vnet-and-linux-cluster.md#createvaultandcert_anchor)를 만들 때 이전에 지정된 것과 동일한 인증서를 사용합니다.

 1. API Management 서비스의 **보안**에서 **클라이언트 인증서**를 선택합니다.
 2. **+추가** 단추를 클릭합니다.
 2. Service Fabric 클러스터를 만들 때 지정한 클러스터 인증서의 개인 키 파일(.pfx)을 선택하고, 이름을 지정한 다음, 개인 키 암호를 제공합니다.

> [!NOTE]
> 이 자습서에서는 클라이언트 인증 및 클러스터 노드 간 보안에 동일한 인증서를 사용합니다. Service Fabric 클러스터에 액세스하도록 구성되어 있는 경우 별도의 클라이언트 인증서를 사용할 수 있습니다.

### <a name="configure-the-backend"></a>백 엔드 구성

API Management 보안이 구성되면 Service Fabric 백 엔드를 구성할 수 있습니다. Service Fabric 백 엔드의 경우 특정 Service Fabric 서비스가 아니라 Service Fabric 클러스터가 백 엔드입니다. 이렇게 하면 단일 정책에서 클러스터에 있는 둘 이상의 서비스로 라우팅할 수 있습니다.

이 단계에서는 이전에 생성한 액세스 토큰과 이전에 API Management에 업로드한 클러스터 인증서의 지문이 필요합니다.

> [!NOTE]
> 이전 단계에서 API Management에 별도의 클라이언트 인증서를 사용한 경우 이 단계에서는 클러스터 인증서 지문 외에도 클라이언트 인증서 지문이 필요합니다.

Service Fabric 백 엔드 서비스를 구성하는 API Management REST API를 사용하도록 설정하는 경우 앞에서 기록한 API Management API URL에 다음 HTTP PUT 요청을 보냅니다. 명령이 성공하면 `HTTP 201 Created` 응답이 표시되어야 합니다. 각 필드에 대한 자세한 내용은 API Management [백 엔드 API 참조 문서](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend)를 참조하세요.

HTTP 명령 및 URL:
```http
PUT https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10
```

헤더 요청:
```http
Authorization: SharedAccessSignature <your access token>
Content-Type: application/json
```

본문 요청:
```http
{
    "description": "<description>",
    "url": "<fallback service name>",
    "protocol": "http",
    "resourceId": "<cluster HTTP management endpoint>",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": [ "<cluster HTTP management endpoint>" ],
            "clientCertificateThumbprint": "<client cert thumbprint>",
            "serverCertificateThumbprints": [ "<cluster cert thumbprint>" ],
            "maxPartitionResolutionRetries" : 5
        }
    }
}
```

여기서 **url** 매개 변수는 백 엔드 정책에 지정된 서비스 이름이 없는 경우 기본적으로 모든 요청이 라우팅되는 클러스터 서비스의 정규화된 서비스 이름입니다. 대체 서비스를 포함하지 않으려는 경우 "fabric:/fake/service" 같은 가짜 서비스 이름을 사용할 수 있습니다.

각 필드에 대한 자세한 내용은 API Management [백 엔드 API 참조 문서](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend)를 참조하세요.

```python
#import requests library for making REST calls
import requests
import json

#specify url
url = 'https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10'

token = "SharedAccessSignature integration&201710140514&Lqnbei7n2Sot6doiNtxMFPUi/m9LsNa+1ZK/PdxqFl49JFWjXh1wW5Gd99r/tIOeHp6dU8UV5iZUdOPfcrm5hg=="

payload = {
    "description": "My Service Fabric backend",
    "url": "fabric:/ApiApplication/ApiWebService",
    "protocol": "http",
    "resourceId": "https://tutorialcluster.eastus.cloudapp.azure.com:19080",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": [ "https://tutorialcluster.eastus.cloudapp.azure.com:19080" ],
            "clientCertificateThumbprint": "97EDD7E4979FB072AF3E480A5E5EE34B1B89DD80",
            "serverCertificateThumbprints": [ "97EDD7E4979FB072AF3E480A5E5EE34B1B89DD80" ],
            "maxPartitionResolutionRetries" : 5
        }
    }
}

headers = {'Authorization': token, "Content-Type": "application/json"}

#Call REST API
response = requests.put(url, data=json.dumps(payload), headers=headers)

#Print Response
print(response.status_code)
print(response.text)
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Service Fabric 백 엔드 서비스 배포

이제 Service Fabric이 API Management에 대한 백 엔드로 구성되었으므로 Service Fabric 서비스로 트래픽을 전송하는 API에 대한 백 엔드 정책을 작성할 수 있습니다. 그러나 먼저 Service Fabric에서 실행 중이며 요청을 수락할 서비스가 있어야 합니다.

### <a name="create-a-service-fabric-service-with-an-http-endpoint"></a>HTTP 끝점이 있는 Service Fabric 서비스 만들기

이 자습서에서는 기본 웹 API 프로젝트 템플릿을 사용하여 기본적인 상태 비저장 ASP.NET Core 신뢰할 수 있는 서비스를 만듭니다. 이렇게 하면 서비스의 HTTP 끝점을 Azure API Management를 통해 노출합니다.

```
/api/values
```

[ASP.NET Core 개발에 대한 개발 환경을 설정](service-fabric-add-a-web-frontend.md#set-up-your-environment-for-aspnet-core)하는 것으로 시작합니다.

개발 환경이 설정되면 Visual Studio를 관리자 권한으로 시작하고 ASP.NET Core 서비스를 만듭니다.

 1. Visual Studio에서 파일 -> 새 프로젝트를 선택합니다.
 2. 클라우드에서 Service Fabric 응용 프로그램 템플릿을 선택하고 이름을 **"ApiApplication"**으로 지정합니다.
 3. ASP.NET Core 서비스 템플릿을 선택하고 프로젝트 이름을 **"WebApiService"**로 지정합니다.
 4. Web API ASP.NET Core 1.1 프로젝트 템플릿을 선택합니다.
 5. 프로젝트가 만들어지면 `PackageRoot\ServiceManifest.xml`을 열고 끝점 리소스 구성에서 `Port` 특성을 제거합니다.
 
    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    이렇게 하면 클러스터 Resource Manager 템플릿에서 네트워크 보안 그룹을 통해 연 응용 프로그램 포트 범위에서 Service Fabric이 동적으로 포트를 지정하여 API Management에서 해당 포트로 트래픽이 흐르도록 할 수 있습니다.
 
 6. Web API를 로컬에서 사용할 수 있는지 확인하려면 Visual Studio에서 F5 키를 누릅니다. 

    Service Fabric Explorer를 열고 ASP.NET Core 서비스의 특정 인스턴스로 드릴다운하여 서비스가 수신 대기 중인 기준 주소를 확인합니다. 기준 주소에 `/api/values`를 추가하고 브라우저에서 엽니다. 이렇게 하면 Web API 템플릿에서 ValuesController의 Get 메서드를 호출합니다. 두 개의 문자열을 포함하는 JSON 배열인 템플릿에서 제공하는 기본 응답을 반환합니다.

    ```json
    ["value1", "value2"]`
    ```

    Azure에서 API Management를 통해 노출될 끝점입니다.

 7. 마지막으로, Azure에서 응용 프로그램을 클러스터에 배포합니다. [Visual Studio를 사용하여](service-fabric-publish-app-remote-cluster.md#to-publish-an-application-using-the-publish-service-fabric-application-dialog-box) 응용 프로그램 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다. 클러스터 끝점(예: `mycluster.westus.cloudapp.azure.com:19000`)을 제공하여 Azure에서 Service Fabric 클러스터에 응용 프로그램을 배포합니다.

`fabric:/ApiApplication/WebApiService`로 명명된 ASP.NET Core 상태 비저장 서비스는 이제 Azure의 Service Fabric 클러스터에서 실행되어야 합니다.

## <a name="create-an-api-operation"></a>API 작업 만들기

이제 외부 클라이언트가 Service Fabric 클러스터에서 실행되는 ASP.NET Core 상태 비저장 서비스와 통신하는 데 사용하는 API Management에서 작업을 만들 준비가 되었습니다.

1. Azure Portal에 로그인하고 API Management 서비스 배포로 이동합니다.
2. API Management 서비스 블레이드에서 **API**를 선택합니다.
3. **+API** 및 **빈 API** 상자를 차례로 클릭하고 대화 상자를 채워 새 API를 추가합니다.

    - **웹 서비스 URL**: Service Fabric 백 엔드에는 이 URL 값이 사용되지 않습니다. 여기에 임의의 값을 입력할 수 있습니다. 이 자습서에서는 "http://servicefabric"을 사용합니다.
    - **표시 이름**: API 이름을 제공합니다. 이 자습서에서는 "Service Fabric 앱"을 사용합니다.
    - **이름**: "service-fabric-app"을 입력합니다.
    - **URL 구성표**: **HTTP**, **HTTPS** 또는 **둘 다**를 선택합니다. 이 자습서에서는 **둘 다**를 사용합니다.
    - **API URL 접미사**: API의 접미사를 제공합니다. 이 자습서에서는 "myapp"을 사용합니다.
 
4. API 목록에서**Service Fabric 앱**을 선택하고 **+ 작업 추가**를 클릭하여 프런트 엔드 API 작업을 추가합니다. 다음 값을 입력합니다.
    
    - **URL**: **GET**을 선택하고 API의 URL 경로를 제공합니다. 이 자습서에서는 "/api/values"를 사용합니다.  기본적으로 여기에 지정된 URL 경로는 백 엔드 Service Fabric 서비스로 전송되는 URL 경로입니다. 여기에 서비스에서 사용하는 것과 동일한 URL 경로, 이 예에서는 "/api/values"를 사용하면 추가 수정 없이 작업이 이루어집니다. 여기에 백 엔드 Service Fabric 서비스에서 사용하는 URL 경로와 다른 URL 경로를 지정할 수도 있습니다. 이 경우 나중에 작업 정책에서도 경로 다시 쓰기를 지정해야 합니다.
    - **표시 이름**: API 이름을 제공합니다. 이 자습서에서는 "값"을 사용합니다.

5. **Save**를 클릭합니다.

## <a name="configure-a-backend-policy"></a>백 엔드 정책 구성

백 엔드 정책은 모든 항목을 서로 연결합니다. 요청이 라우팅되는 백 엔드 Service Fabric 서비스를 구성하는 위치입니다. 이 정책은 모든 API 작업에 적용할 수 있습니다. [Service Fabric에 대한 백 엔드 구성](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService)은 다음 요청 라우팅 컨트롤을 제공합니다. 
 - 하드 코드되거나(예: `"fabric:/myapp/myservice"`) HTTP 요청에서 생성된(예: `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`) Service Fabric 서비스 인스턴스 이름을 지정하여 서비스 인스턴스 선택
 - Service Fabric 분할 체계를 통해 파티션 키를 생성하여 파티션 확인
 - 상태 저장 서비스 복제본 선택
 - 서비스 위치를 다시 확인하고 요청을 다시 보내는 조건을 지정할 수 있는 다시 확인 시도 조건

이 자습서에서는 이전에 배포한 ASP.NET Core 상태 비저장 서비스에 직접 요청을 라우팅하는 백 엔드 정책을 만듭니다.

 1. 편집 아이콘을 클릭하고 **코드 보기**를 선택하여 값 작업에 대해 **인바운드 정책**을 선택하고 편집합니다.
 2. 여기에 표시된 것처럼 정책 코드 편집기의 인바운드 정책에서 `set-backend-service` 정책을 추가하고 **저장** 단추를 클릭합니다.
    
    ```xml
    <policies>
      <inbound>
        <base/>
        <set-backend-service 
           backend-id="servicefabric"
           sf-service-instance-name="fabric:/ApiApplication/WebApiService"
           sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
      </inbound>
      <backend>
        <base/>
      </backend>
      <outbound>
        <base/>
      </outbound>
    </policies>
    ```

전체 Service Fabric 백 엔드 정책 특성 집합은 [API Management 백 엔드 설명서](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService)를 참조하세요.

### <a name="add-the-api-to-a-product"></a>제품에 API 추가 

API를 호출하려면 먼저 사용자에게 액세스 권한을 부여할 수 있는 제품에 API가 추가되어야 합니다. 

 1. API Management 서비스에서 **제품**을 선택합니다.
 2. 기본적으로 API Management는 스타터와 무제한의 두 가지 제품을 제공합니다. 무제한 제품을 선택합니다.
 3. **+API 추가**를 선택합니다.
 4. 이전 단계에서 만든 `Service Fabric App` API를 선택하고 **선택** 단추를 클릭합니다.

### <a name="test-it"></a>테스트

이제 Azure Portal에서 직접 API Management를 통해 Service Fabric의 백 엔드 서비스에 요청을 전송하는 것을 시도해 볼 수 있습니다.

 1. API Management 서비스에서 **API**를 선택합니다.
 2. 이전 단계에서 만든 **Service Fabric 앱** API에서 **테스트** 탭 및 **값** 작업을 차례로 선택합니다.
 3. **전송** 단추를 클릭하여 백 엔드 서비스로 테스트 요청을 보냅니다.  다음과 유사한 HTTP 응답이 표시됩니다.

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Access-Control-Allow-Origin: https://apimanagement.hosting.portal.azure.net

    Access-Control-Allow-Credentials: true

    Access-Control-Expose-Headers: Transfer-Encoding,Date,Server,Vary,Ocp-Apim-Trace-Location

    Ocp-Apim-Trace-Location: https://apimgmtstuvyx3wa3oqhdbwy.blob.core.windows.net/apiinspectorcontainer/RaVVuJBQ9yxtdyH55BAsjQ2-1?sv=2015-07-08&sr=b&sig=Ab6dPyLpTGAU6TdmlEVu32DMfdCXTiKAASUlwSq3jcY%3D&se=2017-09-15T05%3A49%3A53Z&sp=r&traceId=ed9f1f4332e34883a774c34aa899b832

    Date: Thu, 14 Sep 2017 05:49:56 GMT


    [
    "value1",
    "value2"
    ]
    ```

## <a name="clean-up-resources"></a>리소스 정리

클러스터는 클러스터 리소스 외에도 다른 Azure 리소스로 이루어져 있습니다. 클러스터 및 클러스터에서 사용하는 모든 리소스를 삭제하는 가장 간단한 방법은 리소스 그룹을 삭제하는 것입니다.

Azure에 로그인하고 클러스터를 제거할 구독 ID를 선택합니다.  [Azure Portal](http://portal.azure.com)에 로그인하여 구독 ID를 찾을 수 있습니다. [Remove-AzureRMResourceGroup cmdlet](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup)을 사용하여 리소스 그룹 및 모든 클러스터 리소스를 삭제합니다.

```powershell
$ResourceGroupName = "tutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="tutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * API Management 배포
> * API Management 구성
> * API 작업 만들기
> * 백 엔드 정책 구성
> * 제품에 API 추가


[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[apim-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.json
[apim-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.parameters.json

[network-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json

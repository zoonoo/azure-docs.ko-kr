---
title: Azure 애플리케이션 Insights IP 주소 컬렉션 | Microsoft Docs
description: Azure 애플리케이션 정보를 사용 하 여 IP 주소 및 지리적 위치를 처리 하는 방법 이해
ms.topic: conceptual
ms.date: 09/11/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: b702494347874a1b4977179ba882490223bdf924
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032829"
---
# <a name="geolocation-and-ip-address-handling"></a>지리적 위치 및 IP 주소 처리

이 문서에서는 기본 동작을 수정 하는 방법과 함께 지리적 위치 조회 및 IP 주소 처리가 Application Insights 하는 방법을 설명 합니다.

## <a name="default-behavior"></a>기본 동작

기본적으로 IP 주소는 임시로 수집 되지만 Application Insights에 저장 되지 않습니다. 기본 프로세스는 다음과 같습니다.

원격 분석이 Azure로 전송 될 때 IP 주소는 [MaxMind에서 GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/)를 사용 하 여 지리적 위치 조회를 수행 하는 데 사용 됩니다. 이 조회 결과는, 및 필드를 채우는 데 사용 `client_City` 됩니다 `client_StateOrProvince` `client_CountryOrRegion` . 그러면 주소가 삭제 되 고 `0.0.0.0` 필드에 기록 됩니다 `client_IP` .

* 브라우저 원격 분석: 보낸 사람의 IP 주소를 일시적으로 수집 합니다. IP 주소는 수집 끝점에서 계산 됩니다.
* 서버 원격 분석: Application Insights 원격 분석 모듈은 클라이언트 IP 주소를 일시적으로 수집 합니다. 헤더가 설정 된 경우 IP 주소는 로컬로 수집 되지 않습니다 `X-Forwarded-For` .

이 동작은 의도 하지 않은 개인 데이터 컬렉션을 방지 하기 위한 것입니다. 가능 하면 개인 데이터의 수집을 방지 하는 것이 좋습니다. 

## <a name="overriding-default-behavior"></a>기본 동작 재정의

기본값은 IP 주소를 수집 하지 않는 것입니다. 여전히이 동작을 재정의할 수 있는 유연성을 제공 합니다. 그러나 수집이 규정 준수 요구 사항 또는 지역 규정을 위반 하지 않는지 확인 하는 것이 좋습니다. 

Application Insights에서 개인 데이터를 처리 하는 방법에 대 한 자세한 내용은 [개인 데이터에 대 한 지침](../platform/personal-data-mgmt.md)을 참조 하세요.

## <a name="storing-ip-address-data"></a>IP 주소 데이터 저장

IP 컬렉션과 저장소를 사용 하도록 설정 하려면 `DisableIpMasking` Application Insights 구성 요소의 속성을로 설정 해야 합니다 `true` . 이 속성은 Azure Resource Manager 템플릿을 통해 설정 하거나 REST API를 호출 하 여 설정할 수 있습니다. 

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>포털 

단일 Application Insights 리소스에 대 한 동작만 수정 해야 하는 경우 Azure Portal를 사용 합니다. 

1. Application Insights 리소스 > **설정**  >  **템플릿 내보내기** 로 이동 합니다. 

    ![템플릿 내보내기](media/ip-collection/export-template.png)

2. **배포** 선택

    ![빨간색으로 강조 표시 된 단어 "배포"가 있는 단추](media/ip-collection/deploy.png)

3. **템플릿 편집**을 선택 합니다.

    ![빨간색으로 강조 표시 된 단어 "편집"이 있는 단추](media/ip-collection/edit-template.png)

4. 리소스에 대해 json을 다음과 같이 변경 하 고 **저장**을 선택 합니다.

    !["IbizaAIExtension" 뒤에 쉼표를 추가 하 고 "DisableIpMasking": true를 사용 하 여 아래에 새 줄을 추가 합니다.](media/ip-collection/save.png)

    > [!WARNING]
    > 다음 오류가 발생 하는 경우: ** _리소스 그룹이 템플릿에 있는 하나 이상의 리소스에서 지원 되지 않는 위치에 있습니다. 다른 리소스 그룹을 선택 하세요._** 드롭다운에서 다른 리소스 그룹을 일시적으로 선택한 다음 원래 리소스 그룹을 다시 선택 하 여 오류를 해결 합니다.

5. **동의 함**  >  **Purchase**을 선택 합니다. 

    ![선택 된 확인란을 선택 하면 "위에 명시 된 사용 약관에 동의 함" 이라는 단어가 빨간색으로 강조 표시 되 고 "구매" 단어가 빨간색으로 강조 표시 됩니다.](media/ip-collection/purchase.png)

    이 경우에는 새로운 새 항목이 실제로 구매 되지 않습니다. 기존 Application Insights 리소스의 구성을 업데이트 하는 중입니다.

6. 배포가 완료 되 면 새 원격 분석 데이터가 기록 됩니다.

    템플릿을 선택 하 고 편집 하는 경우 새로 추가 된 속성이 없으면 기본 템플릿만 표시 됩니다. IP 주소 데이터가 표시 되지 않고가 설정 되어 있는지 확인 하려면 `"DisableIpMasking": true` 다음 PowerShell을 실행 합니다. 
    
    ```powershell
    # Replace `Fabrikam-dev` with the appropriate resource and resource group name.
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    속성 목록이 결과로 반환 됩니다. 속성 중 하나를 읽어야 `DisableIpMasking: true` 합니다. Azure Resource Manager를 사용 하 여 새 속성을 배포 하기 전에 PowerShell을 실행 하는 경우 속성이 존재 하지 않습니다.

### <a name="rest-api"></a>Rest API

동일한 수정 작업을 수행 하는 [REST API](/rest/api/azure/) 페이로드는 다음과 같습니다.

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>원격 분석 이니셜라이저

보다 유연한 대안이 필요한 경우 `DisableIpMasking` [원격 분석 이니셜라이저](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) 를 사용 하 여 IP 주소 전체 또는 일부를 사용자 지정 필드에 복사할 수 있습니다. 

# <a name="net"></a>[.NET](#tab/net)

### <a name="aspnet--aspnet-core"></a>ASP.NET/ASP.NET Core

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> 에 액세스할 수 없는 경우 `ISupportProperties` 안정적인 최신 릴리스 APPLICATION INSIGHTS SDK를 실행 하 고 있는지 확인 합니다. `ISupportProperties` 는 높은 카디널리티 값을 위한 것 이지만, `GlobalProperties` 는 지역 이름, 환경 이름 등과 같은 낮은 카디널리티 값에 더 적합 합니다. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>ASP.NET에 대 한 원격 분석 이니셜라이저 사용

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>ASP.NET Core에 대 한 원격 분석 이니셜라이저 사용

ASP.NET와 동일한 ASP.NET Core 방식으로 원격 분석 이니셜라이저를 만들 수 있지만 이니셜라이저를 사용 하도록 설정 하려면 다음 예제를 참조에 사용 합니다.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```
# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```
# <a name="client-side-javascript"></a>[클라이언트 쪽 JavaScript](#tab/javascript)

### <a name="client-side-javascript"></a>클라이언트 쪽 JavaScript

서버 쪽 Sdk와 달리 클라이언트 쪽 JavaScript SDK는 IP 주소를 계산 하지 않습니다. 기본적으로 클라이언트 쪽 원격 분석에 대 한 IP 주소 계산은 Azure의 수집 끝점에서 발생 합니다. 

클라이언트 쪽에서 직접 IP 주소를 계산 하려는 경우 고유한 사용자 지정 논리를 추가 하 고 결과를 사용 하 여 태그를 설정 해야 `ai.location.ip` 합니다. 가 설정 되 면 수집 `ai.location.ip` 끝점에서 ip 주소 계산을 수행 하지 않고, 제공 된 ip 주소를 지리적 위치 조회에 사용 합니다. 이 시나리오에서 IP 주소는 기본적으로 0으로 제한 되어 있습니다. 

사용자 지정 논리에서 계산 된 전체 IP 주소를 유지 하기 위해에서 제공 하는 IP 주소 데이터를 `ai.location.ip` 별도의 사용자 지정 필드에 복사 하는 원격 분석 이니셜라이저를 사용할 수 있습니다. 그러나 타사 라이브러리 또는 고유한 사용자 지정 컬렉션 논리를 사용 하지 않고 서버 쪽 Sdk와 달리 클라이언트 쪽 SDK는 주소를 계산 하지 않습니다.    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

수집 끝점으로 전달 하기 전에 클라이언트 쪽 데이터가 프록시를 트래버스하는 경우 IP 주소 계산에는 클라이언트가 아닌 프록시의 IP 주소가 표시 될 수 있습니다. 

---

### <a name="view-the-results-of-your-telemetry-initializer"></a>원격 분석 이니셜라이저의 결과 보기

사이트에 새 트래픽을 보내는 경우 몇 분 정도 기다립니다. 그런 다음 쿼리를 실행 하 여 수집이 작동 하는지 확인할 수 있습니다.

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

새로 수집 된 IP 주소가 열에 표시 됩니다 `customDimensions_client-ip` . 기본 열에는 `client-ip` 여전히 4 개의 8 진수가 모두 0으로 채워집니다. 

Localhost에서 테스트 하는 경우의 값 `customDimensions_client-ip` 이 이면 `::1` 이 값은 예상 된 동작입니다. `::1` i p v 6의 루프백 주소를 나타냅니다. IPv4의와 동일 `127.0.01` 합니다.

## <a name="next-steps"></a>다음 단계

* Application Insights의 [개인 데이터 수집](../platform/personal-data-mgmt.md) 에 대해 자세히 알아보세요.

* Application Insights에서 [IP 주소 수집이](https://apmtips.com/posts/2016-07-05-client-ip-address/) 작동 하는 방식에 대해 자세히 알아보세요. (이 문서에서는 엔지니어 중 한 명에 의해 작성 된 이전 외부 블로그 게시물을 설명 합니다. IP 주소는으로 기록 되는 현재 기본 동작을 이전의 `0.0.0.0` 기본 제공의 메커니즘에는 더 높은 수준으로 이동 `ClientIpHeaderTelemetryInitializer` 합니다.

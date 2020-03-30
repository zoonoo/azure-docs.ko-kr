---
title: Azure 응용 프로그램 인사이트 IP 주소 컬렉션 | 마이크로 소프트 문서
description: Azure 응용 프로그램 인사이트를 통해 IP 주소 및 지리적 위치 처리 방법 이해
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 969061ec89ddd0f13caa675bc324207c6c5d8843
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656520"
---
# <a name="geolocation-and-ip-address-handling"></a>지리적 위치 및 IP 주소 처리

이 문서에서는 기본 동작을 수정하는 방법과 함께 응용 프로그램 인사이트에서 지리적 위치 조회 및 IP 주소 처리가 어떻게 발생하는지 설명합니다.

## <a name="default-behavior"></a>기본 동작

기본적으로 IP 주소는 일시적으로 수집되지만 응용 프로그램 인사이트에저장되지는 않습니다. 기본 프로세스는 다음과 같습니다.

IP 주소는 원격 분석 데이터의 일부로 응용 프로그램 인사이트로 전송됩니다. Azure에서 인기 종료점에 도달하면 IP 주소는 MaxMind 에서 [GeoLite2를](https://dev.maxmind.com/geoip/geoip2/geolite2/)사용하여 지리적 위치 조회를 수행하는 데 사용됩니다. 이 조회의 결과는 다음 필드를 `client_City`채우는 데 `client_StateOrProvince`사용됩니다. `client_CountryOrRegion` 이 때 IP 주소가 삭제되고 `0.0.0.0` `client_IP` 필드에 기록됩니다.

* 브라우저 원격 분석: 발신자의 IP 주소를 일시적으로 수집합니다. IP 주소는 인기 끝점에 의해 계산됩니다.
* 서버 원격 분석: 응용 프로그램 인사이트 모듈은 클라이언트 IP 주소를 일시적으로 수집합니다. `X-Forwarded-For`가 설정된 경우에는 수집되지 않습니다.

이 동작은 개인 데이터의 불필요한 수집을 피하기 위한 것입니다. 가능하면 개인 데이터 수집을 피하는 것이 좋습니다. 

## <a name="overriding-default-behavior"></a>기본 동작 재정의

기본 동작은 개인 데이터 수집을 최소화하는 것이지만, 당사는 여전히 IP 주소 데이터를 수집하고 저장할 수 있는 유연성을 제공합니다. IP 주소와 같은 개인 데이터를 저장하기로 선택하기 전에, 귀하가 준수할 수 있는 규정 준수 요구 사항 또는 현지 규정을 위반하지 않는지 확인하는 것이 좋습니다. 응용 프로그램 인사이트에서 개인 데이터 처리에 대한 자세한 내용은 [개인 데이터에 대한 지침을](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt)참조하십시오.

## <a name="storing-ip-address-data"></a>IP 주소 데이터 저장

IP 수집 및 저장소를 사용하려면 Application Insights 구성 `DisableIpMasking` 요소의 `true`속성을 로 설정해야 합니다. 이 속성은 Azure 리소스 관리자 템플릿을 사용하거나 REST API를 호출하여 설정할 수 있습니다. 

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

단일 Application Insights 리소스에 대한 비헤이비어만 수정해야 하는 경우 이를 수행하는 가장 쉬운 방법은 Azure Portal을 사용하는 것입니다.  

1. **설정** > **내보내기 템플릿을** > 애플리케이션 인사이트 리소스 이동 

    ![템플릿 내보내기](media/ip-collection/export-template.png)

2. **배포** 선택

    ![빨간색으로 강조 표시된 배포 단추](media/ip-collection/deploy.png)

3. **템플릿 편집을**선택합니다. 이 예제 템플릿에 나타나지 않는 추가 속성이나 리소스가 있는 경우 모든 리소스가 증분 변경/업데이트로 템플릿 배포를 수락하도록 주의해서 진행합니다.

    ![템플릿 편집](media/ip-collection/edit-template.png)

4. 리소스의 json을 다음 변경한 다음 **저장을**클릭합니다.

    ![스크린 샷은 "IbizaAIExtension"후 쉼표를 추가하고 "DisableIpMasking"와 함께 아래의 새 줄을 추가 : true](media/ip-collection/save.png)

    > [!WARNING]
    > ** _리소스 그룹이 템플릿에서 하나 이상의 리소스에서 지원되지 않는 위치에 있는 경우 다음과 같은 오류가 발생하는 경우 다른 리소스 그룹을 선택하십시오._** 드롭다운에서 다른 리소스 그룹을 일시적으로 선택한 다음 원래 리소스 그룹을 다시 선택하여 오류를 해결합니다.

5. **구매에** **동의합니다.** >  

    ![템플릿 편집](media/ip-collection/purchase.png)

    이 경우 새 항목만 구입하지 않고 기존 Application Insights 리소스의 구성을 업데이트하고 있습니다.

6. 배포가 완료되면 새 원격 분석 데이터가 기록됩니다.

    템플릿을 선택하고 다시 편집하는 경우 기본 템플릿만 표시되고 새로 추가된 속성 및 관련 값이 표시되지 않습니다. IP 주소 데이터가 표시되지 않고 설정되어 있는지 `"DisableIpMasking": true` 확인하려는 경우. 다음 PowerShell 실행: `Fabrikam-dev` (적절한 리소스 및 리소스 그룹 이름으로 바꾸기).
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    결과적으로 속성 목록이 반환됩니다. 속성 중 하나를 `DisableIpMasking: true`읽어야 합니다. Azure 리소스 관리자를 사용하여 새 속성을 배포하기 전에 PowerShell을 실행하는 경우 속성이 존재하지 않습니다.

### <a name="rest-api"></a>Rest API

동일한 수정을 위한 [나머지 API](https://docs.microsoft.com/rest/api/azure/) 페이로드는 다음과 같습니다.

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

IP 주소의 전체 또는 `DisableIpMasking` 일부를 기록하는 것보다 더 유연한 대안이 필요한 경우 [원격 분석 초기화기를](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) 사용하여 IP의 전부 또는 일부를 사용자 지정 필드에 복사할 수 있습니다. 

### <a name="aspnet--aspnet-core"></a>ASP.NET / ASP.NET 코어

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
> 액세스할 `ISupportProperties`수 없는 경우 응용 프로그램 인사이트 SDK의 최신 안정적인 릴리스를 실행하고 있는지 확인합니다. `ISupportProperties`는 높은 카디널리티 값을 `GlobalProperties` 위한 반면 지역 이름, 환경 이름 등과 같은 낮은 카디널리티 값에 더 적합합니다. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>ASP.NET 대한 원격 분석 초기화 사용

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

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>코어에 대한 원격 분석 초기화 ASP.NET

ASP.NET ASP.NET 코어에 대해 동일한 방식으로 원격 분석 초기화자를 만들 수 있지만 초기화기를 사용하도록 설정하려면 다음 예제를 참조로 사용합니다.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```

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

### <a name="client-side-javascript"></a>클라이언트 쪽 JavaScript

서버 측 SDK와 달리 클라이언트 측 자바스크립트 SDK는 IP 주소를 계산하지 않습니다. 기본적으로 클라이언트 측 원격 분석의 IP 주소 계산은 원격 분석이 도착하면 Azure의 인기 끝점에서 수행됩니다. 즉, 클라이언트 측 데이터를 프록시로 보낸 다음 수집 끝점으로 전달하는 경우 IP 주소 계산에 클라이언트가 아닌 프록시의 IP 주소가 표시될 수 있습니다. 프록시를 사용하지 않으면 문제가 되지 않습니다.

클라이언트 측에서 직접 IP 주소를 계산하려면 이 계산을 수행하고 결과를 사용하여 `ai.location.ip` 태그를 설정하려면 사용자 지정 논리를 사용자 지정 논리를 추가해야 합니다. `ai.location.ip` 설정되면 IP 주소 계산은 인기 끝점에 의해 수행되지 않으며 제공된 IP 주소가 지정되고 지리적 조회를 수행하는 데 사용됩니다. 이 시나리오에서는 IP 주소는 여전히 기본적으로 비워집니다. 

사용자 지정 논리에서 계산된 전체 IP 주소를 유지하려면 제공한 IP 주소 데이터를 별도의 사용자 `ai.location.ip` 지정 필드에 복사하는 원격 분석 초기화자를 사용할 수 있습니다. 그러나 다시 서버 측 SDK와는 달리, 제 3 자 라이브러리 또는 자신의 사용자 정의 클라이언트 측 IP 수집 논리에 의존하지 않고 클라이언트 측 SDK는 당신을 위해 IP를 계산하지 않습니다.    


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

### <a name="view-the-results-of-your-telemetry-initializer"></a>원격 분석 초기화자의 결과 보기

그런 다음 사이트에 대해 새 트래픽을 트리거하고 수집할 시간이 있는지 확인하기 위해 약 2-5분 정도 기다린 경우 Kusto 쿼리를 실행하여 IP 주소 수집이 작동하는지 확인할 수 있습니다.

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

새로 수집된 IP 주소는 `customDimensions_client-ip` 열에 나타납니다. 기본 `client-ip` 열에는 구성 요소 수준에서 IP 주소 컬렉션을 구성한 방법에 따라 4개의 옥텟이 모두 비워지거나 처음 세 옥텟만 표시됩니다. 원격 분석 초기화자를 구현한 후 로컬에서 테스트하는 `customDimensions_client-ip` 경우 `::1` 이에 대한 값이 예상되는 동작입니다. `::1`은 IPv6의 루프백 주소를 나타냅니다. `127.0.01` IPv4와 동일하며 로컬 호스트에서 테스트할 때 표시되는 결과입니다.

## <a name="next-steps"></a>다음 단계

* 응용 프로그램 인사이트에서 [개인 데이터 수집에](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt) 대해 자세히 알아봅니다.

* 애플리케이션 인사이트에서 [IP 주소 수집의](https://apmtips.com/blog/2016/07/05/client-ip-address/) 작동 방식에 대해 자세히 알아보십시오. (이것은 우리의 엔지니어 중 하나에 의해 작성 된 이전 외부 블로그 게시물. IP 주소가 로 `0.0.0.0`기록되는 현재 기본 동작보다 먼저 사용하지만 기본 제공의 메커니즘에 `ClientIpHeaderTelemetryInitializer`대해 더 자세히 룹니다.)

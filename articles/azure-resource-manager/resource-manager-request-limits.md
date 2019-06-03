---
title: 요청 한도 및 제한 - Azure Resource Manager
description: 구독 한도에 도달할 때 Azure Resource Manager 요청에 제한을 사용하는 방법을 설명합니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: b382b9ae35d492b4c779b8f7ee360fb378d54e08
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399706"
---
# <a name="throttling-resource-manager-requests"></a>Resource Manager 요청 제한

각 Azure 구독 및 테넌트에 대해 Resource Manager는 시간당 최대 12,000개 읽기 요청과 1,200개 쓰기 요청을 허용합니다. 이러한 제한의 범위는 요청을 하는 보안 주체 ID 및 구독 ID나 테넌트 ID의 범위로 설정됩니다. 둘 이상의 보안 주체 ID가 요청을 하는 경우 구독 또는 테넌트 전체에 적용되는 제한이 시간당 12,000개/1,200개보다 커집니다.

요청은 구독 또는 테넌트에 적용됩니다. 구독 요청은 구독의 리소스 그룹을 검색 하는 등, 구독 ID 전달을 포함 하는입니다. 유효한 Azure 위치 검색 등의 테넌트 요청에는 구독 ID가 포함되지 않습니다.

이러한 한도는 각 Azure Resource Manager 인스턴스에 적용됩니다. 모든 Azure 지역에 여러 인스턴스가 있으며 Azure Resource Manager가 모든 Azure 지역에 배포됩니다.  따라서 사용자 요청이 일반적으로 다수의 많은 인스턴스에서 서비스되기 때문에 실제 한도는 이러한 한도보다 훨씬 더 높습니다.

애플리케이션 또는 스크립트가 이러한 한도에 도달하면 요청을 제한해야 합니다. 이 문서에서는 한도 도달 하기 전에 남은 요청을 확인 하는 방법 및 제한에 도달 하면 응답 하는 방법을 보여 줍니다.

한도에 도달하면 HTTP 상태 코드 **429 너무 많은 요청**이 표시됩니다.

Azure 리소스 그래프는 해당 작업에 대 한 요청 수를 제한합니다. 나머지 요청 및 제한에 도달 하면 응답 하는 방법을 확인 하려면이 문서의 단계는 리소스 그래프에도 적용 됩니다. 그러나 리소스 그래프는 자신의 제한 및 다시 설정 속도 설정합니다. 자세한 내용은 [Azure 리소스 그래프에서 제한](../governance/resource-graph/overview.md#throttling)합니다.

## <a name="remaining-requests"></a>나머지 요청
응답 헤더를 검사하여 나머지 요청 수를 확인할 수 있습니다. 읽기 요청 나머지 읽기 요청 수에 대 한 헤더에서 값을 반환합니다. 나머지 쓰기 요청 수에 대 한 값을 포함 하는 요청을 작성 합니다. 다음 표에서는 해당 값을 검사할 수 있는 응답 헤더를 설명합니다.

| 응답 헤더 | 설명 |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |구독에 범위가 지정된 나머지 읽기. 이 값은 읽기 작업에서 반환됩니다. |
| x-ms-ratelimit-remaining-subscription-writes |구독에 범위가 지정된 나머지 쓰기. 이 값은 쓰기 작업에서 반환됩니다. |
| x-ms-ratelimit-remaining-tenant-reads |테넌트에 범위가 지정된 나머지 읽기 |
| x-ms-ratelimit-remaining-tenant-writes |테넌트에 범위가 지정된 나머지 쓰기 |
| x-ms-ratelimit-remaining-subscription-resource-requests |구독에 범위가 지정된 나머지 리소스 종류 요청.<br /><br />이 헤더 값은 서비스에서 기본 제한을 재정의한 경우에만 반환됩니다. Resource Manager는 구독 읽기 또는 쓰기 대신 이 값을 추가합니다. |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |구독에 범위가 지정된 나머지 리소스 종류 컬렉션 요청.<br /><br />이 헤더 값은 서비스에서 기본 제한을 재정의한 경우에만 반환됩니다. 이 값은 나머지 컬렉션 요청 수를 제공합니다(리소스 나열). |
| x-ms-ratelimit-remaining-tenant-resource-requests |테넌트에 범위가 지정된 나머지 리소스 종류 요청.<br /><br />이 헤더는 서비스에서 기본 제한을 재정의한 경우에만 테넌트 수준의 요청에 대해서만 추가됩니다. Resource Manager는 테넌트 읽기 또는 쓰기 대신 이 값을 추가합니다. |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |테넌트에 범위가 지정된 나머지 리소스 종류 컬렉션 요청.<br /><br />이 헤더는 서비스에서 기본 제한을 재정의한 경우에만 테넌트 수준의 요청에 대해서만 추가됩니다. |

## <a name="retrieving-the-header-values"></a>헤더 값 검색
코드 또는 스크립트에서 이러한 헤더 값을 검색하는 것은 임의의 헤더 값을 검색하는 것과 같습니다. 

예를 들어 **C#** 에서는 다음 코드로 **response**로 명명된 **HttpWebResponse** 개체에서 헤더 값을 검색합니다.

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

**PowerShell**에서는 Invoke-WebRequest 작업에서 헤더 값을 검색합니다.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

전체 PowerShell 예제는 [구독에 대한 Resource Manager 제한 확인](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI)을 참조하세요.

디버깅을 위해 남은 요청 수를 확인하려는 경우 **PowerShell** cmdlet에 **-Debug** 매개 변수를 제공할 수 있습니다.

```powershell
Get-AzResourceGroup -Debug
```

그러면 다음 응답 값을 포함한 많은 값이 반환됩니다.

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

쓰기 제한을 가져오려면 쓰기 작업을 사용합니다. 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

그러면 다음 값을 포함하는 많은 값이 반환됩니다.

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

**Azure CLI**에서 더 많은 자세한 정보 표시 옵션을 사용하여 헤더 값을 검색합니다.

```azurecli
az group list --verbose --debug
```

그러면 다음 값을 포함하는 많은 값이 반환됩니다.

```azurecli
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

쓰기 제한을 가져오려면 쓰기 작업을 사용합니다. 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

그러면 다음 값을 포함하는 많은 값이 반환됩니다.

```azurecli
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="waiting-before-sending-next-request"></a>다음 요청을 보낼 때까지 대기
요청 제한에 도달하면 Resource Manager는 헤더에서 **429** HTTP 상태 코드 및 **Retry-After** 값을 반환합니다. **Retry-After** 값은 애플리케이션이 다음 요청을 보낼 때까지 대기(또는 절전)하는 시간(초)을 지정합니다. 재시도 값이 경과하기 전에 요청을 보내면 요청이 처리되지 않고 새 재시도 값이 반환됩니다.

## <a name="next-steps"></a>다음 단계

* 전체 PowerShell 예제는 [구독에 대한 Resource Manager 제한 확인](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI)을 참조하세요.
* 제한 및 할당량에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요.
* 비동기 REST 요청 처리에 대해 알아보려면 [Azure 비동기 작업 추적](resource-manager-async-operations.md)을 참조하세요.

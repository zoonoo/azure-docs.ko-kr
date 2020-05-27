---
title: Azure Event Grid 보안 및 인증
description: 이 문서에서는 Event Grid 리소스(웹후크, 구독, 사용자 지정 항목)에 대한 액세스를 인증하는 다양한 방법을 설명합니다.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: babanisa
ms.openlocfilehash: 71d47c83586f7e5e31b148714e2804686422326a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588261"
---
# <a name="authenticating-access-to-azure-event-grid-resources"></a>Azure Event Grid 리소스에 대한 액세스 인증
이 문서는 다음과 같은 시나리오에 대한 정보를 제공합니다.  

- SAS(공유 액세스 서명) 또는 키를 사용하여 Azure Event Grid 토픽에 이벤트를 게시하는 클라이언트를 인증합니다. 
- Azure Event Grid로부터 HTTP 엔드포인트로 이벤트 수신Azure AD(Azure Active Directory)를 사용하여 웹후크 엔드포인트의 보안을 설정하여 이벤트를 엔드포인트로 **전달**하도록 Event Grid를 인증합니다.

## <a name="authenticate-publishing-clients-using-sas-or-key"></a>SAS 또는 키를 사용하여 클라이언트 게시 인증
사용자 지정 토픽은 SAS(공유 액세스 서명) 또는 키 인증을 사용합니다. SAS를 사용하는 것이 좋지만 키 인증에서는 간단한 프로그래밍을 제공하며 기존의 많은 Webhook 게시자와 호환 가능합니다.

HTTP 헤더에서 인증 값을 포함합니다. SAS의 경우 헤더 값에 **aeg-sas-token**을 사용합니다. 키 인증의 경우 헤더 값에 **aeg-sas-key**를 사용합니다.

### <a name="key-authentication"></a>키 인증

키 인증은 인증의 가장 간단한 양식입니다. 메시지 헤더에 `aeg-sas-key: <your key>` 형식을 사용합니다.

예를 들어 다음을 사용하여 키를 전달합니다.

```
aeg-sas-key: XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

`aeg-sas-key`를 쿼리 매개 변수로 지정할 수도 있습니다. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01&&aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS 토큰

Event Grid의 SAS 토큰에는 리소스, 만료 시간 및 서명이 포함됩니다. SAS 토큰의 형식은 다음과 같습니다. `r={resource}&e={expiration}&s={signature}`

리소스는 이벤트를 전송할 Event Grid 항목의 경로입니다. 예를 들어 올바른 리소스 경로는 `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01`입니다. 지원되는 모든 API 버전을 보려면 [Microsoft.EventGrid 리소스 형식](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions)을 참조하세요. 

키에서 서명을 생성합니다.

예를 들어 유효한 **aeg-sas-token** 값은 다음과 같습니다.

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

다음 예제에서는 Event Grid를 사용하기 위한 SAS 토큰을 만듭니다.

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

### <a name="encryption-at-rest"></a>휴지 상태의 암호화

Event Grid 서비스에서 디스크에 기록하는 모든 이벤트 또는 데이터는 Microsoft에서 관리하는 키로 암호화되어 암호화된 상태로 유지됩니다. 또한 이벤트 또는 데이터를 보존하는 최대 기간은 [Event Grid 재시도 정책](delivery-and-retry.md)을 준수하는 24시간입니다. Event Grid는 24시간 후에 모든 이벤트 또는 데이터를 자동으로 삭제하거나, 더 적은 이벤트 Time-to-Live를 삭제합니다.

## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>웹후크 엔드포인트에 대한 이벤트 전달 인증
다음 섹션에서는 웹후크 엔드포인트에 대한 이벤트 전달을 인증하는 방법을 설명합니다. 사용하는 방법에 관계 없이 유효성 검사 핸드셰이크 메커니즘을 사용해야 합니다. 자세한 내용은 [웹후크 이벤트 전달](webhook-event-delivery.md)을 참조하세요. 

### <a name="using-azure-active-directory-azure-ad"></a>Azure AD(Azure Active Directory) 사용
Azure AD(Azure Active Directory)를 사용하여 웹후크 엔드포인트의 보안을 설정하여 엔드포인트에 이벤트를 전달하도록 Event Grid를 인증하고 권한을 부여할 수 있습니다. Azure AD 애플리케이션을 만들고 애플리케이션에서 Event Grid 권한을 부여하는 역할 및 서비스 주체를 만들고 Azure AD 애플리케이션을 사용하도록 이벤트 구독을 구성해야 합니다. [Azure Active Directory를 Event Grid로 구성하는 방법에 대해 알아봅니다](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>클라이언트 암호를 쿼리 매개 변수로 사용
이벤트 구독을 만들 때 Webhook URL에 쿼리 매개 변수를 추가하여 Webhook 엔드포인트를 보호할 수 있습니다. 이러한 쿼리 매개 변수 중 하나를 [액세스 토큰](https://en.wikipedia.org/wiki/Access_token)과 같은 클라이언트 암호 또는 공유 비밀로 설정합니다. 웹후크는 이 비밀을 사용하여 이벤트가 유효한 사용 권한이 있는 Event Grid에서 제공된다고 인식할 수 있습니다. Event Grid는 Webhook에 모든 이벤트 전달에서 해당 쿼리 매개 변수를 포함합니다. 클라이언트 암호를 업데이트하는 경우에는 이벤트 구독도 업데이트해야 합니다. 이 비밀 회전 중에 전달 오류가 발생하지 않도록 하려면 웹후크가 제한된 기간 동안 이전 및 새 비밀을 모두 수락하도록 설정합니다. 

쿼리 매개 변수는 클라이언트 암호를 포함할 수 있으므로 신중하게 처리됩니다. 암호화된 상태로 저장되며 서비스 운영자가 액세스할 수 없습니다. 또한 서비스 로그/추적의 일부로 기록되지 않습니다. 이벤트 구독을 편집할 때 [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) 매개 변수가 Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)에 사용되지 않는 한 쿼리 매개 변수는 표시되거나 반환되지 않습니다.

웹후크에 이벤트를 전달하는 방법에 대한 자세한 내용은 [웹후크 이벤트 전달](webhook-event-delivery.md)을 참조하세요.

> [!IMPORTANT]
Azure Event Grid는 **HTTPS** 웹후크 엔드포인트만 지원합니다. 

## <a name="next-steps"></a>다음 단계

- Event Grid에 대한 소개는 [Event Grid 정보](overview.md)를 참조하세요.

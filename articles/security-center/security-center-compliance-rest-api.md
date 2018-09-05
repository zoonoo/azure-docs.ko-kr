---
title: REST API를 사용하여 Azure Security Center 규정 준수 모니터링 | Microsoft Docs
description: Azure Security Center REST API를 사용하여 자동화된 규정 준수 검사 결과를 검토합니다.
services: security-center
documentationcenter: na
author: rloutlaw
manager: angerobe
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rloutlaw
ms.openlocfilehash: 651d7737258f1b1b17c8392a09882388ddf95635
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42819289"
---
# <a name="review-security-center-compliance-results-using-the-azure-rest-apis"></a>Azure REST API를 사용하여 Security Center 규정 준수 결과 검토

이 문서에서는 Azure REST API를 사용하여 구독 목록에 대한 보안 규정 준수 정보를 검색하는 방법을 알아봅니다.

## <a name="review-compliance-for-each-subscription"></a>각 구독에 대한 규정 준수 검토

아래 예제에서는 [규정 준수 가져오기](/rest/api/securitycenter/compliances/get) 작업을 사용하여 지정된 규정 준수 및 구독에 대한 보안 평가 정보를 가져옵니다.

```http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/compliances/{complianceName}?api-version=2017-08-01-preview
```

`{complianceName}` 매개 변수는 필수 항목이며, `{subscription-id}`에 대한 평가된 규정 준수의 이름이 포함되어야 합니다. 출력에는 다음과 같은 평가 결과가 포함됩니다.

```json
{
...
  "properties": {
    "assessmentResult": [
      {
        "segmentType": "Compliant",
        "percentage": 77.777777777777786
      }
    ],
    "resourceCount": 18,
    "assessmentTimestampUtcDate": "2018-01-01T00:00:00Z"
  }
}
```

## <a name="review-compliance-for-multiple-subscriptions"></a>여러 구독에 대한 규정 준수 검토

여러 구독에 대한 데이터를 가져오려면 [구독 나열하기](/rest/api/resources/subscriptions/list) 작업을 사용하여 먼저 구독 목록을 가져오기 위해 아래 호출을 수행합니다. 각 반환된 구독 ID에 대해 위의 [규정 준수 가져오기](/rest/api/securitycenter/compliances/get)를 호출합니다.

API 호출은 다음과 같습니다.

```http
GET https://management.azure.com/subscriptions?api-version=2016-06-01
```

다음과 같은 값의 배열을 반환합니다. 위의 호출의 subscriptionId 값을 사용하여 모든 구독에 대한 규정 준수 정보를 검토합니다.

```json
"value": [
    {
      "id": "/subscriptions/{subscription-id}",
      "subscriptionId": "{subscription-id}",
      "displayName": "Demo subscription",
      ...
    }
```







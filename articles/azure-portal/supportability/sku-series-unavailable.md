---
title: 지역 또는 SKU 계열 을 사용할 수 없음 - Azure
description: 일부 SKU 계열은 이 리전의 선택한 구독에 사용할 수 없습니다.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: b0f0762ded6804c0b0d90a19223c082f0fb8fd49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843641"
---
# <a name="region-or-sku-unavailable"></a>지역 또는 SKU 사용할 수 없음

이 문서에서는 Azure 구독에 지역 또는 VM SKU에 대한 액세스 권한이 없는 문제를 해결하는 방법을 설명합니다.

## <a name="symptoms"></a>증상

가상 머신을 배포할 때 다음 메시지 중 하나가 표시됩니다.

```
Code: SkuNotAvailable
Message: The requested size for resource '<resource>' is currently not available in location 
'<location>' zones '<zone>' for subscription '<subscriptionID>'. Please try another size or 
deploy to a different location or zones. See https://aka.ms/azureskunotavailable for details.
```

```
Message: Your subscription doesn’t support virtual machine creation in <location>. Choose a 
different location. Supported locations are <list of locations>
```

```
Code: NotAvailableForSubscription
Message: This size is currently unavailable in this location for this subscription
```

예약 가상 머신 인스턴스를 구입할 때 다음 메시지 중 하나가 표시됩니다.

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

계산 핵심 할당량을 늘리기 위한 지원 요청을 만들 때 지역 또는 SKU 제품군을 선택할 수 없습니다.

## <a name="solution"></a>해결 방법

먼저 비즈니스 요구를 충족하는 대체 지역 또는 SKU를 사용하는 것이 좋습니다.

적합한 지역 또는 SKU를 찾을 수 없는 경우 다음 단계에 따라 **구독 관리** [지원 요청을](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) 만듭니다.

1. Azure [포털](https://portal.azure.com) 메뉴에서 **도움말 + 지원을**선택합니다. 그런 다음, **새 지원 요청**을 선택합니다.

1. **기본**에서 **문제 유형에**대해 구독 관리를 **선택합니다.**

1. **구독을** 선택하고 요약 에 간략한 설명을 **입력합니다.**

   ![새 지원 요청의 기본 탭](./media/SKU-series-unavailable/support-request-basics.png)

1. **문제 유형의**경우 **문제 유형 선택을**선택합니다.

1. **문제 유형 선택에**대 한 옵션을 선택, 예를 들어, 내 구독 또는 **리소스내** > 문제에 액세스할 수 없습니다**내 문제는 위에 나열 되지 않습니다.** **저장**을 선택합니다.

   ![요청에 대한 문제 지정](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. **다음: 가능한 솔루션을** 탐색하는 솔루션을 선택합니다. 필요한 경우 **다음: 세부 정보를** 선택하여 계속합니다.

1. 연락처 정보와 함께 제공할 수 있는 추가 정보를 입력합니다.

1. **검토 + 만들기를**선택합니다. 정보를 확인한 후 **만들기를** 선택하여 요청을 만듭니다.

## <a name="send-us-your-suggestions"></a>우리에게 당신의 제안을 보내

우리는 항상 피드백과 제안에 열려 있습니다! [제안 사항](https://feedback.azure.com/forums/266794-support-feedback)을 보내 주세요. 또한 [트위터](https://twitter.com/azuresupport) 나 [MSDN 포럼에서](https://social.msdn.microsoft.com/Forums/azure)우리와 함께 참여할 수 있습니다.

## <a name="learn-more"></a>자세한 정보

[Azure 지원 FAQ](https://azure.microsoft.com/support/faq)

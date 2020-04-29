---
title: 지역 또는 SKU 시리즈를 사용할 수 없음-Azure
description: 이 지역의 선택한 구독에 대해 일부 SKU 시리즈를 사용할 수 없습니다. 구독 관리 지원 요청이 필요할 수 있습니다.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: b0f0762ded6804c0b0d90a19223c082f0fb8fd49
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
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

계산 코어 할당량을 늘리기 위한 지원 요청을 만들 때 지역 또는 SKU 패밀리를 선택할 수 없습니다.

## <a name="solution"></a>솔루션

먼저 비즈니스 요구를 충족하는 대체 지역 또는 SKU를 사용하는 것이 좋습니다.

적합 한 지역 또는 SKU를 찾을 수 없는 경우 다음 단계에 따라 **구독 관리** [지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) 을 만듭니다.

1. [Azure Portal](https://portal.azure.com) 메뉴에서 **도움말 + 지원**을 선택 합니다. 그런 다음, **새 지원 요청**을 선택합니다.

1. **기본 사항**에서 **문제 유형**에 대해 **구독 관리**를 선택 합니다.

1. **구독** 을 선택 하 고 **요약**에 간략 한 설명을 입력 합니다.

   ![새 지원 요청의 기본 사항 탭](./media/SKU-series-unavailable/support-request-basics.png)

1. **문제 유형**에서 **문제 유형 선택**을 선택 합니다.

1. **문제 유형 선택**에 대 한 옵션을 선택 합니다. 예를 들어 >  **내 구독 또는 리소스에 액세스할 수 없습니다**.**문제가 위에 나열 되어 있지 않습니다**. **저장**을 선택합니다.

   ![요청에 대 한 문제 지정](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. **다음: 솔루션** 을 선택 하 여 가능한 해결 방법을 탐색 합니다. 필요한 경우 **다음: 세부 정보** 를 선택 하 여 계속 합니다.

1. 연락처 정보와 함께 제공할 수 있는 추가 정보를 입력 합니다.

1. **검토 + 만들기**를 선택합니다. 정보를 확인 한 후 **만들기** 를 선택 하 여 요청을 만듭니다.

## <a name="send-us-your-suggestions"></a>사용자 의견 보내기

항상 사용자 의견 및 제안 사항에 대 한 의견을 공개 하겠습니다. [제안 사항](https://feedback.azure.com/forums/266794-support-feedback)을 보내 주세요. 또한 [Twitter](https://twitter.com/azuresupport) 또는 [MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure)에서 참여할 수 있습니다.

## <a name="learn-more"></a>자세한 정보

[Azure 지원 FAQ](https://azure.microsoft.com/support/faq)

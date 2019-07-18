---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: ad1527a5e7f1cb2ff1beb9ddace5460f41bb8a87
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461503"
---
## <a name="azure-cognitive-service-subscription-types"></a>Azure Cognitive Service 구독 유형

> [!NOTE]
> 구독 소유자는 [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition)을 적용하고, “허용되는 리소스 종류 없음” 정책 정의를 할당하고, **Microsoft.CognitiveServices/accounts**를 대상 리소스 종류로 지정하여 리소스 그룹 및 구독의 Cognitive Services 계정을 만들지 못하게 할 수 있습니다.

Azure Cognitive Services는 두 개의 서로 다른 구독을 통해 액세스할 수 있습니다. 다중 서비스 구독 또는 하나는 단일 서비스입니다. 이러한 구독을 통해 단일 서비스 또는 여러 서비스를 한 번에 연결할 수 있습니다.

### <a name="multi-service-subscription"></a>다중 서비스 구독

>[!WARNING]
> 이 경우 이러한 서비스는 다음과 같은 다중 서비스 키를 지원하지 **않습니다**. QnA Maker, 음성 서비스, 사용자 지정 비전 및 이상 감지기

대부분의 Azure Cognitive Services에 대 한 단일 구독 및 Azure 리소스를 사용할 수 있습니다 하 고 사용 하는 서비스에서 청구를 통합 하는 Azure Cognitive Services에 대 한 다중 서비스 구독입니다. 자세한 정보는 [Cognitive Services 가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/)을 참조하세요.

### <a name="single-service-subscription"></a>단일 서비스 구독

Computer Vision 또는 음성 서비스와 같은 단일 서비스를 구독 합니다. 단일 서비스 구독은 해당 리소스로 제한됩니다. 

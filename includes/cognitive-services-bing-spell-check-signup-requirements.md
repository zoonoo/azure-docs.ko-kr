---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 05/19/2020
ms.openlocfilehash: 13fb00a7d82639c8f51744b6c2e5374d08d9bb03
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83869812"
---
## <a name="create-an-azure-resource"></a>Azure 리소스 만들기

다음 Azure 리소스 중 하나를 만들어 Bing Spell Check API 사용을 시작합니다.

[평가판 리소스](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api-v7)
   * Azure 구독이 필요하지 않습니다.
   * 평가판은 7일 동안 유효합니다. 등록 후 [Azure 웹 사이트](https://azure.microsoft.com/try/cognitive-services/my-apis/)에서 평가판 키 및 엔드포인트를 사용할 수 있습니다.

[Bing Spell Check 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)
   * 리소스를 삭제할 때까지 Azure Portal을 통해 사용할 수 있습니다.
   * 평가판 가격 책정 계층을 사용하여 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드합니다.
   * Bing Spell Check API는 [Bing Search v7 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7)의 일부 계층에서도 제공됩니다.
    
[다중 서비스 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)
   * 리소스를 삭제할 때까지 Azure Portal을 통해 사용할 수 있습니다.  
   * 여러 Cognitive Services에서 애플리케이션에 동일한 키와 엔드포인트를 사용합니다.

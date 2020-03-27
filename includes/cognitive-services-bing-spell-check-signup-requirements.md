---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 12/16/2019
ms.openlocfilehash: 7c7e6fb7cc99a155c4e89ae930943f79a725beb3
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "75382859"
---
## <a name="create-an-azure-resource"></a>Azure 리소스 만들기

아래 Azure 리소스 중 하나를 만들어 Bing Spell Check API 사용을 시작합니다.

* [평가판 리소스 만들기](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api-v7):
    * Azure 구독이 필요하지 않습니다.
    * 평가판은 7일 동안 유효합니다. 등록 후 [Azure 웹 사이트](https://azure.microsoft.com/try/cognitive-services/my-apis/)에서 평가판 키 및 엔드포인트를 사용할 수 있습니다.

* [Bing Spell Check 리소스 만들기](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7):
    * 리소스를 삭제할 때까지 Azure Portal을 통해 사용할 수 있습니다.
    * 평가판 가격 책정 계층을 사용하여 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드합니다.
    * Bing Spell Check API는 [Bing Search v7 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7)의 일부 계층에서도 제공됩니다.
    
* [다중 서비스 리소스 만들기](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne):
    * 리소스를 삭제할 때까지 Azure Portal을 통해 사용할 수 있습니다.  
    * 여러 Cognitive Services에서 애플리케이션에 동일한 키와 엔드포인트를 사용합니다.

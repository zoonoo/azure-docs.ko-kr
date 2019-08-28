---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 674dd30ff3e493ec4c4036f032f82624a6ca5749
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334239"
---
## <a name="azure-cognitive-service-resource-types"></a>Azure 인식 서비스 리소스 유형

> [!NOTE]
> 구독 소유자는 [Azure 정책을](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition)적용 하 고 "허용 되지 않는 리소스 종류" 정책 정의를 할당 하 고를 지정 **하 여 리소스 그룹 및 구독에 대 한 Cognitive Services 리소스 생성을 사용 하지 않도록 설정할 수 있습니다. Cognitiveservices account/accounts** 를 대상 리소스 유형으로 입력 합니다.

서로 다른 두 리소스를 통해 Azure Cognitive Services에 액세스할 수 있습니다. 다중 서비스 리소스 또는 단일 서비스 리소스입니다. 이러한 구독을 통해 한 번에 단일 서비스 또는 여러 서비스에 연결할 수 있습니다.

### <a name="multi-service-resource"></a>다중 서비스 리소스

>[!WARNING]
> 이 경우 이러한 서비스는 다음과 같은 다중 서비스 키를 지원하지 **않습니다**. QnA Maker, Speech Services, Custom Vision 및 변칙 탐지기가 있습니다.

다중 서비스 Cognitive Services 리소스 구독:
* 대부분의 Azure Cognitive Services에 단일 Azure 리소스를 사용할 수 있습니다.
* 는 사용 하는 서비스에서 청구를 통합 합니다. 자세한 정보는 [Cognitive Services 가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/)을 참조하세요.

### <a name="single-service-resource"></a>단일 서비스 리소스

단일 서비스 리소스 (예: Computer Vision 또는 Speech Services)는 지정 된 서비스로 제한 됩니다.
---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 3b808f4eb853cf05eb08cd1acf08dedccabf71a6
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274607"
---
## <a name="azure-cognitive-service-resource-types"></a>Azure 인식 서비스 리소스 유형

<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
서로 다른 두 리소스를 통해 Azure Cognitive Services에 액세스할 수 있습니다. 다중 서비스 리소스 또는 단일 서비스 리소스입니다. 이러한 구독을 통해 한 번에 단일 인지 서비스 또는 여러 인식 서비스에 연결할 수 있습니다.

### <a name="multi-service-resource"></a>다중 서비스 리소스

다중 서비스 Cognitive Services 리소스 구독:
* 대부분의 Azure Cognitive Services에 단일 Azure 리소스를 사용할 수 있습니다.
* 여러 Azure Cognitive Services에서 사용할 수 있는 단일 키를 가져옵니다.
* 는 사용 하는 서비스에서 청구를 통합 합니다. 자세한 정보는 [Cognitive Services 가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/)을 참조하세요.

>[!WARNING]
> 이 경우 이러한 서비스는 다음과 같은 다중 서비스 키를 지원하지 **않습니다**. QnA Maker, Speech Services, Custom Vision 및 변칙 탐지기가 있습니다.

### <a name="single-service-resource"></a>단일 서비스 리소스

단일 서비스 Cognitive Services 리소스 구독:
* 리소스를 만든 지정 된 인식 서비스 (예: Computer Vision 또는 Speech Services)를 사용할 수 있습니다.
* 리소스를 만드는 인식 서비스에 해당 하는 키를 가져옵니다.
---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: aedfe8783beacfe2e6679848ef4c2defa24d2da0
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557511"
---
<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](../articles/governance/policy/overview.md#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
다음과 같이 서로 다른 두 리소스를 통해 Azure Cognitive Services에 액세스할 수 있습니다. 다중 서비스 리소스 또는 단일 서비스 리소스

* 다중 서비스 리소스
    * 단일 키와 엔드포인트로 여러 Azure Cognitive Services에 액세스합니다.
    * 사용하는 서비스의 청구를 통합합니다.
* 단일 서비스 리소스
    * 만든 각 서비스에 대해 고유한 키와 엔드포인트를 사용하여 단일 Azure Cognitive Service에 액세스합니다. 
    * 무료 계층을 사용하여 서비스를 사용해 봅니다.
---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: d5ed36700e2aea623fb259816e5baddff5215361
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "70381749"
---
<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
다음과 같이 서로 다른 두 리소스를 통해 Azure Cognitive Services에 액세스할 수 있습니다. 다중 서비스 리소스 또는 단일 서비스 리소스

* 다중 서비스 리소스
    * 단일 키와 엔드포인트로 여러 Azure Cognitive Services에 액세스합니다.
    * 사용하는 서비스의 청구를 통합합니다.
* 단일 서비스 리소스
    * 만든 각 서비스에 대해 고유한 키와 엔드포인트를 사용하여 단일 Azure Cognitive Service에 액세스합니다. 
    * 무료 계층을 사용하여 서비스를 사용해 봅니다.   

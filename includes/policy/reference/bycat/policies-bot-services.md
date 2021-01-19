---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d276774ab21563f584d03056ce670a6d01398908
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98047516"
---
|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Bot Service 엔드포인트는 유효한 HTTPS URI여야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |데이터는 전송 중에 변조될 수 있습니다. 암호화를 제공하여 오용 및 변조 문제를 해결하는 프로토콜이 있습니다. 봇이 암호화된 채널을 통해서만 통신하도록 하려면 엔드포인트를 유효한 HTTPS URI로 설정합니다. 이렇게 하면 전송 중인 데이터를 암호화하는 데 HTTPS 프로토콜을 사용하고 규정 또는 업계 표준을 준수해야 하는 경우가 많습니다. [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines)를 방문하세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Services/BotService_ValidEndpoint_Audit.json) |

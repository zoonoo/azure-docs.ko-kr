---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4b71ec6158b50e3e95bc858b2f300406804fdfd6
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611065"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Bot Service 엔드포인트는 유효한 HTTPS URI여야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |데이터는 전송 중에 변조될 수 있습니다. 암호화를 제공하여 오용 및 변조 문제를 해결하는 프로토콜이 있습니다. 봇이 암호화된 채널을 통해서만 통신하도록 하려면 엔드포인트를 유효한 HTTPS URI로 설정합니다. 이렇게 하면 전송 중인 데이터를 암호화하는 데 HTTPS 프로토콜을 사용하고 규정 또는 업계 표준을 준수해야 하는 경우가 많습니다. [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines)를 방문하세요. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_ValidEndpoint_Audit.json) |
|[Bot Service는 고객 관리형 키로 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F51522a96-0869-4791-82f3-981000c2c67f) |Azure Bot Service는 리소스를 자동으로 암호화하여 데이터를 보호하고 조직의 보안 및 규정 준수 약정을 충족합니다. 기본적으로 Microsoft 관리형 암호화 키가 사용됩니다. 키 관리 또는 구독에 대한 액세스 제어의 유연성을 향상하려면 BYOK(Bring Your Own Key)라고도 하는 고객 관리형 키를 선택합니다. Azure Bot Service 암호화에 대한 자세한 정보: [https://docs.microsoft.com/azure/bot-service/bot-service-encryption](https://docs.microsoft.com/azure/bot-service/bot-service-encryption). |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_CMKEnabled_Audit.json) |

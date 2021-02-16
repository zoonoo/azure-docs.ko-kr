---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ed6c120bb5768ffe1fb0b660ae0346e278dc4746
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100100567"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Service Bus 네임스페이스에서 RootManageSharedAccessKey를 제외한 모든 인증 규칙을 제거해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1817ec0-a368-432a-8057-8371e17ac6ee) |Service Bus 클라이언트는 네임스페이스의 모든 큐 및 토픽에 대한 액세스를 제공하는 네임스페이스 수준 액세스 정책을 사용하지 않아야 합니다. 최소 권한 보안 모델에 맞추려면 큐 및 토픽에 대한 엔터티 수준의 액세스 정책을 만들어 특정 엔터티에만 액세스를 제공해야 합니다. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json) |
|[Service Bus에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[Service Bus 프리미엄 네임스페이스는 암호화에 고객 관리형 키를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F295fc8b1-dc9f-4f53-9c61-3f313ceab40a) |Azure Service Bus는 Microsoft 관리형 키(기본값) 또는 고객 관리형 키를 사용하여 미사용 데이터를 암호화하는 옵션을 지원합니다. 고객 관리형 키를 사용하여 데이터를 암호화하도록 선택하면 Service Bus가 네임스페이스의 데이터를 암호화하는 데 사용할 키에 대한 액세스 권한을 할당, 회전, 비활성화 및 취소할 수 있습니다. Service Bus는 프리미엄 네임스페이스에 대해 고객 관리형 키를 사용한 암호화만 지원합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_CustomerManagedKeyEnabled_Audit.json) |

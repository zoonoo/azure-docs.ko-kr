---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3ec3340f01e0d4984687fc258e652622552a710a
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989266"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[RootManageSharedAccessKey를 제외한 모든 권한 부여 규칙을 이벤트 허브 네임스페이스에서 제거해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb278e460-7cfc-4451-8294-cccc40a940d7) |이벤트 허브 클라이언트는 네임스페이스의 모든 큐 및 토픽에 대한 액세스를 제공하는 네임스페이스 수준 액세스 정책을 사용하지 않아야 합니다. 최소 권한 보안 모델에 맞추려면 큐 및 토픽에 대한 엔터티 수준의 액세스 정책을 만들어 특정 엔터티에만 액세스를 제공해야 합니다. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditNamespaceAccessRules_Audit.json) |
|[이벤트 허브 인스턴스의 권한 부여 규칙을 정의해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4826e5f-6a27-407c-ae3e-9582eb39891d) |최소 권한 액세스를 부여하기 위해 이벤트 허브 엔터티 권한 부여 규칙의 존재를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditEventHubAccessRules_Audit.json) |
|[이벤트 허브의 진단 설정을 이벤트 허브에 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef7b61ef-b8e4-4c91-8e78-6946c6b0023f) |이 진단 설정이 누락된 이벤트 허브를 만들거나 업데이트할 때 이벤트 허브의 진단 설정을 배포하여 지역별 이벤트 허브로 스트리밍합니다. |DeployIfNotExists, 사용 안 함 |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/EventHub_DeployDiagnosticLog_Deploy_EventHub.json) |
|[이벤트 허브의 진단 설정을 Log Analytics 작업 영역에 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f6e93e8-6b31-41b1-83f6-36e449a42579) |이 진단 설정이 누락된 이벤트 허브를 만들거나 업데이트할 때 이벤트 허브의 진단 설정을 배포하여 지역별 Log Analytics 작업 영역으로 스트리밍합니다. |DeployIfNotExists, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/EventHub_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[Event Hub의 진단 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |진단 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[이벤트 허브는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 이벤트 허브를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |

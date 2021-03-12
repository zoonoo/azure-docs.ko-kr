---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 023f7c20c2417f3c3f090dfa4dfee4a493b4618e
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102615325"
---
|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Cognitive Search 서비스는 개인 링크를 지 원하는 SKU를 사용 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa049bf77-880b-470f-ba6d-9f21c530cf83) |Azure Cognitive Search의 지원 되는 Sku를 사용 하면 Azure 개인 링크를 사용 하 여 원본 또는 대상에 공용 IP 주소 없이 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 전용 끝점을 검색 서비스에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://aka.ms/azure-cognitive-search/inbound-private-endpoints](https://aka.ms/azure-cognitive-search/inbound-private-endpoints)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePrivateLinkSupportedResource_Deny.json) |
|[Azure Cognitive Search 서비스는 공용 네트워크 액세스를 사용 하지 않도록 설정 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee980b6d-0eca-4501-8d54-f6290fd512c3) |공용 네트워크 액세스를 사용 하지 않도록 설정 하면 Azure Cognitive Search 서비스가 공용 인터넷에 노출 되지 않도록 하 여 보안이 향상 됩니다. 개인 끝점을 만들면 검색 서비스의 노출을 제한할 수 있습니다. [https://aka.ms/azure-cognitive-search/inbound-private-endpoints](https://aka.ms/azure-cognitive-search/inbound-private-endpoints)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePublicNetworkAccessDisabled_Deny.json) |
|[Azure Cognitive Search 서비스를 구성 하 여 공용 네트워크 액세스 사용 안 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9cee519f-d9c1-4fd9-9f79-24ec3449ed30) |공용 인터넷을 통해 액세스할 수 없도록 Azure Cognitive Search 서비스에 대 한 공용 네트워크 액세스를 사용 하지 않도록 설정 합니다. 이로 인해 데이터 누출 위험이 줄어들 수 있습니다. [https://aka.ms/azure-cognitive-search/inbound-private-endpoints](https://aka.ms/azure-cognitive-search/inbound-private-endpoints)에서 자세히 알아보세요. |수정, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_PublicNetworkAccessDisabled_Modify.json) |
|[Search Services의 진단 설정을 이벤트 허브에 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d5da587-71bd-41f5-ac95-dd3330c2d58d) |이 진단 설정이 누락된 Search Services를 만들거나 업데이트할 때 Search Services의 진단 설정을 배포하여 지역별 이벤트 허브로 스트리밍합니다. |DeployIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Search_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Search Services의 진단 설정을 Log Analytics 작업 영역에 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08ba64b8-738f-4918-9686-730d2ed79c7d) |이 진단 설정이 누락된 Search Services를 만들거나 업데이트할 때 Search Services의 진단 설정을 배포하여 지역별 Log Analytics 작업 영역으로 스트리밍합니다. |DeployIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Search_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[Search Service의 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |

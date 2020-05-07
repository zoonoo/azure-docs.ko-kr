---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/05/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a44755805d28d4bc1edee6057af9c0c678cfccf9
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836474"
---
|속성 |Description |효과 |버전 |GitHub |
|---|---|---|---|---|
|[Azure Cosmos DB 허용되는 위치](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0473574d-2d43-4217-aefe-941fcdf7e684) |이 정책을 사용하면 Azure Cosmos DB 조직에서 리소스를 배포할 때 지정할 수 있는 위치를 제한할 수 있습니다. 지역 규정 준수 요구 사항을 적용하는 데 사용합니다. |[parameters('policyEffect')] |1.0.0 |[링크](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_Locations_Deny.json) |
|[Cosmos DB는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 Cosmos DB를 감사합니다. |감사, 사용 안 함 |1.0.0 |[링크](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Cosmos DB 계정에 대한 Advanced Threat Protection 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb5f04e03-92a3-4b09-9410-2cc5e5047656) |이 정책은 Cosmos DB 계정에서 Advanced Threat Protection을 사용하도록 설정합니다. |DeployIfNotExists, 사용 안 함 |1.0.0 |[링크](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/CosmosDbAdvancedThreatProtection_Deploy.json) |

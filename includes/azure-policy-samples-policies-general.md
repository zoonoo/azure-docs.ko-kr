---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 8319ec2bf3965ee30db3e7d4ba1346bd7a3dd7d4
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169889"
---
|속성 |Description |효과 |버전 |
|---|---|---|---|
|[허용되는 위치](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/AllowedLocations_Deny.json) |이 정책을 사용하면 조직에서 리소스를 배포할 때 지정할 수 있는 위치를 제한할 수 있습니다. 지역 규정 준수 요구 사항을 적용하는 데 사용합니다. 리소스 그룹, Microsoft.AzureActiveDirectory/b2cDirectories 및 '글로벌' 지역을 사용하는 리소스를 제외합니다. |deny |1.0.0 |
|[리소스 그룹에 허용된 위치](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/ResourceGroupAllowedLocations_Deny.json) |이 정책을 통해 조직에서 리소스 그룹을 만들 수 있는 위치를 제한할 수 있습니다. 지역 규정 준수 요구 사항을 적용하는 데 사용합니다. |deny |1.0.0 |
|[허용되는 리소스 유형](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/AllowedResourceTypes_Deny.json) |이 정책을 사용하면 조직에서 배포할 수 있는 리소스 유형을 지정할 수 있습니다. '태그' 및 '위치'를 지원하는 리소스 유형만 이 정책의 영향을 받습니다. 모든 리소스를 제한하려면 이 정책을 복제하고 '모드'를 '모두'로 변경하세요. |deny |1.0.0 |
|[리소스 위치가 리소스 그룹 위치와 일치하는지 감사](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/ResourcesInResourceGroupLocation_Audit.json) |리소스 위치가 리소스 그룹 위치와 일치하는지 감사 |감사 |1.0.0 |
|[사용자 지정 RBAC 규칙 사용 감사](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |오류가 발생하기 쉬운 사용자 지정 RBAC 역할 대신 '소유자, 기여자, 읽기 권한자' 같은 기본 제공 역할을 감사합니다. 사용자 지정 역할 사용은 예외로 처리되며 엄격한 검토 및 위협 모델링이 필요합니다. |감사, 사용 안 함 |1.0.0 |
|[사용자 지정 구독 소유자 역할이 없어야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |이 정책은 사용자 지정 구독 소유자 역할이 없도록 합니다. |감사, 사용 안 함 |1.0.0 |
|[허용되지 않는 리소스 유형](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/InvalidResourceTypes_Deny.json) |이 정책을 사용하면 조직에서 배포할 수 없는 리소스 유형을 지정할 수 있습니다. |거부 |1.0.0 |

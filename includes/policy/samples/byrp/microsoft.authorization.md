---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/29/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7c377655e1ff08b2d90b18cdb9b99a61fce3a936
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235316"
---
|속성 |Description |효과 |버전 |GitHub |
|---|---|---|---|---|
|[사용자 지정 RBAC 규칙 사용 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |오류가 발생하기 쉬운 사용자 지정 RBAC 역할 대신 '소유자, 기여자, 읽기 권한자' 같은 기본 제공 역할을 감사합니다. 사용자 지정 역할 사용은 예외로 처리되며 엄격한 검토 및 위협 모델링이 필요합니다. |감사, 사용 안 함 |1.0.0 |[링크](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[사용자 지정 구독 소유자 역할이 없어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |이 정책은 사용자 지정 구독 소유자 역할이 없도록 합니다. |감사, 사용 안 함 |1.0.0 |[링크](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

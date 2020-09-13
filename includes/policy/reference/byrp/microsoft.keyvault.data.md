---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6818a7833f9a62254677716a6d491e71f8984e8c
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90022142"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[지정 된 통합 인증 기관에서 인증서를 발급 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8e826246-c976-48f6-b03e-619bb92b3d82) |Digicert 또는 GlobalSign과 같은 주요 자격 증명 모음에서 인증서를 발급할 수 있는 Azure 통합 인증 기관을 지정 하 여 조직의 규정 준수 요구 사항을 관리 합니다. |감사, 거부, 사용 안 함 |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_Issuers_SupportedCAs.json) |
|[지정 된 비 통합 인증 기관에서 인증서를 발급 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa22f4a40-01d3-4c7d-8071-da157eeff341) |주요 자격 증명 모음에서 인증서를 발급할 수 있는 사용자 지정 또는 내부 인증 기관을 지정 하 여 조직의 규정 준수 요구 사항을 관리 합니다. |감사, 거부, 사용 안 함 |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_Issuers_CustomCAs.json) |
|[인증서에 지정 된 수명 작업 트리거가 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12ef42cb-9903-4e39-9c26-422d29570417) |인증서 수명 동작이 해당 수명의 특정 비율 또는 만료 전 특정 기간 (일) 동안 트리거되는지 여부를 지정 하 여 조직의 규정 준수 요구 사항을 관리 합니다. |감사, 거부, 사용 안 함 |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_LifetimeAction.json) |
|[인증서의 최대 유효 기간을 지정 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a075868-4c26-42ef-914c-5bc007359560) |키 자격 증명 모음 내에서 인증서를 유효 하 게 사용할 수 있는 최대 시간을 지정 하 여 조직의 규정 준수 요구 사항을 관리 합니다. |감사, 거부, 사용 안 함 |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_ValidityPeriod.json) |
|[지정 된 일 수 내에 인증서가 만료 되지 않아야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff772fb64-8e40-40ad-87bc-7706e1949427) |지정 된 기간 (일) 내에 만료 되는 인증서를 관리 하 여 조직에서 만료 되기 전에 인증서를 회전 하는 데 충분 한 시간을 갖도록 합니다. |감사, 거부, 사용 안 함 |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_Expiry_ByDays.json) |
|[인증서는 허용 되는 키 유형을 사용 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1151cede-290b-4ba0-8b38-0ad145ac888f) |인증서에 허용 되는 키 유형을 제한 하 여 조직의 규정 준수 요구 사항을 관리 합니다. |감사, 거부, 사용 안 함 |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_AllowedKeyTypes.json) |
|[타원 curve 암호화를 사용 하는 인증서에는 곡선 이름이 허용 되어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd78111f-4953-4367-9fd5-7e08808b54bf) |키 자격 증명 모음에 저장 된 ECC 인증서에 대해 허용 되는 타원 곡선 이름을 관리 합니다. 자세한 내용은에서 찾을 수 있습니다 [https://aka.ms/akvpolicy](https://aka.ms/akvpolicy) . |감사, 거부, 사용 안 함 |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_EC_AllowedCurveNames.json) |
|[RSA 암호화를 사용 하는 인증서는 지정 된 최소 키 크기를 가져야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcee51871-e572-4576-855c-047c820360f0) |주요 자격 증명 모음에 저장 된 RSA 인증서의 최소 키 크기를 지정 하 여 조직의 규정 준수 요구 사항을 관리 합니다. |감사, 거부, 사용 안 함 |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_RSA_MinimumKeySize.json) |

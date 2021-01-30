---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 11e14a435f6ea33cf9f4f8a7dd3804c95187c9bf
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99106261"
---
## <a name="cmmc-level-3"></a>CMMC Level 3

모든 Azure 서비스에 대해 사용할 수 있는 Azure Policy 기본 제공이이 준수 표준에 매핑되는 방식을 검토 하려면 [Azure Policy 규정 준수-CMMC 수준 3](../../../../articles/governance/policy/samples/cmmc-l3.md)을 참조 하세요.
이 규정 준수 표준에 대 한 자세한 내용은 [CMMC (사이버 보안 성숙도 모델 인증)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf)를 참조 하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Access Control |1.001 |권한 있는 사용자에 대 한 정보 시스템 액세스, 권한 있는 사용자를 대신 하 여 작동 하는 프로세스, 장치 (기타 정보 시스템 포함)를 제한 합니다. |[CORS에서 모든 도메인이 API for FHIR에 액세스하도록 허용하지 않아야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Access Control |1.002 |권한 있는 사용자가 실행할 수 있는 트랜잭션 유형 및 기능에 대 한 정보 시스템 액세스를 제한 합니다. |[CORS에서 모든 도메인이 API for FHIR에 액세스하도록 허용하지 않아야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|Access Control |2.016 |승인된 권한 부여에 따라 CUI의 흐름을 제어합니다. |[CORS에서 모든 도메인이 API for FHIR에 액세스하도록 허용하지 않아야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|구성 관리 |3.068 |불필요한 프로그램, 함수, 포트, 프로토콜 및 서비스의 사용을 제한, 사용 안 함 또는 차단합니다. |[CORS에서 모든 도메인이 API for FHIR에 액세스하도록 허용하지 않아야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
|시스템 및 통신 보호 |3.177 |UI의 기밀성을 보호 하기 위해 사용 하는 경우 FIPS 유효성이 검사 된 암호화를 사용 합니다. |[Azure API for FHIR은 CMK(고객 관리형 키)를 사용하여 미사용 데이터를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|시스템 및 통신 보호 |3.183 |기본적으로 네트워크 통신 트래픽을 거부 하 고 예외 (즉, 모두 거부, 예외로 허용)로 네트워크 통신 트래픽을 허용 합니다. |[CORS에서 모든 도메인이 API for FHIR에 액세스하도록 허용하지 않아야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |


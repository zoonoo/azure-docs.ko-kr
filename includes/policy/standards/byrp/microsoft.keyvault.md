---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 53eb15990322a768dcfeb803203c1c6b0f8a4522
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92308483"
---
## <a name="azure-security-benchmark"></a>Azure 보안 벤치마크

[Azure Security Benchmark](../../../../articles/security/benchmarks/overview.md)는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 이 서비스가 Azure Security Benchmark에 완전히 매핑되는 방법을 보려면 [Azure Security Benchmark 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

모든 Azure 서비스에 사용 가능한 Azure Policy 기본 제공이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|네트워크 보안 |1.1 |Virtual Network에서 네트워크 보안 그룹 또는 Azure Firewall을 사용하여 리소스 보호 |[Key Vault는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|로깅 및 모니터링 |2.3 |Azure 리소스에 대한 감사 로깅 사용 |[Key Vault의 진단 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|보안 구성 |7.11 |안전하게 Azure 비밀 관리 |[Key Vault 개체를 복구할 수 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
|데이터 복구 |9.4 |백업 및 고객 관리형 키 보호 보장 |[Key Vault 개체를 복구할 수 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark"></a>CIS Microsoft Azure Foundations 벤치마크

모든 Azure 서비스에 사용 가능한 Azure Policy 기본 제공이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - CIS Microsoft Azure Foundations Benchmark 1.1.0](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md)을 참조하세요.
이러한 규정 준수 표준에 대한 자세한 내용은 [CIS Microsoft Azure Foundations 벤치마크](https://www.cisecurity.org/benchmark/azure/)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|로깅 및 모니터링 |5.1.7 |Azure KeyVault에 대한 로깅이 '사용'으로 설정되어 있는지 확인 |[Key Vault의 진단 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|기타 보안 고려 사항 |8.4 |키 자격 증명 모음을 복구할 수 있는지 확인 |[Key Vault 개체를 복구할 수 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

모든 Azure 서비스에 사용 가능한 Azure Policy 기본 제공이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md)를 참조하세요.
이 규정 준수 표준에 대한 자세한 내용은 [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|네트워크 분리 |0805.01m1Organizational.12 - 01.m |조직의 보안 게이트웨이(예: 방화벽)는 보안 정책을 적용하고, 도메인 간의 트래픽을 필터링하고 무단 액세스를 차단하도록 구성되며, DMZ를 포함하여 내부 유선, 내부 무선 및 외부 네트워크 세그먼트(예: 인터넷) 간의 분리를 유지하고 각 도메인에 대한 액세스 제어 정책을 적용하는 데 사용됩니다. |[Key Vault는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|네트워크 분리 |0806.01m2Organizational.12356 - 01.m |조직 네트워크는 조직 요구 사항에 따라 내부 네트워크와 논리적으로 분리된 공개적으로 액세스할 수 있는 시스템 구성 요소에 대한 하위 네트워크를 포함하여 정의된 보안 경계 및 분할된 컨트롤 세트를 사용하여 논리적 및 물리적으로 세분화됩니다. 그리고 트래픽은 필요한 기능과 위험 평가 및 해당 보안 요구 사항을 기반으로 하는 데이터/시스템의 분류에 따라 제어됩니다. |[Key Vault는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|네트워크 분리 |0894.01m2Organizational.7 - 01.m |네트워크는 물리적 서버, 애플리케이션 또는 데이터를 가상화된 서버로 마이그레이션할 때 프로덕션 수준 네트워크에서 분리됩니다. |[Key Vault는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|감사 로깅 |1211.09aa3System.4 - 09.aa |조직은 데이터를 지우거나 사용이 여전히 필요한 것으로 기록된 적용 정보의 각 추출에 대해 90일마다 확인합니다. |[Key Vault의 진단 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|네트워크 제어 |0865.09m2Organizational.13 - 09.m |조직은 (i) 상호 연결 보안 계약 또는 기타 정식 규약을 사용하여 정보 시스템에서 조직 외부의 다른 정보 시스템으로의 연결을 승인합니다. (ii) 각 연결, 인터페이스 특징, 보안 요구 사항 및 전달되는 정보의 특성을 문서화합니다. (iii) 정보 시스템에서 조직 외부의 다른 정보 시스템으로의 연결을 허용하기 위해 모두 거부, 예외 허용 정책을 채택합니다. (iv) 명시적으로 허용되는 서비스 및 포트를 제외하고 해당 엔드포인트의 호스트 기반 방화벽 또는 포트 필터링 도구(워크스테이션, 서버 등)를 통해 모든 트래픽을 삭제하는 기본-거부 규칙을 적용합니다. |[Key Vault는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|비즈니스 연속성 및 위험 평가 |1635.12b1Organizational.2 - 12.b |비즈니스 연속성의 정보 보안 측면은 (i) 조직의 중요한 비즈니스 프로세스를 중단시킬 수 있는 이벤트(또는 일련의 이벤트)를 식별합니다(예: 장비 오류, 인적 오류, 도난, 화재, 자연 재해, 테러 행위). (ii) 위험 평가를 통해 시간, 손상 규모 및 복구 기간 측면에서 그러한 중단의 확률 및 영향을 확인합니다. (iii) 위험 평가 결과에 따라 비즈니스 연속성에 대한 전반적인 접근 방식을 식별하기 위한 비즈니스 연속성 전략을 개발합니다. (iv) 이 전략이 만들어지면 경영진이 보증하고 이 전략을 구현하기 위해 계획을 만들고 승인합니다. |[Key Vault 개체를 복구할 수 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |


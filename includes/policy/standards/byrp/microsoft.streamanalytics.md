---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 9525824d98daac6002df6d0339802a3b30d91c14
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108738582"
---
## <a name="azure-security-benchmark"></a>Azure 보안 벤치마크

[Azure Security Benchmark](/security/benchmark/azure/introduction)는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 이 서비스가 Azure Security Benchmark에 완전히 매핑되는 방법을 보려면 [Azure Security Benchmark 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

모든 Azure 서비스에 사용 가능한 Azure Policy 기본 제공이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|로깅 및 위협 탐지 |LT-4 |Azure 리소스에 대한 로깅 사용 |[Azure Stream Analytics에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Azure Security Benchmark v1

[Azure Security Benchmark](/security/benchmark/azure/introduction)는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 이 서비스가 Azure Security Benchmark에 완전히 매핑되는 방법을 보려면 [Azure Security Benchmark 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

모든 Azure 서비스에 사용 가능한 Azure Policy 기본 제공이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|로깅 및 모니터링 |2.3 |Azure 리소스에 대한 감사 로깅 사용 |[Azure Stream Analytics에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark-130"></a>CIS Microsoft Azure Foundations Benchmark 1.3.0

모든 Azure 서비스에 사용 가능한 Azure Policy 기본 제공이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - CIS Microsoft Azure Foundations Benchmark 1.3.0](../../../../articles/governance/policy/samples/cis-azure-1-3-0.md)을 참조하세요.
이러한 규정 준수 표준에 대한 자세한 내용은 [CIS Microsoft Azure Foundations 벤치마크](https://www.cisecurity.org/benchmark/azure/)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|로깅 및 모니터링 |5.3 |진단 로그를 지원하는 모든 서비스에 대해 사용할 수 있는지 확인합니다. |[Azure Stream Analytics에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC Level 3

모든 Azure 서비스에 사용 가능한 Azure Policy 기본 제공이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - CMMC Level 3](../../../../articles/governance/policy/samples/cmmc-l3.md)을 참조하세요.
이 규정 준수 표준에 대한 자세한 내용은 [CMMC(사이버 보안 완성 모델 인증)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|시스템 및 통신 보호 |SC.3.177 |CUI의 기밀성을 보호하기 위해 사용되는 경우 FIPS 인증 암호화를 사용합니다. |[Azure Stream Analytics 작업은 고객 관리형 키를 사용하여 데이터를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87ba29ef-1ab3-4d82-b763-87fcd4f531f7) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_CMK_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

모든 Azure 서비스에 사용 가능한 Azure Policy 기본 제공이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md)를 참조하세요.
이 규정 준수 표준에 대한 자세한 내용은 [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|감사 로깅 |1207.09aa2System.4 - 09.aa |감사 레코드는 90일 동안 보존되며 이전 감사 레코드는 1년 동안 보관됩니다. |[Azure Stream Analytics에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |


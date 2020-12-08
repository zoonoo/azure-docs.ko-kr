---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 12/01/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c2385d07ccb81041bd340a8bec0412a8f14cef56
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96477529"
---
## <a name="azure-security-benchmark"></a>Azure 보안 벤치마크

[Azure Security Benchmark](../../../../articles/security/benchmarks/overview.md)는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 이 서비스가 Azure Security Benchmark에 완전히 매핑되는 방법을 보려면 [Azure Security Benchmark 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

모든 Azure 서비스에 사용 가능한 Azure Policy 기본 제공이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|데이터 보호 |4.8 |중요한 저장 정보 암호화 |[Automation 계정 변수를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |

> [!NOTE]
> Automation 계정 변수를 만들 때 Azure Automation에서 암호화 및 스토리지를 보안 자산으로 지정할 수 있습니다. 변수를 만든 후에는 변수를 다시 만들지 않고는 암호화 상태를 변경할 수 없습니다. 아직 암호화되지 않은 중요한 데이터를 저장하는 Automation 계정 변수가 있는 경우 이를 삭제하고 암호화된 변수로 다시 만들어야 합니다. Azure Security Center 권장 사항은 [Automation 계정 변수를 암호화해야 함](../../../../articles/security-center/recommendations-reference.md#recs-computeapp)에 설명된 대로 모든 Azure Automation 변수를 암호화하는 것입니다. 이 보안 권장 사항에서 제외하려는 암호화되지 않은 변수가 있는 경우 [권장 사항 및 보안 점수에서 리소스 제외](../../../../articles/security-center/exempt-resource.md)를 참조하여 예외 규칙을 만듭니다.
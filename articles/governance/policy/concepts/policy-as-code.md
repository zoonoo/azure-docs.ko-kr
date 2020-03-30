---
title: 코드 워크플로서의 디자인 정책
description: Azure Policy 정의를 코드로 배포하고 리소스의 유효성을 자동으로 검사하는 워크플로를 디자인하는 방법을 알아봅니다.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 0914ba6510c9d2ef87d3f83417f97340d42c8bce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74267272"
---
# <a name="design-policy-as-code-workflows"></a>코드 워크플로서의 디자인 정책

클라우드 거버넌스를 통해 여정을 진행하면서 Azure 포털의 각 정책 정의를 수동으로 관리하는 것에서 다양한 SDK를 통해 엔터프라이즈 규모에서 보다 관리 가능하고 반복 가능한 것으로 전환할 수 있습니다. 클라우드에서 대규모로 시스템을 관리하는 두 가지 주요 접근 방식은 다음과 같습니다.

- 코드로서의 인프라: 리소스 관리자 템플릿에서 Azure 정책 정의, Azure Blueprint에 이르기까지 환경을 정의하는 콘텐츠를 소스 코드로 처리하는 방법입니다.
- DevOps: 최종 사용자에게 가치를 지속적으로 전달할 수 있도록 인력, 프로세스 및 제품의 결합입니다.

코드로서의 정책은 이러한 아이디어의 조합입니다. 기본적으로 정책 정의를 소스 제어에 유지하고 변경이 이루어질 때마다 해당 변경 사항을 테스트하고 유효성을 검사합니다. 그러나 이는 코드 또는 DevOps와 같은 인프라와 정책 참여의 범위가 되어서는 안 됩니다.

유효성 검사 단계는 다른 연속 통합 또는 지속적인 배포 워크플로의 구성 요소여야 합니다. 예를 들어 응용 프로그램 환경 또는 가상 인프라 배포를 포함합니다. Azure Policy 유효성 검사를 빌드 및 배포 프로세스의 초기 구성 요소로 설정하여 응용 프로그램 및 운영 팀은 변경 내용이 불만 사항이 아닌지 확인합니다.

## <a name="workflow-overview"></a>워크플로 개요

코드로 정책의 권장된 일반 워크플로는 이 다이어그램과 같습니다.

![코드 워크플로 개요로서의 정책](../media/policy-as-code/policy-as-code-workflow.png)

### <a name="create-and-update-policy-definitions"></a>정책 정의 생성 및 업데이트

정책 정의는 JSON을 사용하여 만들어지고 소스 제어에 저장됩니다. 각 정책에는 동일한 폴더에 저장해야 하는 매개 변수, 규칙 및 환경 매개 변수와 같은 고유한 파일 집합이 있습니다. 다음 구조는 소스 제어에서 정책 정의를 유지하는 데 권장되는 방법입니다.

```text
.
|
|- policies/  ________________________ # Root folder for policies
|  |- policy1/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- policy2/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

새 정책이 추가되거나 기존 정책이 업데이트되면 워크플로는 Azure에서 정책 정의를 자동으로 업데이트해야 합니다. 새 정책 또는 업데이트된 정책 정의 테스트는 이후 단계에서 제공됩니다.

### <a name="create-and-update-initiative-definitions"></a>이니셔티브 정의 생성 및 업데이트

마찬가지로 이니셔티브에는 동일한 폴더에 저장해야 하는 자체 JSON 파일과 관련 파일이 있습니다. 이니셔티브 정의에는 정책 정의가 이미 있어야 하므로 정책의 원본이 소스 제어에서 업데이트된 다음 Azure에서 업데이트될 때까지 만들거나 업데이트할 수 없습니다. 다음 구조는 소스 제어에서 이니셔티브 정의를 유지하는 권장 되는 방법입니다.

```text
.
|
|- initiatives/ ______________________ # Root folder for initiatives
|  |- init1/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- init2/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

정책 정의와 마찬가지로 기존 이니셔티브를 추가하거나 업데이트할 때 워크플로는 Azure에서 이니셔티브 정의를 자동으로 업데이트해야 합니다. 새 또는 업데이트된 이니셔티브 정의테스트는 이후 단계로 나아옵니다.

### <a name="test-and-validate-the-updated-definition"></a>업데이트된 정의를 테스트하고 유효성 을 검사합니다.

자동화가 새로 생성되거나 업데이트된 정책 또는 이니셔티브 정의를 가져와 Azure의 개체를 업데이트한 후에는 변경 된 내용을 테스트할 차례입니다. 정책 또는 이니셔티브(들)의 일부가 프로덕션환경의 리소스에 할당되어야 합니다. 이 환경은 일반적으로 _개발자입니다._

할당은 사용 하 여 사용 하 [여사용모드](./assignment-structure.md#enforcement-mode) 리소스 생성 및 업데이트 차단 되지 않습니다 하지만 기존 리소스 업데이트 된 정책 정의 준수에 대 한 감사 계속. _disabled_ enforcementMode를 사용 하더라도 할당 범위는 리소스 그룹 또는 정책 유효성 검사에 특별히 사용되는 구독인 것이 좋습니다.

> [!NOTE]
> 적용 모드는 유용하지만 다양한 조건에서 정책 정의를 철저히 테스트하는 것은 아닙니다. 정책 정의는 `PUT` `PATCH` REST API 호출, 규정 준수 및 비준수 리소스 및 리소스에서 누락된 속성과 같은 에지 사례를 통해 테스트해야 합니다.

할당을 배포한 후 정책 SDK를 사용하여 새 할당에 대한 [규정 준수 데이터를 가져옵니다.](../how-to/get-compliance-data.md) 정책 및 할당을 테스트하는 데 사용되는 환경에는 호환 및 비준수 리소스가 모두 있어야 합니다. 코드에 대한 좋은 단위 테스트와 마찬가지로 리소스가 예상대로 테스트되고 거짓 긍정 또는 거짓 부정이 없는지 테스트하려고 합니다. 예상한 것만 테스트하고 유효성을 검사하는 경우 정책에서 예기치 않은 영향을 받을 수 있습니다. 자세한 내용은 [새 Azure 정책의 영향 평가를](./evaluate-impact.md)참조하십시오.

### <a name="enable-remediation-tasks"></a>수정 작업 사용

할당의 유효성 검사가 기대치를 충족하는 경우 다음 단계는 수정의 유효성을 검사하는 것입니다.
[deployIfNotExists](./effects.md#deployifnotexists) 또는 [수정을](./effects.md#modify) 사용하는 정책은 업데이트 관리 작업으로 전환되고 비준수 상태에서 리소스를 수정할 수 있습니다.

이 작업을 수행하는 첫 번째 단계는 정책 정의에 정의된 역할 할당을 정책 할당에 부여하는 것입니다. 이 역할 할당은 정책 할당관리 ID를 제공하여 리소스를 준수하기 위해 필요한 변경을 수행할 수 있는 충분한 권한을 부여합니다.

정책 할당에 적절한 권한이 있으면 정책 SDK를 사용하여 비준수로 알려진 리소스 집합에 대해 수정 작업을 트리거합니다. 계속하기 전에 이러한 수정된 작업에 대해 세 가지 테스트를 완료해야 합니다.

- 업데이트 관리 작업이 성공적으로 완료된지 확인
- 정책 평가 를 실행하여 정책 준수 결과가 예상대로 업데이트되는지 확인합니다.
- 리소스에 대해 직접 환경 단위 테스트를 실행하여 해당 속성이 변경된지 확인합니다.

업데이트된 정책 평가 결과와 환경을 모두 테스트하면 수정 작업이 예상한 내용을 변경하고 정책 정의에서 규정 준수가 예상대로 변경되었다는 확인이 직접 제공됩니다.

### <a name="update-to-enforced-assignments"></a>적용된 할당으로 업데이트

모든 유효성 검사 게이트가 완료되면 **할당을** 업데이트하여 적용 모드를 사용하도록 _enabled_. 이 변경은 처음에는 프로덕션과 멀리 떨어진 동일한 환경에서 이루어져야 합니다. 해당 환경이 예상대로 작동하도록 유효성을 검사하면 정책이 프로덕션 리소스에 배포될 때까지 다음 환경을 포함하도록 변경 범위를 조정해야 합니다.

## <a name="process-integrated-evaluations"></a>프로세스 통합 평가

코드로서의 정책에 대한 일반적인 워크플로는 대규모 환경에 정책 및 이니셔티브를 개발하고 배포하기 위한 것입니다. 그러나 정책 평가는 응용 프로그램을 배포하거나 인프라를 만들기 위해 Resource Manager 템플릿을 실행하는 등 Azure에서 리소스를 배포하거나 만드는 모든 워크플로에 대한 배포 프로세스의 일부여야 합니다.

이러한 경우 응용 프로그램 또는 인프라 배포가 테스트 구독 또는 리소스 그룹에 수행된 후 해당 범위에 대해 모든 기존 정책 및 이니셔티브의 유효성 검사를 검사하는 범위에 대해 정책 평가를 수행해야 합니다. 이러한 **환경에서는 적용 모드로** _disabled_ 구성할 수 있지만 응용 프로그램 이나 인프라 배포가 정책 정의를 조기에 위반하는 경우 조기에 알면 유용합니다. 따라서 이 정책 평가는 이러한 워크플로의 단계여야 하며 비준수 리소스를 만드는 배포에 실패해야 합니다.

## <a name="review"></a>검토

이 문서에서는 정책 용 코드로서의 일반적인 워크플로우와 정책 평가가 다른 배포 워크플로의 일부가 되어야 하는 부분에 대해서도 다룹니다. 이 워크플로는 트리거를 기반으로 스크립팅된 단계 및 자동화를 지원하는 모든 환경에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [정책 정의 구조에](./definition-structure.md)대해 자세히 알아봅니다.
- [정책 할당 구조에](./assignment-structure.md)대해 자세히 알아봅니다.
- [프로그래밍 방식으로 정책을 만드는](../how-to/programmatically-create.md)방법을 이해합니다.
- [규정 준수 데이터를 얻는](../how-to/get-compliance-data.md)방법에 대해 알아봅니다.
- [비준수 리소스를 수정하는](../how-to/remediate-resources.md)방법에 대해 알아봅니다.
- 관리 그룹이 Azure 관리 그룹으로 리소스 구성을 통해 어떤 내용인지 [검토합니다.](../../management-groups/overview.md)
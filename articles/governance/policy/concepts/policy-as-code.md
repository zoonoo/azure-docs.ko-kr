---
title: 코드 워크플로서의 디자인 정책
description: Azure Policy 정의를 코드로 배포 하 고 리소스의 유효성을 자동으로 검사 하는 워크플로를 디자인 하는 방법을 알아봅니다.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 935616145ee32bea8c3b514c495690ce4105cb80
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072083"
---
# <a name="design-policy-as-code-workflows"></a>코드 워크플로서의 디자인 정책

클라우드 거 버 넌 스를 진행 하면서, Azure Portal의 각 정책 정의를 수동으로 관리 하거나 다양 한 Sdk를 통해 엔터프라이즈 규모에서 보다 관리 하기 쉽고 반복 가능한 것으로 전환 하려고 합니다. 클라우드에서 대규모 시스템을 관리 하는 방법에는 다음 두 가지가 있습니다.

- 코드로 서의 인프라: 사용자 환경을 정의 하는 콘텐츠를 처리 하는 방법입니다. 리소스 관리자 템플릿에서 Azure 청사진에 대 한 정의를 Azure Policy 하는 것이 소스 코드입니다.
- DevOps: 최종 사용자에 게 가치를 지속적으로 제공할 수 있는 사람, 프로세스 및 제품의 합집합입니다.

정책은 이러한 아이디어의 조합입니다. 기본적으로 정책 정의는 소스 제어에서 유지 하 고 변경 사항이 있을 때마다 변경 내용을 테스트 하 고 유효성을 검사할 수 있습니다. 그러나이는 코드 또는 DevOps의 인프라와 관련 된 정책 범위에 있어서는 안 됩니다.

또한 유효성 검사 단계는 다른 연속 통합 또는 연속 배포 워크플로의 구성 요소 여야 합니다. 예제에는 응용 프로그램 환경 또는 가상 인프라 배포가 포함 됩니다. 응용 프로그램 및 운영 팀은 빌드 및 배포 프로세스의 초기 구성 요소에 대 한 Azure Policy 유효성 검사를 수행 하 여 변경 내용이 비규격 인지 여부를 확인 합니다 .이는 너무 늦게 진행 되며 프로덕션 환경에서 배포 하려고 합니다.

## <a name="workflow-overview"></a>워크플로 개요

코드로 권장 되는 일반적인 정책 워크플로는 다음 다이어그램과 같습니다.

![코드로 서의 정책 워크플로 개요](../media/policy-as-code/policy-as-code-workflow.png)

### <a name="create-and-update-policy-definitions"></a>정책 정의 만들기 및 업데이트

정책 정의는 JSON을 사용 하 여 생성 되 고 소스 제어에 저장 됩니다. 각 정책에는 동일한 폴더에 저장 해야 하는 매개 변수, 규칙 및 환경 매개 변수와 같은 고유한 파일 집합이 있습니다. 다음 구조는 소스 제어에서 정책 정의를 유지 하는 권장 방법입니다.

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

새 정책을 추가 하거나 기존 정책을 업데이트 하는 경우 워크플로는 Azure에서 정책 정의를 자동으로 업데이트 해야 합니다. 새 정책 정의 또는 업데이트 된 정책 정의의 테스트는 이후 단계에서 제공 됩니다.

### <a name="create-and-update-initiative-definitions"></a>이니셔티브 정의 만들기 및 업데이트

마찬가지로 이니셔티브에는 동일한 폴더에 저장 해야 하는 고유한 JSON 파일 및 관련 파일이 있습니다. 이니셔티브 정의를 사용 하려면 정책 정의가 이미 있어야 합니다. 따라서 정책 원본이 원본 제어에서 업데이트 된 다음 Azure에서 업데이트 될 때까지 만들거나 업데이트할 수 없습니다. 다음 구조는 소스 제어에서 이니셔티브 정의를 유지 하는 권장 되는 방법입니다.

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

정책 정의와 마찬가지로 기존 이니셔티브를 추가 하거나 업데이트할 때 워크플로는 Azure에서 이니셔티브 정의를 자동으로 업데이트 해야 합니다. 신규 또는 업데이트 된 이니셔티브 정의의 테스트는 이후 단계에서 제공 됩니다.

### <a name="test-and-validate-the-updated-definition"></a>업데이트 된 정의 테스트 및 유효성 검사

Automation에서 새로 만들었거나 업데이트 된 정책 또는 이니셔티브 정의를 수행 하 고 Azure에서 개체를 업데이트 한 후에는 변경 내용을 테스트 해야 합니다. 구성 된 정책 또는 이니셔티브는 프로덕션에서 가장 멀리 있는 환경의 리소스에 할당 되어야 합니다. 이 환경은 일반적으로 _개발_입니다.

할당은 리소스 만들기 및 업데이트가 차단 되지 않도록 [enforcementMode](./assignment-structure.md#enforcement-mode) 의 사용 _안 함_ 을 사용 해야 하지만 기존 리소스는 업데이트 된 정책 정의에 대 한 준수 여부를 계속 감사 합니다. EnforcementMode를 사용 하는 경우에도 할당 범위는 정책을 확인 하는 데 특별히 사용 되는 리소스 그룹이 나 구독 중 하나입니다.

> [!NOTE]
> 적용 모드가 유용 하지만 다양 한 조건에서 정책 정의를 철저히 테스트 하는 것은 대체 되지 않습니다. 정책 정의는 `PUT` 및 `PATCH` REST API 호출, 규격 및 비규격 리소스 및 리소스에서 누락 된 속성과 같은에 지 사례를 사용 하 여 테스트 해야 합니다.

할당을 배포한 후 정책 SDK를 사용 하 여 새 할당에 대 한 [호환성 데이터를 가져옵니다](../how-to/get-compliance-data.md) . 정책 및 할당을 테스트 하는 데 사용 되는 환경에는 규격 및 비규격 리소스가 둘 다 있어야 합니다. 코드에 대 한 좋은 단위 테스트와 마찬가지로, 리소스가 예상 대로 작동 하 고 가양성 또는 거짓-부정이 없음을 테스트 하려고 합니다. 필요한 것에 대해서만 테스트 하 고 유효성을 검사 하는 경우 정책에서 예기치 않은 영향이 있을 수 있습니다. 자세한 내용은 [새 Azure 정책의 영향 평가](./evaluate-impact.md)를 참조 하세요.

### <a name="enable-remediation-tasks"></a>재구성 작업 사용

할당의 유효성 검사가 기대치를 충족 하는 경우 다음 단계는 재구성의 유효성을 검사 하는 것입니다.
[Deployifnotexists](./effects.md#deployifnotexists) 또는 [modify](./effects.md#modify) 를 사용 하는 정책은 재구성 작업으로 전환 되 고 비호환 상태에서 올바른 리소스로 전환 될 수 있습니다.

이 작업을 수행 하는 첫 번째 단계는 정책 정의에 정의 된 역할 할당에 정책 할당을 부여 하는 것입니다. 이 역할 할당은 정책 할당 관리 id에 리소스를 준수 하도록 필요한 변경을 수행할 수 있는 충분 한 권한을 제공 합니다.

정책 할당에 적절 한 권한이 있는 경우 정책 SDK를 사용 하 여 비준수로 알려진 리소스 집합에 대해 재구성 작업을 트리거합니다. 계속 하기 전에 이러한 재구성 된 작업에 대해 세 가지 테스트를 완료 해야 합니다.

- 재구성 작업이 성공적으로 완료 되었는지 확인
- 정책 평가를 실행 하 여 정책 준수 결과가 예상 대로 업데이트 되는지 확인 합니다.
- 리소스에 대해 환경 단위 테스트를 직접 실행 하 여 해당 속성이 변경 되었는지 유효성을 검사 합니다.

업데이트 된 정책 평가 결과와 환경을 모두 테스트 하면 재구성 작업이 예상 대로 변경 되었으며 정책 정의가 예상 대로 변경 되었는지 확인 하는 것을 직접 확인할 것입니다.

### <a name="update-to-enforced-assignments"></a>적용 할당으로 업데이트

모든 유효성 검사 게이트가 완료 된 후 **enforcementMode** 사용을 사용 하도록 할당을 업데이트 _합니다._ 이 변경 내용은 프로덕션에서 멀리 떨어진 동일한 환경에서 초기에 수행 되어야 합니다. 예상 대로 작동 하는 환경의 유효성을 검사 한 후에는 정책이 프로덕션 리소스에 배포 될 때까지 다음 환경을 포함 하도록 변경 범위를 지정 해야 합니다.

## <a name="process-integrated-evaluations"></a>프로세스 통합 평가

정책에 대 한 일반적인 워크플로는 정책 및 이니셔티브를 대규모 환경으로 개발 하 고 배포 하기 위한 것입니다. 그러나 인프라를 만들기 위해 응용 프로그램 배포 또는 리소스 관리자 템플릿 실행과 같이 Azure에서 리소스를 배포 하거나 만드는 모든 워크플로의 경우에는 배포 프로세스의 일부로 정책 평가가 포함 되어야 합니다.

이러한 경우, 응용 프로그램 또는 인프라 배포가 테스트 구독 또는 리소스 그룹에 대해 수행 된 후에는 해당 범위에 대 한 정책 평가를 수행 하 여 모든 기존 정책과 이니셔티브의 유효성 검사를 확인 해야 합니다. 이러한 환경에서 **enforcementMode** _disabled_ 로 구성 될 수 있지만 응용 프로그램 또는 인프라 배포가 정책 정의를 조기에 위반 하 고 있는지 조기에 파악 하는 것이 유용 합니다. 따라서이 정책 평가는 이러한 워크플로의 단계 이며 비준수 리소스를 만드는 실패 한 배포에 실패 합니다.

## <a name="review"></a>검토

이 문서에서는 정책에 대 한 일반적인 워크플로를 코드로 설명 하 고, 정책 평가가 다른 배포 워크플로에 포함 되어야 하는 경우도 있습니다. 이 워크플로는 트리거를 기반으로 하는 스크립팅된 단계 및 자동화를 지 원하는 모든 환경에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [정책 정의 구조](./definition-structure.md)에 대해 알아봅니다.
- [정책 할당 구조](./assignment-structure.md)에 대해 알아봅니다.
- [프로그래밍 방식으로 정책을 만드는](../how-to/programmatically-create.md)방법을 알아봅니다.
- [준수 데이터를 가져오는](../how-to/getting-compliance-data.md)방법에 대해 알아봅니다.
- [비준수 리소스](../how-to/remediate-resources.md)를 수정 하는 방법에 대해 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.
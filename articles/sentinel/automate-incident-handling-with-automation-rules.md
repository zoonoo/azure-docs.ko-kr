---
title: Azure 센티널에서 인시던트 처리 자동화 | Microsoft Docs
description: 이 문서에서는 보안 위협에 대 한 응답으로 SOC의 효율성 및 효율성을 최대화 하기 위해 자동화 규칙을 사용 하 여 인시던트 처리를 자동화 하는 방법을 설명 합니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
ms.openlocfilehash: 1ff9fbbb6cd4b8827555a6cb1b222ed4eb0a5299
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609811"
---
# <a name="automate-incident-handling-in-azure-sentinel-with-automation-rules"></a>Automation 규칙을 사용 하 여 Azure 센티널에서 인시던트 처리 자동화

이 문서에서는 Azure 센티널 automation 규칙의 정의 및이 규칙을 사용 하 여 보안 오케스트레이션, Automation and Response (대화 충성도) 작업을 구현 하 고 SOC의 효율성을 높이고 시간과 리소스를 절약 하는 방법에 대해 설명 합니다.

> [!IMPORTANT]
>
> - **Automation 규칙** 기능은 현재 **미리 보기** 상태입니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 추가 약관은 [Microsoft Azure 미리 보기에](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 대 한 추가 사용 약관을 참조 하세요.

## <a name="what-are-automation-rules"></a>Automation 규칙 이란?

Automation 규칙은 Azure 센티널의 새로운 개념입니다. 이 기능을 통해 사용자는 인시던트 처리의 자동화를 중앙에서 관리할 수 있습니다. 이전 처럼 경고가 아닌 인시던트에 플레이 북을 할당 하는 것 외에도, 자동화 규칙을 사용 하 여 한 번에 여러 분석 규칙에 대 한 응답을 자동화 하 고, 플레이 북이 없어도 자동으로 인시던트를 태그 지정, 할당 또는 종결 하 고, 실행 되는 작업의 순서를 제어할 수 있습니다. Automation 규칙은 Azure 센티널에서 automation 사용을 간소화 하 고 인시던트 오케스트레이션 프로세스에 대 한 복잡 한 워크플로를 단순화할 수 있습니다.

## <a name="components"></a>구성 요소

Automation 규칙은 다음과 같은 여러 구성 요소로 구성 됩니다.

### <a name="trigger"></a>트리거

인시던트를 만들 때 Automation 규칙이 트리거됩니다. 

검토-인시던트는 분석 규칙에 따라 생성 되며,이는 [Azure 센티널의 기본 제공 분석 규칙을 사용 하 여 위협 검색](tutorial-detect-threats-built-in.md)자습서에 설명 된 대로 네 가지 유형이 있습니다.

### <a name="conditions"></a>조건

복잡 한 조건 집합을 정의 하 여 작업 (아래 참조)이 실행 되어야 하는 시기를 제어할 수 있습니다. 이러한 조건은 일반적으로 인시던트 및 해당 엔터티의 특성 상태 또는 값을 기반으로 하며 연산자를 포함할 수 있습니다 `AND` / `OR` / `NOT` / `CONTAINS` .

### <a name="actions"></a>작업

작업은 조건 (위 참조)이 충족 될 때 실행 되도록 정의할 수 있습니다. 규칙에서 많은 작업을 정의 하 고 실행 되는 순서를 선택할 수 있습니다 (아래 참조). 다음 작업은 [플레이 북의 고급 기능이](automate-responses-with-playbooks.md)없어도 automation 규칙을 사용 하 여 정의할 수 있습니다.

- 인시던트 상태를 변경 하 여 워크플로를 최신 상태로 유지 합니다.

  - [닫는 이유](tutorial-investigate-cases.md#closing-an-incident) 를 지정 하 고 주석을 추가 하는 "닫힘"으로 변경 하는 경우입니다. 이렇게 하면 성능 및 효율성을 추적 하 고 가양성을 줄이기 위해 미세 조정할 수 있습니다.

- 인시던트의 심각도 변경 – 인시던트에 포함 된 엔터티의 현재 상태, 존재 하지 않는 값 또는 특성에 따라 재평가 하 고 우선 순위를 다시 지정할 수 있습니다.

- 소유자에 게 인시던트 할당-이를 통해 인시던트를 처리 하는 데 가장 적합 한 직원 또는 가장 사용 가능한 담당자에 게 인시던트 유형을 지시할 수 있습니다.

- 인시던트에 태그 추가 – 주체, 공격자 또는 기타 일반적인 분모에 의해 인시던트를 분류 하는 데 유용 합니다.

또한 외부 시스템과 관련 된 작업을 포함 하 여 보다 복잡 한 응답 작업을 수행 하기 위해 [플레이 북](tutorial-respond-threats-playbook.md)를 실행 하는 작업을 정의할 수 있습니다. [인시던트 트리거에서](automate-responses-with-playbooks.md#azure-logic-apps-basic-concepts) 활성화 된 플레이 북만 automation 규칙에서 사용할 수 있습니다. 여러 플레이 북, 플레이 북 및 기타 작업의 조합 및 실행 순서를 포함 하는 작업을 정의할 수 있습니다.

### <a name="expiration-date"></a>만료 날짜

자동화 규칙에서 만료 날짜를 정의할 수 있습니다. 이 날짜 이후에는 규칙이 사용 하지 않도록 설정 됩니다. 이는 침투 테스트와 같이 계획 된 시간 제한 활동으로 인해 발생 하는 "노이즈" 인시던트를 처리 하는 데 유용 합니다.

### <a name="order"></a>주문

Automation 규칙이 실행 되는 순서를 정의할 수 있습니다. 이후 자동화 규칙은 이전 자동화 규칙에서 처리 된 후의 상태에 따라 인시던트의 조건을 평가 합니다.

예를 들어, "첫 번째 Automation 규칙"이 인시던트 심각도를 Medium에서 Low로 변경 하 고 "Second Automation Rule"이 중간 이상의 심각도가 있는 인시던트에 대해 실행 되도록 정의한 경우 해당 인시던트에 실행 되지 않습니다.

## <a name="common-use-cases-and-scenarios"></a>일반적인 사용 사례 및 시나리오

### <a name="incident-triggered-automation"></a>인시던트 트리거 자동화

지금까지 경고만 플레이 북을 사용 하 여 자동화 된 응답을 트리거할 수 있습니다. 이제 인시던트는 자동화 규칙을 사용 하 여 인시던트를 만들 때 새로운 인시던트 트리거된 플레이 북 ([특수 권한 필요](#permissions-for-automation-rules-to-run-playbooks))을 포함할 수 있는 자동화 된 응답 체인을 트리거할 수 있습니다. 

### <a name="trigger-playbooks-for-microsoft-providers"></a>Microsoft 공급자를 위한 플레이 북 트리거

Automation 규칙은 경고를 통해 생성 된 인시던트에 이러한 규칙을 적용 하 여 Microsoft 보안 경고의 처리를 자동화 하는 방법을 제공 합니다. Automation 규칙은 플레이 북 ([특수 권한이 필요 함](#permissions-for-automation-rules-to-run-playbooks))를 호출할 수 있으며 경고 및 엔터티를 비롯 한 모든 세부 정보를 사용 하 여 인시던트를 전달 합니다. 일반적으로 Azure 센티널 모범 사례는 보안 작업의 초점으로 인시던트 큐를 사용 합니다.

Microsoft 보안 경고는 다음과 같습니다.

- MCAS(Microsoft Cloud App Security)
- Azure AD ID 보호
- Azure Defender (ASC)
- Defender for IoT(이전의 ASC for IoT)
- Microsoft Defender for Office 365(이전의 Office 365 ATP)
- Microsoft Defender for Endpoint(이전 명칭 MDATP)
- ID용 Microsoft Defender(이전 Azure ATP)

### <a name="multiple-sequenced-playbooksactions-in-a-single-rule"></a>단일 규칙에서 여러 개의 시퀀싱 된 플레이 북/작업

이제 단일 자동화 규칙에서 작업 및 플레이 북의 실행 순서를 거의 완전히 제어할 수 있습니다. 또한 automation 규칙의 실행 순서를 제어 합니다. 이를 통해 플레이 북을 크게 간소화 하 고, 단일 작업 또는 작고 간단한 일련의 작업으로 축소 하 고, 이러한 작은 플레이 북을 여러 automation 규칙의 여러 조합으로 조합할 수 있습니다.

### <a name="assign-one-playbook-to-multiple-analytics-rules-at-once"></a>한 번에 하나의 플레이 북를 여러 분석 규칙에 할당

모든 분석 규칙에서 자동화 하려는 작업이 있는 경우 (예를 들어 외부 티켓 시스템에서 지원 티켓을 생성 함), 모든 향후 규칙을 포함 하 여 하나 이상의 분석 규칙에 단일 플레이 북를 적용할 수 있습니다. 이렇게 하면 간단 하지만 반복적인 유지 관리 및 정리 작업을 chore 하는 것이 훨씬 더 낮습니다.

### <a name="automatic-assignment-of-incidents"></a>인시던트 자동 할당

인시던트를 올바른 소유자에 게 자동으로 할당할 수 있습니다. SOC에 특정 플랫폼을 전문적으로 하는 분석가가 있는 경우 해당 플랫폼과 관련 된 모든 인시던트를 해당 분석가에 게 자동으로 할당할 수 있습니다.

### <a name="incident-suppression"></a>인시던트 억제

규칙을 사용 하 여 플레이 북을 사용 하지 않고 거짓/무해 한 긍정 인시던트를 자동으로 해결할 수 있습니다. 예를 들어, 침투 테스트를 실행 하는 경우 예약 된 유지 관리 또는 업그레이드를 수행 하거나 자동화 프로시저를 테스트 하는 경우 SOC에서 무시 하고자 하는 다 수의 거짓 긍정 인시던트가 만들어질 수 있습니다. 시간이 제한 된 자동화 규칙은 생성 될 때 이러한 인시던트를 자동으로 닫을 수 있으며, 생성의 원인에 대 한 설명자로 태그를 지정 합니다.

### <a name="time-limited-automation"></a>시간이 제한 된 자동화

Automation 규칙의 만료 날짜를 추가할 수 있습니다. 시간 제한 자동화를 보장 하는 인시던트 억제 이외의 사례가 있을 수 있습니다. 특정 시간 프레임의 특정 사용자 (예를 들어, 인턴 또는 컨설턴트)에 특정 유형의 인시던트를 할당할 수 있습니다. 시간 프레임을 미리 알고 있는 경우에는이 작업을 기억할 필요 없이 관련성의 끝에서 규칙을 사용 하지 않도록 효과적으로 설정할 수 있습니다.

### <a name="automatically-tag-incidents"></a>자동으로 인시던트에 태그

선택 된 조건에 따라 인시던트를 그룹화 하거나 분류 하는 데 사용할 자유 텍스트 태그를 자동으로 추가할 수 있습니다.

## <a name="automation-rules-execution"></a>Automation 규칙 실행

Automation 규칙은 결정 하는 순서에 따라 순차적으로 실행 됩니다. 각 자동화 규칙은 이전 실행이 완료 된 후 실행 됩니다. 자동화 규칙 내에서 모든 작업은 정의 된 순서 대로 순차적으로 실행 됩니다.

플레이 북 동작의 경우 플레이 북 동작의 시작과 목록의 다음 작업 사이에 2 분의 지연이 있습니다.

### <a name="permissions-for-automation-rules-to-run-playbooks"></a>플레이 북을 실행 하기 위한 automation 규칙에 대 한 권한

Azure 센티널 automation 규칙은 플레이 북를 실행 하는 경우이 작업에 대해 특별히 권한이 부여 된 특수 한 Azure 센티널 서비스 계정을 사용 합니다. 사용자 계정이 아닌이 계정을 사용 하면 서비스의 보안 수준이 향상 됩니다.

Automation 규칙에서 플레이 북를 실행 하려면 플레이 북가 있는 리소스 그룹에 대 한 명시적 권한을이 계정에 부여 해야 합니다. 이 시점에서 모든 automation 규칙은 해당 리소스 그룹에서 모든 플레이 북을 실행할 수 있습니다.

Automation 규칙을 구성 하 고 **플레이 북 실행** 작업을 추가 하는 경우 플레이 북의 드롭다운 목록이 표시 됩니다. Azure 센티널에 권한이 없는 플레이 북은 사용할 수 없는 것으로 표시 됩니다 ("회색으로 표시 됨"). **플레이 북 권한 관리** 링크를 선택 하 여 해당 지점에서 playbooks의 리소스 그룹에 Azure 센티널 권한을 부여할 수 있습니다.

> [!NOTE]
> **다중 테 넌 트 아키텍처의 사용 권한**
>
> Automation 규칙은 다중 테 넌 트 및 다중 테 넌 트 배포를 완벽 하 게 지원 합니다 ( [Azure Lighthouse](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)를 사용 하는 다중 테 넌 트의 경우).
>
> 따라서 Azure 센티널 배포에서 다중 테 넌 트 아키텍처를 사용 하는 경우 (예를 들어 mssp를 사용 하는 경우), 한 테 넌 트에서 automation 규칙을 사용 하 여 다른 테 넌 트에 상주 하는 플레이 북을 실행할 수 있습니다. 하지만 플레이 북를 실행 하는 센티널은 automation 규칙이 정의 된 테 넌 트가 아닌 플레이 북이 있는 테 넌 트에서 정의 되어야 합니다

## <a name="creating-and-managing-automation-rules"></a>Automation 규칙 만들기 및 관리

특정 요구 사항 및 사용 사례에 따라 Azure 센티널 환경의 다른 점에서 automation 규칙을 만들고 관리할 수 있습니다.

- **Automation 블레이드**

    자동화 규칙은 새 **자동화** 블레이드에서 중앙에서 관리할 수 있습니다 (플레이 **북** 블레이드를 대체 하는 **자동화 규칙** 탭). (이제 플레이 **북** 탭에서이 블레이드의 플레이 북을 관리할 수도 있습니다.) 여기에서 새 자동화 규칙을 만들고 기존 규칙을 편집할 수 있습니다. Automation 규칙을 끌어 실행 순서를 변경 하 고 사용 하거나 사용 하지 않도록 설정할 수도 있습니다.

    **Automation** 블레이드에서 작업 영역에 정의 된 모든 규칙의 상태 (사용/사용 안 함) 및 적용 되는 분석 규칙을 볼 수 있습니다.

    많은 분석 규칙에 적용 되는 자동화 규칙이 필요한 경우 **automation** 블레이드에서 직접 만듭니다. 상단 메뉴에서 **만들기** 를 클릭 하 고 **새 규칙 추가** 를 클릭 하 여 **새 자동화 규칙 만들기** 패널을 엽니다. 여기에서 규칙을 완전히 유연 하 게 구성할 수 있습니다. 이러한 규칙을 모든 분석 규칙 (향후 포함)에 적용 하 고 가장 넓은 범위의 조건 및 작업을 정의할 수 있습니다.

- **분석 규칙 마법사**

    분석 규칙 마법사의 **자동화 된 응답** 탭에서는 마법사에서 만들거나 편집 중인 특정 분석 규칙에 적용 되는 자동화 규칙을 보고 관리 하 고 만들 수 있습니다.

    **분석** 블레이드의 상단 메뉴에서 **만들기** 를 클릭 하 고 규칙 유형 (**예약 된 쿼리 규칙** 또는 **Microsoft 인시던트 생성 규칙**) 중 하나를 클릭 하거나 기존 분석 규칙을 선택 하 고 **편집** 을 클릭 하면 규칙 마법사를 엽니다. **자동화 된 응답** 탭을 선택 하면 **인시던트 자동화** 라는 섹션이 표시 됩니다 .이 섹션에는 현재이 규칙에 적용 되는 automation 규칙이 표시 됩니다. 기존 automation 규칙을 선택 하 여 편집 하거나 **새로 추가** 를 클릭 하 여 새 규칙을 만들 수 있습니다.

    이 규칙이 이미 마법사에서 편집 중인 분석 규칙에만 적용 되도록 설정 되어 있으므로 여기에서 automation 규칙을 만들 때 **새 자동화 규칙 만들기** 패널에서 **분석 규칙** 조건을 사용할 수 없음으로 표시 합니다. 다른 모든 구성 옵션은 계속 사용할 수 있습니다.

- **인시던트 블레이드**

    단일 되풀이 인시던트에 응답 하기 위해 **인시던트** 블레이드에서 automation 규칙을 만들 수도 있습니다. 이는 "잡음이 있는" 인시던트를 자동으로 닫기 위한 [비 표시 규칙](#incident-suppression) 을 만드는 경우에 유용 합니다. 큐에서 인시던트를 선택 하 고 최상위 메뉴에서 **automation 규칙 만들기** 를 클릭 합니다.

    **새 자동화 규칙 만들기** 패널에서 인시던트의 값으로 모든 필드를 채운 것을 알 수 있습니다. 규칙 이름을 인시던트의 이름과 동일 하 게 하 고, 인시던트를 생성 한 분석 규칙에 적용 하 고, 인시던트에 사용 가능한 모든 엔터티를 규칙의 조건으로 사용 합니다. 또한 기본적으로 비 표시 (닫기) 작업을 제안 하 고 규칙의 만료 날짜를 제안 합니다. 조건 및 작업을 추가 또는 제거 하 고 원하는 대로 만료 날짜를 변경할 수 있습니다.

## <a name="auditing-automation-rule-activity"></a>감사 자동화 규칙 작업

지정 된 인시던트에 어떤 일이 발생 했는지와 특정 automation 규칙이이를 수행 하거나 수행 하지 않을 수 있는 작업을 확인 하는 데 관심이 있을 수 있습니다. **로그** 블레이드의 *securityincident* 테이블에서 사용할 수 있는 인시던트 기록이의 전체 기록을 사용할 수 있습니다. 다음 쿼리를 사용 하 여 모든 automation 규칙 활동을 볼 수 있습니다.

```kusto
SecurityIncident
| where ModifiedBy contains "Automation"
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 automation 규칙을 사용 하 여 Azure 센티널 인시던트 큐를 관리 하 고 몇 가지 기본적인 인시던트 처리 자동화를 구현 하는 방법을 배웠습니다.

- 고급 자동화 옵션에 대 한 자세한 내용은 [Azure 센티널에서 플레이 북을 사용 하 여 위협 대응 자동화](automate-responses-with-playbooks.md)를 참조 하세요.
- Automation 규칙 및 플레이 북 구현에 대 한 도움말은 [자습서: 플레이 북를 사용 하 여 Azure 센티널에서 위협 응답 자동화](tutorial-respond-threats-playbook.md)를 참조 하세요.

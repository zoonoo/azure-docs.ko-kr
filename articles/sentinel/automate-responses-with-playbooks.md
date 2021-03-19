---
title: Azure 센티널에서 플레이 북을 사용 하 여 위협 대응 자동화 | Microsoft Docs
description: 이 문서에서는 Azure 센티널의 자동화에 대해 설명 하 고, 플레이 북을 사용 하 여 위협 방지 및 응답을 자동화 하는 방법을 보여 줍니다.
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
ms.date: 03/17/2021
ms.author: yelevin
ms.openlocfilehash: 0158c9f5b9debf0c47978e816951e25634621645
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609808"
---
# <a name="automate-threat-response-with-playbooks-in-azure-sentinel"></a>Azure 센티널에서 플레이 북을 사용 하 여 위협 대응 자동화

이 문서에서는 Azure 센티널 플레이 북에 대해 설명 하 고,이를 사용 하 여 보안 오케스트레이션, Automation and Response (대화 충성도) 작업을 구현 하는 데 시간과 리소스를 절약 하는 동시에 더 나은 결과를 얻을 수 있는 방법을 설명 합니다.

## <a name="what-is-a-playbook"></a>플레이 북 무엇 인가요?

SIEM/SOC 팀은 일반적으로 정기적으로 보안 경고 및 인시던트를 사용 하 여 사용 가능한 인력이 다양. 이는 대부분의 경고가 무시 되 고 많은 인시던트가 조사 되지 않는 상황에서 대부분의 경우를 발생 시킬 수 있습니다 .이는 조직이 발견 되지 않는 공격에 취약 한 상태로 유지 됩니다.

대부분의 경우 이러한 경고 및 인시던트는 수정 작업의 특정 및 정의 된 집합으로 해결할 수 있는 반복 패턴을 따릅니다.

플레이 북는 Azure 센티널에서 루틴으로 실행할 수 있는 이러한 수정 작업의 컬렉션입니다. 플레이 북는 위협 응답을 자동화 하 고 오케스트레이션 하는 데 도움이 됩니다. 수동으로 실행 하거나, 분석 규칙이 나 automation 규칙에 의해 트리거될 때 특정 경고나 인시던트에 따라 자동으로 실행 되도록 설정할 수 있습니다.

플레이 북은 구독 수준에서 만들어지고 적용 되지만, 플레이 **북** 탭 (새 **자동화** 블레이드에서)은 선택한 구독에서 사용할 수 있는 모든 플레이 북을 표시 합니다.

### <a name="azure-logic-apps-basic-concepts"></a>Azure Logic Apps 기본 개념

Azure 센티널의 플레이 북은 기업 전체 시스템에서 작업 및 워크플로를 예약, 자동화 및 오케스트레이션 하는 데 도움이 되는 클라우드 서비스인 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)에서 빌드된 워크플로를 기반으로 합니다. 즉, 플레이 북은 Logic Apps ' 기본 제공 템플릿의 모든 기능과 사용자 지정 가능성을 활용할 수 있습니다.

> [!NOTE]
> Azure Logic Apps 별도의 리소스 이기 때문에 추가 요금이 적용 될 수 있습니다. 자세한 내용은 [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) 가격 책정 페이지를 참조 하세요.

Azure Logic Apps는 커넥터를 사용 하 여 다른 시스템 및 서비스와 통신 합니다. 커넥터 및 몇 가지 중요 한 특성에 대 한 간략 한 설명은 다음과 같습니다.

- **관리 커넥터:** 특정 제품 또는 서비스에 대 한 API 호출을 래핑하는 작업 및 트리거 집합입니다. Azure Logic Apps는 Microsoft 및 타사 서비스와 통신할 수 있는 수백 개의 커넥터를 제공 합니다.
  - [모든 Logic Apps 커넥터 및 해당 설명서의 목록](/connectors/connector-reference/)

- **사용자 지정 커넥터:** 미리 빌드된 커넥터로 사용할 수 없는 서비스와 통신 하는 것이 좋습니다. 사용자 지정 커넥터는 커넥터를 만들고 공유 하 고 자체 트리거와 작업을 정의할 수 있도록 하 여 이러한 요구를 해결 합니다.
  - [사용자 고유의 사용자 지정 Logic Apps 커넥터 만들기](/connectors/custom-connectors/create-logic-apps-connector)

- **Azure 센티널 커넥터:** Azure 센티널과 상호 작용 하는 플레이 북을 만들려면 Azure 센티널 커넥터를 사용 합니다.
  - [Azure 센티널 커넥터 설명서](/connectors/azuresentinel/)

- **트리거:** 플레이 북을 시작 하는 커넥터 구성 요소입니다. 플레이 북가 트리거될 때 수신 하는 데 필요한 스키마를 정의 합니다. Azure 센티널 커넥터에는 현재 두 개의 트리거가 있습니다.
  - [경고 트리거](/connectors/azuresentinel/#triggers): 플레이 북는 경고를 입력으로 받습니다.
  - [인시던트 트리거](/connectors/azuresentinel/#triggers): 플레이 북는 포함 된 모든 경고 및 엔터티와 함께 인시던트를 입력으로 받습니다.

    > [!IMPORTANT]
    >
    > - 플레이 북의 **인시던트 트리거** 기능은 현재 **미리 보기로** 제공 됩니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 추가 약관은 [Microsoft Azure 미리 보기에](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 대 한 추가 사용 약관을 참조 하세요.

- **작업:** 작업은 트리거 후에 발생 하는 모든 단계입니다. 병렬 또는 복합 조건의 매트릭스에서 순차적으로 정렬할 수 있습니다.

- **동적 필드:** 다음 작업에 사용할 수 있는 트리거 및 작업의 출력 스키마에 의해 결정 되 고 실제 출력으로 채워진 임시 필드입니다.

### <a name="permissions-required"></a>필요한 사용 권한

 Secops 팀에 대화 충성도 (보안 오케스트레이션, 자동화 및 응답) 작업에 대 한 Logic Apps를 사용 하는 기능을 제공 합니다. 즉, azure 센티널에서 플레이 북를 만들고 실행 하려면 보안 운영 팀의 특정 멤버나 전체 팀에 azure 역할을 할당할 수 있습니다. 다음은 사용 가능한 여러 역할과 해당 역할에 할당 되어야 하는 태스크에 대 한 설명입니다.

#### <a name="azure-roles-for-logic-apps"></a>Logic Apps에 대 한 Azure 역할

- **논리 앱 참가자** 를 사용 하면 논리 앱을 관리 하 고 플레이 북을 실행할 수 있지만 **소유자** 역할이 필요 하다 면 해당 앱에 대 한 액세스를 변경할 수 없습니다.
- **논리 앱 연산자**  를 사용 하면 논리 앱을 읽고 사용 하지 않도록 설정할 수 있지만 편집 하거나 업데이트할 수는 없습니다.

#### <a name="azure-roles-for-sentinel"></a>센티널 용 Azure 역할

- **Azure 센티널 참여자** 역할을 사용 하 여 플레이 북를 분석 규칙에 연결할 수 있습니다.
- **Azure 센티널 응답기** 역할을 사용 하면 플레이 북를 수동으로 실행할 수 있습니다.
- **Azure 센티널 Automation 기여자** 를 사용 하면 자동화 규칙에서 플레이 북을 실행할 수 있습니다. 다른 용도로는 사용되지 않습니다.

#### <a name="learn-more"></a>자세한 정보

- [Azure Logic Apps의 Azure 역할에 대해 자세히 알아보세요](../logic-apps/logic-apps-securing-a-logic-app.md#access-to-logic-app-operations).
- Azure [센티널의 azure 역할에 대해 자세히 알아보세요](roles.md).

## <a name="steps-for-creating-a-playbook"></a>플레이 북를 만드는 단계

- [자동화 시나리오를 정의](#use-cases-for-playbooks)합니다.

- [Azure 논리 앱을 빌드](tutorial-respond-threats-playbook.md)하세요.

- [논리 앱을 테스트](#run-a-playbook-manually-on-an-alert)합니다.

- 플레이 북를 [automation 규칙](#incident-creation-automated-response) 또는 [분석 규칙](#alert-creation-automated-response)에 연결 하거나 [필요할 때 수동으로 실행](#run-a-playbook-manually-on-an-alert)합니다.

### <a name="use-cases-for-playbooks"></a>플레이 북의 사용 사례

Azure Logic Apps 플랫폼은 수백 개의 작업과 트리거를 제공 하므로 거의 모든 자동화 시나리오를 만들 수 있습니다. Azure 센티널은 다음 SOC 시나리오부터 시작 하는 것이 좋습니다.

#### <a name="enrichment"></a>보강

**더 나은 결정을 내릴 수 있도록 데이터를 수집 하 고 인시던트에 연결 합니다.**

예를 들면 다음과 같습니다.

IP 주소 엔터티를 생성 하는 분석 규칙에 의해 경고에서 Azure 센티널 인시던트가 생성 되었습니다.

인시던트는 다음 단계에 따라 플레이 북를 실행 하는 자동화 규칙을 트리거합니다.

- [새 Azure 센티널 인시던트가 생성](/connectors/azuresentinel/#triggers)될 때 시작 합니다. 인시던트에 표시 되는 엔터티는 인시던트 트리거의 동적 필드에 저장 됩니다.

- 각 IP 주소에 대해 [바이러스 합계](https://www.virustotal.com/)와 같은 외부 위협 인텔리전스 공급자를 쿼리하여 더 많은 데이터를 검색 합니다.

- 반환 된 데이터 및 정보를 인시던트의 주석으로 추가 합니다.

#### <a name="bi-directional-sync"></a>양방향 동기화

**플레이 북을 사용 하 여 Azure 센티널 인시던트를 다른 티켓 시스템과 동기화 할 수 있습니다.**

예를 들면 다음과 같습니다.

모든 인시던트 생성에 대 한 automation 규칙을 만들고 ServiceNow에서 티켓을 여는 플레이 북를 연결 합니다.

- [새 Azure 센티널 인시던트가 생성](/connectors/azuresentinel/#triggers)될 때 시작 합니다.

- [ServiceNow](/connectors/service-now/)에서 새 티켓을 만듭니다.

- 간편한 피벗을 위해 인시던트 이름, 중요 한 필드 및 Azure 센티널 인시던트에 대 한 URL을 티켓에 포함 합니다.

#### <a name="orchestration"></a>오케스트레이션

**SOC 채팅 플랫폼을 사용 하 여 인시던트 큐를 보다 효과적으로 제어할 수 있습니다.**

예를 들면 다음과 같습니다.

사용자 이름 및 IP 주소 엔터티를 생성 하는 분석 규칙에 의해 경고에서 Azure 센티널 인시던트가 생성 되었습니다.

인시던트는 다음 단계에 따라 플레이 북를 실행 하는 자동화 규칙을 트리거합니다.

- [새 Azure 센티널 인시던트가 생성](/connectors/azuresentinel/#triggers)될 때 시작 합니다.

- [Microsoft 팀](/connectors/teams/) 또는 [여유 시간](/connectors/slack/) 에 보안 작업 채널로 메시지를 보내 보안 분석가가 인시던트를 인식 하는지 확인 합니다.

- 선임 네트워크 관리자 및 보안 관리자에 게 전자 메일로 경고의 모든 정보를 보냅니다. 전자 메일 메시지에는 사용자 옵션 **차단** 및 **무시** 단추가 포함 됩니다.

- 관리자 로부터 응답을 받을 때까지 기다렸다가 계속 실행 합니다.

- 관리자가 **차단** 을 선택한 경우 방화벽에 명령을 보내 경고의 IP 주소를 차단 하 고 다른 사용자를 사용 하지 않도록 설정 하려면 Azure AD를 사용 합니다.

#### <a name="response"></a>응답

**최소한의 사용자 종속성으로 위협에 즉시 대응 합니다.**

두 가지 예:

**예 1:** [Azure AD ID 보호](../active-directory/identity-protection/overview-identity-protection.md)에서 검색 한 손상 된 사용자를 나타내는 분석 규칙에 응답 합니다.

   - [새 Azure 센티널 인시던트가 생성](/connectors/azuresentinel/#triggers)될 때 시작 합니다.

   - 손상 된 것으로 의심 되는 인시던트의 각 사용자 엔터티에 대해 다음을 수행 합니다.

     - 사용자에 게 의심 스러운 조치를 취한 확인을 요청 하는 팀 메시지를 보냅니다.

     - Azure AD ID 보호 확인 하 여 [사용자의 상태를 손상 된 것으로 확인](/connectors/azureadip/#confirm-a-risky-user-as-compromised)합니다. Azure AD ID 보호는 사용자에 게 **위험** 으로 레이블을 붙이고 이미 구성 된 적용 정책을 적용 합니다. 예를 들어, 다음에 로그인 할 때 사용자에 게 MFA를 사용 하도록 요구 합니다.

        > [!NOTE]
        > 플레이 북는 사용자에 대 한 적용 작업을 시작 하지 않으며 적용 정책 구성도 시작 하지 않습니다. 이미 정의 된 정책을 적절 하 게 적용 Azure AD ID 보호에만 지시 합니다. 모든 적용은 Azure AD ID 보호에서 정의 되는 적절 한 정책에 따라 전적으로 달라 집니다.

**예 2:** [끝점에 대해 Microsoft Defender](/windows/security/threat-protection/)에서 검색 한 손상 된 컴퓨터를 나타내는 분석 규칙에 응답 합니다.

   - [새 Azure 센티널 인시던트가 생성](/connectors/azuresentinel/#triggers)될 때 시작 합니다.

   - Azure 센티널에서 **엔터티-호스트 가져오기** 작업을 사용 하 여 인시던트 엔터티에 포함 된 의심 스러운 컴퓨터를 구문 분석 합니다.

   - Microsoft Defender에서 끝점에 대해 명령을 실행 하 여 경고의 [컴퓨터를 격리](/connectors/wdatp/#actions---isolate-machine) 합니다.

## <a name="how-to-run-a-playbook"></a>플레이 북를 실행 하는 방법

플레이 북은 **수동** 또는 **자동으로** 실행할 수 있습니다.

수동으로 실행한다는 것은 경고를 받으면 선택한 경고에 대한 응답으로 요청 시 플레이북을 실행하도록 선택할 수 있다는 것을 의미합니다. 현재이 기능은 인시던트가 아니라 경고에 대해서만 지원 됩니다.

이러한 작업을 자동으로 실행 하는 것은 분석 규칙의 자동 응답으로 설정 하거나 (인시던트의 경우) automation 규칙의 작업으로 설정 하는 것을 의미 합니다. [Automation 규칙에 대해 자세히 알아보세요](automate-incident-handling-with-automation-rules.md).

### <a name="set-an-automated-response"></a>자동화 된 응답 설정

보안 운영 팀은 반복적인 인시던트 및 경고 유형에 대 한 일상적인 응답을 완벽 하 게 자동화 하 여 작업 부하를 대폭 줄일 수 있으므로 고유한 인시던트 및 경고, 패턴 분석, 위협 요소 파악 등에 집중할 수 있습니다.

자동화 된 응답을 설정 하면 경고를 생성 하는 것 외에도 분석 규칙이 트리거될 때마다 규칙은 플레이 북를 실행 하 여 규칙에 의해 생성 된 경고를 입력으로 받게 됩니다.

경고가 인시던트를 만드는 경우 인시던트는 경고를 통해 생성 된 인시던트를 입력으로 받게 되는 플레이 북을 실행할 수 있는 자동화 규칙을 트리거합니다.

#### <a name="alert-creation-automated-response"></a>경고 생성 자동화 된 응답

경고 만들기로 트리거된 재생 북의 경우 경고를 입력으로 수신 하 고 (첫 번째 단계는 "Azure 센티널 경고가 트리거될 때) 플레이 북를 분석 규칙에 연결 합니다.

1. 자동화 된 응답을 정의 하려는 경고를 생성 하는 [분석 규칙](tutorial-detect-threats-custom.md) 을 편집 합니다.

1. **자동화 된 응답** 탭의 **경고 자동화** 에서, 경고가 생성 될 때이 분석 규칙에서 트리거할 플레이 북 또는 플레이 북를 선택 합니다.

#### <a name="incident-creation-automated-response"></a>인시던트 생성 자동화 된 응답

인시던트 생성 및 수신 인시던트를 입력으로 트리거한 플레이 북의 경우 (첫 번째 단계는 "Azure 센티널 인시던트가 트리거될 때") automation 규칙을 만들고 여기에서 **플레이 북 실행** 작업을 정의 합니다. 이 작업은 다음 두 가지 방법으로 수행할 수 있습니다.

- 자동화 된 응답을 정의 하려는 인시던트를 생성 하는 분석 규칙을 편집 합니다. 자동화 된 **응답** 탭의 **인시던트 자동화** 에서 자동화 규칙을 만듭니다. 그러면이 분석 규칙에 대해서만 자동화 된 응답이 생성 됩니다.

- **Automation** 블레이드의 **automation 규칙** 탭에서 새 자동화 규칙을 만들고 적절 한 조건 및 원하는 작업을 지정 합니다. 이 자동화 규칙은 지정 된 조건을 충족 하는 모든 분석 규칙에 적용 됩니다.

    > [!NOTE]
    > Automation 규칙에서 플레이 북를 실행 하기 위해 Azure 센티널은 특별히 권한이 부여 된 서비스 계정을 사용 합니다. 사용자 계정과 달리이 계정을 사용 하면 서비스의 보안 수준을 높이고 automation 규칙 API를 사용 하 여 CI/CD 사용 사례를 지원할 수 있습니다.
    >
    > 이 계정에는 플레이 북 있는 리소스 그룹에 대 한 명시적 권한을 부여 해야 합니다. 이 시점에서 모든 automation 규칙은 해당 리소스 그룹에서 모든 플레이 북을 실행할 수 있습니다.
    >
    > **플레이 북 실행** 작업을 자동화 규칙에 추가 하면 플레이 북의 드롭다운 목록이 표시 됩니다. Azure 센티널에 권한이 없는 플레이 북은 사용할 수 없는 것으로 표시 됩니다 ("회색으로 표시 됨"). **플레이 북 권한 관리** 링크를 선택 하 여 해당 지점에서 Azure 센티널에 사용 권한을 부여할 수 있습니다.
    >
    > 다중 테 넌 트 ([Lighthouse](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)) 시나리오에서 플레이 북를 호출 하는 automation 규칙이 다른 테 넌 트에 있는 경우에도 플레이 북가 있는 테 넌 트에 대 한 권한을 정의 해야 합니다. 이렇게 하려면 플레이 북의 리소스 그룹에 대 한 **소유자** 권한이 있어야 합니다.

[Automation 규칙을 만드는 방법에 대 한](tutorial-respond-threats-playbook.md#respond-to-incidents)자세한 내용은을 참조 하세요.

### <a name="run-a-playbook-manually-on-an-alert"></a>경고에서 수동으로 플레이 북 실행

다음 블레이드의 Azure 센티널 포털에서 수동 트리거를 사용할 수 있습니다.

- **인시던트** 보기에서 특정 인시던트를 선택 하 고, **경고** 탭을 열고, 경고를 선택 합니다.

- **조사** 에서 특정 경고를 선택 합니다.

1. 선택한 경고에 대 한 플레이 **북 보기** 를 클릭 합니다. **Azure 센티널 경고가 트리거되고** 액세스할 수 있는 경우로 시작 하는 모든 플레이 북의 목록이 표시 됩니다.

1. 특정 플레이 북의 줄에서 **실행** 을 클릭 하 여 트리거합니다.

1. **실행** 탭을 선택 하 여이 경고에 대해 플레이 북가 실행 된 모든 시간 목록을 표시 합니다. 이 목록에는 완료 된 모든 실행이 표시 되는 데 몇 초 정도 걸릴 수 있습니다.

1. 특정 실행을 클릭 하면 전체 실행 로그가 Logic Apps 열립니다.

### <a name="run-a-playbook-manually-on-an-incident"></a>인시던트에 수동으로 플레이 북 실행

아직 지원 되지 않습니다. <!--make this a note instead? -->

## <a name="manage-your-playbooks"></a>플레이 북 관리

플레이 **북** 탭에는 사용자가 액세스할 수 있는 모든 플레이 북의 목록이 표시 되 고, 현재 Azure에 표시 되는 구독으로 필터링 됩니다. 구독 필터는 전역 페이지 헤더의 **디렉터리 + 구독** 메뉴에서 사용할 수 있습니다.

플레이 북 이름을 클릭 하면 Logic Apps의 플레이 북 기본 페이지로 이동 합니다. **상태** 열에 사용 또는 사용 안 함이 표시 됩니다.

**트리거 종류** 는이 플레이 북를 시작 하는 Logic Apps 트리거를 나타냅니다.

| 트리거 종류 | 플레이 북의 구성 요소 유형을 나타냅니다. |
|-|-|
| **Azure 센티널 인시던트/경고** | 플레이 북는 센티널 트리거 (경고, 인시던트) 중 하나로 시작 됩니다. |
| **Azure 센티널 작업 사용** | 플레이 북는 비 센티널 트리거를 사용 하 여 시작 되지만 Azure 센티널 작업을 사용 합니다. |
| **기타** | 플레이 북에는 센티널 구성 요소가 포함 되어 있지 않습니다. |
| **초기화 안 됨** | 플레이 북가 만들어졌지만 구성 요소 (트리거 또는 작업)를 포함 하지 않습니다. |
|

플레이 북의 논리 앱 페이지에서 플레이 북에 대 한 자세한 정보를 볼 수 있습니다. 여기에는 실행 된 모든 시간에 대 한 로그 및 결과 (성공 또는 실패 및 기타 세부 정보)가 포함 됩니다. 적절 한 권한이 있는 경우 Logic Apps 디자이너를 입력 하 고 플레이 북를 직접 편집할 수도 있습니다.

### <a name="api-connections"></a>API 연결

API 연결은 Logic Apps를 다른 서비스에 연결 하는 데 사용 됩니다. Logic Apps 커넥터에 대해 새 인증을 수행할 때마다 **API 연결** 형식의 새 리소스가 생성 되 고 서비스에 대 한 액세스를 구성할 때 제공 되는 정보가 포함 됩니다.

모든 API 연결을 보려면 Azure Portal의 헤더 검색 상자에 *api 연결* 을 입력 합니다. 관심 있는 열을 기록해 둡니다.

- 표시 이름-연결할 때마다 연결에 제공 하는 "친숙 한" 이름입니다.
- 상태-연결 상태: 오류, 연결 됨을 나타냅니다.
- 리소스 그룹-API 연결은 플레이 북 (Logic Apps) 리소스의 리소스 그룹에 만들어집니다.

API 연결을 보는 또 다른 방법은 **모든 리소스** 블레이드로 이동 하 여 형식 *api 연결* 로 필터링 하는 것입니다. 이러한 방식으로 여러 연결을 한 번에 선택, 태그 지정 및 삭제할 수 있습니다.

기존 연결에 대 한 권한 부여를 변경 하려면 연결 리소스를 입력 하 고 **API 연결 편집** 을 선택 합니다.

## <a name="next-steps"></a>다음 단계

- [자습서: 플레이 북를 사용 하 여 Azure 센티널에서 위협 응답 자동화](tutorial-respond-threats-playbook.md)

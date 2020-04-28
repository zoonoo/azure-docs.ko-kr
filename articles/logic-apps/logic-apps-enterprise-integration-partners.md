---
title: B2B 통합을 위해 거래 업체 추가
description: Azure Logic Apps와 함께 사용할 통합 계정에 거래 파트너 만들기
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: e58cbe85f30ea09adde45d55bb7b80c710c45495
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74792434"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Azure Logic Apps에 대 한 통합 계정에 거래 파트너 추가

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)에서 논리 앱과 [통합 계정을](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 사용 하 여 자동화 된 b2b (기업 간) 통합 워크플로를 만들 수 있습니다. 조직 및 기타를 나타내려면 거래 파트너를 만들어 통합 계정에 아티팩트로 추가 합니다. 파트너는 B2B 트랜잭션에 참여 하 고 서로 메시지를 교환 하는 엔터티입니다.

이러한 파트너를 만들기 전에 다른 사용자가 보내는 메시지를 식별 하 고 유효성을 검사 하는 방법에 대 한 정보를 파트너와 논의 하 고 공유 해야 합니다. 이러한 세부 정보에 동의 하면 통합 계정에 파트너를 만들 준비가 된 것입니다.

## <a name="partner-roles-in-integration-accounts"></a>통합 계정의 파트너 역할

파트너와 교환 되는 메시지에 대 한 세부 정보를 정의 하려면 통합 계정에 대 한 아티팩트로 [규약](../logic-apps/logic-apps-enterprise-integration-agreements.md) 을 만들고 추가 합니다. 규약에는 통합 계정에 둘 이상의 파트너가 필요 합니다. 조직은 항상 규약의 *호스트 파트너* 입니다. 조직에서 메시지를 교환 하는 조직은 *게스트 파트너*입니다. 게스트 파트너는 다른 회사 또는 조직 내 부서 일 수도 있습니다. 이러한 파트너를 추가한 후에 규약을 만들 수 있습니다.

규약에서 호스트 파트너의 관점에서 들어오고 나가는 메시지 처리에 대 한 세부 정보를 지정 합니다. 들어오는 메시지의 경우 **수신 설정은** 호스트 파트너가 규약의 게스트 파트너 로부터 메시지를 수신 하는 방법을 지정 합니다. 보내는 메시지의 경우 **송신 설정은** 호스트 파트너가 게스트 파트너에 게 메시지를 보내는 방법을 지정 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 아직 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 파트너, 규약 및 기타 B2B 아티팩트를 저장 하는 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 입니다. 이 통합 계정은 Azure 구독과 연결 되어야 합니다.

## <a name="create-partner"></a>파트너 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure 주 메뉴에서 **모든 서비스**를 선택합니다. 검색 상자에 "통합"을 입력 하 고 **통합 계정**을 선택 합니다.

   !["통합 계정"을 선택 합니다.](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. **통합 계정**에서 파트너를 추가하려는 통합 계정을 선택합니다.

   ![통합 계정 선택](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. **파트너** 타일을 선택합니다.

   ![“파트너” 타일 선택](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. **파트너**에서 **추가**를 선택합니다. **파트너 추가**에서 아래 표에 설명 된 대로 파트너의 세부 정보를 제공 합니다.

   !["추가"를 선택 하 고 파트너 세부 정보를 제공 합니다.](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | 속성 | 필수 | 설명 |
   |----------|----------|-------------|
   | **이름** | 예 | 파트너의 이름입니다. |
   | **한정자** | 예 | 조직에 고유한 비즈니스 id를 제공 하는 인증 본문 (예: **D-U-N-S (Dun & Bradstreet))**. <p>파트너는 상호 정의 된 비즈니스 id를 선택할 수 있습니다. 이러한 시나리오에서는 EDIFACT에 대해 **상호 정의 됨** **(X12)** 또는 x12에 대해 상호 정의 됨을 선택 합니다. <p>RosettaNet의 경우에는 표준 인 **DUNS**만 선택 합니다. |
   | **값** | 예 | 논리 앱에서 수신 하는 문서를 식별 하는 값입니다. <p>RosettaNet의 경우이 값은 DUNS 번호에 해당 하는 9 자리 숫자 여야 합니다. |
   ||||

   > [!NOTE]
   > RosettaNet를 사용 하는 파트너의 경우 먼저 이러한 파트너를 만든 후 [나중에 편집](#edit-partner)하 여 추가 정보를 지정할 수 있습니다.

1. 작업을 완료하면 **확인**을 선택합니다.

   이제 새 파트너는 **파트너** 목록에 표시 됩니다. 또한 **파트너** 타일은 현재 파트너 수를 업데이트 합니다.

   ![새 파트너](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>파트너 편집

1. [Azure Portal](https://portal.azure.com)에서 통합 계정을 찾고 선택합니다.
**파트너** 타일을 선택합니다.

   ![“파트너” 타일 선택](./media/logic-apps-enterprise-integration-partners/edit.png)

1. **파트너**에서 편집 하려는 파트너를 선택 하 고 **편집**을 선택 합니다. **편집**에서 변경을 수행 합니다.

   ![변경 후 저장](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   RosettaNet의 경우 **RosettaNet Partner 속성**에서 다음과 같은 추가 정보를 지정할 수 있습니다.

   | 속성 | 필수 | 설명 |
   |----------|----------|-------------|
   | **파트너 분류** | 아니요 | 파트너의 조직 유형입니다. |
   | **공급망 코드** | 아니요 | 파트너의 공급망 코드 (예: "정보 기술" 또는 "전자 구성 요소") |
   | **연락처 이름** | 아니요 | 파트너의 연락처 이름입니다. |
   | **Email** | 아니요 | 파트너의 전자 메일 주소 |
   | **팩스** | 아니요 | 파트너의 팩스 번호 |
   | **전화** | 아니요 | 파트너의 전화 번호입니다. |
   ||||

1. 완료 되 면 **확인** 을 선택 하 여 변경 내용을 저장 합니다.

## <a name="delete-partner"></a>파트너 삭제

1. [Azure Portal](https://portal.azure.com)에서 통합 계정을 찾고 선택합니다. **파트너** 타일을 선택합니다.

   ![“파트너” 타일 선택](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. **파트너**에서 삭제할 파트너를 선택합니다. **삭제**를 선택 합니다.

   ![파트너 삭제](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>다음 단계

* [규약](../logic-apps/logic-apps-enterprise-integration-agreements.md) 에 대 한 자세한 정보
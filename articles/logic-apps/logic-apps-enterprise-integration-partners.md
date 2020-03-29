---
title: B2B 통합을 위한 거래 파트너 추가
description: Azure 논리 앱과 함께 사용할 통합 계정에서 거래 파트너 만들기
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: e58cbe85f30ea09adde45d55bb7b80c710c45495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792434"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Azure 논리 앱의 통합 계정에 거래 파트너 추가

[Azure Logic Apps에서](../logic-apps/logic-apps-overview.md)논리 앱과의 [통합 계정을](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 사용하여 B2B(비즈니스 간) 통합 워크플로를 자동화된 B2B로 만들 수 있습니다. 조직 및 다른 사람을 나타내기 위해 거래 파트너를 만들고 통합 계정에 아티팩트로 추가합니다. 파트너는 B2B 트랜잭션에 참여하고 서로 메시지를 교환하는 엔터티입니다.

이러한 파트너를 만들기 전에 다른 파트너가 보내는 메시지를 식별하고 유효성을 검사하는 방법에 대해 파트너와 논의하고 공유해야 합니다. 이러한 세부 정보에 동의하면 통합 계정에서 파트너를 만들 준비가 된 것입니다.

## <a name="partner-roles-in-integration-accounts"></a>통합 계정의 파트너 역할

파트너와 교환된 메시지에 대한 세부 정보를 정의하려면 [계약을](../logic-apps/logic-apps-enterprise-integration-agreements.md) 만들고 통합 계정에 아티팩트로 추가합니다. 계약에는 통합 계정에 두 명 이상의 파트너가 필요합니다. 조직은 항상 계약의 *호스트 파트너입니다.* 조직과 메시지를 교환하는 조직은 *게스트 파트너입니다.* 게스트 파트너는 다른 회사이거나 조직의 부서일 수도 있습니다. 이러한 파트너를 추가한 후에 규약을 만들 수 있습니다.

계약에서 호스트 파트너의 관점에서 수신 및 나가는 메시지를 처리하기 위한 세부 정보를 지정합니다. 들어오는 메시지의 경우 **수신 설정은** 호스트 파트너가 계약의 게스트 파트너로부터 메시지를 받는 방법을 지정합니다. 나가는 메시지의 경우 **보내기 설정은** 호스트 파트너가 게스트 파트너에게 메시지를 보내는 방법을 지정합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 아직 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 파트너, 계약 및 기타 B2B 아티팩트를 저장하기 위한 [통합 계정입니다.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 이 통합 계정은 Azure 구독과 연결되어 있어야 합니다.

## <a name="create-partner"></a>파트너 만들기

1. [Azure 포털에](https://portal.azure.com)로그인합니다.

1. Azure 주 메뉴에서 **모든 서비스**를 선택합니다. 검색 상자에 "통합"을 입력하고 **통합 계정을**선택합니다.

   !["통합 계정"을 선택합니다.](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. **통합 계정**에서 파트너를 추가하려는 통합 계정을 선택합니다.

   ![통합 계정 선택](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. **파트너** 타일을 선택합니다.

   ![“파트너” 타일 선택](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. **파트너**에서 **추가**를 선택합니다. **파트너 추가**에서 아래 표에 설명된 대로 파트너의 세부 정보를 제공합니다.

   !["추가"를 선택하고 파트너 세부 정보 제공](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | 속성 | 필수 | 설명 |
   |----------|----------|-------------|
   | **이름** | yes | 파트너 이름 |
   | **한정자** | yes | 조직에 고유한 비즈니스 ID를 제공하는 인증 기관(예: **D-U-N-S)& 브래드스트리트.** <p>파트너는 상호 정의된 비즈니스 ID를 선택할 수 있습니다. 이러한 시나리오의 경우 EDIFACT에 대해 **상호 정의또는** X12에 대해 **상호 정의(X12)를** 선택합니다. <p>RosettaNet의 경우 표준인 **DUNS만**선택합니다. |
   | **값** | yes | 논리 앱이 받는 문서를 식별하는 값입니다. <p>RosettaNet의 경우 이 값은 DUNS 번호에 해당하는 9자리 숫자여야 합니다. |
   ||||

   > [!NOTE]
   > RosettaNet을 사용하는 파트너의 경우 먼저 이러한 파트너를 만든 다음 [나중에 편집하여](#edit-partner)추가 정보를 지정할 수 있습니다.

1. 작업을 완료하면 **확인**을 선택합니다.

   이제 새 파트너가 **파트너** 목록에 표시됩니다. 또한 **파트너** 타일은 현재 파트너 수를 업데이트합니다.

   ![새로운 파트너](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>파트너 편집

1. [Azure Portal](https://portal.azure.com)에서 통합 계정을 찾고 선택합니다.
**파트너** 타일을 선택합니다.

   ![“파트너” 타일 선택](./media/logic-apps-enterprise-integration-partners/edit.png)

1. **파트너에서**편집할 파트너를 선택하고 **편집을**선택합니다. **편집에서**변경합니다.

   ![변경 후 저장](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   RosettaNet의 경우 **RosettaNet 파트너 속성에서**다음 추가 정보를 지정할 수 있습니다.

   | 속성 | 필수 | 설명 |
   |----------|----------|-------------|
   | **파트너 분류** | 예 | 파트너의 조직 유형 |
   | **공급망 코드** | 예 | 파트너의 공급망 코드(예: "정보 기술" 또는 "전자 부품") |
   | **연락처 이름** | 예 | 파트너의 연락처 이름 |
   | **메일 주소** | 예 | 파트너의 이메일 주소 |
   | **팩스** | 예 | 파트너의 팩스 번호 |
   | **전화** | 예 | 파트너의 전화번호 |
   ||||

1. 작업이 완료되면 **확인을** 선택하여 변경 내용을 저장합니다.

## <a name="delete-partner"></a>파트너 삭제

1. [Azure Portal](https://portal.azure.com)에서 통합 계정을 찾고 선택합니다. **파트너** 타일을 선택합니다.

   ![“파트너” 타일 선택](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. **파트너**에서 삭제할 파트너를 선택합니다. **삭제를 선택합니다.**

   ![파트너 삭제](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>다음 단계

* [계약에](../logic-apps/logic-apps-enterprise-integration-agreements.md) 대해 자세히 알아보기
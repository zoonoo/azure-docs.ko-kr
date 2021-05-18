---
title: B2B 통합을 위한 거래 업체 추가
description: Azure Logic Apps와 함께 사용할 통합 계정에 거래 업체 만들기
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 8e3805fae5bf6cc5ad8cf759d3ba75220c6ddbd8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91565074"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Azure Logic Apps에 대한 통합 계정에 거래 업체 추가

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)에서 논리 앱과 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)을 사용하여 자동화된 B2B(business-to-business) 통합 워크플로를 만들 수 있습니다. 조직 및 기타를 나타내려면 거래 업체를 만들어 통합 계정에 아티팩트로 추가합니다. 거래 업체는 B2B 트랜잭션에 참여하고 서로 메시지를 교환하는 주체입니다.

이러한 파트너를 만들기 전에 다른 사용자가 보내는 메시지를 식별하고 유효성을 검사하는 방법에 대한 정보를 파트너와 논의하고 공유해야 합니다. 이러한 세부 정보에 동의하면 통합 계정에 파트너를 만들 준비가 된 것입니다.

## <a name="partner-roles-in-integration-accounts"></a>통합 계정의 파트너 역할

파트너와 교환되는 메시지에 대한 세부 정보를 정의하려면 통합 계정에 대한 아티팩트로 [규약](../logic-apps/logic-apps-enterprise-integration-agreements.md)을 만들고 추가합니다. 규약에는 통합 계정에 둘 이상의 파트너가 필요합니다. 조직은 항상 규약의 *호스트 파트너* 입니다. 조직에서 메시지를 교환하는 조직은 *게스트 파트너* 입니다. 게스트 파트너는 다른 회사 또는 조직 내 다른 부서일 수 있습니다. 이러한 파트너를 추가한 후에 규약을 만들 수 있습니다.

규약에서 호스트 파트너의 관점에서 수신 및 발신 메시지를 처리하기 위한 세부 정보를 지정합니다. 수신 메시지의 경우 **수신 설정** 은 호스트 파트너가 규약의 게스트 파트너로부터 메시지를 받는 방법을 지정합니다. 발신 메시지의 경우 **발신 설정** 은 호스트 파트너가 규약의 게스트 파트너에게 메시지를 보내는 방법을 지정합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 아직 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 파트너, 규약 및 기타 B2B 아티팩트를 저장하는 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)입니다. 이 통합 계정은 Azure 구독과 연결되어야 합니다.

## <a name="create-partner"></a>파트너 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure 주 메뉴에서 **모든 서비스** 를 선택합니다. 검색 상자에 ‘통합’을 입력하고 **통합 계정** 을 선택합니다.

   ![‘통합 계정’ 선택](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. **통합 계정** 에서 파트너를 추가하려는 통합 계정을 선택합니다.

   ![통합 계정 선택](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. **파트너** 타일을 선택합니다.

   ![파트너 타일을 보여주는 스크린샷입니다.](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. **파트너** 에서 **추가** 를 선택합니다. **파트너 추가** 에서 아래 표에 설명된 대로 파트너의 세부 정보를 제공합니다.

   ![‘추가’선택 및 파트너 세부 정보 제공](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **이름** | 예 | 파트너 이름 |
   | **한정자** | 예 | 조직에 고유한 비즈니스 ID를 제공하는 인증 기관(예: **D-U-N-S(Dun &amp; Bradstreet))** 입니다. <p>파트너는 상호 정의된 비즈니스 ID를 선택할 수 있습니다. 이런 시나리오에서는 **상호 정의됨**(EDIFACT의 경우) 또는 **상호 정의됨(X12)** (X12의 경우)를 선택합니다. <p>RosettaNet의 경우에는 표준인 **DUNS** 만 선택합니다. |
   | **값** | 예 | 논리 앱에서 수신하는 문서를 식별하는 값입니다. <p>RosettaNet의 경우 이 값은 DUNS 번호에 해당하는 9자리 숫자여야 합니다. |
   ||||

   > [!NOTE]
   > RosettaNet을 사용하는 파트너의 경우 먼저 이러한 파트너를 만든 다음 [나중에 편집하여](#edit-partner) 추가 정보를 지정할 수 있습니다.

1. 작업을 완료하면 **확인** 을 선택합니다.

   이제 새 파트너가 **파트너** 목록에 표시됩니다. 또한 **파트너** 타일은 현재 파트너 수를 업데이트합니다.

   ![새 파트너](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>파트너 편집

1. [Azure Portal](https://portal.azure.com)에서 통합 계정을 찾고 선택합니다.
**파트너** 타일을 선택합니다.

   ![“파트너” 타일 선택](./media/logic-apps-enterprise-integration-partners/edit.png)

1. **파트너** 에서 편집할 파트너를 선택하고 **편집** 을 선택합니다. **편집** 에서 변경을 수행합니다.

   ![변경 후 저장](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   RosettaNet의 경우 **RosettaNet 파트너 속성** 에서 다음 추가 정보를 지정할 수 있습니다.

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **파트너 분류** | 예 | 파트너의 조직 유형 |
   | **공급망 코드** | 예 | 파트너의 공급망 코드(예: ‘정보 기술’ 또는 ‘전자 구성 요소’) |
   | **담당자 이름** | 예 | 파트너의 담당자 이름 |
   | **Email** | 예 | 파트너의 이메일 주소 |
   | **Fax** | 예 | 파트너의 팩스 번호 |
   | **Telephone** | 예 | 파트너의 전화 번호 |
   ||||

1. 선택을 마치면 **확인** 을 선택하여 변경 내용을 저장합니다.

## <a name="delete-partner"></a>파트너 삭제

1. [Azure Portal](https://portal.azure.com)에서 통합 계정을 찾고 선택합니다. **파트너** 타일을 선택합니다.

   ![파트너를 삭제할 때 선택한 파트너 타일을 보여 주는 스크린샷입니다.](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. **파트너** 에서 삭제할 파트너를 선택합니다. **삭제** 를 선택합니다.

   ![파트너 삭제](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>다음 단계

* [규약](../logic-apps/logic-apps-enterprise-integration-agreements.md)에 대한 자세한 정보
---
title: 거래 업체 계약
description: Azure Logic 앱 및 엔터프라이즈 통합 팩을 사용하여 거래 업체 간의 계약 생성 및 관리
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 521a0ef4053be55e6c7322da5af26ccfc6c844e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790731"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>Azure 논리 앱에서 거래 업체 계약 생성 및 관리

[거래 업체](../logic-apps/logic-apps-enterprise-integration-partners.md) 
*계약은* B2B(비즈니스 간) 메시지를 교환할 때 사용할 특정 산업 표준 프로토콜을 정의하여 조직과 기업이 원활하게 통신할 수 있도록 지원합니다. 계약은 다음과 같은 일반적인 이점을 제공합니다.

* 조직에서 잘 알려진 형식을 사용하여 정보를 교환할 수 있습니다.
* B2B 트랜잭션을 수행할 때 효율성을 개선합니다.
* 엔터프라이즈 통합 솔루션을 구축하기 위해 쉽게 만들, 관리 및 사용할 수 있습니다.

이 문서에서는 [엔터프라이즈 통합 팩](../logic-apps/logic-apps-enterprise-integration-overview.md) 및 Azure 논리 앱을 사용하여 B2B 시나리오에 대한 엔터프라이즈 통합 솔루션을 빌드할 때 사용할 수 있는 AS2, EDIFACT 또는 X12 계약을 만드는 방법을 보여 [주며](../logic-apps/logic-apps-overview.md)있습니다. 계약을 만든 후 AS2, EDIFACT 또는 X12 커넥터를 사용하여 B2B 메시지를 교환할 수 있습니다.

RosettaNet 메시지 교환에 대한 계약을 만들려면 [Exchange RosettaNet 메시지를](../logic-apps/logic-apps-enterprise-integration-rosettanet.md)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 아직 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 계약 및 기타 B2B 아티팩트를 저장하기 위한 [통합 계정입니다.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 이 통합 계정은 Azure 구독과 연결되어 있어야 합니다.

* 통합 계정에서 이미 만든 [거래 파트너가](../logic-apps/logic-apps-enterprise-integration-partners.md) 두 개 이상 있습니다. 계약을 체결하려면 호스트 파트너와 게스트 파트너가 모두 필요합니다. 두 파트너는 AS2, X12 또는 EDIFACT와 같이 만들려는 계약과 동일한 "비즈니스 ID" 한정자를 사용해야 합니다.

* 선택 사항: 계약을 사용하려는 논리 앱과 논리 앱의 워크플로를 시작하는 트리거입니다. 통합 계정과 B2B 아티팩트를 만들려면 논리 앱이 필요하지 않습니다. 그러나 논리 앱이 통합 계정에서 B2B 아티팩트를 사용하려면 먼저 통합 계정을 논리 앱에 연결해야 합니다. 논리 앱을 처음 사용하는 경우 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토합니다.

## <a name="create-agreements"></a>규약 만들기

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
Azure 주 메뉴에서 **모든 서비스**를 선택합니다. 검색 상자에 필터로 "통합"을 입력합니다. 결과에서 이 리소스: **통합 계정을** 선택합니다.

   ![통합 계정 찾기](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. **통합 계정에서**계약을 만들 통합 계정을 선택합니다.

   ![규약을 만들 통합 계정 선택](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. 오른쪽 창에서 **구성 요소**아래에서 **계약** 타일을 선택합니다.

   !["계약"을 선택합니다.](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. **규약**에서 **추가**를 선택합니다. **추가** 창에서 계약에 대한 정보를 제공합니다.

   !["추가" 선택](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | 속성 | 필수 | 값 | 설명 |
   |----------|----------|-------|-------------|
   | **이름** | yes | <*계약 이름*> | 계약서의 이름 |
   | **계약 유형** | yes | **AS2**, **X12,** 또는 **EDIFACT** | 계약의 프로토콜 유형입니다. 계약 파일을 만들 때 해당 파일의 콘텐츠가 계약 유형과 일치해야 합니다. | |  
   | **호스트 파트너** | yes | <*호스트 파트너 이름*> | 호스트 파트너는 계약을 지정하는 조직을 나타냅니다. |
   | **호스트 ID** | yes | <*호스트 파트너 식별자*> | 호스트 파트너의 식별자 |
   | **게스트 파트너** | yes | <*게스트 파트너 이름*> | 게스트 파트너는 호스트 파트너와 비즈니스를 수행하는 조직을 나타냅니다. |
   | **게스트 ID** | yes | <*게스트 파트너 식별자*> | 게스트 파트너의 식별자 |
   | **수신 설정** | 다양함 | 다양함 | 이러한 속성은 호스트 파트너가 계약의 게스트 파트너로부터 들어오는 모든 메시지를 받는 방법을 지정합니다. 자세한 내용은 해당 계약 유형을 참조하십시오. <p>- [AS2 메시지 설정](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT 메시지 설정](logic-apps-enterprise-integration-edifact.md) <br>- [X12 메시지 설정](logic-apps-enterprise-integration-x12.md) |
   | **송신 설정** | 다양함 | 다양함 | 이러한 속성은 호스트 파트너가 계약의 게스트 파트너에게 보내는 모든 메시지를 보내는 방법을 지정합니다. 자세한 내용은 해당 계약 유형을 참조하십시오. <p>- [AS2 메시지 설정](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT 메시지 설정](logic-apps-enterprise-integration-edifact.md) <br>- [X12 메시지 설정](logic-apps-enterprise-integration-x12.md) |
   |||||

1. 계약 만들기가 완료되면 **추가** 페이지에서 **확인을**선택하고 통합 계정으로 돌아갑니다.

   이제 **계약** 목록에 새 계약이 표시됩니다.

## <a name="edit-agreements"></a>계약 편집

1. Azure [포털에서](https://portal.azure.com)기본 Azure 메뉴에서 **모든 서비스를**선택합니다.

1. 검색 상자에 필터로 "통합"을 입력합니다. 결과에서 이 리소스: **통합 계정을** 선택합니다.

1. **통합 계정에서**편집할 계약이 있는 통합 계정을 선택합니다.

1. 오른쪽 창에서 **구성 요소**아래에서 **계약** 타일을 선택합니다.

1. **계약에**따라 계약을 선택하고 **편집을**선택합니다.

1. 변경 내용을 수정한 다음 저장합니다.

## <a name="delete-agreements"></a>계약 삭제

1. Azure [포털에서](https://portal.azure.com)기본 Azure 메뉴에서 **모든 서비스를**선택합니다.

1. 검색 상자에 필터로 "통합"을 입력합니다. 결과에서 이 리소스: **통합 계정을** 선택합니다.

1. **통합 계정에서**삭제하려는 계약이 있는 통합 계정을 선택합니다.

1. 오른쪽 창에서 **구성 요소**아래에서 **계약** 타일을 선택합니다.

1. **계약에**따라 계약을 선택하고 **삭제를**선택합니다.

1. 선택한 규약을 삭제할지 확인합니다.

## <a name="next-steps"></a>다음 단계

* [AS2 메시지 교환](logic-apps-enterprise-integration-as2.md)
* [EDIFACT 메시지 교환](logic-apps-enterprise-integration-edifact.md)
* [X12 메시지 교환](logic-apps-enterprise-integration-x12.md)

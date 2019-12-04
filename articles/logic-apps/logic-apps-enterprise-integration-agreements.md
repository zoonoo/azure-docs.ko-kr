---
title: 거래 업체 규약
description: Azure Logic Apps 및 엔터프라이즈 통합 팩를 사용 하 여 거래 업체 간의 규약 만들기 및 관리
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 521a0ef4053be55e6c7322da5af26ccfc6c844e5
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790731"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>Azure Logic Apps에서 거래 업체 규약 만들기 및 관리

[거래 업체](../logic-apps/logic-apps-enterprise-integration-partners.md) 
*규약* 은 b2b (기업 간) 메시지를 교환할 때 사용할 특정 산업 표준 프로토콜을 정의 하 여 조직과 기업이 서로 원활 하 게 통신할 수 있도록 합니다. 규약은 다음과 같은 일반적인 이점을 제공 합니다.

* 조직에서 잘 알려진 형식을 사용 하 여 정보를 교환할 수 있도록 합니다.
* B2B 트랜잭션을 수행할 때 효율성 향상
* 엔터프라이즈 통합 솔루션 빌드를 위해 쉽게 만들고 관리 하 고 사용할 수 있습니다.

이 문서에서는 [엔터프라이즈 통합 팩](../logic-apps/logic-apps-enterprise-integration-overview.md) 및 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)를 사용 하 여 B2B 시나리오에 대 한 엔터프라이즈 통합 솔루션을 빌드할 때 사용할 수 있는 AS2, EDIFACT 또는 X12 규약을 만드는 방법을 보여 줍니다. 규약을 만든 후에는 AS2, EDIFACT 또는 X12 커넥터를 사용 하 여 B2B 메시지를 교환할 수 있습니다.

RosettaNet 메시지 교환에 대 한 규약을 만들려면 [Exchange RosettaNet 메시지](../logic-apps/logic-apps-enterprise-integration-rosettanet.md)를 참조 하세요.

## <a name="prerequisites"></a>전제 조건

* Azure 구독. 아직 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 규약 및 기타 B2B 아티팩트를 저장 하는 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 입니다. 이 통합 계정은 Azure 구독과 연결 되어야 합니다.

* 통합 계정에 이미 만든 두 개 이상의 [거래 업체](../logic-apps/logic-apps-enterprise-integration-partners.md) 규약에는 호스트 파트너와 게스트 파트너가 모두 필요 합니다. 두 파트너 모두 AS2, X12 또는 EDIFACT와 같이 만들려는 계약과 동일한 "비즈니스 id" 한정자를 사용 해야 합니다.

* 선택 사항: 논리 앱 워크플로를 시작 하는 트리거와 계약을 사용 하려는 논리 앱입니다. 통합 계정 및 B2B 아티팩트만 만들려면 논리 앱이 필요 하지 않습니다. 그러나 논리 앱이 통합 계정에서 B2B 아티팩트를 사용 하려면 먼저 통합 계정을 논리 앱에 연결 해야 합니다. 논리 앱을 처음 사용하는 경우 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토합니다.

## <a name="create-agreements"></a>규약 만들기

1. [Azure portal](https://portal.azure.com)에 로그인합니다.
Azure의 주 메뉴에서 **모든 서비스**를 선택합니다. 검색 상자에서 필터로 "통합"을 입력 합니다. 결과에서 다음 리소스: **통합 계정** 을 선택 합니다.

   ![통합 계정 찾기](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. **통합 계정**에서 규약을 만들려는 통합 계정을 선택 합니다.

   ![규약을 만들 통합 계정 선택](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. 오른쪽 창의 **구성 요소**에서 **규약** 타일을 선택 합니다.

   !["규약"을 선택 합니다.](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. **규약**에서 **추가**를 선택합니다. **추가** 창에서 규약에 대 한 정보를 제공 합니다. 예를 들면 다음과 같습니다.

   !["추가" 선택](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | 자산 | 필수 | Value | 설명 |
   |----------|----------|-------|-------------|
   | **Name** | yes | <*규약-이름*> | 규약의 이름 |
   | **계약 유형** | yes | **AS2**, **X12**또는 **EDIFACT** | 규약에 대 한 프로토콜 유형입니다. 규약 파일을 만들 때 해당 파일의 내용이 규약 유형과 일치 해야 합니다. | |  
   | **호스트 파트너** | yes | <*호스트-이름*> | 호스트 파트너는 규약을 지정 하는 조직을 나타냅니다. |
   | **호스트 Id** | yes | <*호스트-파트너 식별자*> | 호스트 파트너의 식별자입니다. |
   | **게스트 파트너** | yes | <*게스트 파트너 이름*> | 게스트 파트너는 호스트 파트너와 비즈니스를 수행하는 조직을 나타냅니다. |
   | **게스트 Id** | yes | <*게스트-파트너 식별자*> | 게스트 파트너의 식별자입니다. |
   | **수신 설정** | 다름 | 다름 | 이러한 속성은 호스트 파트너가 규약의 게스트 파트너 로부터 들어오는 모든 메시지를 수신 하는 방법을 지정 합니다. 자세한 내용은 해당 계약 유형을 참조 하세요. <p>[AS2 메시지 설정](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) -  <br>[EDIFACT 메시지 설정](logic-apps-enterprise-integration-edifact.md) -  <br>[X12 메시지 설정](logic-apps-enterprise-integration-x12.md) -  |
   | **송신 설정** | 다름 | 다름 | 이러한 속성은 호스트 파트너가 규약의 게스트 파트너에 게 보내는 모든 메시지를 보내는 방법을 지정 합니다. 자세한 내용은 해당 계약 유형을 참조 하세요. <p>[AS2 메시지 설정](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) -  <br>[EDIFACT 메시지 설정](logic-apps-enterprise-integration-edifact.md) -  <br>[X12 메시지 설정](logic-apps-enterprise-integration-x12.md) -  |
   |||||

1. 규약 만들기가 완료 되 면 **추가** 페이지에서 **확인**을 선택 하 여 통합 계정으로 돌아갑니다.

   이제 **계약** 목록에 새 규약이 표시 됩니다.

## <a name="edit-agreements"></a>계약 편집

1. [Azure Portal](https://portal.azure.com)의 기본 Azure 메뉴에서 **모든 서비스**를 선택 합니다.

1. 검색 상자에서 필터로 "통합"을 입력 합니다. 결과에서 다음 리소스: **통합 계정** 을 선택 합니다.

1. **통합 계정**에서 편집 하려는 규약이 있는 통합 계정을 선택 합니다.

1. 오른쪽 창의 **구성 요소**에서 **규약** 타일을 선택 합니다.

1. 규약 **에서 계약**을 선택 하 고 **편집**을 선택 합니다.

1. 만든 다음 변경 내용을 저장 합니다.

## <a name="delete-agreements"></a>계약 삭제

1. [Azure Portal](https://portal.azure.com)의 기본 Azure 메뉴에서 **모든 서비스**를 선택 합니다.

1. 검색 상자에서 필터로 "통합"을 입력 합니다. 결과에서 다음 리소스: **통합 계정** 을 선택 합니다.

1. **통합 계정**에서 삭제 하려는 규약이 있는 통합 계정을 선택 합니다.

1. 오른쪽 창의 **구성 요소**에서 **규약** 타일을 선택 합니다.

1. 규약 **에서 계약**을 선택 하 고 **삭제**를 선택 합니다.

1. 선택한 규약을 삭제할지 확인합니다.

## <a name="next-steps"></a>다음 단계

* [AS2 메시지 교환](logic-apps-enterprise-integration-as2.md)
* [EDIFACT 메시지 교환](logic-apps-enterprise-integration-edifact.md)
* [X12 메시지 교환](logic-apps-enterprise-integration-x12.md)

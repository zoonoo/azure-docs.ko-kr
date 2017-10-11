---
title: "B2B(기업 간) 메시지에 대해 파트너 만들기 - Azure Logic Apps | Microsoft Docs"
description: "엔터프라이즈 통합 팩 및 Logic Apps와 통합 계정에 파트너를 추가하는 방법 알아보기"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 950cb449b53f400f0f0f860caf5415bbb5212269
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="add-or-update-partners-in-business-to-business-agreements-in-your-workflow"></a>워크플로에서 기업 간 규약에 파트너 추가 또는 업데이트

파트너는 B2B(기업 간) 트랜잭션에 참여하고 서로 메시지를 교환하는 주체입니다. 사용자와 이러한 트랜잭션의 다른 조직을 나타내는 파트너를 만들려면 서로 전송한 메시지를 식별하고 유효성을 확인하는 정보를 공유해야 합니다. 이러한 세부 정보를 확인하고 비즈니스 관계를 시작할 준비가 되면 통합 계정에 쌍방을 나타내는 파트너를 만들 수 있습니다.

## <a name="what-roles-do-partners-have-in-your-integration-account"></a>파트너는 통합 계정에서 어떤 역할을 갖나요?

파트너 간에 교환되는 메시지에 대한 세부 정보를 정의하려면 해당 파트너 간의 규약을 만듭니다. 그러나 규약을 만들기 전에 통합 계정에 둘 이상의 파트너를 추가해야 합니다. 조직은 **호스트 파트너**로서 규약에 속해야 합니다. 다른 파트너 또는 **게스트 파트너**는 사용자 조직과 메시지를 교환하는 조직을 나타냅니다. 게스트 파트너는 다른 회사 또는 조직 내 다른 부서일 수 있습니다.

이러한 파트너를 추가한 후에 규약을 만들 수 있습니다.

수신 및 송신 설정은 호스트 파트너의 관점에서 시작됩니다. 예를 들어 규약의 수신 설정은 호스트 파트너가 게스트 파트너로부터 받은 메시지를 수신하는 방법을 결정합니다. 마찬가지로, 규약의 송신 설정은 호스트 파트너가 게스트 파트너에게 메시지를 보내는 방법을 나타냅니다.

## <a name="how-to-create-a-partner"></a>파트너를 만드는 방법

1. Azure Portal에서 **찾아보기**를 선택합니다.

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. 필터 검색 상자에 **통합**을 입력하고 결과 목록에서 **통합 계정**을 선택합니다.

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. 파트너를 추가할 통합 계정을 선택합니다.

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. **파트너** 타일을 선택합니다.

    ![](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. 파트너 블레이드에서 **추가**를 선택합니다.

    ![](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. 파트너의 이름을 입력하고 **한정자**를 선택합니다. 마지막으로 앱에 들어오는 문서를 식별하는 데 도움이 되는 **값**을 입력합니다.

    ![](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. 파트너 생성 프로세스의 진행 상태를 확인하려면 *벨* 알림 아이콘을 선택합니다.

    ![](./media/logic-apps-enterprise-integration-partners/partner-4.png)

8. 새 파트너가 성공적으로 추가되었는지 확인하려면 **파트너** 타일을 선택합니다.

    ![](./media/logic-apps-enterprise-integration-partners/partner-5.png)

    파트너 타일을 선택한 후에 새로 추가된 파트너가 파트너 블레이드에 표시됩니다.

    ![](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="how-to-edit-existing-partners-in-your-integration-account"></a>통합 계정에서 기존 파트너를 편집하는 방법

1. **파트너** 타일을 선택합니다.
2. 파트너 블레이드가 열리면 편집할 파트너를 선택합니다.
3. **파트너 업데이트** 타일에서 필요한 내용을 변경합니다.
4. 작업을 마친 후에 **저장**을 선택하고, 변경 내용을 취소하려면 **취소**를 선택합니다.

    ![](./media/logic-apps-enterprise-integration-partners/edit-1.png)

## <a name="how-to-delete-a-partner"></a>파트너 삭제 방법

1. **파트너** 타일을 선택합니다.
2. 파트너 블레이드가 열리면 삭제할 파트너를 선택합니다.
3. **삭제**를 선택합니다.

    ![](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>다음 단계
* [규약에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-agreements.md "엔터프라이즈 통합 규약에 대해 알아보기")  


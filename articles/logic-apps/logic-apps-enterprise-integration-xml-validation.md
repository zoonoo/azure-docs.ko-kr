---
title: B2B 엔터프라이즈 통합용 XML 유효성 검사
description: 엔터프라이즈 통합 팩에서 Azure Logic Apps 스키마를 사용 하 여 XML 유효성 검사
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: ff21b059e712489c1914b2d12c6aa6a3d78d66d3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74792172"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에서 B2B 엔터프라이즈 통합용 XML 유효성 검사

종종 B2B 시나리오에서 규약의 거래 업체는 데이터 처리를 시작 하기 전에 교환 하는 메시지가 유효한 지 확인 해야 합니다. 엔터프라이즈 통합 팩에서 사용할 수 있는 XML 유효성 검사 작업을 사용 하 여 미리 정의 된 스키마에 대해 문서의 유효성을 검사할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 아직 구독이 없는 경우 [무료 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* XML 유효성 검사 작업을 사용 하려는 빈 또는 기존 논리 앱입니다. 논리 앱을 처음 사용하는 경우 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토합니다.

* Azure 구독과 연결 된 [통합 계정은](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) xml 유효성 검사 작업을 사용 하려는 논리 앱에 연결 되며 xml 콘텐츠의 유효성을 검사 하는 데 사용할 스키마를 포함 합니다. 논리 앱과 통합 계정은 모두 동일한 위치 또는 Azure 지역에 있어야 합니다.

## <a name="add-xml-validation-action"></a>XML 유효성 검사 동작 추가

1. [Azure Portal](https://portal.azure.com)에서 논리 앱 디자이너에서 논리 앱을 엽니다.

1. 논리 앱이 비어 있는 경우 논리 앱 디자이너의 검색 상자에 필터를 입력 `HTTP request` 하 고 **HTTP 요청을 받을 때** 트리거를 선택 합니다. 그렇지 않은 경우 다음 단계를 계속 진행합니다.

1. 워크플로의 마지막 단계에서 **새 단계**를 선택 합니다.

   기존 단계 사이에 작업을 추가 하려면 더하기 기호 (**+**)가 표시 되도록 해당 단계를 연결 하는 화살표 위로 포인터를 이동 합니다. 더하기 기호를 선택 하 고 **작업 추가**를 선택 합니다.

1. **작업 선택** 아래에서 **기본 제공**을 선택합니다. 검색 상자에서 필터로 `xml validation`을 입력합니다. 작업 목록에서 **XML 유효성 검사**를 선택 합니다.

   !["XML 유효성 검사" 작업을 찾아 선택 합니다.](./media/logic-apps-enterprise-integration-xml-validation/select-xml-validation-action.png)

1. 유효성을 검사할 XML 콘텐츠를 지정 하려면 동적 콘텐츠 목록이 표시 되도록 **콘텐츠** 상자 내부를 클릭 합니다.

   ![동적 콘텐츠 목록 열기](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list.png)

   동적 콘텐츠 목록에는 워크플로의 이전 단계의 출력을 나타내는 속성 토큰이 표시 됩니다. 목록에 필요한 속성이 표시 되지 않으면 **자세히 보기**를 선택할 수 있는지 여부에 관계 없이 트리거 또는 작업 머리글을 선택 합니다.

1. 동적 콘텐츠 목록에서 유효성을 검사할 콘텐츠가 있는 속성을 선택 합니다.

   이 예에서는 트리거에서 **본문** 출력을 선택 합니다.

   ![유효성을 검사할 콘텐츠 선택](./media/logic-apps-enterprise-integration-xml-validation/select-content-to-validate.png)

1. 유효성 검사에 사용할 스키마를 지정 하려면 **스키마 이름** 목록을 열고 연결 된 통합 계정에 추가한 유효성 검사 스키마를 선택 합니다.

   ![유효성 검사에 사용할 스키마를 선택 합니다.](./media/logic-apps-enterprise-integration-xml-validation/select-validation-schema.png)

1. 논리 앱을 저장합니다.

   이제 유효성 검사 설정이 완료 되었습니다. 실제 응용 프로그램에서는 SalesForce와 같은 LOB (기간 업무) 앱에 유효성이 검사 된 데이터를 저장 하는 것이 좋습니다. 유효성을 검사한 출력을 Salesforce에 보내려면 작업을 추가합니다.

1. 유효성 검사 동작을 테스트 하기 위해 논리 앱의 워크플로를 트리거하는 요청을 보낼 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [엔터프라이즈 통합 팩](../logic-apps/logic-apps-enterprise-integration-overview.md) 에 대 한 자세한 정보
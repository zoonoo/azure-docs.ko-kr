---
title: 그룹 또는 컬렉션으로 EDI 메시지를 일괄 처리 - Azure Logic Apps | Microsoft Docs
description: 논리 앱에서의 일괄 처리를 위해 EDI 메시지 보내기
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 08/19/2018
ms.openlocfilehash: 7e058b7cebb9c2cdc3fb8b97bf99554b2f26dd8c
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43121578"
---
# <a name="send-edi-messages-in-batches-to-trading-partners-with-azure-logic-apps"></a>Azure Logic Apps를 사용하여 일괄 처리로 거래 업체에 EDI 메시지 보내기

B2B(Business to Business) 시나리오에서 파트너는 그룹 또는 *일괄 처리*로 메시지를 교환하는 경우가 많습니다. Logic Apps를 사용하여 일괄 처리 솔루션을 빌드할 때 거래 파트너에게 메시지를 보내고 해당 메시지를 일괄 처리로 함께 처리할 수 있습니다. 이 문서에서는 X12를 사용하여(예: "일괄 처리 발신자" 논리 앱 및 "일괄 처리 수신자" 논리 앱을 만들어) EDI 메시지를 일괄 처리할 수 있는 방법을 보여줍니다. 

X12 메시지 일괄 처리는 다른 메시지를 일괄 처리하는 것처럼 작동합니다. 메시지를 일괄 처리로 수집하는 일괄 처리 트리거 및 일괄 처리에 메시지를 전송하는 일괄 처리 작업을 사용합니다. 또한 X12 일괄 처리는 메시지가 거래 업체 또는 다른 대상으로 이동하기 전에 X12 인코딩 단계를 포함합니다. 일괄 처리 트리거 및 작업에 대해 자세히 알아보려면 [메시지 일괄 처리 프로세스](../logic-apps/logic-apps-batch-process-send-receive-messages.md)를 참조하세요.

이 문서에서는 동일한 Azure 구독, Azure 지역 내에서 두 개의 논리 앱을 만들고, 이 특정 순서를 따라 일괄 처리 솔루션을 빌드합니다.

* ["일괄 처리 수신자"](#receiver) 논리 앱은 해당 메시지 해제 및 처리에 대해 지정된 조건을 충족할 때까지 메시지를 허용하고 일괄 처리로 수집합니다. 이 시나리오에서는 일괄 처리 수신자도 지정된 X12 계약 또는 파트너 ID를 사용하여 일괄 처리로 메시지를 인코딩합니다.

  먼저 일괄 처리 수신자를 만들어 나중에 일괄 처리 발신자를 만들 때 일괄 처리 대상을 선택할 수 있도록 합니다.

* ["일괄 처리 발신자"](#sender) 논리 앱은 이전에 만든 일괄 처리 수신자에게 메시지를 보냅니다. 

일괄 처리 수신자 및 일괄 처리 발신자가 동일한 Azure 구독 *및* Azure 지역을 공유하도록 합니다. 그렇지 않은 경우 서로 표시되지 않기 때문에 일괄 처리 발신자를 만들 때 일괄 처리 수신자를 선택할 수 없습니다.

## <a name="prerequisites"></a>필수 조건

이 예제를 수행하려면 다음과 같은 항목이 필요합니다.

* Azure 구독. 구독이 없는 경우 [Azure 계정을 사용하여 시작](https://azure.microsoft.com/free/)할 수 있습니다. 또는 [종량제 구독에 등록합니다](https://azure.microsoft.com/pricing/purchase-options/).

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* Azure 구독에 연결되고 논리 앱에 연결된 기존 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* 통합 계정에서 둘 이상의 기존 [파트너](../logic-apps/logic-apps-enterprise-integration-partners.md) 각 파트너는 파트너의 속성에서 X12(표준 이동 통신 사업자 알파 코드) 한정자를 비즈니스 ID로 사용해야 합니다.

* 통합 계정에서 기존 [X12 계약](../logic-apps/logic-apps-enterprise-integration-x12.md)

* Azure Portal 대신 Visual Studio를 사용하려면 [Logic Apps 사용을 위해 Visual Studio를 설정](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)해야 합니다.

<a name="receiver"></a>

## <a name="create-x12-batch-receiver"></a>X12 일괄 처리 수신자 만들기

일괄 처리에 메시지를 보내려면 해당 일괄 처리는 해당 메시지를 보내는 대상으로 먼저 존재해야 합니다. 따라서 먼저 **일괄 처리** 트리거를 시작하는 "일괄 처리 수신자" 논리 앱을 만들어야 합니다. 이런 방식으로 "일괄 처리 발신자" 논리 앱을 만들 때 일괄 처리 수신자 논리 앱을 선택할 수 있습니다. 일괄 처리 수신자는 지정된 조건이 충족되어 해당 메시지를 해제하고 처리할 때까지 메시지 수집을 계속합니다. 일괄 처리 수신자는 일괄 처리 발신자에 대해 알 필요가 없지만 일괄 처리 발신자는 메시지를 보내는 대상을 알고 있어야 합니다. 

이 일괄 처리 수신자에 대한 일괄 처리 모드, 이름, 릴리스 기준, X12 계약 및 다른 설정을 지정합니다. 

1. [Azure Portal](https://portal.azure.com) 또는 Visual Studio에서 "BatchX12Messages"라는 이름으로 논리 앱을 만듭니다.

2. [통합 계정에 논리 앱을 연결합니다](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

3. Logic Apps 디자이너에서 논리 앱 워크플로를 시작하는 **Batch** 트리거를 추가합니다. 검색 상자에서 필터로 “일괄 처리”를 입력합니다. **일괄 처리 메시지** 트리거를 선택합니다.

   ![Batch 트리거 추가](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

4. 일괄 처리 수신자 속성을 설정합니다. 

   | 자산 | 값 | 메모 | 
   |----------|-------|-------|
   | **일괄 처리 모드** | 인라인 |  |  
   | **일괄 처리 이름** | TestBatch | **인라인** 일괄 처리 모드에서만 사용 가능 | 
   | **릴리스 기준** | 메시지 수 기반, 일정 기반 | **인라인** 일괄 처리 모드에서만 사용 가능 | 
   | **메시지 수** | 10 | **메시지 수 기반** 릴리스 기준에서만 사용 가능 | 
   | **간격** | 10 | **일정 기반** 릴리스 기준에서만 사용 가능 | 
   | **Frequency(빈도)** | 분 | **일정 기반** 릴리스 기준에서만 사용 가능 | 
   ||| 

   ![일괄 처리 트리거 세부 정보 제공](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-release-criteria.png)

   > [!NOTE]
   > 이 예제에서는 일괄 처리에 대한 파티션을 설정하지 않으므로 각 일괄 처리는 동일한 파티션 키를 사용합니다. 파티션에 대해 자세히 알아보려면 [메시지 일괄 처리 프로세스](../logic-apps/logic-apps-batch-process-send-receive-messages.md#batch-sender)를 참조하세요.

5. 이제 각 일괄 처리를 인코딩하는 작업을 추가합니다. 

   1. 일괄 처리 트리거에서 **새 단계**를 선택합니다.

   2. 검색 상자에 "X12 일괄 처리"를 필터로 입력하고, **<*version*> - X12 일괄 처리 인코딩** 작업(모든 버전)을 선택합니다. 

      ![X12 일괄 처리 인코딩 작업 선택](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)

   3. 이전에 통합 계정에 연결하지 않은 경우 지금 연결을 만듭니다. 연결의 이름을 제공하고, 원하는 통합 계정을 선택한 다음, **만들기**를 선택합니다.

      ![일괄 처리 인코더와 통합 계정 간의 연결 만들기](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encoder-connect-integration-account.png)

   4. 일괄 처리 인코더 작업에 대해 이러한 속성을 설정합니다.

      | 자산 | 설명 |
      |----------|-------------|
      | **X12 계약 이름** | 목록을 열고, 기존 계약을 선택합니다. <p>목록이 비어 있으면 원하는 계약이 있는 [통합 계정에 논리 앱을 연결했는지](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) 확인합니다. | 
      | **BatchName** | 이 상자 내부를 클릭하고, 동적 콘텐츠 목록이 나타나면 **일괄 처리 이름** 토큰을 선택합니다. | 
      | **PartitionName** | 이 상자 내부를 클릭하고, 동적 콘텐츠 목록이 나타나면 **파티션 이름** 토큰을 선택합니다. | 
      | **Items** | 항목 세부 정보 상자를 닫은 다음, 이 상자 내부를 클릭합니다. 동적 콘텐츠 목록이 나타나면 **일괄 처리된 항목** 토큰을 선택합니다. | 
      ||| 

      ![일괄 처리 인코딩 작업 세부 정보](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

      **Items** 상자의 경우:

      ![일괄 처리 인코딩 작업 항목](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-items.png)

6. 논리 앱을 저장합니다. 

7. Visual Studio를 사용하는 경우 [일괄 처리 수신자 논리 앱을 Azure에 배포](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)해야 합니다. 그렇지 않으면 일괄 처리 발신자를 만들 때 일괄 처리 수신자를 선택할 수 없습니다.

### <a name="test-your-logic-app"></a>논리 앱 테스트

일괄 처리 수신자가 예상대로 작동하는지 확인하기 위해 테스트 목적으로 HTTP 작업을 추가하고, [요청 Bin 서비스](https://requestbin.fullcontact.com/)에 일괄 처리된 메시지를 보낼 수 있습니다. 

1. X12 인코딩 작업에서 **새 단계**를 선택합니다. 

2. 검색 상자에 "http"를 필터로 입력합니다. **HTTP - HTTP** 작업을 선택합니다.
    
   ![HTTP 작업 선택](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action.png)

3. HTTP 작업에 대한 속성을 설정합니다.

   | 자산 | 설명 | 
   |----------|-------------|
   | **메서드** | 이 목록에서 **POST**를 선택합니다. | 
   | **Uri** | 요청 bin에 대한 URI를 생성한 다음, 이 상자에 해당 URI를 입력합니다. | 
   | **본문** | 이 상자 내부를 클릭하고, 동적 콘텐츠 목록이 열리면 섹션에 나타나는 **본문** 토큰, **계약 이름으로 일괄 처리 인코딩**을 선택합니다. <p>**본문** 토큰이 없는 경우 **계약 이름으로 일괄 처리 인코딩** 옆의 **자세히 보기**를 선택합니다. | 
   ||| 

   ![HTTP 작업 세부 정보 제공](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-add-http-action-details.png)

4. 논리 앱을 저장합니다. 

   일괄 처리 수신자 논리 앱은 다음 예와 같습니다. 

   ![일괄 처리 수신자 논리 앱 저장](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receiver-finished.png)

<a name="sender"></a>

## <a name="create-x12-batch-sender"></a>X12 일괄 처리 발신자 만들기

이제 일괄 처리 수신자 논리 앱에 메시지를 보내는 하나 이상의 논리 앱을 만듭니다. 각 일괄 처리 발신자에서 일괄 처리 수신자 논리 앱 및 일괄 처리 이름, 메시지 콘텐츠 및 다른 설정을 지정합니다. 필요에 따라 해당 키가 있는 메시지를 수집하도록 일괄 처리를 하위 집합으로 나누는 고유한 파티션 키를 제공할 수 있습니다. 

* 일괄 처리 발신자를 만들 때 기존 일괄 처리 수신자를 대상 일괄 처리로 선택할 수 있도록 [일괄 처리 수신자를 만들었는지](#receiver) 확인합니다. 일괄 처리 수신자는 일괄 처리 발신자에 대해 인식하고 있을 필요가 없지만, 일괄 처리 발신자는 메시지를 보내는 위치를 인식하고 있어야 합니다. 

* 일괄 처리 수신자와 일괄 처리 발신자에서 동일한 Azure 지역 *및* Azure 구독을 공유하는지 확인합니다. 그렇지 않은 경우 서로 표시되지 않기 때문에 일괄 처리 발신자를 만들 때 일괄 처리 수신자를 선택할 수 없습니다.

1. "SendX12MessagesToBatch"라는 이름으로 또 다른 논리 앱을 만듭니다. 

2. 검색 상자에 “http 요청”을 필터로 입력합니다. **HTTP 요청을 받은 경우** 트리거를 선택합니다. 
   
   ![요청 트리거 추가](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

3. 일괄 처리로 메시지를 보내기 위한 작업을 추가합니다.

   1. HTTP 요청 작업에서 **새 단계**를 선택합니다.

   2. 검색 상자에서 필터로 “일괄 처리”를 입력합니다. 
   **작업** 목록을 선택한 다음, **일괄 처리 트리거가 있는 Logic Apps 워크플로를 선택합니다. - 일괄 처리로 메시지 보내기** 작업을 선택합니다.

      !["일괄 처리 트리거가 있는 Logic Apps 워크플로 선택"을 선택합니다.](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-trigger.png)

   3. 이제 이전에 만든 "BatchX12Messages" 논리 앱을 선택합니다.

      ![“일괄 처리 수신기” 논리 앱 선택](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-receiver.png)

   4. **Batch_messages - <*your-batch-receiver*>** 작업을 선택합니다.

      !["Batch_messages" 작업 선택](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-select-batch-messages-action.png)

4. 일괄 처리 발신자의 속성을 설정합니다.

   | 자산 | 설명 | 
   |----------|-------------| 
   | **일괄 처리 이름** | 수신자 논리 앱에서 정의된 일괄 처리 이름입니다(이 예의 경우 "TestBatch"). <p>**중요**: 일괄 처리 이름은 런타임에 유효성이 검사되고 수신자 논리 앱에서 지정된 이름과 일치해야 합니다. 일괄 처리 이름을 변경하면 일괄 처리 발신자가 실패하게 됩니다. | 
   | **메시지 콘텐츠** | 보내려는 메시지에 대한 콘텐츠입니다. 이 예에서는 **본문** 토큰입니다. | 
   ||| 
   
   ![일괄 처리 속성 설정](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-set-batch-properties.png)

5. 논리 앱을 저장합니다. 

   일괄 처리 발신자 논리 앱은 다음 예와 같습니다.

   ![일괄 처리 발신자 논리 앱 저장](./media/logic-apps-scenario-EDI-send-batch-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>논리 앱 테스트

일괄 처리 솔루션을 테스트하려면 X12 메시지를 [Postman](https://www.getpostman.com/postman) 또는 유사한 도구에서 일괄 처리 발신자 논리 앱에 게시합니다. 곧 요청 bin에 10분 간격으로 또는 10개의 일괄 처리로 동일한 파티션 키가 있는 X12 메시지를 받기 시작합니다.

## <a name="next-steps"></a>다음 단계

* [일괄 처리로 메시지 처리](../logic-apps/logic-apps-batch-process-send-receive-messages.md) 

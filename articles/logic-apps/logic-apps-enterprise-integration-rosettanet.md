---
title: B2B 통합을 위한 RosettaNet 메시지
description: 엔터프라이즈 통합 팩을 사용하여 Azure Logic Apps에서 RosettaNet 메시지 교환
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: f02cbdc7ca8822c5fcc91b106856d7f8f547536b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91565108"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Azure Logic Apps에서 B2B 엔터프라이즈 통합을 위해 RosettaNet 메시지 교환

[RosettaNet](https://resources.gs1us.org)은 비즈니스 정보를 공유하기 위한 표준 프로세스를 설정한 비영리 컨소시엄입니다. 해당 표준은 공급망 프로세스에 일반적으로 적용되며 반도체, 전자 제품, 물류 업계에서 널리 사용됩니다. RosettaNet 컨소시엄은 모든 RosettaNet 메시지 교환의 공통 비즈니스 프로세스 정의를 제공하는 PIP(파트너 인터페이스 프로세스)를 만들고 유지 관리합니다. RosettaNet은 XML을 기반으로 하며 메시지 지침, 비즈니스 프로세스 인터페이스, 회사 간 통신을 위한 구현 프레임워크를 정의합니다.

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)에서 RosettaNet 커넥터를 사용하면 RosettaNet 표준을 지원하는 통합 솔루션을 만들 수 있습니다. 이 커넥터는 RNIF(RosettaNet 구현 프레임워크) 버전 2.0.01을 기반으로 합니다. RNIF는 비즈니스 파트너가 공동으로 RosettaNet PIP를 실행할 수 있도록 하는 개방형 네트워크 애플리케이션 프레임워크입니다. 이 프레임워크는 메시지 구조, 승인 요구 사항, MIME(Multipurpose Internet Mail Extensions) 인코딩, 디지털 서명을 정의합니다.

특히 커넥터는 다음과 같은 기능을 제공합니다.

* RosettaNet 메시지를 인코드하거나 받습니다.
* RosettaNet 메시지를 디코드하거나 보냅니다.
* 응답 및 실패 알림 생성을 기다립니다.

위의 기능을 위해 커넥터는 RNIF 2.0.01에서 정의된 모든 PIP를 지원합니다. 파트너와의 통신은 동기 또는 비동기일 수 있습니다.

## <a name="rosettanet-concepts"></a>RosettaNet 개념

RosettaNet 사양에서 고유하게 사용되고 RosettaNet 기반 통합을 빌드할 때 중요한 몇 가지 개념과 용어는 다음과 같습니다.

* **PIP**

  RosettaNet 조직은 모든 RosettaNet 메시지 교환의 공통 비즈니스 프로세스 정의를 제공하는 PIP(파트너 인터페이스 프로세스)를 만들고 유지 관리합니다. 각 PIP 사양은 DTD(문서 종류 정의) 파일 및 메시지 지침 문서를 제공합니다. DTD 파일은 서비스 콘텐츠 메시지 구조를 정의합니다. 사람이 읽을 수 있는 HTML 파일인 메시지 지침 문서는 요소 수준의 제약 조건을 지정합니다. 모든 파일이 결합되어 비즈니스 프로세스의 전체 정의를 제공합니다.

   PIP는 상위 수준의 비즈니스 기능 또는 클러스터와 하위 함수 또는 세그먼트로 분류됩니다. 예를 들어 “3A4”는 구매 주문 PIP로, “3”은 주문 관리 함수 이고 “3A”는 견적 및 주문 입력 하위 함수입니다. 자세한 내용은 [RosettaNet 사이트](https://resources.gs1us.org)를 참조하세요.

* **동작**

  PIP의 일부로, 작업 메시지는 파트너 간에 교환되는 비즈니스 메시지입니다.

* **신호**

   PIP의 일부로, 신호 메시지는 작업 메시지에 대한 응답으로 전송되는 승인입니다.

* **단일 작업 및 이중 작업**

  단일 작업 PIP의 경우 유일한 응답은 승인 신호 메시지입니다. 이중 작업 PIP의 경우 단일 작업 메시지 흐름 외에 개시 장치가 응답 메시지를 받고 승인으로 회신합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 아직 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 규약 및 기타 B2B 아티팩트를 저장하기 위한 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). 이 통합 계정은 Azure 구독과 연결되어야 합니다.

* 통합 계정에서 정의되고 **비즈니스 ID** 아래에 “DUNS” 한정자로 구성된 둘 이상의 [파트너](../logic-apps/logic-apps-enterprise-integration-partners.md)

* 통합 계정에서 RosettaNet 메시지를 보내거나 받는 데 필요한 PIP 프로세스 구성. 프로세스 구성은 모든 PIP 구성 특성을 저장합니다. 그런 다음, 파트너와 규약을 만들 때 이 구성을 참조할 수 있습니다. 통합 계정에서 PIP 프로세스 구성을 만들려면 [PIP 프로세스 구성 추가](#add-pip)를 참조하세요.

* 메시지를 암호화, 암호 해독 또는 서명하는 데 사용되며 통합 계정에 업로드하는 [인증서](../logic-apps/logic-apps-enterprise-integration-certificates.md)(선택 사항). 인증서는 서명 또는 암호화를 사용하는 경우에만 필요합니다.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>PIP 프로세스 구성 추가

통합 계정에 PIP 프로세스 구성을 추가하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 통합 계정을 찾아서 엽니다.

1. **개요** 창에서 **RosettaNet PIP** 타일을 선택합니다.

   ![RosettaNet 타일 선택](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. **RosettaNet PIP** 에서 **추가** 를 선택합니다. PIP 세부 정보를 제공합니다.

   ![RosettaNet PIP 세부 정보 추가](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **이름** | 예 | PIP 이름입니다. |
   | **PIP 코드** | 예 | 세 자리 숫자로 이루어진 PIP 코드입니다. 자세한 내용은 [RosettaNet PIP](/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips)를 참조하세요. |
   | **PIP 버전** | 예 | 선택한 PIP 코드에 따라 사용할 수 있는 PIP 버전 번호입니다. |
   ||||

   PIP 속성에 대한 자세한 내용은 [RosettaNet 웹 사이트](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg)를 참조하세요.

1. 완료되면 **확인** 을 선택합니다. PIP 구성이 생성됩니다.

1. 프로세스 구성을 보거나 편집하려면 PIP를 선택한 다음 **JSON으로 편집** 을 선택합니다.

   모든 프로세스 구성 설정은 PIP 사양에서 가져온 것입니다. Logic Apps는 대부분의 설정을 기본값으로 채웁니다. 기본값은 가장 일반적으로 사용되는 속성 값입니다.

   ![RosettaNet PIP 구성 편집](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. 설정이 적절한 PIP 사양의 값에 해당하고 비즈니스 요구 사항을 충족하는지 확인합니다. 필요한 경우 JSON으로 값을 업데이트하고 변경 내용을 저장합니다.

## <a name="create-rosettanet-agreement"></a>RosettaNet 규약 만들기

1. [Azure Portal](https://portal.azure.com)에서 통합 계정이 아직 열려 있지 않으면 찾아서 엽니다.

1. **개요** 창에서 **규약** 타일을 선택합니다.

   ![규약 타일 선택](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. **규약** 에서 **추가** 를 선택합니다. 규약 세부 정보를 제공합니다.

   ![규약 세부 정보 추가](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **이름** | 예 | 규약 이름입니다. |
   | **규약 유형** | 예 | **RosettaNet** 을 선택합니다. |
   | **호스트 파트너** | 예 | 규약에는 호스트와 게스트 파트너가 모두 필요합니다. 호스트 파트너는 규약을 구성하는 조직을 나타냅니다. |
   | **호스트 ID** | 예 | 호스트 파트너의 식별자입니다. |
   | **게스트 파트너** | 예 | 규약에는 호스트와 게스트 파트너가 모두 필요합니다. 게스트 파트너는 호스트 파트너와 함께 일하는 조직을 나타냅니다. |
   | **게스트 ID** | 예 | 게스트 파트너의 식별자입니다. |
   | **수신 설정** | 상황에 따라 다름 | 이 속성은 호스트 파트너가 받는 모든 메시지에 적용됩니다. |
   | **송신 설정** | 상황에 따라 다름 | 이 속성은 호스트 파트너가 보내는 모든 메시지에 적용됩니다. |  
   | **RosettaNet PIP 참조** | 예 | 규약의 PIP 참조입니다. 모든 RosettaNet 메시지에는 PIP 구성이 필요합니다. |
   ||||

1. 게스트 파트너로부터 들어오는 메시지를 수신하도록 규약을 설정하려면 **수신 설정** 을 선택합니다.

   ![수신 설정](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. 들어오는 메시지에 서명 또는 암호화를 사용하도록 설정하려면 **메시지** 에서 **메시지 서명 필요** 또는 **메시지 암호화 필요** 를 각각 선택합니다.

      | 속성 | 필수 | Description |
      |----------|----------|-------------|
      | **메시지 서명 필요** | 예 | 선택한 인증서를 사용하여 들어오는 메시지에 서명합니다. |
      | **MSSQLSERVER에 대한 프로토콜 속성** | 예(서명이 사용하도록 설정된 경우) | 서명에 사용할 인증서입니다. |
      | **메시지 암호화 사용** | 예 | 선택한 인증서를 사용하여 들어오는 메시지를 암호화합니다. |
      | **MSSQLSERVER에 대한 프로토콜 속성** | 예(암호화가 사용하도록 설정된 경우) | 암호화에 사용할 인증서입니다. |
      ||||

   1. 각 선택 항목에서 서명 또는 암호화에 사용하기 위해 이전에 통합 계정에 추가한 해당 [인증서](./logic-apps-enterprise-integration-certificates.md)를 선택합니다.

1. 게스트 파트너에게 메시지를 보내도록 규약을 설정하려면 **송신 설정** 을 선택합니다.

   ![송신 설정](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. 보내는 메시지에 서명 또는 암호화를 사용하도록 설정하려면 **메시지** 에서 **메시지 서명 사용** 또는 **메시지 암호화 사용** 을 각각 선택합니다. 각 선택 항목에서 서명 또는 암호화에 사용하기 위해 이전에 통합 계정에 추가한 해당 알고리즘과 [인증서](./logic-apps-enterprise-integration-certificates.md)를 선택합니다.

      | 속성 | 필수 | Description |
      |----------|----------|-------------|
      | **메시지 서명 사용** | 예 | 선택한 서명 알고리즘과 인증서를 사용하여 보내는 메시지에 서명합니다. |
      | **서명 알고리즘** | 예(서명이 사용하도록 설정된 경우) | 선택한 인증서를 기준으로 사용할 서명 알고리즘입니다. |
      | **MSSQLSERVER에 대한 프로토콜 속성** | 예(서명이 사용하도록 설정된 경우) | 서명에 사용할 인증서입니다. |
      | **메시지 암호화 사용** | 예 | 선택한 암호화 알고리즘과 인증서를 사용하여 보내는 메시지를 암호화합니다. |
      | **암호화 알고리즘** | 예(암호화가 사용하도록 설정된 경우) | 선택한 인증서를 기준으로 사용할 암호화 알고리즘입니다. |
      | **MSSQLSERVER에 대한 프로토콜 속성** | 예(암호화가 사용하도록 설정된 경우) | 암호화에 사용할 인증서입니다. |
      ||||

   1. **엔드포인트** 에서 작업 메시지와 승인을 보내는 데 사용할 필수 URL을 지정합니다.

      | 속성 | 필수 | Description |
      |----------|----------|-------------|
      | **작업 URL** |  예 | 작업 메시지를 보내는 데 사용할 URL입니다. 이 URL은 동기 및 비동기 메시지 둘 다에서 필수 필드입니다. |
      | **승인 URL** | 예 | 승인 메시지를 보내는 데 사용할 URL입니다. 이 URL은 비동기 메시지에서 필수 필드입니다. |
      ||||

1. 파트너용 RosettaNet PIP 참조를 사용하여 규약을 설정하려면 **RosettaNet PIP 참조** 를 선택합니다. **PIP 이름** 에서 이전에 만든 PIP의 이름을 선택합니다.

   ![PIP 참조](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   선택 항목에 따라 통합 계정에서 설정한 PIP를 기반으로 하는 나머지 속성이 채워집니다. 필요한 경우 **PIP 역할** 을 변경할 수 있습니다.

   ![선택한 PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

위의 단계를 완료했으면 RosettaNet 메시지를 보내거나 받을 준비가 된 것입니다.

## <a name="rosettanet-templates"></a>RosettaNet 템플릿

개발을 가속화하고 통합 패턴을 권장하기 위해 논리 앱 템플릿을 사용하여 RosettaNet 메시지를 디코드 및 인코드할 수 있습니다. 논리 앱을 만들 때 논리 앱 디자이너의 템플릿 갤러리에서 선택할 수 있습니다. 해당 템플릿은 [Azure Logic Apps용 GitHub 리포지토리](https://github.com/Azure/logicapps)에서도 찾을 수 있습니다.

![RosettaNet 템플릿](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>RosettaNet 메시지 받기 또는 디코드

1. [빈 논리 앱을 만듭니다](quickstart-create-first-logic-app-workflow.md).

1. [통합 계정을 논리 앱에 연결](logic-apps-enterprise-integration-create-integration-account.md#link-account)합니다.

1. RosettaNet 메시지를 디코드하는 작업을 추가하려면 먼저 요청 트리거와 같은 논리 앱 시작 트리거를 추가해야 합니다.

1. 트리거를 추가한 후 **새 단계** 를 선택합니다.

   ![요청 트리거 추가](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. 검색 상자에 “rosettanet”을 입력하고 **RosettaNet 디코드** 작업을 선택합니다.

   ![“RosettaNet 디코드” 작업을 찾아서 선택](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. 다음과 같은 작업 속성의 정보를 제공합니다.

   ![작업 속성의 정보를 제공해야 하는 위치 스크린샷](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **Message** | 예 | 디코드할 RosettaNet 메시지입니다.  |
   | **헤더** | 예 | 버전(RNIF 버전) 및 응답 유형의 값을 제공하는 HTTP 헤더입니다. 응답 유형은 파트너 간 통신 유형을 나타내며 동기 또는 비동기일 수 있습니다. |
   | **Role** | 예 | PIP에서 호스트 파트너의 역할입니다. |
   ||||

   RosettaNet 디코드 작업의 출력에는 다른 속성과 함께 **아웃바운드 신호** 가 포함됩니다. 인코드하여 파트너에게 다시 반환하거나 출력에 대해 다른 작업을 수행하도록 신호를 선택할 수 있습니다.

## <a name="send-or-encode-rosettanet-messages"></a>RosettaNet 메시지 보내기 또는 인코드

1. [빈 논리 앱을 만듭니다](quickstart-create-first-logic-app-workflow.md).

1. [통합 계정을 논리 앱에 연결](logic-apps-enterprise-integration-create-integration-account.md#link-account)합니다.

1. RosettaNet 메시지를 인코드하는 작업을 추가하려면 먼저 요청 트리거와 같은 논리 앱 시작 트리거를 추가해야 합니다.

1. 트리거를 추가한 후 **새 단계** 를 선택합니다.

   ![요청 트리거 추가](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. 검색 상자에 “rosettanet”을 입력하고 **RosettaNet 인코드** 작업을 선택합니다.

   ![“RosettaNet 인코드” 작업을 찾아서 선택](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. 다음과 같은 작업 속성의 정보를 제공합니다.

   ![작업 세부 정보 제공](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **Message** | 예 | 인코드할 RosettaNet 메시지입니다.  |
   | **호스트 파트너** | 예 | 호스트 파트너 이름입니다. |
   | **게스트 파트너** | 예 | 게스트 파트너 이름입니다. |
   | **PIP 코드** | 예 | PIP 코드입니다. |
   | **PIP 버전** | 예 | PIP 버전입니다. |  
   | **PIP 인스턴스 ID** | 예 | 이 PIP 메시지의 고유 식별자입니다. |  
   | **메시지 유형** | 예 | 인코드할 메시지 유형입니다. |  
   | **Role** | 예 | 호스트 파트너의 역할입니다. |
   ||||

   이제 인코드된 메시지를 파트너에게 보낼 준비가 되었습니다.

1. 인코드된 메시지를 보내기 위해 이 예제에서는 “HTTP - 파트너에게 인코드된 메시지 보내기”로 이름이 변경된 **HTTP** 작업을 사용합니다.

   ![RosettaNet 메시지를 보내기 위한 HTTP 작업](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   RosettaNet 표준에 따라 비즈니스 트랜잭션은 PIP에서 정의된 모든 단계가 완료된 경우에만 완료된 것으로 간주됩니다.

1. 호스트는 인코드된 메시지를 파트너에게 보낸 후 신호와 승인을 기다립니다. 이 작업을 수행하려면 **RosettaNet 응답 대기** 작업을 추가합니다.

   ![“RosettaNet 응답 대기” 작업 추가](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   사용할 대기 기간과 재시도 횟수는 통합 계정의 PIP 구성을 기반으로 합니다. 응답이 수신되지 않으면 이 작업은 실패 알림을 생성합니다. 재시도를 처리하려면 항상 **Until** 루프에 **인코드** 및 **응답 대기** 작업을 넣습니다.

   ![RosettaNet 작업이 포함된 Until 루프](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>다음 단계

* [엔터프라이즈 통합 팩](../logic-apps/logic-apps-enterprise-integration-overview.md)을 사용하여 유효성 검사, 변환 및 기타 메시지 작업을 수행하는 방법을 알아봅니다.
* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.

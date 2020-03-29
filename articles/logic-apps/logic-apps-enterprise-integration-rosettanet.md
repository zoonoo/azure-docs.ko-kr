---
title: B2B 통합을 위한 로제타넷 메시지
description: 엔터프라이즈 통합 팩을 사용한 Azure 논리 앱의 RosettaNet 메시지 교환
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 2cc2ac08b9624c1d1d9bee9ce91a7c91189d7f2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792425"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Azure 논리 앱에서 B2B 엔터프라이즈 통합을 위한 RosettaNet 메시지 교환

[RosettaNet은](https://resources.gs1us.org) 비즈니스 정보를 공유하기 위한 표준 프로세스를 수립한 비영리 컨소시엄입니다. 이러한 표준은 일반적으로 공급망 프로세스에 사용되며 반도체, 전자 및 물류 산업에 널리 사용됩니다. RosettaNet 컨소시엄은 모든 RosettaNet 메시지 교환에 대해 공통비즈니스 프로세스 정의를 제공하는 파트너 인터페이스 프로세스(PIP)를 만들고 유지 관리합니다. RosettaNet은 XML을 기반으로 하며 메시지 지침, 비즈니스 프로세스 인터페이스 및 회사 간 통신을 위한 구현 프레임워크를 정의합니다.

[Azure 논리 앱에서](../logic-apps/logic-apps-overview.md)RosettaNet 커넥터를 사용하면 RosettaNet 표준을 지원하는 통합 솔루션을 만들 수 있습니다. 커넥터는 로제타넷 구현 프레임워크(RNIF) 버전 2.0.01을 기반으로 합니다. RNIF는 비즈니스 파트너가 RosettaNet PIP를 공동으로 실행할 수 있는 개방형 네트워크 응용 프로그램 프레임워크입니다. 이 프레임워크는 메시지 구조, 승인 필요, MIME(다목적 인터넷 메일 확장) 인코딩 및 디지털 서명을 정의합니다.

특히 커넥터는 다음과 같은 기능을 제공합니다.

* RosettaNet 메시지를 인코딩하거나 수신합니다.
* 로제타넷 메시지를 디코딩하거나 보냅니다.
* 오류 알림의 응답 및 생성을 기다립니다.

이러한 기능의 경우 커넥터는 RNIF 2.0.01에 의해 정의된 모든 PIP를 지원합니다. 파트너와의 통신은 동기 또는 비동기일 수 있습니다.

## <a name="rosettanet-concepts"></a>로제타넷 컨셉

다음은 RosettaNet 사양에 고유하며 RosettaNet 기반 통합을 구축할 때 중요한 몇 가지 개념 및 용어입니다.

* **핍**

  RosettaNet 조직은 모든 RosettaNet 메시지 교환에 대해 공통비즈니스 프로세스 정의를 제공하는 파트너 인터페이스 프로세스(PIP)를 만들고 유지 관리합니다. 각 PIP 사양은 문서 유형 정의(DTD) 파일과 메시지 지침 문서를 제공합니다. DTD 파일은 서비스 콘텐츠 메시지 구조를 정의합니다. 사람이 읽을 수 있는 HTML 파일인 메시지 지침 문서는 요소 수준 제약 조건을 지정합니다. 이러한 파일은 함께 비즈니스 프로세스의 완전한 정의를 제공합니다.

   PIP는 상위 수준의 비즈니스 기능 또는 클러스터, 하위 기능 또는 세그먼트로 분류됩니다. 예를 들어 "3A4"는 구매 주문서의 PIP이고 "3"은 주문 관리 기능이고 "3A"는 주문 항목의 견적 & 하위 기능입니다. 자세한 내용은 [RosettaNet 사이트를](https://resources.gs1us.org)참조하십시오.

* **작업**

  PIP의 일부인 작업 메시지는 파트너 간에 교환되는 비즈니스 메시지입니다.

* **신호**

   PIP의 일부인 신호 메시지는 작업 메시지에 대한 응답으로 전송되는 승인입니다.

* **단일 액션 및 이중 액션**

  단일 작업 PIP의 경우 유일한 응답은 승인 신호 메시지입니다. 이중 작업 PIP의 경우 개시자는 응답 메시지를 수신하고 단일 작업 메시지 흐름 외에 승인이 있는 응답을 받습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 아직 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 계약 및 기타 B2B 아티팩트를 저장하기 위한 [통합 계정입니다.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 이 통합 계정은 Azure 구독과 연결되어 있어야 합니다.

* 통합 계정에 정의되고 **비즈니스 ID에서** "DUNS" 한정자로 구성된 두 개 이상의 [파트너](../logic-apps/logic-apps-enterprise-integration-partners.md)

* 통합 계정에서 RosettaNet 메시지를 보내거나 받는 데 필요한 PIP 프로세스 구성입니다. 프로세스 구성은 모든 PIP 구성 특성을 저장합니다. 그런 다음 파트너와 계약을 만들 때 이 구성을 참조할 수 있습니다. 통합 계정에서 PIP 프로세스 구성을 만들려면 [PIP 프로세스 구성 추가를](#add-pip)참조하십시오.

* 통합 계정에 [업로드한](../logic-apps/logic-apps-enterprise-integration-certificates.md) 메시지를 암호화, 해독 또는 서명하기 위한 선택적 인증서입니다. 인증서는 서명 또는 암호화를 사용하는 경우에만 필요합니다.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>PIP 프로세스 구성 추가

통합 계정에 PIP 프로세스 구성을 추가하려면 다음 단계를 따르세요.

1. Azure [포털에서](https://portal.azure.com)통합 계정을 찾아 엽니다.

1. **개요** 창에서 **RosettaNet PIP** 타일을 선택합니다.

   ![로제타넷 타일 선택](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. **로제타넷 PIP에서** **추가를 선택합니다.** PIP 세부 정보를 제공합니다.

   ![로제타넷 PIP 세부 정보 추가](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | 속성 | 필수 | 설명 |
   |----------|----------|-------------|
   | **이름** | yes | PIP 이름 |
   | **핍 코드** | yes | PIP 세 자리 코드입니다. 자세한 내용은 [RosettaNet PIP](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips)를 참조하십시오. |
   | **PIP 버전** | yes | 선택한 PIP 코드에 따라 사용할 수 있는 PIP 버전 번호 |
   ||||

   이러한 PIP 속성에 대한 자세한 내용은 [RosettaNet 웹 사이트를](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg)방문하십시오.

1. 작업이 완료되면 PIP 구성을 만드는 **확인을**선택합니다.

1. 프로세스 구성을 보거나 편집하려면 PIP를 선택하고 **JSON으로 편집을**선택합니다.

   모든 프로세스 구성 설정은 PIP 사양에서 비롯됩니다. Logic Apps는 대부분의 설정을 이러한 속성에 대해 가장 일반적으로 사용되는 값인 기본값으로 채웁니다.

   ![로제타넷 PIP 구성 편집](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. 설정이 적절한 PIP 사양의 값에 해당하는지 확인하고 비즈니스 요구 사항을 충족합니다. 필요한 경우 JSON의 값을 업데이트하고 이러한 변경 내용을 저장합니다.

## <a name="create-rosettanet-agreement"></a>로제타넷 계약 만들기

1. [Azure Portal](https://portal.azure.com)에서 통합 계정이 아직 열려 있지 않으면 찾아서 엽니다.

1. **개요** 창에서 **계약** 타일을 선택합니다.

   ![계약 타일 선택](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. **규약**에서 **추가**를 선택합니다. 계약 세부 정보를 제공합니다.

   ![계약 세부 정보 추가](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | 속성 | 필수 | 설명 |
   |----------|----------|-------------|
   | **이름** | yes | 계약 이름 |
   | **계약 유형** | yes | **로제타넷**을 선택합니다. |
   | **호스트 파트너** | yes | 규약에는 호스트와 게스트 파트너가 모두 필요합니다. 호스트 파트너는 규약을 구성하는 조직을 나타냅니다. |
   | **호스트 ID** | yes | 호스트 파트너의 식별자입니다. |
   | **게스트 파트너** | yes | 규약에는 호스트와 게스트 파트너가 모두 필요합니다. 게스트 파트너는 호스트 파트너와 함께 일하는 조직을 나타냅니다. |
   | **게스트 ID** | yes | 게스트 파트너의 식별자입니다. |
   | **수신 설정** | 다양함 | 이러한 속성은 호스트 파트너가 받은 모든 메시지에 적용됩니다. |
   | **송신 설정** | 다양함 | 이러한 속성은 호스트 파트너가 보낸 모든 메시지에 적용됩니다. |  
   | **로제타넷 PIP 참조** | yes | PIP는 계약에 대한 참조입니다. 모든 RosettaNet 메시지에는 PIP 구성이 필요합니다. |
   ||||

1. 게스트 파트너로부터 수신 메시지를 수신하기 위한 계약을 설정하려면 **설정 수신을**선택합니다.

   ![설정 받기](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. 들어오는 메시지에 대한 서명 또는 암호화를 사용하려면 **메시지**에서 **메시지 선택에서 메시지에 서명해야 하거나** 메시지를 각각 **암호화해야 합니다.**

      | 속성 | 필수 | 설명 |
      |----------|----------|-------------|
      | **메시지 서명 필요** | 예 | 선택한 인증서로 들어오는 메시지에 로그인합니다. |
      | **인증서** | 예. 서명이 활성화된 경우 | 서명에 사용할 인증서 |
      | **메시지 암호화 사용** | 예 | 선택한 인증서로 들어오는 메시지를 암호화합니다. |
      | **인증서** | 예. 암호화가 활성화된 경우 | 암호화에 사용할 인증서 |
      ||||

   1. 각 선택 항목에서 이전에 통합 계정에 추가한 각 [인증서를](./logic-apps-enterprise-integration-certificates.md)선택하여 서명 또는 암호화에 사용합니다.

1. 게스트 파트너에게 메시지를 보내는 계약을 설정하려면 **설정 보내기를**선택합니다.

   ![설정 보내기](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. 나가는 메시지에 대해 서명 또는 암호화를 사용하려면 **메시지**에서 **메시지 서명 활성화** 또는 메시지 암호화 **활성화를** 각각 선택합니다. 각 선택 항목에서 이전에 통합 계정에 추가한 각 알고리즘 및 [인증서를](./logic-apps-enterprise-integration-certificates.md)선택하여 서명 또는 암호화에 사용합니다.

      | 속성 | 필수 | 설명 |
      |----------|----------|-------------|
      | **메시지 서명 사용** | 예 | 선택한 서명 알고리즘 및 인증서로 발출 메시지를 서명합니다. |
      | **서명 알고리즘** | 예. 서명이 활성화된 경우 | 선택한 인증서를 기반으로 사용할 서명 알고리즘 |
      | **인증서** | 예. 서명이 활성화된 경우 | 서명에 사용할 인증서 |
      | **메시지 암호화 사용** | 예 | 선택한 암호화 알고리즘 및 인증서로 발후를 암호화합니다. |
      | **암호화 알고리즘** | 예. 암호화가 활성화된 경우 | 선택한 인증서를 기반으로 사용할 암호화 알고리즘 |
      | **인증서** | 예. 암호화가 활성화된 경우 | 암호화에 사용할 인증서 |
      ||||

   1. **끝점에서**작업 메시지 및 승인 을 보내는 데 사용할 필수 URL을 지정합니다.

      | 속성 | 필수 | 설명 |
      |----------|----------|-------------|
      | **작업 URL** |  yes | 작업 메시지를 보내는 데 사용할 URL입니다. URL은 동기 메시지와 비동기 메시지 모두에 필요한 필드입니다. |
      | **승인 URL** | yes | 승인 메시지를 보내는 데 사용할 URL입니다. URL은 비동기 메시지에 필요한 필드입니다. |
      ||||

1. 파트너에 대한 RosettaNet PIP 참조와 계약을 설정하려면 **RosettaNet PIP 참조를**선택합니다. **PIP 이름에서**이전에 만든 PIP의 이름을 선택합니다.

   ![PIP 참조](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   선택한 속성은 통합 계정에서 설정한 PIP를 기반으로 하는 나머지 속성을 채웁니다. 필요한 경우 **PIP 역할을**변경할 수 있습니다.

   ![선택한 PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

이 단계를 완료하면 RosettaNet 메시지를 보내거나 받을 준비가 된 것입니다.

## <a name="rosettanet-templates"></a>로제타넷 템플릿

개발을 가속화하고 통합 패턴을 권장하려면 RosettaNet 메시지를 디코딩하고 인코딩하기 위해 논리 앱 템플릿을 사용할 수 있습니다. 논리 앱을 만들 때 논리 앱 디자이너의 템플릿 갤러리에서 선택할 수 있습니다. [Azure 논리 앱에 대한 GitHub 리포지토리에서](https://github.com/Azure/logicapps)이러한 템플릿을 찾을 수도 있습니다.

![로제타넷 템플릿](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>RosettaNet 메시지 수신 또는 디코딩

1. [빈 논리 앱을 만듭니다.](quickstart-create-first-logic-app-workflow.md)

1. 통합 계정을 논리 앱에 [연결합니다.](logic-apps-enterprise-integration-create-integration-account.md#link-account)

1. RosettaNet 메시지를 디코딩하는 작업을 추가하려면 먼저 요청 트리거와 같은 논리 앱을 시작하기 위한 트리거를 추가해야 합니다.

1. 트리거를 추가한 후 **새 단계를**선택합니다.

   ![요청 트리거 추가](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. 검색 상자에서 "rosettanet"을 입력하고 이 **RosettaNet Decode** 작업을 선택합니다.

   ![찾기 및 "로제타넷 디코딩" 작업 선택](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. 작업 속성에 대한 정보를 제공합니다.

   ![작업 세부 정보 제공](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | 속성 | 필수 | 설명 |
   |----------|----------|-------------|
   | **메시지** | yes | 디코딩할 RosettaNet 메시지  |
   | **헤더** | yes | RNIF 버전인 버전에 대한 값을 제공하는 HTTP 헤더와 파트너 간의 통신 유형을 나타내고 동기 또는 비동기일 수 있는 응답 유형 |
   | **Role** | yes | PIP에서 호스트 파트너의 역할 |
   ||||

   RosettaNet 디코딩 작업에서 출력에는 다른 속성과 함께 **아웃바운드 신호가**포함되며, 이 신호는 파트너에게 인코딩및 반환하거나 해당 출력에 대한 다른 작업을 수행하도록 선택할 수 있습니다.

## <a name="send-or-encode-rosettanet-messages"></a>RosettaNet 메시지 보내기 또는 인코딩

1. [빈 논리 앱을 만듭니다.](quickstart-create-first-logic-app-workflow.md)

1. 통합 계정을 논리 앱에 [연결합니다.](logic-apps-enterprise-integration-create-integration-account.md#link-account)

1. RosettaNet 메시지를 인코딩하는 작업을 추가하려면 먼저 요청 트리거와 같은 논리 앱을 시작하기 위한 트리거를 추가해야 합니다.

1. 트리거를 추가한 후 **새 단계를**선택합니다.

   ![요청 트리거 추가](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. 검색 상자에서 "rosettanet"을 입력하고 이 작업을 **선택합니다.**

   !["로제타넷 인코딩" 작업 찾기 및 선택](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. 작업 속성에 대한 정보를 제공합니다.

   ![작업 세부 정보 제공](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | 속성 | 필수 | 설명 |
   |----------|----------|-------------|
   | **메시지** | yes | 인코딩할 RosettaNet 메시지  |
   | **호스트 파트너** | yes | 호스트 파트너 이름 |
   | **게스트 파트너** | yes | 게스트 파트너 이름 |
   | **PIP 코드** | yes | PIP 코드 |
   | **PIP 버전** | yes | PIP 버전 |  
   | **PIP 인스턴스 ID** | yes | 이 PIP 메시지의 고유 식별자 |  
   | **메시지 유형** | yes | 인코딩할 메시지 의 유형 |  
   | **Role** | yes | 호스트 파트너의 역할 |
   ||||

   이제 인코딩된 메시지를 파트너에게 보낼 준비가 되었습니다.

1. 인코딩된 메시지를 보내기 위해 이 예제에서는 이름이 변경된 **HTTP** 작업을 사용하여 "HTTP - 파트너에게 인코딩된 메시지 보내기"로 변경됩니다.

   ![로제타넷 메시지 전송을 위한 HTTP 작업](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   RosettaNet 표준에 따라 비즈니스 트랜잭션은 PIP에 의해 정의된 모든 단계가 완료된 경우에만 완료된 것으로 간주됩니다.

1. 호스트가 파트너에게 인코딩된 메시지를 보낸 후 호스트는 신호 및 승인을 기다립니다. 이 작업을 수행하려면 **RosettaNet 대기 응답을** 추가합니다.

   !["로제타넷대기 응답 대기" 작업 추가](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   대기 기간 및 재시도 횟수는 통합 계정의 PIP 구성을 기반으로 합니다. 응답을 받지 못하면 이 작업은 오류 알림을 생성합니다. 다시 시도를 처리하려면 항상 **인코딩을** 넣고 **Until** 루프에 응답 작업을 **기다립니다.**

   ![로제타넷 액션이 있는 루프까지](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>다음 단계

* [엔터프라이즈 통합 팩](../logic-apps/logic-apps-enterprise-integration-overview.md)을 사용하여 유효성 검사, 변환 및 기타 메시지 작업을 수행하는 방법을 알아봅니다.
* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.

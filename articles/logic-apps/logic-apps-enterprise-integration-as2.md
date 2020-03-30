---
title: B2B용 AS2 메시지 송수신
description: 엔터프라이즈 통합 팩을 사용하여 Azure 논리 앱을 사용하여 B2B 엔터프라이즈 통합 시나리오에 대한 AS2 메시지 교환
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: 0ce813e91750db3cdfa1e651a68fbb82d593eb32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650568"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에서 B2B 엔터프라이즈 통합용 AS2 메시지 교환

> [!IMPORTANT]
> 원래 AS2 커넥터가 더 이상 사용되지 않으므로 **대신 AS2(v2)** 커넥터를 사용해야 합니다. 이 버전은 원래 버전과 동일한 기능을 제공하며 논리 앱 런타임에 기본제공되며 처리량 및 메시지 크기 측면에서 상당한 성능 향상을 제공합니다. 또한 기본 v2 커넥터는 통합 계정에 대한 연결을 만들 필요가 없습니다. 대신 필수 구성 프로그램에 설명된 대로 통합 계정을 커넥터를 사용하려는 논리 앱에 연결해야 합니다.

Azure Logic Apps에서 AS2 메시지로 작업하려면 AS2 통신 관리를 위한 트리거 및 작업을 제공하는 AS2 커넥터를 사용할 수 있습니다. 예를 들어 메시지를 전송할 때 보안 및 안정성을 설정하려면 다음 작업을 사용할 수 있습니다.

* 메시지 처리 알림(MDN)을 통해 암호화, 디지털 서명 및 승인을 제공하기 위한 [ **AS2 인코딩** 작업을](#encode) 통해 부인 방지를 지원합니다. 예를 들어 이 작업은 AS2/HTTP 헤더를 적용하고 구성할 때 이러한 작업을 수행합니다.

  * 나가는 메시지에 서명합니다.
  * 나가는 메시지를 암호화합니다.
  * 메시지를 압축합니다.
  * MIME 헤더로 파일 이름을 전송합니다.

* 메시지 처리 알림(MDN)을 통해 암호 해독, 디지털 서명 및 승인을 제공하기 위한 [ **AS2 디코딩** 작업입니다.](#decode) 예를 들어 이 작업은 다음 작업을 수행합니다.

  * AS2/HTTP 헤더를 처리합니다.
  * 수신된 MdN을 원래 아웃바운드 메시지와 조정합니다.
  * 부인 방지 데이터베이스의 레코드를 업데이트하고 상호 연관시다.
  * AS2 상태 보고에 대한 레코드를 씁니다.
  * 페이로드 내용을 base64 인코딩으로 출력합니다.
  * MDN이 필요한지 여부를 결정합니다. AS2 계약에 따라 MDN이 동기또는 비동기인지 여부를 결정합니다.
  * AS2 규약에 따라 동기 또는 비동기 MDN을 생성합니다.
  * MDN에서 상관 관계 토큰 및 속성을 설정합니다.

  이 작업은 구성할 때도 이러한 작업을 수행합니다.

  * 서명을 확인합니다.
  * 메시지의 암호를 해독합니다.
  * 메시지 압축을 해제합니다.
  * 메시지 ID 중복을 확인하고 허용하지 않습니다.

이 문서에서는 기존 논리 앱에 AS2 인코딩 및 디코딩 작업을 추가하는 방법을 보여 주며 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 아직 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* AS2 커넥터를 사용하려는 논리 앱과 논리 앱의 워크플로를 시작하는 트리거입니다. AS2 커넥터는 트리거가 아닌 작업만 제공합니다. 논리 앱을 처음 사용하는 경우 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토합니다.

* Azure 구독과 연결되고 AS2 커넥터를 사용할 예정인 논리 앱에 연결된 [통합 계정입니다.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 논리 앱과 통합 계정은 모두 동일한 위치 또는 Azure 지역에 있어야 합니다.

* AS2 ID 한정자를 사용하여 통합 계정에 이미 정의한 거래 [파트너](../logic-apps/logic-apps-enterprise-integration-partners.md) 두 개 이상.

* AS2 커넥터를 사용하려면 먼저 거래 업체 간에 AS2 [계약을](../logic-apps/logic-apps-enterprise-integration-agreements.md) 만들고 해당 계약을 통합 계정에 저장해야 합니다.

* 인증서 관리에 [Azure Key Vault를](../key-vault/key-vault-overview.md) 사용하는 경우 자격 증명 모음 키에서 **암호화** 및 **암호 해독** 작업을 허용하는지 확인합니다. 그렇지 않으면 인코딩 및 디코딩 작업이 실패합니다.

  Azure 포털에서 키 자격 증명 모음의 키로 이동하여 키의 **허용된 작업을**검토하고 **암호화** 및 **암호 해독** 작업이 선택되어 있는지 확인합니다.

  ![볼트 키 작업 확인](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>AS2 메시지 인코딩

1. 아직 하지 않은 경우 [Azure Portal에서](https://portal.azure.com)논리 앱 디자이너에서 논리 앱을 엽니다.

1. 디자이너에서 논리 앱에 새 작업을 추가합니다.

1. **작업 선택** 및 검색 상자 아래에서 **모두**를 선택합니다. 검색 상자에서 "as2 인코딩"을 입력하고 AS2(v2) 동작: **AS2 인코딩을** 선택해야 합니다.

   !["AS2 인코딩" 선택](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. 이제 이러한 속성에 대한 정보를 제공합니다.

   | 속성 | 설명 |
   |----------|-------------|
   | **인코딩할 메시지** | 메시지 페이로드 |
   | **AS2 부터** | AS2 계약에 지정된 메시지 발신자의 식별자 |
   | **AS2 ~** | AS2 계약에 지정된 메시지 수신자의 식별자 |
   |||

   예를 들어:

   ![메시지 인코딩 속성](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

> [!TIP]
> 서명된 메시지 나 암호화 된 메시지를 보낼 때 문제가 발생하면 다른 SHA256 알고리즘 형식을 시도하는 것이 좋습니다. AS2 사양은 SHA256 형식에 대한 정보를 제공하지 않으므로 각 공급자는 자체 구현 또는 형식을 사용합니다.

<a name="decode"></a>

## <a name="decode-as2-messages"></a>AS2 메시지 디코딩

1. 아직 하지 않은 경우 [Azure Portal에서](https://portal.azure.com)논리 앱 디자이너에서 논리 앱을 엽니다.

1. 디자이너에서 논리 앱에 새 작업을 추가합니다.

1. **작업 선택** 및 검색 상자 아래에서 **모두**를 선택합니다. 검색 상자에서 "as2 디코딩"을 입력하고 AS2(v2) 동작: **AS2 디코딩을** 선택해야 합니다.

   !["AS2 디코딩" 선택](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. **인코딩할 메시지** 및 **Message 헤더** 속성의 경우 이전 트리거 또는 작업 출력에서 이러한 값을 선택합니다.

   예를 들어 논리 앱이 요청 트리거를 통해 메시지를 수신한다고 가정합니다. 해당 트리거에서 출력을 선택할 수 있습니다.

   ![요청 출력에서 본문 및 헤더를 선택합니다.](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>예제

완벽하게 작동하는 논리 앱 및 샘플 AS2 시나리오를 배포하려면 [AS2 논리 앱 템플릿 및 시나리오](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)를 참조하세요.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 파일에 설명된 작업 및 제한과 같은 이 커넥터에 대한 자세한 내용은 [커넥터의 참조 페이지를](https://docs.microsoft.com/connectors/as2/)참조하십시오. 

> [!NOTE]
> [통합 서비스 환경(ISE)의](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)논리 앱의 경우 이 커넥터의 원래 ISE 레이블 버전은 [ISE 메시지 제한을](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) 대신 사용합니다.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.

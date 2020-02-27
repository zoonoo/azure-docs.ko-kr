---
title: B2B 용 AS2 메시지 송신 및 수신
description: 엔터프라이즈 통합 팩와 함께 Azure Logic Apps를 사용 하 여 B2B 엔터프라이즈 통합 시나리오용 AS2 메시지 교환
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: 0ce813e91750db3cdfa1e651a68fbb82d593eb32
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650568"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에서 B2B 엔터프라이즈 통합용 AS2 메시지 교환

> [!IMPORTANT]
> 원래 AS2 커넥터는 더 이상 사용 되지 않으므로 **as2 (v2)** 커넥터를 대신 사용 해야 합니다. 이 버전은 원래 버전과 동일한 기능을 제공 하 고, Logic Apps 런타임으로 기본 사용 되며, 처리량 및 메시지 크기 측면에서 상당한 성능 향상을 제공 합니다. 또한 native v2 커넥터를 사용할 경우 통합 계정에 대 한 연결을 만들 필요가 없습니다. 대신, 필수 구성 요소에 설명 된 대로 커넥터를 사용 하려는 논리 앱에 통합 계정을 연결 해야 합니다.

Azure Logic Apps에서 AS2 메시지를 사용 하려면 as2 통신을 관리 하기 위한 트리거와 작업을 제공 하는 AS2 커넥터를 사용할 수 있습니다. 예를 들어 메시지를 전송할 때 보안 및 안정성을 설정 하려면 다음 작업을 사용할 수 있습니다.

* 비 거부를 지 원하는 MDN (메시지 처리 알림)을 통해 암호화, 디지털 서명 및 승인을 제공 하기 위한 [ **AS2 인코딩** 동작](#encode) 입니다. 예를 들어이 작업은 AS2/HTTP 헤더를 적용 하 고 구성 될 때 이러한 작업을 수행 합니다.

  * 보내는 메시지에 서명 합니다.
  * 나가는 메시지를 암호화 합니다.
  * 메시지를 압축 합니다.
  * MIME 헤더의 파일 이름을 전송 합니다.

* MDN (메시지 처리 알림)을 통해 암호 해독, 디지털 서명 및 승인을 제공 하기 위한 [ **AS2 디코드** 동작](#decode) 입니다. 예를 들어이 작업은 다음 작업을 수행 합니다.

  * AS2/HTTP 헤더를 처리 합니다.
  * 수신 된 Mdn를 원래 아웃 바운드 메시지와 조정 합니다.
  * 부인 방지 데이터베이스에서 레코드를 업데이트 하 고 상관 관계를 합니다.
  * AS2 상태 보고에 대 한 레코드를 씁니다.
  * 페이로드 콘텐츠를 b a s e 64로 인코딩된로 출력 합니다.
  * Mdn가 필요한 지 여부를 결정 합니다. AS2 규약을 기반으로 Mdn이 동기 인지 비동기 인지 결정 합니다.
  * AS2 규약에 따라 동기 또는 비동기 Mdn 생성 합니다.
  * Mdn의 상관 관계 토큰과 속성을 설정 합니다.

  또한이 작업은 구성 될 때 다음 작업을 수행 합니다.

  * 서명을 확인합니다.
  * 메시지의 암호를 해독 합니다.
  * 메시지의 압축을 풉니다.
  * 메시지 ID 중복을 확인 하 고 허용 하지 않습니다.

이 문서에서는 기존 논리 앱에 AS2 인코딩 및 디코딩 작업을 추가 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독 아직 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* AS2 커넥터를 사용 하려는 논리 앱 및 논리 앱의 워크플로를 시작 하는 트리거 AS2 커넥터는 트리거가 아니라 동작만 제공 합니다. 논리 앱을 처음 사용하는 경우 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토합니다.

* Azure 구독과 연결 되 고 AS2 커넥터를 사용 하려는 논리 앱에 연결 된 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 입니다. 논리 앱과 통합 계정은 모두 동일한 위치 또는 Azure 지역에 있어야 합니다.

* AS2 id 한정자를 사용 하 여 통합 계정에 이미 정의 된 두 개 이상의 [거래 업체](../logic-apps/logic-apps-enterprise-integration-partners.md)

* AS2 커넥터를 사용 하려면 먼저 거래 업체 간에 AS2 [규약](../logic-apps/logic-apps-enterprise-integration-agreements.md) 을 만들고 통합 계정에 해당 규약을 저장 해야 합니다.

* 인증서 관리에 [Azure Key Vault](../key-vault/key-vault-overview.md) 를 사용 하는 경우 자격 증명 모음 키에서 **암호화** 및 **암호 해독** 작업을 허용 하는지 확인 합니다. 그렇지 않으면 인코딩 및 디코딩 작업이 실패 합니다.

  Azure Portal에서 키 자격 증명 모음의 키로 이동 하 고, 키의 **허용 된 작업**을 검토 하 고, **암호화** 및 **암호 해독** 작업이 선택 되었는지 확인 합니다. 예를 들면 다음과 같습니다.

  ![자격 증명 모음 키 작업 확인](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>AS2 메시지 인코딩

1. 아직 [Azure Portal](https://portal.azure.com)하지 않은 경우 논리 앱 디자이너에서 논리 앱을 엽니다.

1. 디자이너에서 논리 앱에 새 작업을 추가 합니다.

1. **작업 선택** 및 검색 상자 아래에서 **모두**를 선택합니다. 검색 상자에 "as2 인코드"를 입력 하 고 AS2 (v2) 작업을 선택 했는지 확인 합니다. **As2 인코드**

   !["AS2 인코드"를 선택 합니다.](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. 이제 이러한 속성에 대 한 정보를 제공 합니다.

   | 속성 | 설명 |
   |----------|-------------|
   | **인코딩할 메시지** | 메시지 페이로드 |
   | **AS2 원본** | AS2 규약에서 지정한 메시지 보낸 사람의 식별자입니다. |
   | **AS2 to** | AS2 규약에서 지정한 메시지 수신기의 식별자입니다. |
   |||

   예를 들면 다음과 같습니다.

   ![메시지 인코딩 속성](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

> [!TIP]
> 서명 되거나 암호화 된 메시지를 보낼 때 문제가 발생 하면 다른 SHA256 알고리즘 형식을 사용해 보세요. AS2 사양은 SHA256 형식에 대 한 정보를 제공 하지 않으므로 각 공급자는 고유한 구현 또는 형식을 사용 합니다.

<a name="decode"></a>

## <a name="decode-as2-messages"></a>AS2 메시지 디코딩

1. 아직 [Azure Portal](https://portal.azure.com)하지 않은 경우 논리 앱 디자이너에서 논리 앱을 엽니다.

1. 디자이너에서 논리 앱에 새 작업을 추가 합니다.

1. **작업 선택** 및 검색 상자 아래에서 **모두**를 선택합니다. 검색 상자에 "as2 디코드"를 입력 하 고 AS2 (v2) 작업: **As2 디코딩** 을 선택 했는지 확인 합니다.

   !["AS2 디코드"를 선택 합니다.](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. **인코딩할 메시지** 와 **메시지 헤더** 속성에 대해 이전 트리거 또는 작업 출력에서 이러한 값을 선택 합니다.

   예를 들어 논리 앱에서 요청 트리거를 통해 메시지를 수신 한다고 가정 합니다. 해당 트리거에서 출력을 선택할 수 있습니다.

   ![요청 출력에서 본문 및 헤더를 선택합니다.](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>샘플

완벽하게 작동하는 논리 앱 및 샘플 AS2 시나리오를 배포하려면 [AS2 논리 앱 템플릿 및 시나리오](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)를 참조하세요.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 파일에 설명 된 작업 및 제한과 같이이 커넥터에 대 한 자세한 기술 정보는 [커넥터의 참조 페이지](https://docs.microsoft.com/connectors/as2/)를 참조 하세요. 

> [!NOTE]
> [Ise (통합 서비스 환경](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md))의 논리 앱의 경우이 커넥터의 원래 ise 레이블 버전은 [ise 메시지 제한을](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) 대신 사용 합니다.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.

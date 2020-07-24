---
title: 플랫 파일 인코딩 또는 디코딩
description: 엔터프라이즈 통합 팩를 사용 하 여 Azure Logic Apps에서 엔터프라이즈 통합을 위한 플랫 파일 인코딩 또는 디코딩
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 05/09/2020
ms.openlocfilehash: aebce8f284ed4bb21d99efffc8dd6d0c51b39533
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87001488"
---
# <a name="encode-and-decode-flat-files-in-azure-logic-apps-by-using-the-enterprise-integration-pack"></a>엔터프라이즈 통합 팩을 사용하여 Azure Logic Apps에서 플랫 파일 인코딩 및 디코딩

B2B (기업 간) 시나리오에서 비즈니스 파트너에 게 XML 콘텐츠를 보내기 전에 먼저 해당 콘텐츠를 인코딩해야 할 수 있습니다. 논리 앱을 빌드하여 **플랫 파일** 커넥터를 사용 하 여 플랫 파일을 인코딩 및 디코딩할 수 있습니다. 논리 앱은 요청 트리거, 다른 앱 또는 [Azure Logic Apps에서 지 원하는 다른 커넥터](../connectors/apis-list.md)와 같은 다양 한 소스에서이 XML 콘텐츠를 가져올 수 있습니다. 자세한 내용은 [Azure Logic Apps](logic-apps-overview.md)란?을 참조 하세요.

## <a name="prerequisites"></a>필수 조건

* Azure 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 논리 앱 워크플로를 시작 하는 트리거 및 **플랫 파일** 커넥터를 사용 하려는 논리 앱입니다. **플랫 파일** 커넥터는 트리거가 아니라 동작만 제공 합니다. 트리거 또는 다른 작업을 사용 하 여 XML 콘텐츠를 인코딩 또는 디코딩을 위해 논리 앱에 제공할 수 있습니다. 논리 앱을 처음 접하는 경우 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토하세요.

* Azure 구독과 연결 되 고 **플랫 파일** 커넥터를 사용 하려는 [논리 앱에 연결](./logic-apps-enterprise-integration-create-integration-account.md#link-account) 된 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 입니다. 논리 앱과 통합 계정이 동일한 위치 또는 Azure 지역에 있어야 합니다.

* XML 콘텐츠를 인코딩 또는 디코딩하는 통합 계정에 업로드 한 플랫 파일 [스키마](logic-apps-enterprise-integration-schemas.md)

* 통합 계정에 이미 정의 된 두 개 이상의 [거래 업체](logic-apps-enterprise-integration-partners.md)

## <a name="add-flat-file-encode-action"></a>플랫 파일 인코딩 작업 추가

1. [Azure Portal](https://portal.azure.com)의 Logic Apps 디자이너에서 논리 앱을 엽니다.

1. 논리 앱의 트리거 또는 동작에서 **새 단계**  >  **작업 추가**를 선택 합니다. 이 예제에서는 **HTTP 요청을 받을 때**명명 된 요청 트리거를 사용 하 고 논리 앱 외부에서 인바운드 요청을 처리 합니다.

   > [!TIP]
   > JSON 스키마를 제공 하는 것은 선택 사항입니다. 인바운드 요청의 샘플 페이로드가 있으면 **샘플 페이로드를 사용 하 여 스키마 생성**을 선택 하 고 샘플 페이로드를 입력 한 다음 **완료**를 선택 합니다. 스키마가 **요청 본문 JSON 스키마** 상자에 나타납니다.

1. **작업 선택**아래에서를 입력 `flat file` 합니다. 작업 목록에서 **플랫 파일 인코딩** 작업을 선택 합니다.

   !["플랫 파일 인코딩" 동작을 선택 합니다.](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-encoding.png)

1. **콘텐츠** 상자 내부를 클릭 하 여 동적 콘텐츠 목록이 표시 되도록 합니다. 목록에서 **HTTP 요청이 수신** 되는 경우 섹션에서 트리거의 요청 본문 출력과 인코딩할 콘텐츠를 포함 하는 **body** 속성을 선택 합니다.

   ![동적 콘텐츠 목록에서 인코딩할 콘텐츠 선택](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode.png)

   > [!TIP]
   > 동적 콘텐츠 목록에 **본문** 속성이 표시 되지 않으면 **HTTP 요청을 수신** 하는 경우 섹션 레이블 옆에 있는 **자세히 보기** 를 선택 합니다.
   > **콘텐츠** 상자에 디코딩할 콘텐츠를 직접 입력할 수도 있습니다.

1. **스키마 이름** 목록에서 인코딩에 사용할 연결 된 통합 계정에 있는 스키마를 선택 합니다. 예를 들면 다음과 같습니다.

   ![인코딩에 사용할 스키마 선택](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-encoding.png)

   > [!NOTE]
   > 목록에 스키마가 표시 되지 않으면 통합 계정에 인코딩에 사용할 스키마 파일이 포함 되지 않습니다. 사용 하려는 스키마를 통합 계정에 업로드 합니다.

1. 논리 앱을 저장합니다. 커넥터를 테스트 하려면 요청 트리거의 **HTTP POST URL** 속성에 표시 되는 HTTPS 끝점에 요청 하 고 요청 본문에 인코딩할 XML 콘텐츠를 포함 합니다.

이제 플랫 파일 인코딩 작업을 설정 하는 작업이 완료 되었습니다. 실제 응용 프로그램에서는 Salesforce와 같은 LOB (기간 업무) 앱에 인코딩된 데이터를 저장 하는 것이 좋습니다. 또는 인코딩된 데이터를 거래 파트너에 게 보낼 수 있습니다. 인코딩 작업의 출력을 Salesforce 또는 거래 파트너로 보내려면 [Azure Logic Apps에서 사용할 수](../connectors/apis-list.md)있는 다른 커넥터를 사용 합니다.

## <a name="add-flat-file-decode-action"></a>플랫 파일 디코딩 작업 추가

1. [Azure Portal](https://portal.azure.com)의 Logic Apps 디자이너에서 논리 앱을 엽니다.

1. 논리 앱의 트리거 또는 동작에서 **새 단계**  >  **작업 추가**를 선택 합니다. 이 예제에서는 **HTTP 요청을 받을 때**명명 된 요청 트리거를 사용 하 고 논리 앱 외부에서 인바운드 요청을 처리 합니다.

   > [!TIP]
   > JSON 스키마를 제공 하는 것은 선택 사항입니다. 인바운드 요청의 샘플 페이로드가 있으면 **샘플 페이로드를 사용 하 여 스키마 생성**을 선택 하 고 샘플 페이로드를 입력 한 다음 **완료**를 선택 합니다. 스키마가 **요청 본문 JSON 스키마** 상자에 나타납니다.

1. **작업 선택**아래에서를 입력 `flat file` 합니다. 작업 목록에서 다음 작업을 선택 합니다. **플랫 파일 디코딩**

   !["플랫 파일 디코딩" 동작을 선택 합니다.](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-decoding.png)

1. **콘텐츠** 상자 내부를 클릭 하 여 동적 콘텐츠 목록이 표시 되도록 합니다. 목록에서 **HTTP 요청이 수신** 되는 경우 섹션에서 트리거의 요청 본문 출력과 디코딩할 콘텐츠를 포함 하는 **body** 속성을 선택 합니다.

   ![동적 콘텐츠 목록에서 디코딩할 콘텐츠를 선택 합니다.](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode.png)

   > [!TIP]
   > 동적 콘텐츠 목록에 **본문** 속성이 표시 되지 않으면 **HTTP 요청을 수신** 하는 경우 섹션 레이블 옆에 있는 **자세히 보기** 를 선택 합니다. **콘텐츠** 상자에 디코딩할 콘텐츠를 직접 입력할 수도 있습니다.

1. **스키마 이름** 목록에서 디코딩에 사용할 연결 된 통합 계정에 있는 스키마를 선택 합니다. 예를 들면 다음과 같습니다.

   ![디코딩에 사용할 스키마 선택](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-decoding.png)

   > [!NOTE]
   > 목록에 스키마가 표시 되지 않으면 통합 계정에 디코딩에 사용할 스키마 파일이 포함 되지 않습니다. 사용 하려는 스키마를 통합 계정에 업로드 합니다.

1. 논리 앱을 저장합니다. 커넥터를 테스트 하려면 요청 트리거의 **HTTP POST URL** 속성에 표시 되는 HTTPS 끝점에 요청 하 고 요청 본문에서 디코드 하려는 XML 콘텐츠를 포함 합니다.

이제 플랫 파일 디코딩 작업을 설정 하는 작업이 완료 되었습니다. 실제 응용 프로그램에서는 Salesforce와 같은 LOB (기간 업무) 앱에 디코딩된 데이터를 저장 하는 것이 좋습니다. 또는 디코딩된 데이터를 거래 업체에 보낼 수 있습니다. 디코딩 작업의 출력을 Salesforce 또는 거래 파트너에 게 보내려면 [Azure Logic Apps에서 사용할 수](../connectors/apis-list.md)있는 다른 커넥터를 사용 합니다.

## <a name="next-steps"></a>다음 단계

* [엔터프라이즈 통합 팩](logic-apps-enterprise-integration-overview.md) 에 대 한 자세한 정보

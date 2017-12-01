---
title: "SOAP 커넥터 만들기 및 등록 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps에서 사용할 SOAP 커넥터 설정"
author: divyaswarnkar
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 0323b0f7ee03dce209d5a71c6711988a34ba7633
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2017
---
# <a name="create-and-register-soap-connectors-in-azure-logic-apps"></a>Azure Logic Apps에서 SOAP 커넥터 만들기 및 등록

SOAP 서비스를 논리 앱 워크플로에 통합하려면 SOAP 서비스를 설명하는 WSDL(웹 서비스 기술 언어)을 사용하여 사용자 지정 SOAP(Simple Object Access Protocol) 커넥터를 만들고 등록합니다. SOAP 커넥터는 미리 작성된 커넥터와 함께 작동하기 때문에 논리 앱의 다른 커넥터와 동일한 방식으로 사용할 수 있습니다.


## <a name="prerequisites"></a>필수 조건

SOAP 커넥터를 등록하려면 다음 항목이 필요합니다.

* Azure 구독. 구독이 없는 경우 [무료 Azure 계정](https://azure.microsoft.com/free/)으로 시작할 수 있습니다. 아니면 [종량제 구독](https://azure.microsoft.com/pricing/purchase-options/)에 등록하세요.

* 다음의 모든 항목:
  * SOAP 서비스와 API를 정의하는 WSDL의 URL
  * SOAP 서비스와 API를 정의하는 WSDL 파일

  이 자습서에서는 [Orders SOAP Service](http://fazioapisoap.azurewebsites.net/FazioService.svc?singleWsdl)라는 예제를 사용할 수 있습니다.

* 선택 사항: 사용자 지정 커넥터에 대한 아이콘으로 사용할 이미지


## <a name="1-create-your-connector"></a>1. 커넥터 만들기

1. Azure Portal의 기본 Azure 메뉴에서 **새로 만들기**를 선택합니다. 검색 창에 필터로 "Logic Apps 커넥터"를 입력하고 Enter 키를 누릅니다.

   ![새로 만들기, "Logic Apps 커넥터" 검색](./media/logic-apps-soap-connector-create-register/create-logic-apps-connector.png)

2. 결과 목록에서 **Logic Apps 커넥터** > **만들기**를 선택합니다.

   ![Logic Apps 커넥터 만들기](./media/logic-apps-soap-connector-create-register/choose-logic-apps-connector.png)

3. 테이블의 설명대로 커넥터 등록을 위한 세부 정보를 제공합니다. 완료면 **대시보드에 고정** > **만들기**를 선택합니다.

   ![Logic App 사용자 지정 커넥터 세부 정보](./media/logic-apps-soap-connector-create-register/logic-apps-soap-connector-details.png)

   | 속성 | 제안 값 | 설명 | 
   | -------- | --------------- | ----------- | 
   | **Name** | *soap-connector-name* | 커넥터 이름을 제공합니다. | 
   | **구독** | *Azure-subscription-name* | Azure 구독을 선택합니다. | 
   | **리소스 그룹** | *Azure-resource-group-name* | Azure 리소스를 구성할 Azure 그룹을 만들거나 선택합니다. | 
   | **위치**: | *deployment-region* | 커넥터에 대한 배포 영역을 선택합니다. | 
   |||| 

   Azure에서 커넥터가 배치되면 논리 앱 커넥터 메뉴가 자동으로 열립니다. 
   그렇지 않은 경우 Azure 대시보드에서 soap 커넥터를 선택합니다.

## <a name="2-define-your-connector"></a>2. 커넥터 정의

이제 커넥터를 만들기 위한 WSDL 파일 또는 URL, 커넥터에서 사용하는 인증 및 SOAP 커넥터가 제공하는 작업 및 트리거를 지정합니다.


### <a name="2a-specify-the-wsdl-file-or-url-for-your-connector"></a>2a. 커넥터의 WSDL 파일 또는 URL 지정

1. 커넥터 메뉴에서 **Logic Apps 커넥터**가 선택되어 있지 않으면 선택합니다. 도구 모음에서 **편집**을 선택합니다.

   ![사용자 지정 커넥터 편집](./media/logic-apps-soap-connector-create-register/edit-soap-connector.png)

2. SOAP 커넥터에 대한 작업 및 트리거를 생성, 보호 및 정의하기 위해 다음 테이블에 세부 정보를 제공할 수 있도록 **일반**을 선택합니다.

   1. **사용자 지정 커넥터**의 경우 API를 설명하는 WSDL 파일을 제공할 수 있도록 **API 끝점**에 대해 **SOAP**를 선택합니다.

      ![API용 WSDL 파일 제공](./media/logic-apps-soap-connector-create-register/provide-wsdl-file.png)

      | 옵션 | 형식 |설명 | 
      | ------ | ------ | ----------- | 
      | **파일에서 WSDL 업로드** | *WSDL-파일* | WSDL 파일의 위치로 이동하여 해당 파일을 선택합니다. | 
      | **URL에서 WSDL 업로드** | http://*wsdl-파일-경로* | 서비스 WSDL 파일의 URL을 제공합니다. | 
      | **SOAP를 REST로** |   | SOAP 서비스의 API를 REST API로 변환합니다. | 
      |||| 

   2. **일반 정보**에서 커넥터 아이콘을 업로드합니다. 
   일반적으로**설명**, **호스트** 및 **기준 URL** 필드는 WSDL 파일에서 자동으로 채워집니다. 
   그렇지 않은 경우 테이블의 설명대로 다음 정보를 추가하고 **계속**을 선택합니다. 

      ![커넥터 세부 정보](./media/logic-apps-soap-connector-create-register/add-general-details.png)

      | 옵션 또는 설정 | 형식 | 설명 | 
      | ----------------- | ------ | ----------- | 
      | **아이콘 업로드** | *png-or-jpg-file-under-1-MB* | 커넥터를 나타내는 아이콘 <p>색: 컬러 배경에 흰색 로고를 사용하는 것이 좋습니다. <p>크기: 230픽셀 사각형 내에 ~160 픽셀 로그 | 
      | **아이콘 배경색** | *icon-brand-color-hexadecimal-code* | <p>아이콘 파일의 배경색과 일치하는 아이콘 뒤의 색상입니다. <p>형식: 16진수. 예를 들어 #007ee5는 파란색을 나타냅니다. | 
      | **설명** | *connector-description* | 커넥터에 대한 간단한 설명을 제공합니다. | 
      | **호스트** | *connector-host* | SOAP 서비스에 대한 호스트 도메인을 제공합니다. | 
      | **기준 URL** | *connector-base-URL* | SOAP 서비스에 대한 기준 URL을 제공합니다. | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. 커넥터에서 사용할 인증을 설명합니다.

1. 이제 커넥터에 사용되는 인증을 검토하거나 설명할 수 있도록 **보안**을 선택합니다. 인증은 서비스와 클라이언트 사이에 사용자의 ID가 적절히 흐르도록 합니다.

   기본적으로 커넥터의 **인증 유형**은 **인증 안 함**으로 설정됩니다.
   
   ![인증 유형](./media/logic-apps-soap-connector-create-register/security-authentication-options.png)

   인증 유형을 변경하려면 **편집**을 선택합니다. **기본 인증**을 선택할 수 있습니다. 기본값 이외의 매개 변수 레이블을 사용하려면 **매개 변수 레이블** 아래에서 매개 변수 레이블을 업데이트합니다.

   ![기본 인증](./media/logic-apps-soap-connector-create-register/security.png)

   
2. 보안 정보를 입력한 후 커넥터를 저장하려면 페이지 위쪽에서 **커넥터 업데이트**를 선택한 다음 **계속**을 선택합니다. 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. 커넥터의 작업 및 트리거 검토, 업데이트 또는 정의

1. 이제 사용자가 자신의 워크플로에 추가할 수 있는 새로운 작업 및 트리거를 검토, 편집 또는 정의할 수 있도록 **정의**를 선택합니다.

   작업 및 트리거는 **정의** 페이지를 자동으로 채우고 요청 및 응답 값을 포함하는 WSDL 파일에 정의된 작업을 기반으로 합니다. 따라서 필요한 작업이 여기에 표시되어 있으면 이 페이지에서 변경하지 않고 등록 프로세스의 다음 단계로 이동할 수 있습니다.

   ![커넥터 정의](./media/logic-apps-soap-connector-create-register/definition.png)

2. 선택적으로, 기존 작업 및 트리거를 편집하거나 새 작업을 추가하려면 [이러한 단계를 계속 진행합니다](logic-apps-custom-connector-register.md#add-action-or-trigger).


## <a name="3-finish-creating-your-connector"></a>3. 커넥터 생성 완료

준비가 되면 커넥터를 배포할 수 있도록 **커넥터 업데이트**를 선택합니다. 

축하합니다. 이제 논리 앱을 만들 때 논리 앱 디자이너에서 커넥터를 찾아서 논리 앱에 해당 커넥터를 추가할 수 있습니다.

![논리 앱 디자이너에서 커넥터 찾기](./media/logic-apps-soap-connector-create-register/soap-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>다른 Logic Apps 사용자와 커넥터 공유

등록되었으나 인증되지 않은 사용자 지정 커넥터는 Microsoft에서 관리하는 커넥터처럼 작동하지만 커넥터의 작성자와 논리 앱이 배포된 지역에서 논리 앱에 대해 동일한 Azure Active Directory 테넌트 및 Azure 구독이 있는 사용자*만* 보고 사용할 수 있습니다. 공유는 선택 사항이지만 다른 사용자와 커넥터를 공유하려는 시나리오가 있을 수 있습니다. 

> [!IMPORTANT]
> 커넥터를 공유하는 경우 다른 사람이 해당 커넥터를 사용하기 시작할 수 있습니다. 
> ***커넥터를 삭제하면 해당 커넥터에 대한 모든 연결이 삭제됩니다.***
 
이러한 경계 외부의 사용자(예: 모든 Logic Apps 사용자)와 커넥터를 공유하려면 [Microsoft 인증을 위해 커넥터를 제출하세요](../logic-apps/custom-connector-submit-certification.md).

## <a name="faq"></a>FAQ

**Q:** SOAP 커넥터를 일반적으로 사용(GA)할 수 있나요? </br>
**A:** SOAP 커넥터는 **미리 보기** 상태이며 아직 GA 서비스가 지원되지 않습니다.

**Q:** SOAP 커넥터에 대한 제한 사항 및 알려진 문제점이 있나요? </br>
**A:** 예, [SOAP 커넥터의 제한 사항 및 알려진 문제](../api-management/api-management-api-import-restrictions.md#wsdl)를 참조하세요.

**Q:** 사용자 지정 커넥터에 제한이 있나요? </br>
**A:** 예, [여기에서 사용자 지정 커넥터 제한](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits)을 참조하세요.

## <a name="get-support"></a>지원 받기

* 개발 및 온보딩에 대한 지원이 필요하거나 등록 마법사에서 사용할 수 없는 기능을 요청하려면 [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com)에 문의하세요. Microsoft는 이 계정에서 개발자 질문 및 문제점을 모니터링한 후 적절한 팀으로 전송합니다.

* 질문하거나 질문에 답변하고, 다른 Azure Logic Apps 사용자가 무엇을 하는지 보려면 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문하세요.

* Logic Apps 개선에 도움을 주려면 [Logic Apps 사용자 의견 사이트](http://aka.ms/logicapps-wish)에서 투표하고 아이디어를 제출하세요. 

## <a name="next-steps"></a>다음 단계

* [선택 사항: 커넥터 인증](../logic-apps/custom-connector-submit-certification.md)
* [사용자 지정 커넥터 FAQ](../logic-apps/custom-connector-faq.md)
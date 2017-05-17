---
title: "Azure에서 서버 없는 소셜 미디어 대시보드 구축 | Microsoft Docs"
description: "Azure에서 서버 없는 소셜 미디어 대시보드 구축"
services: functions, logic-apps, cognitive-services
keywords: "워크플로, 클라우드 앱, 클라우드 서비스, 비즈니스 프로세스, 시스템 통합, Enterprise Application Integration, EAI"
documentationcenter: 
author: rick-anderson
manager: wpickett
editor: 
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: riande
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0d3eb2af197e9923d8e4a86bf1a0033f61e3c568
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---

# <a name="building-a-serverless-social-media-dashboard-in-azure"></a>Azure에서 서버 없는 소셜 미디어 대시보드 구축

[Azure Functions](functions-overview.md)는 [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)와 통합되어 다른 Azure 및 타사 서비스와의 복합 오케스트레이션을 구축할 수 있습니다. 이 항목에서는 소셜 미디어 피드에서 논리 앱을 트리거하고 [Azure Cognitive Services](../cognitive-services/Welcome.md)로 텍스트를 분석하는 방법을 보여 줍니다.

이 문서에서는 다음을 수행하는 논리 앱을 Azure Portal에서 만드는 방법을 보여 줍니다.

> [!div class="checklist"]
> * 제공한 키워드 또는 해시 태그를 사용하여 새 트윗을 확인합니다.
> * **감정 검색** 커넥터를 사용하여 트윗 감정(좋음부터 나쁨)을 평가합니다.
> * Azure 함수를 사용하여 트윗 감정을 3가지 범주(나쁨, 보통 및 좋음에 대해 각각 RED, YELLOW 또는 GREEN)로 처리합니다.
> * 조건을 사용하여 감정이 RED(나쁨)인지 확인합니다.
> * 조건이 RED이면 전자 메일을 보냅니다.

다음 이미지는 디자이너의 논리 앱 부분을 보여 줍니다.

![논리 앱 디자이너에서 앱의 처음 2단계를 보여 주는 이미지](media/functions-twitter-email/designer1.png)

## <a name="prerequisites"></a>필수 조건

* Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/) 을 만듭니다.
* Twitter 계정입니다.

## <a name="create-a-function-app"></a>함수 앱 만들기
 
[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal2.md)]

### <a name="create-a-categorize-function"></a>범주 함수 만들기

함수 앱 배포가 완료되면 새 함수 앱을 엽니다. 이 섹션에서는 함수를 사용하여 트윗 감정을 3가지 범주(나쁨, 보통 및 좋음에 대해 각각 RED, YELLOW 또는 GREEN)로 구분합니다.

![함수 앱 블레이드, 함수 +](media/functions-twitter-email/add_fun.png)

기본 **웹후크 + API**, **CSharp**을 그대로 두고 **이 함수 만들기**를 선택합니다.

![함수 앱 블레이드, 함수 +](media/functions-twitter-email/add_fun2.png)

빌드하는 앱이 필요할 때 호출할 수 있는 웹후크/API(HTTP 트리거라고도 함) 함수를 만들었습니다. 일정에 따라 실행되는 함수를 만들려면 타이머 함수를 만듭니다.

*run.csx* 파일 내용을 다음 코드로 바꿉니다.

```c#
using System.Net;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    string category = "GREEN";

    // Get request body.
    double score = await req.Content.ReadAsAsync<double>();

    if (score < .3)
    {
        category = "RED";
    }
    else if (score < .6)
    {
        category = "YELLOW";
    }

    return req.CreateResponse(HttpStatusCode.OK, category);
}
```

변경 내용을 저장합니다.

### <a name="test-the-function"></a>함수 테스트

**테스트**(코드 상자 오른쪽)를 선택합니다.  **요청 본문** 텍스트 상자에 0.2를 입력하고 **실행**을 선택합니다. 출력에는 "RED"가 표시되고 HTTP 상태는 200 OK입니다.

 ![test ](media/functions-twitter-email/test.png)

## <a name="cognitive-services"></a>Cognitive Services

Cognitive Services 계정을 만듭니다. Cognitive Services 계정은 모니터링하는 트윗의 감정을 검색하는 데 필요합니다.

**새로 만들기 > 인텔리전스 + 분석 > Cognitive Services**로 이동합니다. 각 필수 필드를 설정합니다.

![Cognitive 계정 만들기 블레이드](media/functions-twitter-email/cog_svcs_account.png)

| 필드               | 샘플 값 | 주석 |
| ----------------- | ------------ | ------------- |
| 계정 이름 | MyCognitiveServicesAccnt | 고유한 이름을 입력합니다. |
| API 형식 | Text Analytics API | 텍스트 분석 선택 |
| 위치 | 미국 서부 | 현재 **미국 서부**만 사용할 수 있습니다. |
| 가격 책정 계층  | F0 | 호출에서 실행하는 경우 더 높은 계층을 설정합니다.|
| 리소스 그룹 | rg1 | 이전에 지정한 리소스 그룹을 사용합니다.|

### <a name="copy-the-cognitive-services-key"></a>Cognitive Services 키 복사

**키**를 선택합니다. 나중에 키가 필요합니다.

 ![구성](media/functions-twitter-email/keys.png)

## <a name="create-a-logic-app"></a>논리 앱 만들기

Azure Portal에서 **새로 만들기 >  엔터프라이즈 통합 > 논리 앱**을 클릭합니다.

![새 논리 앱 단계 이전 단계](media/functions-twitter-email/new_logicApp.png)

**논리 앱 만들기** 블레이드에서 각 필드에 값을 입력하고 **만들기**를 선택합니다.

![논리 앱 만들기 단계 이전 단계](media/functions-twitter-email/new_logicApp2.png)

논리 앱이 만들어지면 디자이너에서 열립니다. **빈 논리 앱** 템플릿을 선택합니다.

![빈 논리 앱](media/functions-twitter-email/blank.png)

## <a name="add-a-trigger-to-twitter"></a>Twitter에 트리거 추가

**논리 앱 디자이너**에는 연결할 수 있는 많은 서비스 및 트리거가 표시됩니다.

**Twitter** 서비스를 선택합니다.

![twitter 커넥터](media/functions-twitter-email/twitter_connector.png)

트리거 **새 트윗이 게시될 때**를 선택합니다.

![새 트윗이 게시될 때 트리거](media/functions-twitter-email/tw_trig.png)

Twitter 계정에 로그인합니다.

![Twitter 계정에 로그인](media/functions-twitter-email/signin_twit.png)

암호를 입력하고 **앱 권한 부여**를 선택합니다.

![위의 새 창에서 twitter 인증](media/functions-twitter-email/auth_twit.png)

검색 텍스트, 빈도 및 간격을 입력합니다. 널리 사용되는 해시 태그(예: #football, #soccer 또는 #futbol)를 지정하면 Cognitive Services 서비스에서 할당된 모든 서비스 호출을 빠르게 사용할 수 있습니다. 호출에서 실행하는 경우 가격 책정 계층을 늘릴 수 있습니다. 

15분마다 #Azure를 검색합니다.

![15분마다 #Azure](media/functions-twitter-email/azure_tweet.png)

앱을 저장합니다.

### <a name="add-a-text-analytics-connector"></a>**텍스트 분석** 커넥터 추가

텍스트 분석 커넥터는 트윗 감정을 검색합니다.

**새 단계**를 선택한 다음 **작업 추가**를 선택합니다.

![새 단계 후 작업 추가](media/functions-twitter-email/new_step.png)

**텍스트 분석** 커넥터를 추가합니다.

![작업 창 선택](media/functions-twitter-email/choose_action.png)

**감정 검색** 작업을 선택합니다. 감정 등급은 주로 정확한 편이지만 텍스트를 잘못 해석하기도 합니다.

![감정 검색](media/functions-twitter-email/detect_sent.png)

### <a name="create-the-detect-sentiment-action"></a>감정 검색 작업 만들기

  * **MyKey**라는 연결 이름을 입력합니다.
  * [Cognitive Services 서비스 계정 만들기](#cognitive-services) 단계에서 만든 키를 복사한 후 붙여 넣습니다.
  * **만들기**를 선택합니다.
  * 앱을 저장합니다.

![감정 검색](media/functions-twitter-email/ta_detect_sent.png)

**분석할 텍스트**에 대해 **트윗 텍스트** 아이콘을 선택합니다.

![감정 검색](media/functions-twitter-email/ds_tta.png)

![감정 검색](media/functions-twitter-email/ds_tta2.png)

앱을 저장합니다.

## <a name="connect-to-the-azure-function"></a>Azure 함수에 연결

이 섹션에서는 이전에 만든 RED, YELLOW 또는 GREEN으로 트윗 감정을 분류하는 함수를 추가합니다.

* Logic Apps 디자이너에서 **새 단계**를 선택한 다음 **작업 추가**를 선택합니다.
* **Azure Functions**를 선택합니다.
* **Azure 함수 선택**을 선택합니다.

![Azure 함수 선택을 보여 주는 Azure 함수 상자](media/functions-twitter-email/choose_fun.png)

* 이전에 만든 Azure 함수를 선택합니다.
* **점수**를 선택하여 **요청 본문**을 채웁니다.

![Score](media/functions-twitter-email/trigger_score.png)

앱을 저장합니다.

## <a name="add-email-notification"></a>메일 알림 추가

이 섹션에서는 부정적인 감정 트윗(조건 RED)에 대한 조건부 검사를 추가합니다.

* **새 단계**를 선택합니다.
* **조건 추가**를 선택합니다.
* 첫 번째 **값 선택** 텍스트 상자에서 **본문**을 선택합니다.
* 두 번째 **값 선택** 텍스트 상자에 "RED"를 입력입니다.
* 앱을 저장합니다.

![조건 상자](media/functions-twitter-email/condition.png)

* **IF YES, DO NOTHING** 상자에서 **작업 추가**를 선택합니다.
* **모든 서비스 및 작업 검색** 상자에 Outlook 또는 Gmail을 입력합니다. 이 자습서에서는 Outlook이 사용됩니다. Gmail 지침에 대해서는 [Gmail 작업 추가](../logic-apps/logic-apps-create-a-logic-app.md#add-an-action-that-responds-to-your-trigger)를 참조하세요. 참고: 개인 [Microsoft 계정](https://account.microsoft.com/account)이 있는 경우 Outlook.com 계정에 사용할 수 있습니다.

![작업 상자 선택](media/functions-twitter-email/outlook.png)

**Outlook.com 전자 메일 보내기**를 선택합니다.

![Outlook.com 상자](media/functions-twitter-email/sendEmail.png)

Outlook.com에 로그인합니다.

![로그인 상자](media/functions-twitter-email/signin_outlook.png)

다음 항목을 입력합니다.

   * **받는 사람**: 메시지를 보낼 전자 메일
   * **제목**: Score
   * **본문**: 위치 및 트윗 텍스트

![전자 메일 보내기 상자](media/functions-twitter-email/sendEmail2.png)

앱을 저장합니다.
**실행**을 선택하여 앱을 시작합니다.

### <a name="check-the-status"></a>상태 확인

논리 앱 블레이드에서 **개요**를 선택하고 **실행 기록** 열에서 행을 선택합니다.

![개요 블레이드](media/functions-twitter-email/over1.png)

다음 이미지는 조건이 참이 아닐 때 전자 메일 전송되지 않는다는 실행 세부 정보를 보여 줍니다.

![개요 블레이드](media/functions-twitter-email/skipped.png)

**전자 메일 보내기** 함수를 즉시 테스트하려는 경우

* 첫 번째 단계(**새 트윗이 게시 될 때**)의 **INPUTS**를 #football, #soccer, #futbol 등의 인기 있는 용어로 변경합니다.

인기 있는 용어를 처리하면 인기가 별로 없는 용어보다 더 많은 리소스가 사용됩니다. 전자 메일이 작동하는지 확인한 후에 검색 용어를 변경할 수도 있습니다.

다음 이미지는 조건이 참일 때 전자 메일 전송된다는 실행 세부 정보를 보여 줍니다.

![개요 블레이드](media/functions-twitter-email/sent.png)

서비스 상자 중 하나를 선택해서 실행에 사용되는 데이터에 대한 자세한 정보를 표시할 수 있습니다. **새 트윗이 게시될 때**를 선택하면 검색 텍스트와 사용하고 있지 않은 출력을 비롯한 모든 출력이 표시됩니다.

## <a name="next-steps"></a>다음 단계

*  [Azure Functions 소개](functions-overview.md)
*  [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)
*  [조건 추가 및 워크플로 실행](../logic-apps/logic-apps-use-logic-app-features.md)
*  [논리 앱 템플릿](../logic-apps/logic-apps-use-logic-app-templates.md)
*  [Azure Resource Manager 템플릿에서 논리 앱 만들기](../logic-apps/logic-apps-arm-provision.md)

## <a name="get-help"></a>도움말 보기

질문하고, 질문에 답변하고, 다른 Azure Logic Apps 사용자가 어떤 일을 하는지 알아보려면 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문하세요.

Azure Logic Apps 및 커넥터 개선에 도움을 주려면 [Azure Logic Apps 사용자 의견 사이트](http://aka.ms/logicapps-wish)에서 투표하고 아이디어를 제출하세요.


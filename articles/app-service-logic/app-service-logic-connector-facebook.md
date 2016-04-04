<properties
   pageTitle="논리 앱에서 Facebook 커넥터 사용 | Microsoft Azure 앱 서비스"
   description="Facebook 커넥터 또는 API 앱을 만들어서 구성하고 Azure 앱 서비스의 논리 앱에서 사용하는 방법"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="03/16/2016"
   ms.author="rajram"/>


# Facebook 커넥터 시작 및 논리 앱에 추가
>[AZURE.NOTE] 이 문서 버전은 논리 앱 2014-12-01-미리 보기 스키마 버전에 적용됩니다. 2015-08-01-preview 스키마 버전에 대한 내용을 보려면 [Facebook API](../connectors/connectors-create-api-facebook.md)를 클릭하세요.

Facebook 계정에 연결하여 메시지를 게시하거나 사진을 게시합니다. 논리 앱은 다양한 데이터 원본을 기반으로 트리거하고 흐름의 일부로 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다.

Facebook 커넥터를 사용하여 다음을 수행할 수 있습니다.

- 트리거를 사용하여 "New Post on User Timeline(사용자 타임라인의 새 게시물)" 또는 "New Post on Page(페이지의 새 게시물)"를 검색합니다. 새로운 게시물이 검색되면 흐름의 새 인스턴스를 트리거하고 요청에서 수신된 데이터를 처리하기 위해 흐름에 전달합니다.
- "Publish Post(게시물 게시)", "Publish Photo(사진 게시)" 등의 동작을 사용합니다. 이러한 동작은 응답을 다시 가져오고 흐름의 동작에서 이를 사용할 수 있도록 합니다.

Facebook 커넥터를 비즈니스 워크플로에 추가하고 논리 앱 내에서 이 워크플로의 일부로 데이터를 처리할 수 있습니다.

## 트리거 및 작업

트리거 | 작업
--- | ---
<ul><li>사용자 타임라인의 새 게시물</li><li>페이지의 새 게시물</li></ul> | <ul><li>게시물 게시</li><li>사진 게시</li></ul>



## 논리 앱용 Facebook 커넥터 만들기
커넥터는 논리 앱 내에서 만들거나 Azure 마켓플레이스에서 직접 만들 수 있습니다. 마켓플레이스에서 커넥터를 만들려면

1. Azure 시작 보드에서 **마켓플레이스**를 선택합니다.
2. "Facebook 커넥터"를 검색하여 선택하고 **만들기**를 선택합니다.
3. 이름, 앱 서비스 계획 및 기타 속성을 입력합니다. ![][1]
4.	**만들기**를 선택합니다.


## 논리 앱에 Facebook Connector 사용
API 앱을 만들고 나면 이제 Facebook Connector를 논리 앱의 트리거/동작으로 사용할 수 있습니다. 이렇게 하려면 다음을 수행해야 합니다.

1.	논리 앱에서 **트리거 및 동작**을 열어 논리 앱 디자이너를 열고 흐름을 구성합니다. ![][3]
2.	갤러리에 Facebook 커넥터가 나열됩니다. ![][4]
3. Facebook 커넥터를 선택하면 자동으로 디자이너에 추가됩니다. **권한 부여**를 선택하고 자격 증명을 입력한 후 **허용**을 선택합니다. ![][5] ![][6] ![][7] ![][8]
4.	트리거를 선택합니다. ![][9]

이제 Facebook 트리거에서 검색된 게시물을 다른 동작에 사용할 수 있습니다. 아래 흐름에서는 사용자의 Facebook 타임라인에 새 게시물이 게시될 때마다 동일한 게시물이 사용자의 Twitter 타임라인에 트윗됩니다. ![][10]

비슷한 방식으로 Facebook Connector 동작을 사용하여 흐름을 만들 수 있습니다. 아래 흐름에서는 Yammer 그룹에 게시된 새 메시지를 검색하여 사용자가 관리하는 Facebook 페이지에 동일한 게시물을 게시합니다. ![][11]

> [AZURE.TIP] Facebook 페이지 ID 또는 Yammer 그룹 ID를 가져오려면 URL에서 숫자 코드를 찾습니다.

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 워크플로에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

>[AZURE.NOTE] Azure 계정을 등록하기 전에 Azure 논리 앱을 시작하려는 경우 [논리 앱 평가](https://tryappservice.azure.com/?appservice=logic)로 이동합니다. 앱 서비스에서 즉시 단기 스타터 논리 앱을 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

[커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)의 Swagger REST API 참조를 봅니다.

커넥터의 성능 통계를 검토하고 보안을 제어할 수 있습니다. [기본 제공 API 앱 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)을 참조하세요.

<!--Image references-->
[1]: ./media/app-service-logic-connector-facebook/img1.png
[2]: ./media/app-service-logic-connector-facebook/img2.png
[3]: ./media/app-service-logic-connector-facebook/img3.png
[4]: ./media/app-service-logic-connector-facebook/img4.png
[5]: ./media/app-service-logic-connector-facebook/img5.png
[6]: ./media/app-service-logic-connector-facebook/img6.png
[7]: ./media/app-service-logic-connector-facebook/img7.png
[8]: ./media/app-service-logic-connector-facebook/img8.png
[9]: ./media/app-service-logic-connector-facebook/img9.png
[10]: ./media/app-service-logic-connector-facebook/img10.png
[11]: ./media/app-service-logic-connector-facebook/img11.png

<!---HONumber=AcomDC_0323_2016-->
<properties 
   pageTitle="논리 앱에서 Wait 커넥터 사용 | Microsoft Azure 앱 서비스" 
   description="Wait 커넥터 또는 API 앱을 만들어서 구성하고 Azure 앱 서비스의 논리 앱에서 사용하는 방법" 
   services="logic-apps" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/19/2016"
   ms.author="rajram"/>

# Wait 커넥터 시작 및 논리 앱에 추가

>[AZURE.NOTE] 이제 새 논리 앱을 만들 때 이 커넥터의 기능이 기본적으로 **지연 작업**으로 포함되므로 이 커넥터에 대한 지원은 곧 종료될 예정입니다. 이 커넥터를 사용하는 모든 논리 앱을 업그레이드하는 것이 좋습니다. 이 문서 버전은 논리 앱 2014-12-01-미리 보기 스키마 버전에 적용됩니다.

Wait 커넥터를 사용하면 앱 실행을 지정된 기간 동안 또는 지정된 시간이 될 때까지 지연할 수 있습니다. Wait 커넥터를 비즈니스 워크플로에 추가하고 논리 앱 내에서 이 워크플로의 일부로 데이터를 처리할 수 있습니다. 논리 앱에서 사용할 경우 실행을 지연하는 데 사용될 수 있습니다.

## Wait 커넥터 사용
Wait 커넥터를 사용하려면 먼저 Wait 커넥터 API 앱의 인스턴스를 만들어야 합니다. 이 작업은 논리 앱을 만드는 동안 인라인으로 수행하거나 Azure 마켓플레이스에서 Wait 커넥터 API 앱을 선택하여 수행할 수 있습니다.

## 논리 앱 디자이너 화면에서 Wait 커넥터 사용
Wait 커넥터는 동작으로 사용할 수 있습니다. 트리거가 필요하지 않습니다.

### 작업
- 오른쪽 창에서 Wait 커넥터를 클릭합니다. ![작업 목록][1]
- Wait 커넥터는 다음 두 동작을 지원합니다.
	- 지연
	- 다음까지 지연
	 
- *지연*을 선택합니다. ![지연 입력][2]
- 동작에 대한 입력을 제공하고 구성합니다. ![구성된 동작][3]

매개 변수|형식|매개 변수에 대한 설명
---|---|---
기간(분)|정수|지연 기간(분)


## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 흐름에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

>[AZURE.NOTE] Azure 계정을 등록하기 전에 Azure 논리 앱을 시작하려는 경우 [논리 앱 평가](https://tryappservice.azure.com/?appservice=logic)로 이동합니다. 앱 서비스에서 즉시 단기 스타터 논리 앱을 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

[커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)의 Swagger REST API 참조를 봅니다.

 

<!--References -->
[1]: ./media/app-service-logic-wait/ListOfActions.PNG
[2]: ./media/app-service-logic-wait/DelayInput.PNG
[3]: ./media/app-service-logic-wait/ActionConfigured.PNG

<!---HONumber=AcomDC_0803_2016-->
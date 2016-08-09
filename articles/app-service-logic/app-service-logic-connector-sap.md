<properties
   pageTitle="논리 앱에서 SAP 커넥터 사용 | Microsoft Azure 앱 서비스"
   description="SAP 커넥터 또는 API 앱을 만들어서 구성하고 Azure 앱 서비스의 논리 앱에서 사용하는 방법"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="harishkragarwal"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="sameerch"/>


# SAP 커넥터 시작 및 논리 앱에 추가

>[AZURE.NOTE] 고객의 의견에 따라 이 커넥터를 업그레이드하고 있습니다. 준비되는 대로 이 페이지를 업데이트할 것입니다. 새 버전을 사용하기 전에 SAP 커넥터를 사용하는 것은 권장되지 않습니다. 이 문서 버전은 논리 앱 2014-12-01-미리 보기 스키마 버전에 적용됩니다.

온-프레미스 SAP에 연결하여 RFC를 호출하거나 메타데이터를 가져옵니다. 온-프레미스 및 방화벽 뒤에 설치되는 SAP를 사용해야 하는 경우가 있습니다. 흐름에서 SAP 커넥터를 활용하여 다양한 시나리오를 얻을 수 있습니다. 몇 가지 예:

1.	웹 또는 모바일 사용자 프런트 엔드를 통해 SAP에 상주하는 데이터의 섹션을 표시합니다.
2.	적절한 처리 후 SAP에 데이터를 게시합니다.
3.	비즈니스 프로세스에서 사용할 데이터를 SAP에서 추출합니다.

커넥터는 논리 앱에 사용하여 흐름의 일부로 데이터 가져오기, 처리 또는 푸시를 수행할 수 있습니다. SAP 커넥터를 비즈니스 워크플로에 추가하고 논리 앱 내에서 이 워크플로의 일부로 데이터를 처리할 수 있습니다.


이러한 시나리오의 경우 다음과 같은 요구 사항을 수행해야 합니다.

1. SAP 커넥터 API 앱의 인스턴스를 만듭니다.
2. 온-프레미스 SAP와 통신하는 API 앱에 대한 하이브리드 연결을 설정합니다.
3. 논리 앱에서 생성된 API 앱을 사용하여 원하는 비즈니스 프로세스를 달성합니다.


## SAP 커넥터 API 앱의 인스턴스를 만듭니다. ##

커넥터는 논리 앱 내에서 만들거나 Azure 마켓플레이스에서 직접 만들 수 있습니다. 마켓플레이스에서 커넥터를 만들려면

1. Azure 시작 보드에서 **마켓플레이스**를 선택합니다.
2. "SAP 커넥터"를 검색하여 선택하고 **만들기**를 선택합니다.
3. 다음과 같이 구성합니다.
	1. 첫 번째 블레이드에서 이름, 앱 서비스 계획 등과 같은 일반 세부 정보를 제공합니다.

	2. 패키지 설정의 일부로 SAP 자격 증명을 제공합니다. 또한 Azure 서비스 버스 연결 문자열을 제공합니다. 이 문자열은 온-프레미스 SAP에 대한 하이브리드 연결을 설정하는 데 사용됩니다.

	3. 시나리오 요구에 따라 RFC, TRFC, BAPI 및 IDOC를 구성해야 합니다. 여러 값을 제공해야 할 경우 쉼표로 구분할 수 있습니다.

![][1]

## 방금 만든 SAP 커넥터 API 앱 구성 ##

찾아보기-> API 앱 -> <방금 만든 API 앱의 이름>을 통해 방금 만든 API 앱으로 이동하면 다음과 같은 동작이 표시됩니다. 하이브리드 연결이 아직 설정되지 않았으므로 설치가 완료되지 않습니다. ![][2]

SAP 커넥터는 *모든* SAP 끝점을 연결하는 데 하이브리드 연결이 필요합니다. 하이브리드 연결을 설정하려면 다음을 수행합니다.

1. 기본 연결 문자열을 복사합니다.
2. ‘'다운로드 및 구성’ 링크를 클릭합니다.
3. 시작된 설치 프로세스를 따르고 메시지가 표시되면 기본 연결 문자열을 제공합니다.
4. 설치 프로세스가 완료되면 다음과 유사한 대화 상자가 표시됩니다. ![][3]

[온-프레미스 SAP 서버와 통합](app-service-logic-integrate-with-an-on-premise-sap-server.md)에 대한 자세한 내용.

이제 생성된 API 앱으로 다시 이동하면 하이브리드 연결 상태가 연결됨인 것을 확인할 수 있습니다. ![][4]

참고: 보조 연결 문자열로 전환하려는 경우 하이브리드 설치 프로세스를 다시 수행하고 기본 연결 문자열 대신 보조 연결 문자열을 제공합니다.

## 논리 앱에서 사용 ##

SAP 커넥터는 논리 앱에서 작업/단계로만 사용할 수 있습니다.

논리 앱 만들기/편집을 수행할 때 위에서 만든 SAP 커넥터 API 앱을 선택합니다. 그러면 허용 가능한 모든 동작이 나열되며 선택할 수 있습니다. ![][5]

동작을 선택하면 해당 동작의 입력 매개 변수가 나열됩니다. 적절한 값을 제공하고 확인 아이콘을 클릭합니다. ![][6]

이제 논리 앱에서 구성된 대로 단계/동작이 표시됩니다. 동작의 출력이 표시되며, 이후 단계에서 입력으로 사용할 수 있습니다. ![][7]

논리 앱을 완료하여 비즈니스 프로세스를 정의하고 실행하여 원하는 목적을 달성합니다.

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 워크플로에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

>[AZURE.NOTE] Azure 계정을 등록하기 전에 Azure Logic Apps를 시작하려는 경우 [논리 앱 평가](https://tryappservice.azure.com/?appservice=logic)로 이동합니다. 앱 서비스에서 즉시 단기 스타터 논리 앱을 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

[커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)의 Swagger REST API 참조를 봅니다.

커넥터의 성능 통계를 검토하고 보안을 제어할 수 있습니다. [기본 제공 API 앱 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)을 참조하세요.

<!--Image references-->
[1]: ./media/app-service-logic-connector-sap/Create.jpg
[2]: ./media/app-service-logic-connector-sap/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-sap/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-sap/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-sap/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-sap/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-sap/LogicApp3.jpg

<!---HONumber=AcomDC_0727_2016-->
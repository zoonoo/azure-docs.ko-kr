<properties 
   pageTitle="SAP 커넥터" 
   description="SAPConnector를 사용하는 방법" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="harishkragarwal" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="hariag"/>


# SAP 커넥터 #

커넥터는 논리 앱에 사용하여 흐름의 일부로 데이터 가져오기, 처리 또는 푸시를 수행할 수 있습니다. 온-프레미스 및 방화벽 뒤에 설치되는 SAP를 사용해야 하는 경우가 있습니다. 흐름에서 SAP 커넥터를 활용하여 다양한 시나리오를 얻을 수 있습니다. 몇 가지 예:

1.	웹 또는 모바일 사용자 프런트 엔드를 통해 SAP에 상주하는 데이터의 섹션을 표시합니다.
2.	적절한 처리 후 SAP에 데이터를 게시합니다.
3.	비즈니스 프로세스에서 사용할 데이터를 SAP에서 추출합니다.

이러한 시나리오의 경우 다음과 같은 요구 사항을 수행해야 합니다.

1. SAP 커넥터 API 앱의 인스턴스를 만듭니다.
2. 온-프레미스 SAP와 통신하는 API 앱에 대한 하이브리드 연결을 설정합니다.
3. 논리 앱에서 생성된 API 앱을 사용하여 원하는 비즈니스 프로세스를 달성합니다.

## SAP 커넥터 API 앱의 인스턴스를 만듭니다. ##

SAP 커넥터를 사용하려면 먼저 ‘SAP 커넥터’ API 앱의 인스턴스를 만들어야 합니다. 이 작업은 다음과 같이 수행할 수 있습니다.

1. Azure 포털의 왼쪽 아래에 있는 '+ 새로 만들기' 옵션을 사용하여 Azure 마켓플레이스를 엽니다.
2. "웹 및 모바일 > API 앱"으로 이동하고 "SAP 커넥터"를 검색합니다.
3. 다음과 같이 구성합니다.
	1. 첫 번째 블레이드에서 이름, 앱 서비스 계획 등과 같은 일반 세부 정보를 제공합니다.
	2. 패키지 설정의 일부로 SAP 자격 증명을 제공합니다. 또한 Azure 서비스 버스 연결 문자열을 제공합니다. 이 문자열은 온-프레미스 SAP에 대한 하이브리드 연결을 설정하는 데 사용됩니다. 
	3. 시나리오 요구에 따라 RFC, TRFC, BAPI 및 IDOC를 구성해야 합니다. 여러 값을 제공해야 할 경우 쉼표로 구분할 수 있습니다.

![][1]

## 방금 만든 SAP 커넥터 API 앱 구성 ##

찾아보기-> API 앱 -> <Name of the API App just created>을 통해 방금 만든 API 앱으로 이동하면 다음과 같은 동작이 표시됩니다. 하이브리드 연결이 아직 설정되지 않았으므로 설치가 완료되지 않습니다.

![][2]

하이브리드 연결을 설정하려면 다음을 수행합니다.

1. 기본 연결 문자열을 복사합니다.
2. ‘'다운로드 및 구성’ 링크를 클릭합니다.
3. 시작된 설치 프로세스를 따르고 메시지가 표시되면 기본 연결 문자열을 제공합니다.
4. 설치 프로세스가 완료되면 다음과 유사한 대화 상자가 표시됩니다.

![][3]

이제 생성된 API 앱으로 다시 이동하면 하이브리드 연결 상태가 연결됨인 것을 볼 수 있습니다.

![][4]

참고: 보조 연결 문자열로 전환하려는 경우 하이브리드 설치 프로세스를 다시 수행하고 기본 연결 문자열 대신 보조 연결 문자열을 제공합니다.

## 논리 앱에서 사용 ##

SAP 커넥터는 논리 앱에서 작업/단계로만 사용할 수 있습니다.

논리 앱 만들기/편집을 수행할 때 위에서 만든 SAP 커넥터 API 앱을 선택합니다. 그러면 허용 가능한 모든 작업이 나열되며 선택할 수 있습니다.

![][5]

작업을 선택하면 해당 작업의 입력 매개 변수가 나열됩니다. 적절한 값을 제공하고 확인 아이콘을 클릭합니다.

![][6]

단계/작업이 이제 논리 앱에서 구성된 대로 표시됩니다. 작업의 출력이 표시되며, 이후 단계에서 입력으로 사용할 수 있습니다.

![][7]

논리 앱을 완료하여 비즈니스 프로세스를 정의하고 실행하여 원하는 목적을 달성합니다.

<!--Image references-->
[1]: ./media/app-service-logic-connector-sap/Create.jpg
[2]: ./media/app-service-logic-connector-sap/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-sap/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-sap/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-sap/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-sap/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-sap/LogicApp3.jpg



<!--HONumber=54--> 
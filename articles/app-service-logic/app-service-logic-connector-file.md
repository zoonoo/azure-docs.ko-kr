<properties
	pageTitle="논리 앱에서 File 커넥터 사용 | Microsoft Azure 앱 서비스"
	description="파일 커넥터 또는 API 앱을 만들어서 구성하고 Azure 앱 서비스의 논리 앱에서 사용하는 방법"
	authors="rajeshramabathiran"
	manager="erikre"
	editor=""
	services="logic-apps"
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2016"
	ms.author="rajram"/>

# 파일 커넥터 시작 및 논리 앱에 추가
>[AZURE.NOTE] 이 문서 버전은 논리 앱 2014-12-01-미리 보기 스키마 버전에 적용됩니다.

파일 시스템에 연결하여 호스트 컴퓨터에서 파일 업로드, 다운로드 등을 수행합니다. 논리 앱은 다양한 데이터 원본을 기반으로 트리거되고 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다. 파일 커넥터를 비즈니스 워크플로에 추가하고 논리 앱 내에서 이 워크플로의 일부로 데이터를 처리할 수 있습니다.

파일 커넥터는 호스트 파일 시스템에 대한 하이브리드 연결에 하이브리드 연결 관리자를 사용합니다.

## 논리 앱용 파일 커넥터 만들기 ##
파일 커넥터를 사용하려면 먼저 파일 커넥터 API 앱의 인스턴스를 만들어야 합니다. 이 작업은 다음과 같이 수행할 수 있습니다.

1.	Azure 포털 왼쪽에 있는 + 새로 만들기 옵션을 사용하여 Azure Marketplace를 엽니다.
2.	"파일 커넥터"를 검색합니다.
3.	검색 결과에서 **파일 커넥터(미리 보기)**를 선택합니다.
4.	**만들기** 단추를 선택합니다.
5.	다음과 같이 파일 커넥터를 구성합니다.![][1]

	- **이름** - 파일 커넥터의 이름을 지정합니다.
	- **패키지 설정**
		- **루트 폴더** - 호스트 컴퓨터에서 루트 폴더 경로를 지정합니다. 예: D:\\FileConnectorTest
		- **서비스 버스 연결 문자열** - 서비스 버스 연결 문자열을 제공합니다. 서비스 버스 릴레이를 사용할 수 있도록 하려면 서비스 버스 네임스페이스는 기본이 아닌 표준 유형이어야 합니다. 서비스 버스 릴레이는 하이브리드 연결 관리자에 연결하는 데 사용됩니다.
	- **앱 서비스 계획** - 앱 서비스 계획을 선택하거나 만듭니다.
	- **가격 책정 계층** - 커넥터에 대한 가격 책정 계층을 선택합니다.
	- **리소스 그룹** - 커넥터가 상주할 리소스 그룹을 선택하거나 만듭니다.
	- **구독** - 이 커넥터를 만들 구독을 선택합니다.
	- **위치** - 커넥터를 배포할 지리적 위치를 선택합니다.

4. 만들기를 클릭합니다. 새 파일 커넥터가 만들어집니다.

## 하이브리드 연결 관리자 구성 ##
API 앱 인스턴스가 만들어지면 해당 대시보드를 찾습니다. 찾아보기 > API 앱 > 파일 커넥터 API 앱 선택을 클릭하여 완료할 수 있습니다. 여기서 하이브리드 연결 관리자를 구성해야 합니다. 하이브리드 연결 관리자의 구성 및 문제 해결에 대한 자세한 내용은 [하이브리드 연결 관리자 사용]을 참조하십시오.

## 논리 앱에서 파일 커넥터 사용 ##
API 앱을 만들고 나면 이제 파일 커넥터를 논리 앱에 대한 동작으로 사용할 수 있습니다. 이렇게 하려면 다음을 수행해야 합니다.

1.	새 논리 앱을 만들고 파일 커넥터가 있는 동일한 리소스 그룹을 선택합니다. 지침에 따라 [새 논리 앱을 만듭니다].

2.	만들어진 논리 앱 내에서 "트리거 및 작업"을 열어 논리 앱 디자이너를 열고 흐름을 구성합니다.

3.	파일 커넥터가 오른쪽의 갤러리에 있는 "이 리소스 그룹의 API 앱" 섹션에 나타납니다.

4.	"파일 커넥터"를 클릭하여 파일 커넥터 API 앱을 편집기에 놓을 수 있습니다. 파일 커넥터는 트리거 1개 및 동작 4개를 노출합니다. ![][5]

6.	각각은 특정 속성을 노출합니다. 아래 이미지는 트리거 및 파일 가져오기 작업에 대한 속성을 나열합니다. ![][6]

7. 이러한 속성이 구성되면 흐름에서 트리거 및 동작을 사용할 수 있습니다. 마찬가지로 다른 동작도 구성할 수 있습니다.

> [AZURE.NOTE] 파일 트리거는 파일을 폴더에서 성공적으로 읽은 후 해당 파일을 삭제합니다.

## 파일 커넥터 REST API ##
논리 앱 외부에서 커넥터를 사용하려면 커넥터를 통해 노출되는 REST API를 활용할 수 있습니다. 탐색->Api 앱->파일 커넥터를 사용하여 이 API 정의를 볼 수 있습니다. 이제 통계 섹션 아래의 API 정의 렌즈를 클릭하여 이 커넥터를 통해 노출되는 모든 API를 볼 수 있습니다.![][7]

API의 세부 정보는 [파일 커넥터 API 정의]에서 찾을 수 있습니다.

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 워크플로에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

>[AZURE.NOTE] Azure 계정을 등록하기 전에 Azure Logic apps을 시작하려는 경우 [논리 앱 평가](https://tryappservice.azure.com/?appservice=logic)로 이동합니다. App Service에서 즉시 단기 스타터 논리 앱을 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

[커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)의 Swagger REST API 참조를 봅니다.

커넥터의 성능 통계를 검토하고 보안을 제어할 수 있습니다. [기본 제공 API Apps 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)을 참조하세요.

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[새 논리 앱을 만듭니다]: app-service-logic-create-a-logic-app.md
[파일 커넥터 API 정의]: https://msdn.microsoft.com/library/dn936296.aspx
[하이브리드 연결 관리자 사용]: app-service-logic-hybrid-connection-manager.md

<!---HONumber=AcomDC_0907_2016-->
<properties 
	pageTitle="논리 앱이란 무엇인가요?" 
	description="앱 서비스 논리 앱에 대해 자세히 알아봅니다." 
	authors="kevinlam1" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/04/2016"
	ms.author="klam"/>

#논리 앱이란 무엇인가요?

| 빠른 참조 |
| --------------- |
| [논리 앱 정의 언어](https://msdn.microsoft.com/library/azure/dn948512.aspx?f=255&MSPPError=-2147217396) |
| [논리 앱 커넥터 문서](https://azure.microsoft.com/documentation/articles/app-service-logic-connectors-list/) |
| [논리 앱 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=azurelogicapps) |

Azure 앱 서비스는 웹앱, 모바일 앱 및 통합 앱을 쉽게 빌드할 수 있게 해주는 완전히 관리되는 개발자용 PaaS(Platform as a Service)입니다. 논리 앱은 이 제품군의 일부로, 기술 사용자나 개발자가 사용하기 쉬운 비주얼 디자이너를 사용하여 비즈니스 프로세스 실행 및 워크플로를 자동화할 수 있게 합니다.

무엇보다도, 논리 앱을 마켓플레이스의 [커넥터][connectors]와 결합하여 까다로운 통합 시나리오도 쉽게 해결할 수 있습니다.

![흐름 앱 디자이너](./media/app-service-logic-what-are-logic-apps/Designer.png)

자동으로 SQL DB에 새 레코드를 복제하고 프런트 데스크에 메일을 보낼 수 있습니다. 또는 부정적인 트윗을 자동으로 찾아 여유 시간 채널에 보낼 수 있습니다.

##논리 앱 사용 이유

논리 앱을 통해 개발자는 트리거에서 시작된 다음 일련의 단계를 실행하는 워크플로를 디자인할 수 있습니다. 검사점 설정 및 지속적 실행과 같은 모범 사례 및 인증을 안전하게 처리하는 동시에 각각 앱 서비스 API 앱을 호출합니다.

비즈니스 프로세스를 자동화하려는 경우(예: 부정적인 트윗을 찾아 내부 여유 시간 채널에 게시하거나 도착 시 SQL의 새 고객 레코드를 CRM 시스템으로 복제) 논리 앱은 클라우드의 개별 데이터 원본을 온-프레미스로 쉽게 통합할 수 있게 해줍니다. [커넥터][connectors]에서 자세한 내용을 검토하고 지금 [시작][create]하여 어떤 작업을 수행할 수 있는지 확인하세요.

추가적으로 [BizTalk API 앱][biztalk]을 사용하여 [규칙 엔진][rules], [사업자 파트너 관리][tpm] 등의 권한으로 통합 시나리오를 확장할 수 있습니다.

- **사용하기 쉬운 디자인 도구** - 브라우저에서 종단 간 논리 앱을 디자인할 수 있습니다. 트리거로 시작 - 단순한 일정부터 회사에 대한 트윗이 나타날 때마다까지 다양합니다. 그런 다음 풍부한 커넥터 갤러리를 사용하여 작업을 개수 제한 없이 오케스트레이션합니다.

- **쉽게 SaaS 작성** - 설명하기 쉬운 컴퍼지션 작업도 코드에서 구현하기는 어렵습니다. 논리 앱을 통해 개별 시스템을 쉽게 연결할 수 있습니다. Facebook 또는 Twitter 계정의 활동을 기반으로 하는 작업을 CRM 소프트웨어에서 만들고 싶으세요? 클라우드 마케팅 솔루션을 온-프레미스 청구 시스템에 연결하고 싶으세요? 논리 앱은 이러한 문제에 대한 솔루션을 제공하는 가장 빠르고 신뢰할 수 있는 방법입니다.

- **템플릿으로 빠르게 시작** - 빠르게 시작할 수 있도록 일부 일반적인 솔루션을 신속하게 만들 수 있는 [템플릿의 갤러리][templates]를 제공합니다. 고급 BizTalk 솔루션부터 간단한 SaaS 연결 및 일부 ‘오락용' 솔루션에 이르기까지 갤러리는 논리 앱의 기능을 이해하는 가장 빠른 방법입니다.

- **내재된 확장성** - 필요한 커넥터가 보이지 않나요? 논리 앱은 앱 서비스 제품군의 일부이며 API 앱과 함께 작동하도록 설계되었습니다. 커넥터로 사용할 고유한 API 앱을 쉽게 만들 수 있습니다. 전용 앱을 새로 빌드하거나 마켓플레이스에서 공유하여 수익을 창출합니다.

- **진정한 통합 능력** - 쉽게 시작되고 필요에 따라 확장됩니다. 논리 앱은 통합 전문가가 필요한 솔루션을 빌드할 수 있도록 하기 위해 Microsoft의 업계 선도적인 통합 솔루션인 BizTalk 기능을 쉽게 활용할 수 있습니다. 자세한 내용은 [앱 서비스와 함께 제공되는 BizTalk 기능][biztalk]을 참조하세요.

## 논리 앱 개념

논리 앱 환경을 구성하는 몇 가지 주요 부분은 다음과 같습니다.

- **워크플로** - 논리 앱은 비즈니스 프로세스를 일련의 단계나 워크플로로 모델링하는 그래픽 방법을 제공합니다.
- **커넥터** -논리 앱은 데이터 및 서비스에 액세스해야 합니다. 커넥터는 특수 유형의 API 앱입니다. 데이터에 연결하고 작업할 때 도움이 되도록 특별히 생성됩니다. [커넥터 사용][connectors]에서 현재 사용 가능한 커넥터 목록을 확인하세요.
- **트리거** - 일부 커넥터는 트리거로도 작동할 수 있습니다. 트리거는 메일 도착이나 Azure 저장소 계정 변경과 같은 특정 이벤트에 따라 워크플로의 새 인스턴스를 시작합니다.
-  **동작** - 워크플로에서 트리거 후의 각 단계를 동작이라고 합니다. 각 동작은 일반적으로 커넥터 또는 사용자 지정 API 앱의 작업에 매핑됩니다.
- **BizTalk** - 고급 통합 시나리오를 위해 Azure 앱 서비스에는 Biztalk의 기능이 포함됩니다. Biztalk는 Microsoft의 업계 선도적인 통합 플랫폼입니다. BizTalk API 앱을 통해 유효성 검사, 변환, 규칙 등을 논리 앱 워크플로에 쉽게 포함할 수 있습니다. 자세한 내용은 [BizTalk API 앱 정의][biztalk]를 참조하세요.

## 시작

논리 앱을 시작하려면 [논리 앱 만들기][create] 자습서를 따르세요.

Azure 앱 서비스 플랫폼에 대한 자세한 내용은 [Azure 앱 서비스][appservice]를 참조하세요.

[biztalk]: app-service-logic-what-are-biztalk-api-apps.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: app-service-logic-create-a-logic-app.md
[connectors]: app-service-logic-connectors-list.md
[tpm]: app-service-logic-create-a-trading-partner-agreement.md
[rules]: app-service-logic-use-biztalk-rules.md
[templates]: app-service-logic-use-logic-app-templates.md
 

<!---HONumber=AcomDC_0107_2016-->
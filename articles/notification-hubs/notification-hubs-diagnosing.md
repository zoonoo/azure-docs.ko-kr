<properties 
	pageTitle="Azure 알림 허브 - 진단 지침" 
	description="Azure 알림 허브의 일반적인 문제를 진단하는 방법에 대한 지침입니다." 
	services="notification-hubs" 
	documentationCenter="" 
	authors="piyushjo" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="01/27/2015" 
	ms.author="piyushjo"/>

#Azure 알림 허브 - 진단 지침

Azure 알림 허브 고객으로부터 가장 많이 듣는 질문 중 하나는 클라이언트 장치에 나타나는 고객의 응용 프로그램 백엔드에서 보낸 알림을 보지 못하는 이유와 알림이 삭제되는 위치와 이유 그리고 이 문제를 해결하는 방법에 대한 것입니다. 이 문서에서는 장치에서 알림이 삭제되거나 끝나지 않는 다양한 이유에 대해 알아보겠습니다. 또한 근본 원인을 분석하여 해결책을 찾아낼 수 있는 방법도 살펴보겠습니다. 

먼저, Azure 알림 허브에서 장치로 알림을 푸시하는 방법을 이해하는 것이 중요합니다.
![][0]

일반적인 보내기 알림 흐름에서 메시지는 **응용 프로그램 백엔드**에서 **Azure 알림 허브(NH)**로 전송됩니다. 이어서 "대상"(예: 푸시 알림을 받아야 하는 모든 등록)을 결정하기 위해 구성된 태그 및 태그 식을 고려하는 모든 등록에 대해 일부 처리 작업을 수행합니다. 이러한 등록은 지원되는 일부 또는 모든 플랫폼(iOS, Google, Windows, Windows Phone, Kindle 및 중국 Android용 Baidu)에 적용될 수 있습니다. 대상이 설정되면 NH에서 여러 등록 일괄 처리로 분할된 알림을 특정 장치 플랫폼 **푸시 알림 서비스(PNS** - Apple용 APNS, Google용 GCM 등)로 푸시합니다 NH는 구성 알림 허브 페이지의 Azure 포털에서 설정한 자격 증명을 기반으로 개별 PNS를 인증합니다. 그런 다음 PNS는 해당 알림을 개별 **클라이언트 장치**로 전달합니다. 이 방법은 푸시 알림을 전달하기 위한 플랫폼 권장 방식이며 최종 알림 레그는 플랫폼 PNS와 장치에서 발생합니다. 따라서 4가지 주요 구성 요소(*client*, *application backend*, *Azure Notification Hubs (NH)* 및 *Push Notification Services (PNS)*)가 있으며 이러한 알림은 삭제될 수 있습니다.  이 아키텍처에 대한 자세한 내용은 [알림 허브 개요]에서 확인할 수 있습니다.

알림을 전달하지 못하는 현상은 초기 테스트/준비 단계에서 구성에 문제가 있어서 발생할 수 있고 응용 프로그램이 심층적이거나 패턴 문제가 있어서 전체 또는 일부 알림이 삭제될 수 있는 프로덕션에서 발생할 수도 있습니다. 이 섹션에서는 일반적인 경우에서 매우 드문 경우까지 다양한 알림 삭제 시나리오에 대해 살펴보도록 하겠습니다. 이 중에는 확실하다고 느끼는 것도 있고 심하지 않다고 생각되는 것도 있습니다. 

###Azure 알림 허브의 잘못된 구성 

Azure 알림 허브는 각 PNS에 알림을 성공적으로 보낼 수 있도록 개발자의 응용 프로그램 컨텍스트에서 자신을 인증해야 합니다. 이렇게 하려면 개발자가 각 플랫폼(Google, Apple, Windows 등)에서 개발자 계정을 만든 다음 알림 허브 구성 섹션의 Azure 포털에서 구성해야 하는 자격 증명을 얻는 응용 프로그램을 등록하면 됩니다. 알림을 만들지 않은 경우 먼저 특정 플랫폼 개발자 계정에서 생성한 응용 프로그램과 일치하는 알림 허브에 올바른 자격 증명이 구성되어 있는지 확인해야 합니다. [자습서 시작하기]는 단계별 방식으로 이 프로세스를 이해하는 데 유용합니다. 다음은 잘못된 구성의 몇 가지 예입니다.

1. **일반**
 
	a) 알림 허브 이름(오타 없이)이 다음과 동일한지 확인합니다.

	- 클라이언트에서 등록하는 위치 
	- 백엔드에서 알림을 보내는 위치  
	- PNS 자격 증명 구성한 위치 
	- 클라이언트와 백엔드에서 SAS 자격 증명을 구성한 사람 
		
	b) 클라이언트와 응용 프로그램 백엔드에서 올바른 SAS 구성 문자열을 사용하고 있는지 확인합니다. 경험으로 보아, 클라이언트에서는 **DefaultListenSharedAccessSignature** 사용해야 하며 응용 프로그램 백엔드에서는 **DefaultFullSharedAccessSignature**을 사용해야 합니다(NH에 알림을 보낼 수 있도록 권한을 제공).

2. **Apple 푸시 알림 서비스(APNS) 구성**
 
	서로 다른 2개의 허브를 유지해야 하는데, 하나는 프로덕션에 다른 하나는 테스트 목적으로 유지해야 합니다. 즉, 샌드박스 환경에서 사용하려는 인증서를 별도의 허브에 업로드하고 프로덕션에서 사용하려는 인증서를 별도의 허브에 업로드합니다. 알림을 보내는 데 완전히 실패할 수 있기 때문에 동일한 허브에 다른 종류의 인증서를 업로드하지 마십시오. 실수로 동일한 허브에 다른 종류의 인증서를 업로드한 위치를 직접 찾으려면 허브를 삭제하고 새로 고침하는 것이 좋습니다. 어떤 이유로 최소한 허브를 삭제할 수 없는 경우 허브에서 기존의 모든 등록을 삭제해야 합니다. 

3. **메시징 GCM(Google Cloud) 구성**

	a) 클라우드 프로젝트에서 "Android용 Google 클라우드 메시징"을 설정했는지 확인합니다. 
	
	![][2]
	
	b) NH가 GCM을 인증하는 데 사용할 자격 증명을 가져오는 동안 "서버 키"를 만들었는지 확인합니다. 
	
	![][3]
	
	c) Dashboard에서 얻을 수 있는 완전히 숫자 엔터티인 클라이언트에서 "프로젝트 ID"를 구성했는지 확인합니다.
	
	![][1]

###응용 프로그램 문제

1) **태그/태그 식**

태그 또는 태그 식을 사용하여 청중을 분할하는 경우 알림을 보낼 때 보내기 호출에서 지정하는태그/태그 식을 기반으로 찾을 수 있는 대상은 항상 없습니다. 알림을 보낸 다음 해당 등록을 가진 클라이언트에서만 알림 수신을 확인할 경우 일치하는 태그가 있는지 확인하기 위해 등록을 검토하는 것이 가장 좋습니다. 예: NH에서 한 모든 등록이 "Politics"를 말하는 태그로 완료되었고 "Sports" 태그로 알림을 보내는 경우 어떤 장치로도 전송되지 않습니다. 복잡한 경우에는 "태그 A" 또는 "태그 B"로만 등록한 태그 식뿐만 아니라 "태그 A && 태그 B"를 대상으로 알림을 보내는 경우도 포함될 수 있습니다. 아래의 자체 진단 팁 섹션에는 사용자 등록을 등록에 포함된 태그와 함께 검토할 수 있는 방법을 표시합니다. 

2) **템플릿 문제**

템플릿을 사용하는 경우 [템플릿 지침]에 설명된 지침을 따르는지 확인합니다. 

3) **잘못된 등록**

알림 허브가 올바르게 구성되었고 모든 태그/태그 식이 올바르게 사용되어 알림을 보내야 하는 유효한 대상을 찾았다고 가정하고 NH는 여러 일괄 처리를 병렬로 시작하며 각 일괄 처리는 등록 집합에 메시지를 보냅니다. 

> [AZURE.NOTE] 병렬로 처리했기 때문에 알림이 전달되는 순서는 보장하지 않습니다. 

이제 Azure 알림 허브는 "최대 한 번의" 메시지 전달 모델에 대해 최적화되었습니다. 즉, 장치에 알림이 두 번 이상 전달되지 않도록 중복 제거를 시도합니다. 이렇게 하려면 등록을 확인하고 실제로 PNS에 메시지를 보내기 전에 장치 식별자당 하나의 메시지만 보냈는지 확인합니다. 각 일괄 처리는 PNS로 보내지고 이어서 등록을 수락하고 유효성을 검사하기 때문에 일괄 처리에 있는 하나 이상의 등록에서 오류를 탐지하여 이 오류를 Azure NH로 반환한 다음 처리를 중지시켜 일괄 처리가 완전히 삭제되도록 할 수 있습니다. TCP 스트림 프로토콜을 사용하는 APNS와 특히 유용합니다. 최대 한 번의 전달에 대해 최적화했기 때문에 PNS가 일괄 처리를 모두 삭제했는지 아니면 부분적으로만 삭제했는지 알지 못한다면 실패한 이 일괄 처리를 재시도하지 않습니다. 그러나 PNS는 Azure NH에 오류를 발생시킨 등록을 알려주고 피드백에 따라 데이터베이스에서 해당 등록을 제거합니다. 이는 하나의 일괄 처리나 해당 일괄 처리의 하위 집합이 알림을 받지 못할 수도 있다는 것을 의미합니다. 하지만 잘못된 등록을 제거한 후 그 다음에 보내기를 시도하면 보내기에 성공할 가능성이 더 높아집니다. 대상 장치 수의 범위가 증가함에 따라(일부 고객이 수백만 대의 장치에 알림을 보냄) 여기에서 홀수 일괄 처리를 삭제해도 알림을 받는 장치의 전반적인 비율에는 큰 차이가 없습니다. 하지만 적은 수의 알림을 보내고 PNS 오류가 발생할 경우 전체 또는 대부분의 알림을 받지 못한다는 메시지가 표시될 수 있습니다. 이 동작이 반복해서 표시되는 경우 잘못된 모든 등록을 확인하여 이를 삭제해야 합니다. 직접 형성한 등록은 삭제된 알림의 가장 일반적인 원인이기 때문에 확실히 삭제해야 합니다. 테스트 환경일 경우 장치에서 앱이 열렸을 때 재시도하여 알림 허브를 재등록하기 때문에 모든 등록을 바로 삭제하여 이전에 만든 모든 등록이 유효한 등록이 되도록 할 수도 있습니다. 

###PNS 문제

각 PNS에서 알림 메시지를 받으면 해당 알림을 장치로 보내는 것은 PNS의 책임입니다. Azure 알림 허브는 여기에 관여하지 않으며 알림이 장치로 전달되는 동안에는 제어하지 않습니다. 플랫폼 알림 서비스는 꽤 경고하므로 알림은 PNS로부터 몇 초 이내에 장치에 도달하게 됩니다. 그러나 PNS가 제한하는 경우 Azure 알림 허브는 기하급수적인 백오프 전략을 적용하고 PNS가 30분 안에 도달할 수 없을 경우 해당 메시지를 영구적으로 만료하여 삭제하기 위한 정책을 사용합니다. 

PNS가 알림을 전달하려고 하지만 장치가 오프라인일 경우 PNS가 일정 시간 동안 알림을 저장했다가 사용할 수 있을 때 장치로 전달합니다. 특정 앱에 대한 최근 알림 하나만 저장됩니다. 장치가 오프라인일 때 여러 알림을 전송하는 경우 각 새 알림이 이전 알림을 삭제하도록 합니다. 가장 최신 알림만 유지하는 이 동작은 APNS에서는 결합 알림 그리고 GCM에서는 축소 알림(축소 키 사용)이라고 합니다. 장치가 오랫동안 오프라인 상태일 경우 저장된 모든 알림이 삭제됩니다. 
소스- [APNS 지침] & [GCM 지침]

Azure 알림 허브를 사용하면 일반 `SendNotification` API(예:-.NET SDK용  `SendNotificationAsync`)를 사용하여 HTTP 헤더를 통해 결합 키를 전달할 수 있고 각 PNS에 그대로 전달하는 HTTP 헤더를 받을 수도 있습니다. 

##자체 진단 팁

여기에서는 모든 알림 허브 문제를 진단하여 근본 원인을 알아내기 위한 다양한 방안을 검토합니다.

###자격 증명 확인

1. **PNS 개발자 포털**

	해당 PNS 개발자 포털(APNS, GCM WNS 등)에서 당사의 [자습서 시작하기]를 사용하여 확인합니다.

2. **Azure 관리 포털**

	구성 탭으로 이동하여 자격 증명을 검토하고 PNS 개발자 포털에서 얻은 자격 증명과 일치시킵니다. 

	![][4]

###등록 확인

1. **Visual Studio**

	개발에 Visual Studio를 사용하는 경우 Microsoft Azure에 연결한 다음 "서버 탐색기"에서 알림 허브를 포함한 다양한 Azure 서비스를 보고 관리할 수 있습니다. 개발/테스트 환경에서 주로 유용합니다. 

	![][9]

	허브에서 플랫폼, 네이티브 또는 템플릿 등록, 태그, PNS 식별자, 등록 id 및 만료 날짜별로 보기 좋게 분류된 모든 등록을 보고 관리할 수 있습니다. 또한 즉석에서 등록을 편집할 수도 있는데, 태그를 편집하려는 경우에 유용합니다. 

	![][8]
 
	> [AZURE.NOTE] 등록을 편집할 수 있는 visual Studio 기능은 제한된 수의 등록을 가진 개발/테스트 동안에만 사용해야 합니다. 등록을 대량으로 수정해야 할 경우 [등록 내보내기/가져오기](표준 계층에서만 사용 가능)에 설명된 등록 내보내기/가져오기 기능 사용을 고려합니다.

2. **서비스 버스 탐색기**

	많은 고객이 [ServiceBus 탐색기]에 설명된 ServiceBus 탐색기를 사용하여 알림 허브를 보고 관리합니다 Code.microsoft.com - [ServiceBus 탐색기 코드]에서 사용할 수 있는 공개 소스 프로젝트입니다.

###알림 메시지 확인

1. **Azure 포털**

	"디버그" 탭으로 이동하여 모든 서비스를 백엔드 및 실행하지 않고 클라이언트에 테스트 알림을 보낼 수 있습니다. 

	![][7]

2. **Visual Studio**

	또한 Visual Studio의 comforts에서 테스트 알림을 보낼 수도 있습니다.

	![][10]

	자세한 내용은 다음 Visual Studio 알림 허브 Azure 탐색기 기능에서 확인할 수 있습니다. 
	
	- [VS 서버 탐색기 개요]
	- [VS 서버 탐색기 블로그 게시물 - 1]
	- [VS 서버 탐색기 블로그 게시물 - 2]

###실패한 알림 디버그/알림 결과 검토

**EnableTestSend 속성**

알림 허브를 통해 알림을 보내면 처음에는 NH에서 처리하여 모든 대상을 파악하기를 기다리기만 했다가 결국에는 NH가 PNS로 알림을 보냅니다. 즉, REST API 또는 클라이언트 SDK 중 하나를 사용하는 경우 송신 호출의 성공적으로 반환은 메시지가 알림 허브에 성공적으로 대기한다는 것만을 의미합니다. NH가 결국 PNS로 메시지를 보냈을 때 어떤 일이 발생하는지에 대한 통찰력을 제공하지는 않습니다. 사용자 알림이 클라이언트 장치에 도착하지 않을 경우 NH가 PNS로 메시지를 보내려고 시도했을 때 오류가 발생했을 수 있습니다. 예를 들어, 페이로드 크기가 PNS에서 허용되는 최대값을 초과했거나 NH에서 구성된 자격 증명이 잘못되는 등의 가능성이 있습니다. 
PNS 오류에 대한 통찰력을 얻기 위해 [EnableTestSend 기능]이라는 속성을 도입했습니다. 이 속성은 포털 또는 Visual Studio 클라이언트에서 테스트 메시지를 보낼 때 자동으로 활성화되기 때문에 자세한 디버깅 정보를 볼 수 있습니다. 현재 사용할 수 있는 .NET SDK의 예를 수행하는 API를 통해 이를 사용할 수 있고 결국 모든 클라이언트 SDK에 추가됩니다. 이를 REST 호출과 함께 사용하려면 예를 들어, 송신 호출 끝에 "test"라는 쿼리 문자열 매개변수를 추가 하기만 하면 됩니다. 

	https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

*예(.NET SDK)*
 
.NET SDK를 사용하여 네이티브 토스트 알림을 보낸다고 가정합니다.

    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);
 
`result.State` 는 푸시에서 발생하는 일에 대한 이해 없이 실행 끝에서  `Enqueued` 상태를 나타내기만 합니다. 
이제  `NotificationHubClient`를 초기화하는 동안  `EnableTestSend` 부울 속성을 사용할 수 있고 알림을 보내는 동안 발생한 PNS 오류에 대한 자세한 상태를 확인할 수 있습니다. 보내기 호출을 반환할 때는 NH가 PNS로 알림을 전달하여 결과를 결정한 후에만 반환하기 때문에 시간이 추가로 소요됩니다. 
 
	bool enableTestSend = true;
	NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);
	
	var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
	Console.WriteLine(outcome.State);
	
	foreach (RegistrationResult result in outcome.Results)
	{
	    Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
	}

*샘플 출력*

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong
 
이 메시지는 알림 허브에 잘못된 자격 증명이 구성되었거나 허브에 있는 등록에 문제가 있다는 것을 나타내며 메시지를 보내기 전에 이 구성을 삭제하고 클라이언트가 다시 만들도록 하려면 권장된 코스를 사용합니다. 
 
> [AZURE.NOTE] 이 속성의 사용은 엄격하게 제한되므로 제한된 등록 집합을 가진 개발/테스트 환경에서만 이 속성을 사용해야 합니다. 10개의 장치에만 디버그 알림을 보냅니다. 분당 10개의 디버그 보내기만 처리할 수 있습니다. 

###원격 분석 검토 

1. **Azure 포털 사용**

	Azure 포털을 사용하면 알림 허브의 모든 활동에 대한 간략한 개요를 확인할 수 있습니다. 
	
	a) "대시보드" 탭에서 등록, 알림뿐만 아니라 플랫폼별 오류에 대한 집계 보기를 볼 수 있습니다. 
	
	![][5]
	
	b) 또한 "모니터" 탭에서 다른 많은 플랫폼 특정 메트릭을 추가하여 특히 NH에서 PNS로 알림을 보내려고 할 때 반환된 PNS 특정 오류를 더 자세히 살펴볼 수 있습니다. 
	
	![][6]
	
	c) **들어오는 메시지**, **등록 작업** 및 **성공한 알림** 검토를 시작으로 플랫폼별 탭으로 이동하여 PNS 특정 오류를 검토해야 합니다. 
	
	d) 인증 설정에서 알림 허브를 잘못 구성한 경우 PNS 인증 오류가 나타납니다. 이것은 PNS 자격 증명을 확인하는 좋은 표시입니다. 

2) **프로그래밍 방식 액세스**

자세한 내용은 다음 참조 

- [프로그래밍 방식 원격 분석 액세스]
- [API 샘플을 통한 원격 분석 액세스]

> [AZURE.NOTE] **등록 내보내기/가져오기**, **API를 통한 원격 분석 액세스** 등의 여러 원격 분석 관련 기능은 표준 계층에서만 사용할 수 있습니다. 무료 또는 기본 계층에 있을 때 이러한 기능을 사용하려고 하면 REST API에서 직접 사용하는 경우 SDK 및 HTTP 403(금지됨)을 사용하는 동안 이 효과에 대한 예외 메시지를 받게 됩니다. Azure 관리 포털을 통해 표준 계층으로 이동했는지 확인합니다.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/GCMConfigure.png
[2]: ./media/notification-hubs-diagnosing/GCMEnable.png
[3]: ./media/notification-hubs-diagnosing/GCMServerKey.png
[4]: ./media/notification-hubs-diagnosing/PortalConfigure.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalMonitoring.png
[7]: ./media/notification-hubs-diagnosing/PortalTestNotification.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png
 
<!-- LINKS -->
[알림 허브 개요]: http://azure.microsoft.com/documentation/articles/notification-hubs-overview/
[자습서 시작하기]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[템플릿 지침]: https://msdn.microsoft.com/library/dn530748.aspx 
[APNS 지침]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW4
[GCM 지침]: http://developer.android.com/google/gcm/adv.html
[등록 내보내기/가져오기]: http://msdn.microsoft.com/library/dn790624.aspx
[ServiceBus 탐색기]: http://msdn.microsoft.com/library/dn530751.aspx
[ServiceBus 탐색기 코드]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[VS 서버 탐색기 개요]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[VS 서버 탐색기 블로그 게시물 - 1]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[VS 서버 탐색기 블로그 게시물 - 2]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[EnableTestSend 기능]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx
[프로그래밍 방식 원격 분석 액세스]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[API 샘플을 통한 원격 분석 액세스]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel


<!--HONumber=49--> 
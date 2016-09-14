<properties
	pageTitle="Azure 알림 허브"
	description="Azure에서 푸시 알림을 사용하는 방법에 대해 알아봅니다. 코드 샘플은 .NET API를 사용하여 C#으로 작성되었습니다."
	authors="wesmc7777"
	manager="erikre"
	editor=""
	services="notification-hubs"
	documentationCenter=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="multiple"
	ms.devlang="multiple"
	ms.topic="hero-article"
	ms.date="08/25/2016"
	ms.author="wesmc"/>


#Azure 알림 허브

##개요

Azure 알림 허브는 백 엔드(클라우드 또는 온-프레미스)에서 모바일 플랫폼으로 모바일 푸시 알림을 보낼 수 있는 사용하기 쉽고 확장된 다중 플랫폼 푸시 인프라를 제공합니다.

알림 허브를 사용하면 쉽게 다양한 PNS(플랫폼 알림 시스템) 세부 정보를 요약하여 플랫폼 간 개인 설정된 푸시 알림을 보낼 수 있습니다. 한 번의 API 호출로 어떤 장치에서나 개별 사용자 또는 수백만 명의 사용자가 포함된 전체 고객 세그먼트를 대상으로 지정할 수 있습니다.

엔터프라이즈 시나리오 및 소비자 시나리오 모두에서 알림 허브를 사용할 수 있습니다. 예:

- 수백만 명에게 대기 시간이 짧은 속보 알림을 보냅니다. 알림 허브는 모든 Windows 및 Windows Phone 장치에 미리 설치되어 있는 Bing 응용 프로그램을 구동합니다.
- 사용자 세그먼트에 위치 기반 쿠폰을 보냅니다.
- 사용자 또는 그룹에 스포츠/금융/게임 응용 프로그램에 대한 이벤트 알림을 보냅니다.
- 새 메시지/전자 메일과 같은 엔터프라이즈 이벤트를 사용자 및 잠재 고객에게 알립니다.
- 다단계 인증에 필요한 일회용 암호를 보냅니다.



##푸시 알림이란 무엇인가요?

스마트폰 및 태블릿은 이벤트 발생 시 사용자에게 "알릴" 수 있습니다. 이러한 알림은 다양한 형식으로 사용될 수 있습니다.

Windows 스토어 및 Windows Phone 응용 프로그램에서는 알림이 새 알림 신호 소리와 함께 모덜리스 창이 나타나는 _알림(toast)_ 형식일 수 있습니다. _타일_, _원시_ 및 _배지_ 알림을 비롯한 다른 알림 유형도 지원됩니다. Windows 장치에서 지원되는 알림의 형식에 대한 자세한 내용은 [타일, 배지 및 알림](http://msdn.microsoft.com/library/windows/apps/hh779725.aspx)을 참조하세요.

Apple iOS 장치에서도 마찬가지로 푸시가 대화 상자로 표시되어 사용자에게 알림을 보거나 닫도록 요청합니다. **보기**를 클릭하면 메시지를 받고 있는 응용 프로그램이 열립니다. iOS 알림에 대한 자세한 내용은 [iOS 알림](http://go.microsoft.com/fwlink/?LinkId=615245)을 참조하세요.

푸시 알림은 모바일 장치가 에너지 효율성을 유지하면서 새로운 정보를 표시하도록 도와줍니다. 장치의 해당 앱이 활성 상태가 아닌 경우에도 백 엔드 시스템에서 모바일 장치로 알림을 보낼 수 있습니다. 푸시 알림은 앱 참여 및 사용을 증가시키는 데 사용되는 소비자 앱의 필수 구성 요소입니다. 알림은 최신 정보가 비즈니스 이벤트에 대한 직원의 응답성을 높이는 경우 엔터프라이즈에도 유용합니다.

모바일 참여 시나리오의 몇 가지 구체적인 예는 다음과 같습니다.

1.  Windows 8 또는 Windows Phone에서 현재 재무 정보로 타일 업데이트
2.  워크플로 기반 엔터프라이즈 앱에서 사용자에게 작업 항목이 할당되었다는 사실을 해당 사용자에게 알림으로 알림
3.  CRM 앱(예: Microsoft Dynamics CRM)에서 현재 잠재 고객 수를 배지로 표시

##푸시 알림 작동 방식

푸시 알림은 PNS(_플랫폼 알림 시스템_)라는 플랫폼별 인프라를 통해 전달됩니다. PNS는 기본 기능을 제공하며(즉, 브로드캐스트 및 개인 설정에 대한 지원 없음), 공용 인터페이스를 포함하고 있지 않습니다. 예를 들어, Windows 스토어 앱에 알림을 보내려면 개발자는 WNS(Windows 알림 서비스)에 문의해야 합니다. iOS 장치로 알림을 보내려면 동일한 개발자는 APNS(Apple Push Notification Service)에 문의하고 다시 메시지를 보내야 합니다. Azure 알림 허브는 각 플랫폼에서 푸시 알림을 지원하는 다른 기능과 함께 공통 인터페이스를 제공합니다.

그러나 높은 수준에서 봤을 때 모든 플랫폼 알림 시스템은 다음과 같은 동일한 패턴을 따릅니다.

1.  클라이언트 앱이 PNS에 연결하여 해당 _핸들_을 검색합니다. 핸들 형식은 시스템에 따라 다릅니다. WNS의 경우 URI 또는 "알림 채널"입니다. APNS의 경우 토큰입니다.
2.  클라이언트 앱은 나중에 사용하기 위해 이 핸들을 _백 엔드_ 앱에 저장합니다. WNS의 경우 백 엔드는 일반적으로 클라우드 서비스입니다. Apple의 경우 이 시스템을 _공급자_라고 합니다.
3.  푸시 알림을 보내기 위해 앱 백 엔드가 핸들을 사용하여 PNS에 연결하는 방식으로 특정 클라이언트 앱 인스턴스를 대상으로 합니다.
4.  PNS가 핸들에 의해 지정된 장치로 알림을 전달합니다.

![][0]

##푸시 알림의 문제

이러한 시스템은 매우 강력하지만 앱 개발자는 푸시 알림을 분할된 사용자에게 브로드캐스트하거나 보내는 등의 일반적인 푸시 알림 시나리오를 구현하는 것에서조차 많은 작업을 수행해야 합니다.

푸시 알림은 모바일 앱용 클라우드 서비스에서 가장 많이 요청되는 기능 중 하나입니다. 그 이유는 푸시 알림이 작동하도록 하는 데 필요한 인프라가 상당히 복잡하며 일반적으로 앱의 기본 비즈니스 논리와 무관하기 때문입니다. 주문형 푸시 인프라 빌드 시의 몇 가지 문제는 다음과 같습니다.

- **플랫폼 종속성.** 다양한 플랫폼의 장치로 알림을 보내려면 여러 인터페이스를 백 엔드에서 코딩해야 합니다. 하위 수준 세부 정보가 다를 뿐만 아니라 알림 표시(타일, 알림 또는 배지)도 플랫폼 종속적입니다. 이러한 차이로 인해 복잡하고 유지 관리가 어려운 백 엔드 코드가 발생할 수 있습니다.

- **크기 조정.** 이 인프라를 확장하는 것은 두 가지 측면에서 이루어집니다.
	+ PNS 지침에 따라 장치 토큰은 앱이 시작될 때마다 새로 고쳐져야 합니다. 따라서 단지 장치 토큰을 최신 상태로 유지하기 위해 많은 양의 트래픽(및 그에 따른 데이터베이스 액세스)이 발생합니다. 장치 수가 늘어나면(수백 개에 달할 수 있음) 이 인프라를 만들고 유지 관리하기 위한 비용을 무시할 수 없게 됩니다.

	+ 대부분의 PNS에서는 여러 장치로의 브로드캐스트를 지원하지 않습니다. 수백 개의 장치로 브로드캐스트하면 당연히 PNS에 대한 호출이 수백 개 발생합니다. 앱 개발자가 일반적으로 총 대기 시간을 절감하려 하기 때문에 이러한 요청을 확장할 수 있는 기능은 사소하지 않습니다. 예를 들어 메시지를 받을 마지막 장치가 알림이 전송되고 나서 30분 후에 알림을 받아서는 안 됩니다. 그렇게 되면 많은 경우에 푸시 알림을 사용하는 목적이 무색해지기 때문입니다.
- **라우팅.** PNS는 장치로 메시지를 보내는 방법을 제공합니다. 그러나 대부분의 앱에서 알림은 사용자 및/또는 관심 그룹(예: 특정 고객 계정에 할당된 모든 직원)을 대상으로 합니다. 이러한 경우 알림을 올바른 장치로 라우팅하려면 앱 백 엔드에서 관심 그룹을 장치 토큰과 연결하는 레지스트리를 유지 관리해야 합니다. 이 오버헤드로 인해 앱의 총 출시 기간 및 유지 관리 비용이 늘어납니다.

##알림 허브를 사용하는 이유는?

알림 허브를 사용하면 복잡성을 없애줍니다. 푸시 알림 문제를 관리할 필요 없이 알림 허브를 사용할 수 있습니다. 알림 허브는 완전한 다중 플랫폼, 규모 확장 푸시 알림 인프라를 사용하여 앱 백 엔드에서 실행되는 푸시 관련 코드를 상당히 줄여 줍니다. 알림 허브는 푸시 인프라의 모든 기능을 구현합니다. 아래 그림에 표시된 것처럼 장치는 PNS 핸들을 등록하는 것만 담당하며, 백 엔드는 사용자 또는 관심 그룹으로 플랫폼 독립적 메시지를 보내는 것을 담당합니다.

![][1]


알림 허브는 다음과 같은 장점이 있는 사용 준비가 된 푸시 알림 인프라를 제공합니다.

- **다중 플랫폼.**
	+  모든 주요 모바일 플랫폼을 지원합니다. 알림 허브는 Windows 스토어, iOS, Android 및 Windows Phone 앱으로 푸시 알림을 보낼 수 있습니다.

	+  알림 허브는 지원되는 모든 플랫폼으로 알림을 보내기 위한 공용 인터페이스를 제공합니다. 플랫폼별 프로토콜이 필요하지 않습니다. 앱 백 엔드에서는 플랫폼별 형식이나 플랫폼 독립적 형식으로 알림을 보낼 수 있습니다. 응용 프로그램은 알림 허브하고만 통신합니다.

	+  장치 핸들을 관리합니다. 알림 허브는 PNS의 핸들 레지스트리 및 피드백을 유지 관리합니다.

- **어떠한 백 엔드에서도 작동**: 클라우드 또는 온-프레미스,.NET, PHP, Java, 노드 등에서 작동합니다.

- **크기 조정.** 알림 허브는 다시 설계하거나 분할할 필요 없이 수백만 개의 장치로 확장됩니다.


- **다양한 배달 패턴**:

	- *브로드캐스트*: 한 번의 API 호출을 통해 수백만 대의 장치로 거의 동시에 브로드캐스트할 수 있습니다.

	- *유니캐스트/멀티캐스트*: 장치를 모두 포함한 개별 사용자 또는 별도의 폼 팩터(태블릿 및 휴대폰)와 같은 더 광범위한 그룹을 나타내는 태그로 푸시합니다.

	- *구분*: 태그 식에 정의된 복합 세그먼트(예: 뉴욕에서 Yankees를 팔로잉하는 장치)로 푸시합니다.

	각 장치는 해당 핸들을 알림 허브로 보내는 경우 하나 이상의 _태그_를 지정할 수 있습니다. [태그]에 대한 자세한 정보를 참조하세요. 태그는 사전 프로비전하거나 삭제할 필요가 없습니다. 태그는 사용자 또는 관심 그룹으로 알림을 보낼 수 있는 간단한 방법을 제공합니다. 태그는 모든 앱별 식별자(예: 사용자 또는 그룹 ID)를 포함할 수 있으므로 태그를 사용하면 앱 백 엔드가 장치 핸들을 저장하고 관리해야 하는 부담에서 자유로워집니다.

- **개인 설정**: 각 장치에는 백 엔드 코드에 영향을 주지 않고 장치별 지역화 및 개인 설정을 수행할 수 있는 하나 이상의 템플릿이 있을 수 있습니다.

- **보안**: SAS(Shared Access Secret) 또는 페더레이션 인증입니다.

- **다양한 원격 분석**: 포털에서 프로그래밍 방식으로 사용할 수 있습니다.


##앱 서비스 모바일 앱과 통합

Azure 서비스 전반에서 원활하고 일관적인 사용 환경을 조성하기 위하여 알림 허브를 사용한 푸시 알림이 [앱 서비스 모바일 앱]에 기본적으로 지원됩니다. [앱 서비스 모바일 앱]은 엔터프라이즈 개발자 및 시스템 통합자를 위해 확장성이 크고 전 세계에서 사용 가능한 모바일 응용 프로그램 개발 플랫폼을 제공합니다. 이 플랫폼은 모바일 개발자에게 풍부한 기능 집합을 제공합니다.

모바일 앱 개발자는 다음 워크플로에서 알림 허브를 활용할 수 있습니다.

1. 장치 PNS 핸들을 검색합니다.
2. 편리한 모바일 앱 클라이언트 SDK 등록 API를 통해 장치와 [템플릿]을 알림 허브에 등록합니다.
    + 모바일 앱은 등록 시 보안을 목적으로 모든 태그를 제거합니다. 백 엔드에서 알림 허브와 직접 작업하여 장치와 태그를 연결합니다.
3. 알림 허브를 통해 앱 백 엔드에서 알림을 보냅니다.

이 통합으로 인해 개발자에게 다음과 같은 편리한 기능이 제공됩니다.

- **모바일 앱 클라이언트 SDK.** 이러한 다중 플랫폼 SDK는 등록을 위한 간단한 API를 제공하고 모바일 앱과 자동으로 연결되는 알림 허브와 통신합니다. 개발자는 알림 허브 자격 증명을 심도있게 분석하고 추가 서비스로 작업할 필요가 없습니다.
    + SDK는 모바일 앱 인증된 사용자 ID로 지정된 장치를 자동으로 태그하여 사용자 시나리오를 푸시할 수 있습니다.
    + SDK는 자동으로 모바일 앱 설치 ID를 GUID로 사용하여 알림 허브로 등록하며 이는 개발자가 여러 서비스 GUID를 유지 관리하는 문제를 해결합니다.
    
- **설치 모델.** 모바일 앱은 알림 허브의 최신 푸시 모델과 함께 사용되어 푸시 알림 서비스에 맞게 조정되어 사용이 편리한 JSON 설치의 장치와 연결된 모든 푸시 속성을 나타냅니다.

- **유연성.** 개발자는 통합이 되어 있더라도 알림 허브와 직접 작업하도록 언제든지 선택할 수 있습니다.

- **[Azure 포털]의 통합된 환경.** 푸시는 하나의 기능으로 모바일 앱에 시각적으로 표현되며 개발자는 모바일 앱을 통해 연결된 알림 허브와 쉽게 작업할 수 있습니다.



##다음 단계

다음 항목에서 알림 허브에 대한 자세한 내용을 확인할 수 있습니다.

+ **[고객이 알림 허브를 사용하는 방법]**

+ **[알림 허브 자습서 및 가이드]**

+ **알림 허브 시작 자습서**([iOS], [Android], [Windows 범용], [Windows Phone], [Kindle], [Xamarin.iOS], [Xamarin.Android])

푸시 알림에 대한 관련 .NET의 관리되는 API 참조는 다음 항목을 참조하세요.

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications]


  [0]: ./media/notification-hubs-overview/registration-diagram.png
  [1]: ./media/notification-hubs-overview/notification-hub-diagram.png
  [고객이 알림 허브를 사용하는 방법]: http://azure.microsoft.com/services/notification-hubs
  [알림 허브 자습서 및 가이드]: http://azure.microsoft.com/documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started
  [Windows 범용]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
  [앱 서비스 모바일 앱]: https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-value-prop/
  [템플릿]: notification-hubs-templates-cross-platform-push-messages.md
  [Azure 포털]: https://portal.azure.com
  [태그]: (http://msdn.microsoft.com/library/azure/dn530749.aspx)

<!---HONumber=AcomDC_0907_2016-->
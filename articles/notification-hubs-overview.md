<properties 
	pageTitle="Azure 알림 허브" 
	description="Azure에서 푸시 알림을 사용하는 방법에 대해 알아봅니다. 코드 샘플은 .NET API를 사용하여 C#으로 작성되었습니다." 
	authors="ggailey777" 
	manager="dwrede" 
	editor="" 
	services="notification-hubs" 
	documentationCenter=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="glenga"/>


# Azure 알림 허브


Azure의 푸시 알림 지원을 통해 사용하기 쉬운 다중 플랫폼 및 규모 확장 푸시 인프라에 액세스할 수 있어, 모바일 플랫폼용 소비자 응용 프로그램 및 엔터프라이즈 응용 프로그램 모두에 대한 푸시 알림을 매우 간단하게 구현할 수 있습니다.

<h2>푸시 알림이란 무엇인가요?</h2>

스마트폰 및 태블릿에는 이벤트 발생 시 사용자에게 "알리는" 기능이 있습니다. Windows 스토어 응용 프로그램에서는 알림으로 인해 _알림_(toast)이 생성되어 모덜리스 창이 표시되고 새 푸시를 알리는 소리가 재생될 수 있습니다. Apple iOS 장치에서는 마찬가지로 푸시가 대화 상자로 표시되어 사용자에게 알림을 보거나 닫도록 요청합니다. **보기**를 클릭하면 메시지를 수신하는 응용 프로그램이 열립니다.

푸시 알림은 모바일 장치가 에너지 효율성을 유지하면서 새로운 정보를 표시하도록 도와줍니다. 푸시 알림은 앱 참여 및 사용을 증가시키는 데 사용되는 소비자 앱의 필수 구성 요소입니다. 알림은 최신 정보가 비즈니스 이벤트에 대한 직원의 응답성을 높이는 경우 엔터프라이즈에도 유용합니다. 

모바일 참여 시나리오의 몇 가지 구체적인 예는 다음과 같습니다.

1.  Windows 8 또는 Windows Phone에서 현재 재무 정보로 타일 업데이트
2.  워크플로 기반 엔터프라이즈 앱에서 사용자에게 작업 항목이 할당되었다는 사실을 해당 사용자에게 알림으로 알림
3.  CRM 앱(예: Microsoft Dynamics CRM)에서 현재 잠재 고객 수를 배지로 표시

<h2>푸시 알림 작동 방식</h2>

푸시 알림은 PNS(_플랫폼 알림 시스템_)라는 플랫폼별 인프라를 통해 전달됩니다. PNS는 기본 기능을 제공하며(즉, 브로드캐스트 및 개인 설정에 대한 지원 없음), 공용 인터페이스를 포함하고 있지 않습니다. 예를 들어 Windows 스토어 앱으로 알림을 보내려면 개발자가 WNS(Windows 알림 서비스)에 연결하여 iOS 장치로 알림을 보내야 하며, 동일한 개발자가 APNS(Apple Push Notification Service)에 연결하여 메시지를 다시 보내야 합니다.

그러나 높은 수준에서 봤을 때 모든 플랫폼 알림 시스템은 다음과 같은 동일한 패턴을 따릅니다.

1.  클라이언트 앱이 PNS에 연결하여 해당 _핸들_을 검색합니다. 핸들 형식은 시스템에 따라 다릅니다. WNS의 경우 URI 또는 "알림 채널"입니다. APNS의 경우 토큰입니다.
2.  클라이언트 앱은 나중에 사용하기 위해 이 핸들을 앱 _백 엔드_에 저장합니다. WNS의 경우 백 엔드는 일반적으로 클라우드 서비스입니다. Apple의 경우 이 시스템을 _공급자_라고 합니다.
3.  푸시 알림을 보내기 위해 앱 백 엔드가 핸들을 사용하여 PNS에 연결하는 방식으로 특정 클라이언트 앱 인스턴스를 대상으로 합니다.
4.  PNS가 핸들에 의해 지정된 장치로 알림을 전달합니다.

![][0]

<h2>푸시 알림의 문제</h2>

이러한 시스템은 매우 강력하지만 앱 개발자는 푸시 알림을 사용자에게 브로드캐스트하거나 보내는 등의 일반적인 푸시 알림 시나리오를 구현하는 것에서조차 많은 작업을 수행해야 합니다.

푸시 알림은 모바일 앱용 클라우드 서비스에서 가장 많이 요청되는 기능 중 하나입니다. 그 이유는 푸시 알림이 작동하도록 하는 데 필요한 인프라가 상당히 복잡하며 일반적으로 앱의 기본 비즈니스 논리와 무관하기 때문입니다. 주문형 푸시 인프라 빌드 시의 몇 가지 문제는 다음과 같습니다.

- **플랫폼 종속성.** 여러 플랫폼의 장치로 알림을 보내려면 백 엔드에서 여러 인터페이스를 코딩해야 합니다. 이러한 인터페이스에서는 하위 수준 세부 정보도 다르며 알림의 표시(타일, 알림, 배지)도 플랫폼에 따라 다릅니다. 이러한 차이로 인해 백 엔드 코드가 복잡해져 유지 관리하기가 어려워질 수 있습니다.

- **확장.** 이 인프라를 확장할 때는 두 가지 측면을 고려해야 합니다.
1. PNS 지침에 따라 장치 토큰은 앱이 시작될 때마다 새로 고쳐져야 합니다. 따라서 단지 장치 토큰을 최신 상태로 유지하기 위해 많은 양의 트래픽(및 그에 따른 데이터베이스 액세스)이 발생합니다. 장치 수가 늘어나면(수백 개에 달할 수 있음) 이 인프라를 만들고 유지 관리하기 위한 비용을 무시할 수 없게 됩니다.
2.  대부분의 PNS에서는 여러 장치로의 브로드캐스트를 지원하지 않습니다. 수백 개의 장치로 브로드캐스트하면 당연히 PNS에 대한 호출이 수백 개 발생합니다. 이러한 요청을 확장할 수 있는 기능은 사소하지 않습니다. 대개 앱 개발자는 총 대기 시간을 낮게 유지하려고 하기 때문입니다. 예를 들어 메시지를 받을 마지막 장치가 알림이 전송되고 나서 30분 후에 알림을 받아서는 안 됩니다. 그렇게 되면 많은 경우에 푸시 알림을 사용하는 목적이 무색해지기 때문입니다.
- **라우팅.** PNS를 사용하면 장치로 메시지를 보낼 수 있습니다. 그러나 대부분의 앱에서 알림의 대상은 사용자 및/또는 이해 그룹(예: 특정 고객 계정에 할당된 모든 직원)입니다. 따라서 알림을 올바른 장치로 라우팅하려면 앱 백 엔드에서 이해 그룹을 장치 토큰과 연결하는 레지스트리를 유지 관리해야 합니다. 이러한 오버헤드로 인해 전체 출시 시간이 늦어지고 앱 유지 관리 비용이 증가하게 됩니다.

<h2>알림 허브를 사용하는 이유는?</h2>

알림 허브는 다음을 지원하는 사용 준비가 된 푸시 알림 인프라를 제공합니다.

- **여러 플랫폼.** 알림 허브는 지원되는 모든 플랫폼으로 알림을 보낼 수 있는 공통 인터페이스를 제공합니다. 앱 백 엔드에서는 플랫폼별/플랫폼 독립적 형식으로 알림을 보낼 수 있습니다. 알림 허브는 Windows 스토어, iOS, Android 및 Windows Phone 앱으로 푸시 알림을 보낼 수 있습니다.
- **게시/구독 라우팅.** 각 장치는 알림 허브로 핸들을 보낼 때 _태그_를 하나 이상 지정할 수 있습니다. 태그에 대한 자세한 내용은 다음 섹션을 참조하세요. 태그는 미리 프로비전하거나 삭제하지 않아도 되며, 사용자나 이해 그룹에 알림을 간편하게 보내는 데 사용할 수 있습니다. 태그는 사용자, 그룹 ID 등 모든 앱 관련 식별자를 포함할 수 있으므로 태그 사용 시에는 앱 백 엔드에서 장치 핸들을 저장하고 관리할 필요가 없습니다.
- **확장.** 알림 허브는 다시 설계하거나 분할하지 않고도 매우 많은 장치로 확장할 수 있습니다.

알림 허브는 완전한 다중 플랫폼, 규모 확장 푸시 알림 인프라를 사용하여 앱 백 엔드에서 실행되는 푸시 관련 코드를 상당히 줄여 줍니다. 알림 허브는 푸시 인프라의 모든 기능을 구현합니다. 장치는 해당 PNS 핸들을 등록하는 것만 담당하며, 백 엔드는 사용자 또는 관심 그룹으로 플랫폼 독립적 메시지를 보내는 것을 담당합니다.

![][1]

## 다음 단계

다음 항목에서 알림 허브에 대한 자세한 내용을 확인할 수 있습니다.

+ **[고객이 알림 허브를 사용하는 방법]**

+ **[알림 허브 자습서 및 가이드]** 

+ **알림 허브 시작 자습서**([iOS], [Android], [Windows Universal], [Windows Phone], [Kindle], [Xamarin.iOS], [Xamarin.Android])

푸시 알림에 대한 관련 .NET의 관리되는 API 참조는 다음 항목을 참조하세요.

+ [Microsoft.WindowsAzure.Messaging.NotificationHub]
+ [Microsoft.ServiceBus.Notifications] 


  [0]: ./media/notification-hubs-overview/SBPushNotifications1.gif
  [1]: ./media/notification-hubs-overview/SBPushNotifications2.gif
  [고객이 알림 허브를 사용하는 방법]: http://azure.microsoft.com/ services/notification-hubs
  [알림 허브 자습서 및 가이드]: http://azure.microsoft.com/ documentation/services/notification-hubs
  [iOS]: http://azure.microsoft.com/ documentation/articles/notification-hubs-ios-get-started
  [Android]: http://azure.microsoft.com/ documentation/articles/notification-hubs-android-get-started
  [Windows Universal]: http://azure.microsoft.com/ documentation/articles/notification-hubs-windows-store-dotnet-get-started
  [Windows Phone]: http://azure.microsoft.com/ documentation/articles/notification-hubs-windows-phone-get-started
  [Kindle]: http://azure.microsoft.com/ documentation/articles/notification-hubs-kindle-get-started
  [Xamarin.iOS]: http://azure.microsoft.com/ documentation/articles/partner-xamarin-notification-hubs-ios-get-started
  [Xamarin.Android]: http://azure.microsoft.com/ documentation/articles/partner-xamarin-notification-hubs-android-get-started
  [Microsoft.WindowsAzure.Messaging.NotificationHub]: http://msdn.microsoft.com/library/microsoft.windowsazure.messaging.notificationhub.aspx
  [Microsoft.ServiceBus.Notifications]: http://msdn.microsoft.com/library/microsoft.servicebus.notifications.aspx
  
<!--HONumber=45--> 

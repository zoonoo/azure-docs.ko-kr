<properties 
	pageTitle="단일 모바일 서비스 백 엔드에서 여러 클라이언트를 사용하는 방법 | Azure 모바일" 
	description="Windows 스토어 및 Windows Phone을 비롯한 다양한 모바일 플랫폼을 대상으로 하는 여러 클라이언트 앱에서 단일 모바일 서비스 백 엔드를 사용하는 방법에 대해 알아봅니다." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor="mollybos"/>
<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# 단일 모바일 장치에서 여러 장치 플랫폼 지원
 
모바일 앱 개발에서 Azure 모바일 서비스를 사용할 때의 주요 이점 중 하나는 여러 클라이언트 플랫폼에서 앱을 지원하는 단일 백 엔드 서비스를 사용할 수 있다는 것입니다. 모바일 서비스에서는 모든 주요 장치 플랫폼에 대해 네이티브 클라이언트 라이브러리를 제공하기 때문에 단일 백 엔드 서비스를 사용하고 교차 플랫폼 개발자 도구를 사용하여 앱을 좀 더 쉽게 개발할 수 있습니다. 이 항목에서는 단일 모바일 서비스 백 엔드를 사용하면서도 여러 클라이언트 플랫폼에서 앱을 실행할 수 있도록 하기 위한 고려 사항에 대해 설명합니다.

##<a id="push"></a>교차 플랫폼 푸시 알림

모바일 서비스는 Azure 알림 허브를 사용하여 모든 주요 장치 플랫폼의 클라이언트 앱에 푸시 알림을 보냅니다. 알림 허브에서는 장치 등록을 만들고 관리하며 교차 플랫폼 푸시 알림을 보내는 데 사용되는 일관된 통합 인프라를 제공합니다. 알림 허브는 다음과 같은 플랫폼별 알림 서비스를 사용하여 푸시 알림을 보내는 것을 지원합니다.

+ iOS 앱용 APNS(Apple Push Notification Service)
+ Android 앱용 GCM(Google Cloud Messaging) 서비스
+ Windows Store, Windows Phone 8.1 Store 및 범용 Windows 앱용 WNS(Windows Notification Service) 
+ Windows Phone Silverlight 앱용 MPNS(Microsoft Push Notification Service)

>[AZURE.NOTE]현재, 알림 허브는 WNS를 사용하여 Windows Phone Silverlight 8.1 앱에 푸시 알림을 보내는 것을 지원하지 않습니다. Silverlight와 Windows Phone 8.0 및 7.0 앱에는 MPNS를 사용하여 알림을 보내야 합니다.

자세한 내용은 [Azure 알림 허브]를 참조하십시오.

클라이언트 등록은 플랫폼별 모바일 서비스 클라이언트 라이브러리에서 등록 기능을 사용하거나 모바일 서비스 REST API를 사용하여 만듭니다. 알림 허브는 두 가지 종류의 장치 등록을 지원합니다.

+ **기본 등록**<br/>기본 등록은 플랫폼별 푸시 알림 서비스에 맞춰져 있습니다. 기본 등록을 사용하여 등록된 장치에 알림을 보낼 때는 모바일 서비스에서 플랫폼별 API를 호출해야 합니다. 여러 플랫폼의 장치에 알림을 보내려면 여러 개의 플랫폼별 호출이 필요합니다.   
  
+ **템플릿 등록**<br/>알림 허브는 플랫폼별 템플릿 등록도 지원합니다. 템플릿 등록을 사용하면 단일 API 호출을 사용하여 등록된 플랫폼에서 실행 중인 앱에 알림을 보낼 수 있습니다. 자세한 내용은 [알림 허브를 통해 사용자에게 플랫폼 간 알림 보내기]를 참조하십시오.

>[AZURE.NOTE]장치 등록이 존재하지 않는 기본 장치 플랫폼에 메시지를 보내려고 하면 오류가 발생합니다. 템플릿 알림을 보낼 때는 이 오류가 발생하지 않습니다.

다음 섹션에 있는 표는 .NET 및 JavaScript 백 엔드 모바일 서비스 모두에서 푸시 알림을 구현하는 방법을 보여 주는 클라이언트별 자습서로 연결됩니다.

###.NET 백 엔드

.NET 백 엔드 모바일 서비스에서는 [ApiServices.Push] 속성에서 가져온 [PushClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.notifications.pushclient.aspx) 개체에 [SendAsync](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.apiservices.push.aspx) 메서드를 호출함으로써 알림을 보냅니다. 보낸 푸시 알림(기본 또는 템플릿)은 다음 표에서와 같이 [SendAsync](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.notifications.ipushmessage.aspx) 메서드에 전달된 특정 [IPushMessage] 파생 개체에 따라 다릅니다.

|플랫폼 |[APNS](mobile-services-dotnet-backend-ios-get-started-push.md)|[GCM](mobile-services-dotnet-backend-android-get-started-push.md) |[WNS](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md) |[MPNS](mobile-services-dotnet-backend-windows-phone-get-started-push.md)|
|-----|-----|----|----|-----|
|기본|[ApplePushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.applepushmessage.aspx) |[GooglePushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.googlepushmessage.aspx) |[WindowsPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.windowspushmessage.aspx) | [MpnsPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.mpnspushmessage.aspx) |

다음 코드는 .NET 백 엔드 서비스에서 모든 iOS 및 Windows Store 디바이스 등록에 푸시 알림을 보냅니다.

	// Define a push notification for APNS.
	ApplePushMessage apnsMessage = new ApplePushMessage(item.Text, TimeSpan.FromHours(1));    

	// Define a push notification for WNS.
	WindowsPushMessage wnsMessage = new WindowsPushMessage();
    wnsMessage.XmlPayload = @"<?xml version=""1.0"" encoding=""utf-8""?>" +
                         @"<toast><visual><binding template=""ToastText01"">" +
                         @"<text id=""1"">" + item.Text + @"</text>" +
                         @"</binding></visual></toast>";
    
	// Send push notifications to all registered iOS and Windows Store devices. 
    await Services.Push.SendAsync(apnsMessage);
	await Services.Push.SendAsync(wnsMessage);

다른 기본 클라이언트 플랫폼에 푸시 알림을 보내는 방법에 대한 예제를 보려면 위 표의 헤더에서 플랫폼 링크를 클릭하십시오.

기본 클라이언트 등록 대신 템플릿 클라이언트 등록을 사용할 때는 [SendAsync]에 대한 단일 호출만으로 같은 알림을 보냄으로써 다음과 같이 [TemplatePushMessage] 개체를 제공할 수 있습니다.

	// Create a new template message and add the 'message' parameter.    
	var templatePayload = new TemplatePushMessage();
    templatePayload.Add("message", item.Text);

	// Send a push notification to all template registrations.
    await Services.Push.SendAsync(templatePayload); 
 
###JavaScript 백 엔드

JavaScript 백 엔드 모바일 서비스에서는 다음 표에서와 같이 글로벌 **푸시 개체**에서 가져온 플랫폼별 개체에 [send] 메서드를 호출함으로써 알림을 보냅니다.

|플랫폼 |[APNS](mobile-services-javascript-backend-ios-get-started-push.md)|[GCM](mobile-services-javascript-backend-android-get-started-push.md) |[WNS](mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) |[MPNS](mobile-services-javascript-backend-windows-phone-get-started-push.md)|
|-----|-----|----|----|-----|
|기본|[apns 개체](http://msdn.microsoft.com/library/azure/jj839711.aspx) |[gcm 개체](http://msdn.microsoft.com/library/azure/dn126137.aspx) |[wns 개체](http://msdn.microsoft.com/library/azure/jj860484.aspx) | [mpns 개체](http://msdn.microsoft.com/library/azure/jj871025.aspx) |

다음 코드는 모든 Android 및 Windows Phone 등록에 푸시 알림을 보냅니다.

	// Define a push notification for GCM.
	var gcmPayload = 
    '{"data":{"message" : item.text }}';

	// Define the payload for a Windows Phone toast notification.
	var mpnsPayload = '<?xml version="1.0" encoding="utf-8"?>' +
    '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
    '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text + 
    '</wp:Text2></wp:Toast></wp:Notification>';

	// Send push notifications to all registered Android and Windows Phone 8.0 devices. 
	push.mpns.send(null, mpnsPayload, 'toast', 22, {
            success: function(pushResponse) {
                // Push succeeds.
                },              
                error: function (pushResponse) {
                    // Push fails.
                    }
                });
    push.gcm.send(null, gcmPayload, {
            success: function(pushResponse) {
                // Push succeeds.
                },              
                error: function (pushResponse) {
                    // Push fails.
                    }
                });

다른 기본 클라이언트 플랫폼에 푸시 알림을 보내는 방법에 대한 예제를 보려면 위 표의 헤더에서 플랫폼 링크를 클릭하십시오.

기본 클라이언트 등록 대신 템플릿 클라이언트 등록을 사용할 때는 글로벌 **푸시 개체**에 대한 [send] 기능의 단일 호출만으로 같은 알림을 보냄으로써 다음과 같이 템플릿 메시지 페이로드를 제공할 수 있습니다.

	// Create a new template message with the 'message' parameter.    
	var templatePayload = { "message": item.text };

	// Send a push notification to all template registrations.
    push.send(null, templatePayload, {
            success: function(pushResponse) {
                // Push succeeds.
                },              
                error: function (pushResponse) {
                    // Push fails.
                    }
                }); 

##<a id="xplat-app-dev"></a>교차 플랫폼 앱 개발
모든 주요 모바일 장치 플랫폼에 대한 기본 모바일 장치 앱을 개발하려면 최소한 Objective-C, Java 및 C# 또는 JavaScript 프로그래밍 언어에 대한 전문 지식이 필요합니다. 이처럼 여러 가지 플랫폼에서 개발해야 하는 비용 부담 때문에 일부 개발자들은 완전한 웹 브라우저 기반 환경을 앱에 사용하기로 선택합니다. 하지만 이러한 웹 기반 환경은 사용자들이 모바일 장치에서 기대하는 풍부한 환경을 제공하는 대다수의 기본 리소스에 액세스할 수 없습니다.

교차 플랫폼 도구는 단일 코드 기반(주로 JavaScript)을 공유하면서도 모바일 장치에 보다 풍부한 기본 환경을 제공합니다. 모바일 서비스를 사용하면 다음과 같은 개발 플랫폼에 대해 빠른 시작 자습서를 제공함으로써 교차 플랫폼 앱 개발 플랫폼의 백 엔드 서비스를 쉽게 만들고 관리할 수 있습니다.

+ [**Appcelerator**](http://go.microsoft.com/fwlink/p/?LinkId=509987)<br/>Appcelerator는 JavaScript를 사용하여 모든 모바일 장치 플랫폼에서 기본 앱처럼 실행되도록 컴파일된 단일 앱을 개발할 수 있게 해 줍니다. 풍부한 UI 사용자 환경, 모든 기본 장치 리소스에 대한 액세스, 기본 앱 성능을 제공합니다. 자세한 내용은 [Appcelerator 자습서][Appcelerator]를 참조하십시오.
 
+ [**PhoneGap**](https://go.microsoft.com/fwLink/p/?LinkID=390707)**/**[**Cordova**](http://cordova.apache.org/)<br/>PhoneGap(Apache Cordova 프로젝트 배포)은 표준화된 웹 API, HTML 및 JavaScript를 사용하여 Android, iOS 및 Windows 장치에서 실행되는 단일 앱을 개발할 수 있도록 해주는 무료 오픈 소스 프레임워크입니다. PhoneGap은 웹 뷰 기반의 UI를 제공하지만 푸시 알림, 가속도계, 카메라, 저장소, 지리적 위치, 앱 내 브라우저 등 장치의 기본 리소스에 액세스함으로써 사용자 환경을 개선합니다. 자세한 내용은 [PhoneGap 빠른 시작 자습서][PhoneGap]를 참조하십시오.
	
	이제 Visual Studio에서는 시험판 소프트웨어인 Visual Studio용 Multi-Device Hybrid Apps 확장을 사용하여 교차 플랫폼 Cordova 앱을 구축할 수 있습니다. 자세한 내용은 [HTML 및 JavaScript를 사용하여 Multi-Device Hybrid Apps 시작](http://msdn.microsoft.com/library/dn771545.aspx)(영문)을 참조하십시오.

+ [**Sencha Touch**](http://go.microsoft.com/fwlink/p/?LinkId=509988)<br/>Sencha Touch는 단일 HTML 및 JavaScript 코드 기반에서 다양한 모바일 장치에 기본 환경과 비슷한 환경을 제공하는 터치 스크린에 최적화된 다양한 컨트롤을 갖추고 있습니다. Sencha Touch를 PhoneGap 또는 Cordova 라이브러리와 함께 사용하여 기본 장치 리소스에 대한 사용자 액세스를 제공할 수 있습니다. 자세한 내용은 [Sencha Touch 빠른 시작 자습서][Sencha]를 참조하십시오.

+ [**Xamarin**](https://go.microsoft.com/fwLink/p/?LinkID=330242)<br/>Xamarin을 사용하면 iOS 및 Android 장치 모두에서 완전한 기본 UI를 갖추고 모든 장치 리소스에 액세스할 수 있는 완전한 기본 앱을 만들 수 있습니다. Xamarin 앱은 Objective-C 및 Java가 아니라 C#으로 코딩되어 있습니다. 따라서 .NET 개발자는 앱을 iOS 및 Android에 게시하고 Windows 프로젝트의 코드를 공유할 수 있습니다. Xamarin은 C# 코드에서 iOS 및 Android 장치 모두에 완전한 기본 사용자 환경을 제공합니다. 덕분에 iOS 및 Android 장치의 Windows 앱에서 일부 모바일 서비스 코드를 재사용할 수 있습니다. 자세한 내용은 아래의 [Xamarin 개발](#xamarin)을 참조하십시오.

	Xamarin Studio 또는 Visual Studio 2013을 사용하여 Xamarin 앱을 만들 수 있습니다. 자세한 내용은 [Visual Studio에서의 교차 플랫폼 개발](http://msdn.microsoft.com/library/dn771552.aspx)(영문)을 참조하십시오.


##<a id="shared-vs"></a>Visual Studio 프로젝트에서 코드 공유 및 재사용

모바일 서비스에는 모든 Windows 플랫폼에서의 개발을 지원하는 .NET Framework PCL(Portable Class Library)인 .NET 클라이언트 라이브러리가 포함되어 있습니다. 자세한 내용은 [.NET 클라이언트를 모바일 서비스와 함께 사용하는 방법]을 참조하십시오. 따라서 여러 C# 프로젝트에서 데이터 액세스 또는 인증 등에 동일 모바일 서비스 코드를 쉽게 재사용할 수 있습니다.

프로젝트 간에 C# 코드를 공유하고 재사용하는 일반적인 방법 중 하나는 MVVM(Model-View-View Model) 패턴을 구현하고 여러 플랫폼에서 어셈블리를 공유하는 것입니다. Visual Studio의 이식 가능한 클래스 라이브러리 프로젝트에서 모델 및 뷰 모델 클래스를 구현한 다음 다른 플랫폼에 대해 사용자 지정된 뷰를 만들 수 있습니다. 예를 들어 플랫폼 간에 공통되는 모델 코드에서는 플랫폼에 상관없이 원본(예: 모바일 서비스)에서 데이터를 검색할 수 있습니다. MSDN 라이브러리에서는 <a href="http://msdn.microsoft.com/library/gg597391(v=vs.110)">개요 및 예제</a>, <a href="http://msdn.microsoft.com/library/gg597392(v=vs.110)">API 차이점</a> 설명, <a href="http://msdn.microsoft.com/library/hh563947(v=vs.110)">이식 가능한 클래스 라이브러리를 사용한 MVVM 패턴 구현</a> 예제, 추가 <a href="http://msdn.microsoft.com/library/windowsphone/develop/jj714086(v=vs.105).aspx">규범 지침</a> 및 이식 가능한 클래스 라이브러리 프로젝트에서의 <a href="http://msdn.microsoft.com/library/hh871422(v=vs.110)">리소스 관리</a> 방법에 대한 정보를 제공합니다.

이러한 일반적인 지침 외에도 Visual Studio는 여러 클라이언트 앱 프로젝트 간에 모바일 서비스 코드를 재사용하기 위한 특정 기능을 제공합니다. 자세한 내용은 이후의 섹션에서 설명합니다. Visual Studio 2013을 사용하여 교차 플랫폼 앱을 만드는 방법에 대해 더 많은 일반적인 정보를 보려면 [Visual Studio에서의 교차 플랫폼 개발](http://msdn.microsoft.com/library/dn771552.aspx)(영문)을 참조하십시오.

### 범용 Windows 앱

Visual Studio 2013 업데이트 2는 범용 Windows 앱 프로젝트에 대한 지원을 추가합니다. 범용 앱은 Windows Store 8.1 및 Windows Phone 스토어 8.1 앱 프로젝트를 공유 코드 프로젝트와 함께 포함하고 있는 솔루션입니다. 이러한 종류의 프로젝트에서 공유 코드는 Windows Store 및 Windows Phone 프로젝트 모두에 속한 것처럼 취급됩니다. 자세한 내용은 [모든 Windows 장치에 대한 범용 Windows 앱 만들기](영문)를 참조하십시오. 범용 Windows 앱은 C#/XAML 및 JavaScript/HTML로 작성할 수 있습니다.

기본적으로, [Azure 관리 포털]에 있는 모바일 서비스 빠른 시작 탭은 처음 시작할 때 사용할 수 있는 TodoList 샘플 앱의 범용 Windows 앱 버전을 생성합니다. C#/XAML 또는 JavaScript/HTML 버전의 프로젝트를 다운로드할 수 있습니다. 자세한 내용은 [모바일 서비스 시작](../mobile-services-windows-store-get-started.md)을 참조하십시오.

>[AZURE.NOTE]포털에서 제공하는 빠른 시작 앱 프로젝트의 C# 버전은 MainPage.xaml.cs 코드 숨김 페이지를 공유하지만 뷰 모델을 사용하지 않습니다. C#에서 MVVM을 사용하는 범용 Windows 앱 프로젝트로서의 TodoList 앱에 대한 예를 보려면 [MVVM을 사용하는 Azure 모바일 서비스의 범용 Windows 앱 프로젝트](영문)를 참조하십시오.

###<a id="xamarin"></a>Xamarin 개발

Xamarin 및 Visual Studio 또는 Xamarin Studio를 사용하여 iOS 및 Android용 앱을 개발함으로써 Visual Studio 및 C# 개발 환경을 활용할 수 있습니다. Xamarin은 C# 코드를 사용하여 iOS 및 Android 앱을 개발할 수 있게 해 주는 .NET Framework의 교차 플랫폼 구현을 사용합니다. Xamarin을 사용하면 Windows 프로젝트에서 모바일 서비스 .NET 클라이언트 라이브러리를 사용하는 기존 코드를 활용하여 모바일 서비스에 액세스할 수 있습니다. 자세한 내용은 [Visual Studio에서의 교차 플랫폼 개발](http://msdn.microsoft.com/library/dn771552.aspx)(영문)을 참조하십시오.

모바일 서비스를 사용하는 Xamarin 앱을 만들기 시작하려면 Xamarin 빠른 시작 자습서([iOS](mobile-services-ios-get-started.md) / [Android](mobile-services-android-get-started.md))를 참조하십시오.


### Windows Store 및 Windows Phone Silverlight 앱

Windows Phone 8.1에서는 이전 Silverlight 기반 XAML을 사용하여 앱을 개발하거나, 범용 Windows 앱을 개발할 수 있게 해 주는 Windows 런타임 기반 XAML을 사용할 수 있습니다. Windows Phone 8.1 Silverlight 앱 및 Windows Phone Store 8.1 앱에 대한 자세한 내용은 [Windows Phone 8 개발자를 위한 향후 계획](영문)을 참조하십시오.

모바일 서비스 .NET 클라이언트 라이브러리는 Windows Phone Store 8.1 및 Windows Phone Silverlight 8.1 앱을 모두 지원합니다. Windows 런타임 앱과 Windows Phone Silverlight 앱 모두를 동일 프로젝트로부터 만들 수는 없기 때문에 위에 설명된 PCL 및 MVVM 같은 코드 재사용 전략을 고려해 봐야 합니다.

>[AZURE.NOTE]Windows 런타임 앱과 Windows Phone Silverlight 앱 모두에서 Microsoft 계정을 통해 Single Sign-On 클라이언트 인증을 사용하려면 먼저 Windows 스토어 대시보드에서 Windows 런타임 앱을 등록해야 합니다. Windows Phone용 Live Connect 등록을 만든 이후에는 Windows 스토어용 Live Connect 등록을 만들 수 없기 때문입니다. 작업 방법에 대한 자세한 내용은 **Live Connect Single Sign-On을 사용하여 Windows 스토어 앱 인증**([Windows 스토어][SSO Windows Store]/[Windows Phone][SSO Windows Phone]) 항목을 참조하십시오.


<!-- URLs -->
[Azure 관리 포털]: https://manage.windowsazure.com
[Azure 알림 허브]: /develop/net/how-to-guides/service-bus-notification-hubs/
[SSO Windows Store]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
[SSO Windows Phone]: /develop/mobile/tutorials/single-sign-on-wp8/
[Tutorials and resources]: /develop/mobile/resources/
[Get started with Notification Hubs]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[알림 허브를 통해 사용자에게 플랫폼 간 알림 보내기]: /manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Get started with push Windows dotnet]: /develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/
[Get started with push Windows js]: /develop/mobile/tutorials/get-started-with-push-js-vs2012/
[Get started with push Windows Phone]: /develop/mobile/tutorials/get-started-with-push-wp8/
[Get started with push iOS]: /develop/mobile/tutorials/get-started-with-push-ios/
[Get started with push Android]: /develop/mobile/tutorials/get-started-with-push-android/
[Dynamic schema]: http://msdn.microsoft.com/library/windowsazure/jj193175.aspx
[.NET 클라이언트를 모바일 서비스와 함께 사용하는 방법]: documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[send]: http://msdn.microsoft.com/library/windowsazure/jj554217.aspx
[TemplatePushMessage]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.templatepushmessage.aspx
[PhoneGap]: mobile-services-javascript-backend-phonegap-get-started.md
[Sencha]: partner-sencha-mobile-services-get-started.md
[Appcelerator]: ../partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started.md
[SendAsync]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.notifications.pushclient.sendasync.aspx
[ApiServices.Push]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.notifications.pushclient.sendasync.aspx
[IPushMessage]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.notifications.pushclient.sendasync.aspx
[Windows Phone 8 개발자를 위한 향후 계획]: http://msdn.microsoft.com/library/windows/apps/dn655121(v=vs.105).aspx
[모든 Windows 장치에 대한 범용 Windows 앱 만들기]: http://go.microsoft.com/fwlink/p/?LinkId=509905
[MVVM을 사용하는 Azure 모바일 서비스의 범용 Windows 앱 프로젝트]: http://code.msdn.microsoft.com/Universal-Windows-app-for-db3564de
 

<!---HONumber=July15_HO4-->
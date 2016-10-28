<properties
	pageTitle="Azure 모바일 앱의 인증 및 권한 부여 | Microsoft Azure"
	description="Azure 모바일 앱에 대한 인증/권한 부여 기능의 개념 참조 및 개요"
	services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson"
	manager="erikref"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="mahender"/>

# Azure 모바일 앱의 인증 및 권한 부여

## 앱 서비스 인증/권한 부여란?

> [AZURE.NOTE] 이 항목은 웹, 모바일 및 API 앱을 다루는 통합된 [앱 서비스 인증/권한 부여](../app-service/app-service-authentication-overview.md) 항목으로 마이그레이션됩니다.

앱 서비스 인증/권한 부여는 응용 프로그램이 앱 백 엔드에 필요한 코드 변경 없이 사용자를 로그인할 수 있도록 만드는 기능입니다. 응용 프로그램을 보호하고 사용자 단위당 데이터로 작업하는 쉬운 방법을 제공합니다.

앱 서비스는 페더레이션된 ID를 사용하며 여기서 타사 **ID 공급자**("IDP")는 계정을 저장하고 사용자를 인증하며 응용 프로그램은 자체 ID 대신 이 ID를 사용합니다. 앱 서비스는 기본적으로 _Azure Active Directory_, _Facebook_, _Google_, _Microsoft 계정_, 및 _Twitter_와 같은 다섯 가지 ID 공급자를 지원합니다. 또한 다른 ID 공급자 또는 사용자 고유의 사용자 지정 ID 솔루션을 통합하여 앱에 이 지원을 확장할 수 있습니다.

앱은 개수에 관계 없이 이러한 ID 공급자를 활용할 수 있으므로 최종 사용자에게 로그인 방법에 대한 옵션을 제공할 수 있습니다.

지금 바로 시작하려는 경우 다음 자습서 중 하나를 참조하세요.

- [iOS 앱에 인증 추가]
- [Xamarin.iOS 앱에 인증 추가]
- [Xamarin Android 앱에 인증 추가]
- [Windows 앱에 인증 추가]

## 인증 작동 방법

ID 공급자 중 하나를 사용하여 인증하려면 먼저 ID 공급자를 구성하여 응용 프로그램에 대해 알아야 합니다. 그러면 ID 공급자는 응용 프로그램에 다시 제공하는 ID 및 암호를 제공합니다. 트러스트 관계를 완료하고 앱 서비스가 제공된 ID의 유효성을 검사하도록 합니다.

이러한 단계는 다음 항목에서 자세히 설명합니다.

- [Azure Active Directory 로그인을 사용하도록 앱을 구성하는 방법]
- [Facebook 로그인을 사용하도록 앱을 구성하는 방법]
- [Google 로그인을 사용하도록 앱을 구성하는 방법]
- [Microsoft 계정 로그인을 사용하도록 앱을 구성하는 방법]
- [Twitter 로그인을 사용하도록 앱을 구성하는 방법]

모든 것이 백 엔드에서 구성되면 클라이언트를 수정하여 로그인할 수 있습니다. 두 가지 접근 방법은 다음과 같습니다.

- 한 줄의 코드를 사용하여 모바일 앱 클라이언트 SDK가 사용자를 로그인하도록 할 수 있습니다.
- 지정된 ID 공급자를 통해 게시된 SDK를 활용하여 ID를 설정한 다음 앱 서비스에 액세스를 얻습니다.

>[AZURE.TIP] 많은 응용 프로그램은 공급자 SDK를 사용하여 더 원시적인 로그인 환경을 가져오고 새로 고침 지원 및 기타 공급자별 혜택을 활용해야 합니다.

### 공급자 SDK 없이 인증 작동 방법

공급자 SDK 설정하지 않으려는 경우 모바일 앱이 로그인을 실행하도록 할 수 있습니다. 모바일 앱 클라이언트 SDK는 선택한 공급자에게 웹 보기를 열고 로그인을 완료합니다. 서버가 로그인을 관리하고 클라이언트 SDK가 공급자 토큰을 받지 않기 때문에 경우에 따라 블로그 및 포럼에 "서버 흐름" 또는 "서버에서 제어된 흐름"이라는 것이 나타납니다.

이 흐름을 시작하는 데 필요한 코드는 각 플랫폼에 대한 인증 자습서에서 다룹니다. 흐름의 끝에서 클라이언트 SDK에는 앱 서비스 토큰이 있고 토큰은 백 엔드에 대한 모든 요청에 자동으로 연결됩니다.

### 공급자 SDK를 통한 인증 작동 방법

공급자 SDK로 작업하면 로그인 환경이 앱을 실행하는 플랫폼 OS와 더 밀접하게 상호 작용할 수 있습니다. 또한 이렇게 하면 클라이언트의 공급자 토큰 및 사용자 정보를 가져오며 이는 Graph API를 사용하고 사용자 환경을 사용자 지정하는 작업을 용이하게 합니다. 클라이언트의 코드가 로그인을 처리하고 클라이언트 코드가 공급자 토큰에 대한 액세스를 갖기 때문에 경우에 따라 블로그 및 포럼에서 "클라이언트 흐름" 또는 "클라이언트에서 제어된 흐름"이라는 것을 확인합니다.

공급자 토큰을 가져오면 유효성 검사에 대한 앱 서비스에 전송되어야 합니다. 흐름의 끝에서 클라이언트 SDK에는 앱 서비스 토큰이 있고 토큰은 백 엔드에 대한 모든 요청에 자동으로 연결됩니다. 또한 선택하는 경우 개발자는 공급자 토큰에 대한 참조를 유지할 수 있습니다.

## 권한 부여 작동 원리

앱 서비스 인증/권한 부여는 **요청이 인증되지 않을 때 수행할 동작**에 대한 여러 가지 선택을 노출합니다. 코드가 지정된 요청을 받기 전에 앱 서비스가 요청이 인증되지를 확인하도록 하고 그렇지 않은 경우 거부하고 다시 시도하기 전에 사용자가 로그인을 시도할 수 있습니다.

한 가지 옵션은 ID 공급자 중 하나로 인증되지 않은 요청을 리디렉션하는 것입니다. 웹 브라우저에서 사용자를 새 페이지에 실제로 가져옵니다. 그러나 모바일 클라이언트는 이러한 방식으로 리디렉션할 수 없고 인증되지 않은 응답은 HTTP _401 권한 없는_ 응답을 받습니다. 이 점을 고려하면 클라이언트가 만든 첫 번째 요청은 로그인 끝점에 항상 위치해야 하고 사용자가 다른 API 호출을 만들 수 있습니다. 로그인하기 전에 다른 API를 호출하려면 클라이언트에 오류가 발생합니다.

또한 어떤 끝점이 인증을 요구하는지에 대해 보다 세부적으로 제어하려는 경우 인증되지 않은 요청에 대한 "작업 없음(요청 허용)"을 선택합니다. 이 경우에 모든 인증 결정은 응용 프로그램 코드에 연기됩니다. 또한 사용자 지정 권한 부여 규칙에 기반하여 특정 사용자에 대한 액세스를 허용할 수 있습니다.

## 설명서

다음 자습서는 앱 서비스를 사용하여 모바일 클라이언트에 인증을 추가하는 방법을 보여줍니다.

- [iOS 앱에 인증 추가]
- [Xamarin.iOS 앱에 인증 추가]
- [Xamarin Android 앱에 인증 추가]
- [Windows 앱에 인증 추가]

다음 자습서에서는 앱 서비스를 구성하여 다른 인증 공급자를 활용하는 방법을 보여줍니다.

- [Azure Active Directory 로그인을 사용하도록 앱을 구성하는 방법]
- [Facebook 로그인을 사용하도록 앱을 구성하는 방법]
- [Google 로그인을 사용하도록 앱을 구성하는 방법]
- [Microsoft 계정 로그인을 사용하도록 앱을 구성하는 방법]
- [Twitter 로그인을 사용하도록 앱을 구성하는 방법]

여기에 제공된 것 이외의 ID 시스템을 사용하려는 경우 [.NET 서버 SDK에서 사용자 지정 인증 지원 미리 보기](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth)를 활용할 수 있습니다.

[iOS 앱에 인증 추가]: app-service-mobile-ios-get-started-users.md
[Xamarin.iOS 앱에 인증 추가]: app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin Android 앱에 인증 추가]: app-service-mobile-xamarin-android-get-started-users.md
[Windows 앱에 인증 추가]: app-service-mobile-windows-store-dotnet-get-started-users.md

[Azure Active Directory 로그인을 사용하도록 앱을 구성하는 방법]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook 로그인을 사용하도록 앱을 구성하는 방법]: app-service-mobile-how-to-configure-facebook-authentication.md
[Google 로그인을 사용하도록 앱을 구성하는 방법]: app-service-mobile-how-to-configure-google-authentication.md
[Microsoft 계정 로그인을 사용하도록 앱을 구성하는 방법]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter 로그인을 사용하도록 앱을 구성하는 방법]: app-service-mobile-how-to-configure-twitter-authentication.md

<!---HONumber=AcomDC_0824_2016-->
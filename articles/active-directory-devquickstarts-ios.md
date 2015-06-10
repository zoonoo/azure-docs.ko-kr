<properties
	pageTitle="Azure AD iOS 시작 | Microsoft Azure"
	description="로그인을 위해 Azure AD와 통합되고 OAuth를 사용하여 Azure AD로 보호되는 API를 호출하는 iOS 응용 프로그램 빌드 방법"
	services="active-directory"
	documentationCenter="ios"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="brandwe"/>

# iOS 앱에 Azure AD 통합

[AZURE.INCLUDE [active-directory-devguide](../includes/active-directory-devguide.md)]

데스크톱 응용 프로그램을 개발하는 경우 Azure AD를 사용하면 간단하고 편리하게 Active Directory 계정에 사용하여 사용자를 인증할 수 있습니다. 또한 응용 프로그램에서 Office 365 API 또는 Azure API와 같이 Azure AD를 통해 보호되는 웹 API를 안전하게 사용할 수 있습니다.

보호된 리소스에 액세스해야 하는 iOS 클라이언트의 경우 Azure AD는 Active Directory 인증 라이브러리 또는 ADAL을 제공합니다. ADAL의 유일한 용도는 앱이 쉽게 액세스 토큰을 가져오도록 하는 것입니다. 액세스 토큰을 얼마나 쉽게 가져올 수 있는지 보여 주기 위해 다음을 수행하는 Objective C To-Do List 응용 프로그램을 빌드해 보겠습니다.

-	[OAuth 2.0 인증 프로토콜](https://msdn.microsoft.com/library/azure/dn645545.aspx)을 사용하여 Azure AD Graph API를 호출하기 위한 액세스 토큰을 가져옵니다.
-	지정된 별칭을 가진 사용자를 디렉터리에서 검색합니다.
-	사용자를 로그아웃합니다.

완전하게 작동하는 응용 프로그램을 빌드하려면 다음 작업이 필요합니다.

2. Azure AD에 응용 프로그램을 등록합니다.
3. ADAL을 설치 및 구성합니다.
5. ADAL을 사용하여 Azure AD에서 토큰을 가져옵니다.

시작하려면 [앱 기본 사항을 다운로드](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip)하거나 [완성된 샘플을 다운로드](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip)하세요. 또한 사용자를 만들고 응용 프로그램을 등록할 수 있는 Azure AD 테넌트도 필요합니다. 테넌트가 아직 없는 경우 [가져오는 방법을 알아봅니다](active-directory-howto-tenant.md).

## 1단계: .Net 또는 Node.js REST API TODO 샘플 서버 다운로드 및 실행

이 샘플은 Microsoft Azure Active Directory용 단일 테넌트 ToDo REST API를 빌드하기 위한 기존 샘플에서도 작동하도록 특수하게 작성되었습니다. 다음은 빠른 시작을 위한 필수 구성 요소입니다.

설정 방법에 대한 자세한 내용은 다음에서 기존 샘플을 참조하세요.

* [Node.js에 대한 Microsoft Azure Active Directory 샘플 REST API 서비스](active-directory-devquickstarts-webapi-nodejs.md)

## 2단계: Microsoft Azure AD 테넌트에 Web API 등록

**수행할 작업**

*Microsoft Active Directory는 두 가지 유형의 응용 프로그램 추가를 지원합니다. 하나는 사용자에게 서비스를 제공하는 Web API이고, 다른 하나는 해당 Web API에 액세스하는 응용 프로그램(웹 또는 장치에서 실행 중인 응용 프로그램)입니다. 이 단계에서는 이 샘플을 테스트하기 위해 로컬로 실행하는 Web API를 등록할 것입니다. 일반적으로 이 Web API는 앱에서 액세스할 기능을 제공하는 REST 서비스가 됩니다. Microsoft Azure Active Directory는 모든 끝점을 보호할 수 있습니다.*

*여기서는 위에 참조된 TODO REST API를 등록하게 되지만 Azure Active Directory를 통해 보호하려는 어떤 Web API도 마찬가지로 등록할 수 있습니다.*

Microsoft Azure AD에 Web API를 등록하는 단계

1. [Azure 관리 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 왼쪽 탐색 창에서 Active Directory를 클릭합니다.
3. 샘플 응용 프로그램을 등록할 디렉터리 테넌트를 클릭합니다.
4. 응용 프로그램 탭을 클릭합니다.
5. 서랍에서 추가를 클릭합니다.
6. “내 조직에서 개발 중인 응용 프로그램 추가”를 클릭합니다.
7. 응용 프로그램의 이름(예: "TodoListService")을 입력하고 “웹 응용 프로그램 및/또는 Web API”를 선택한 후 다음을 클릭합니다.
8. 로그온 URL로는 샘플의 기준 URL(기본적으로 `https://localhost:8080`임)을 입력합니다.
9. 앱 ID URI로는 `https://<your_tenant_name>/TodoListService`를 입력하고 `<your_tenant_name>`을 해당 Azure AD 테넌트 이름으로 바꿉니다. 등록을 완료하려면 확인을 클릭합니다.
10. 계속 Azure 포털에 있는 상태에서 응용 프로그램의 구성 탭을 클릭합니다.
11. **클라이언트 ID 값을 찾아 따로 복사합니다.** 나중에 응용 프로그램을 구성할 때 필요합니다.

## 3단계: 샘플 iOS 네이티브 클라이언트 응용 프로그램 등록

웹 응용 프로그램을 등록하는 것이 첫 번째 단계입니다. 다음으로 Azure Active Directory에 응용 프로그램에 대해 알려야 합니다. 이렇게 하면 응용 프로그램이 방금 등록한 Web API와 통신할 수 있습니다.

**수행할 작업**

*앞서 설명한 것처럼 Microsoft Azure Active Directory는 두 가지 유형의 응용 프로그램 추가를 지원합니다. 하나는 사용자에게 서비스를 제공하는 Web API이고, 다른 하나는 해당 Web API에 액세스하는 응용 프로그램(웹 또는 장치에서 실행 중인 응용 프로그램)입니다. 이 단계에서는 이 샘플에 응용 프로그램을 등록합니다. 이 응용 프로그램이 방금 등록한 Web API에 대한 액세스를 요청하려면 이 작업을 수행해야 합니다. 등록되지 않은 응용 프로그램의 경우 로그인을 요청하더라도 Azure Active Directory에서 거부합니다. 이것은 모델 보안의 한 부분입니다.*

*여기서는 위에 참조된 이 샘플 응용 프로그램을 등록하게 되지만 개발하는 어떤 앱도 등록할 수 있습니다.*

**한 테넌트에 응용 프로그램과 Web API를 모두 배치하는 이유는 무엇인가요?**

*짐작할 수에 있는 것처럼 다른 테넌트에서 Azure Active Directory에 등록된 외부 API에 액세스하는 응용 프로그램을 빌드할 수 있습니다. 이렇게 하면 응용 프로그램에서 API 사용에 동의할지 묻는 메시지가 표시됩니다. 다행인 것은 iOS용 Active Directory 인증 라이브러리가 자동으로 동의 과정을 진행한다는 것입니다. 고급 기능을 좀 더 살펴보게 되면 다른 서비스 공급자는 물론 Azure 및 Office에서 Microsoft API 모음에 액세스하는 데 필요한 작업의 중요한 부분이라는 것을 알 수 있게 될 것입니다. 이제 Web API와 응용 프로그램을 동일한 테넌트에서 등록했으므로 동의를 묻는 메시지는 표시되지 않습니다. 일반적으로 회사를 위해서만 응용 프로그램을 개발하는 경우가 여기에 해당합니다.*

1. [Azure 관리 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 왼쪽 탐색 창에서 Active Directory를 클릭합니다.
3. 샘플 응용 프로그램을 등록할 디렉터리 테넌트를 클릭합니다.
4. 응용 프로그램 탭을 클릭합니다.
5. 서랍에서 추가를 클릭합니다.
6. “내 조직에서 개발 중인 응용 프로그램 추가”를 클릭합니다.
7. 응용 프로그램의 이름(예: "TodoListClient-iOS")을 입력하고 “네이티브 클라이언트 응용 프로그램”을 선택한 후 다음을 클릭합니다.
8. 리디렉션 URI로 `http://TodoListClient`를 입력합니다. 마침을 클릭합니다.
9. 응용 프로그램의 구성 탭을 클릭합니다.
10. 클라이언트 ID 값을 찾아 따로 복사합니다. 나중에 응용 프로그램을 구성할 때 필요합니다.
11. “다른 응용 프로그램에 대한 권한”에서 “응용 프로그램 추가”를 클릭합니다. “표시” 드롭다운에서 “기타”를 선택하고 위쪽 확인 표시를 클릭합니다. TodoListService를 찾아 클릭하고 아래쪽 확인 표시를 클릭하여 응용 프로그램을 추가합니다. “위임된 권한” 드롭다운에서 "Access TodoListService"를 선택하고 구성을 저장합니다.


## 4단계: iOS 네이티브 클라이언트 샘플 코드 다운로드

* `$ git clone git@github.com:AzureADSamples/NativeClient-iOS.git`

## 5단계: iOS용 ADAL을 다운로드하고 XCode 작업 영역에 추가

#### iOS용 ADAL 다운로드

* `git clone git@github.com:MSOpenTech/azure-activedirectory-library-for-ios.git`

#### 라이브러리를 작업 영역으로 가져오기

XCode에서 프로젝트 디렉터리를 마우스 오른쪽 단추로 클릭하고 "iOS 샘플에 파일 추가"를 선택합니다.

디렉터리를 지정하라는 메시지가 표시되는 경우 iOS용 ADAL을 복제한 디렉터리를 선택합니다.

#### 연결된 프레임워크 및 라이브러리에 libADALiOS.a 추가

"연결된 프레임워크 및 라이브러리" 아래에서 추가 단추를 클릭하고 가져온 프레임워크에서 라이브러리 파일을 추가합니다.

프로젝트를 빌드하여 모든 항목이 올바르게 컴파일되는지 확인합니다.


## 6단계: Web API 정보로 settings.plist 파일 구성

"지원 파일"에서 settings.plist 파일을 찾습니다. 이 파일에는 다음 정보가 포함되어 있습니다.

```XML
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>authority</key>
	<string>https://login.windows.net/common/oauth2/token</string>
	<key>clientId</key>
	<string>xxxxxxx-xxxxxx-xxxxxxx-xxxxxxx</string>
	<key>resourceString</key>
	<string>https://localhost/todolistservice</string>
	<key>redirectUri</key>
	<string>http://demo_todolist_app</string>
	<key>userId</key>
	<string>user@domain.com</string>
	<key>taskWebAPI</key>
	<string>https://localhost/api/todolist/</string>
</dict>
</plist>
```

plist 파일의 정보를 Web API 설정으로 바꿉니다.

##### 참고

현재 기본값은 [Node.js용 Azure Active Directory 샘플 REST API 서비스](https://github.com/AzureADSamples/WebAPI-Nodejs)(영문)에서 작동하도록 설정되어 있습니다. 그러나 Web API의 clientID를 지정해야 합니다. 사용자 고유의 API를 실행하는 경우 필요에 따라 끝점을 업데이트해야 합니다.

## 7단계: 응용 프로그램 빌드 및 실행

REST API 끝점에 연결할 수 있고 Azure Active Directory 계정의 자격 증명을 묻는 메시지가 표시되어야 합니다.

추가 리소스를 보려면 다음을 확인하세요. [GitHub의 AzureADSamples >>](https://github.com/AzureAdSamples)(영문) - [Azure.com >>](http://azure.microsoft.com/documentation/services/active-directory/)(영문)의 Azure AD 설명서

<!---HONumber=58-->
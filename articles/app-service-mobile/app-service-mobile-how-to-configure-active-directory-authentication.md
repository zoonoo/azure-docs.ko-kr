<properties
	pageTitle="앱 서비스 응용 프로그램에 대해 Azure Active Directory 인증을 구성하는 방법"
	description="앱 서비스 응용 프로그램에 대해 Azure Active Directory 인증을 구성하는 방법을 알아봅니다."
	authors="mattchenderson"
	services="app-service"
	documentationCenter=""
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="mahender"/>

# Azure Active Directory 로그인을 사용하도록 앱 서비스 응용 프로그램을 구성하는 방법

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 항목에서는 Azure Active Directory를 인증 공급자로 사용하도록 Azure 앱 서비스를 구성하는 방법을 보여 줍니다.

## <a name="express"> </a>기본 설정을 사용하여 Azure Active Directory 구성

13. [Azure 포털]에서 응용 프로그램으로 이동합니다. **설정**을 클릭한 다음 **인증/권한 부여**를 클릭합니다.

14. 인증/권한 부여 기능이 사용하도록 설정되지 않은 경우 스위치를 **On**으로 전환합니다.

15. **Azure Active Directory**를 클릭한 다음 **관리 모드**에서 **Express**(기본)를 클릭합니다.

16. Azure Active Directory에서 응용 프로그램을 등록하려면 **확인**을 클릭합니다. 이렇게 하면 새롭게 등록됩니다. 기존 등록을 대신 선택하려는 경우 **기존 앱 선택**을 클릭한 다음 테넌트 내에서 이전에 만든 등록 이름을 검색합니다. 등록을 클릭하여 선택하고 **확인**을 클릭합니다. Azure Active Directory 설정 블레이드에서 **확인**을 클릭합니다.

    ![][0]

	기본적으로 앱 서비스는 인증을 제공하지만 사이트 콘텐츠 및 API에 액세스하는 권한을 제한하지는 않습니다. 앱 코드에서 사용자 권한을 부여해야 합니다.

17. (옵션) Azure Active Directory에서 인증된 사용자만 사이트에 액세스하도록 제한하려면 **요청이 인증되지 않으면 수행할 동작**을 **Azure Active Directory로 로그인**으로 설정합니다. 이렇게 하려면 모든 요청이 인증되어야 하며 모든 인증되지 않은 요청은 인증을 위해 Azure Active Directory로 리디렉션되어야 합니다.

17. **Save**를 클릭합니다.

이제 앱에서 Azure Active Directory를 인증에 사용할 준비가 되었습니다.

## <a name="advanced"> </a>(대체 방법) 고급 설정을 사용하여 Azure Active Directory를 수동으로 구성
수동으로 구성 설정을 제공하도록 선택할 수도 있습니다. 사용하려는 AAD 테넌트가 Azure에 로그인하는 테넌트와 다른 경우 권장되는 솔루션입니다. 구성을 완료하려면 먼저 Azure Active Directory에 등록을 만든 다음 앱 서비스에 등록 세부 정보 중 일부를 제공해야 합니다.

### <a name="register"></a>Azure Active Directory에 응용 프로그램 등록

1. [Azure 포털]에 로그온한 다음 응용 프로그램으로 이동합니다. **URL**을 복사합니다. Azure Active Directory 앱을 구성하는 데 이 정보를 사용합니다.

3. [Azure 클래식 포털]에 로그인한 다음 **Active Directory**로 이동합니다.

    ![][2]

4. 디렉터리를 선택한 다음 맨 위에서 **응용 프로그램** 탭을 선택합니다. 맨 아래에서 **추가**를 클릭하여 새로운 앱 등록을 만듭니다.

5. **내 조직에서 개발 중인 응용 프로그램 추가**를 클릭합니다.

6. 응용 프로그램 추가 마법사에서 응용 프로그램의 **이름**을 입력하고 **웹 응용 프로그램 및/또는 웹 API** 유형을 클릭합니다. 계속하려면 클릭합니다.

7. **SIGN-ON URL** 상자에 앞에서 복사한 응용 프로그램 URL을 붙여넣습니다. **앱 ID URI** 상자에 동일한 URL을 입력합니다. 계속하려면 클릭합니다.

8. 응용 프로그램이 추가되면 **구성** 탭을 클릭합니다. **Single Sign-on** 아래 **회신 URL**을 앞에 _/.auth/login/aad/callback_ 경로가 추가된 응용 프로그램 URL이 되도록 편집합니다. 예: `https://contoso.azurewebsites.net/.auth/login/aad/callback` HTTPS 체계를 사용 중인지 확인합니다.

    ![][3]

9. **저장**을 클릭합니다. 그런 다음 앱의 **클라이언트 ID**를 복사합니다. 나중에 이를 사용하도록 응용 프로그램을 구성합니다.

10. 맨 아래 명령 모음에서 **끝점 보기**를 클릭한 다음 **페더레이션 메타데이터 문서** URL을 복사하고 해당 문서를 다운로드하거나 브라우저에서 이 문서로 이동합니다.

11. 루트 **EntityDescriptor** 요소 내에 테넌트에 관련된 GUID(“테넌트 ID”라고 함) 앞에 양식 `https://sts.windows.net/`의 **entityID** 특성이 있어야 합니다. 이 값을 복사하여 **발급자 URL**로 사용합니다. 나중에 이를 사용하도록 응용 프로그램을 구성합니다.

### <a name="secrets"> </a>응용 프로그램에 Azure Active Directory 정보 추가

13. [Azure 포털]로 돌아가서 응용 프로그램으로 이동합니다. **설정**을 클릭한 다음 **인증/권한 부여**를 클릭합니다.

14. 인증/권한 부여 기능이 사용하도록 설정되지 않은 경우 스위치를 **설정**으로 전환합니다.

15. **Azure Active Directory**를 클릭한 다음 **관리 모드**에서 **고급**을 클릭합니다. 앞에서 얻은 클라이언트 ID 및 발급자 URL 값을 붙여넣습니다. 그런 후 **OK**를 클릭합니다.

    ![][1]

	기본적으로 앱 서비스는 인증을 제공하지만 사이트 콘텐츠 및 API에 액세스하는 권한을 제한하지는 않습니다. 앱 코드에서 사용자 권한을 부여해야 합니다.

17. (옵션) Azure Active Directory에서 인증된 사용자만 사이트에 액세스하도록 제한하려면 **요청이 인증되지 않으면 수행할 동작**을 **Azure Active Directory로 로그인**으로 설정합니다. 이렇게 하려면 모든 요청이 인증되어야 하며 모든 인증되지 않은 요청은 인증을 위해 Azure Active Directory로 리디렉션되어야 합니다.

17. **Save**를 클릭합니다.

이제 앱에서 Azure Active Directory를 인증에 사용할 준비가 되었습니다.

## (옵션) 네이티브 클라이언트 응용 프로그램 구성

Azure Active Directory를 사용하면 권한 매핑에 대해 보다 강력한 제어를 제공하는 네이티브 클라이언트를 등록할 수도 있습니다. 이는 **Active Directory 인증 라이브러리**와 같은 라이브러리를 사용하여 로그인을 수행하려는 경우 필요합니다.

1. [Azure 클래식 포털]에서 **Active Directory**로 이동합니다.

2. 디렉터리를 선택한 다음 맨 위에서 **응용 프로그램** 탭을 선택합니다. 맨 아래에서 **추가**를 클릭하여 새로운 앱 등록을 만듭니다.

3. **내 조직에서 개발 중인 응용 프로그램 추가**를 클릭합니다.

4. 응용 프로그램 추가 마법사에서 응용 프로그램의 **이름**을 입력하고 **네이티브 클라이언트 응용 프로그램** 유형을 클릭합니다. 계속하려면 클릭합니다.

5. HTTPS 체계를 사용하여 **리디렉션 URI** 상자에 사이트의 _/.auth/login/done_ 끝점을 입력합니다. 이 값은 \_https://contoso.azurewebsites.net/.auth/login/done_와 유사해야 합니다. Windows 응용 프로그램을 만드는 경우 URI로 [패키지 SID](app-service-mobile-dotnet-how-to-use-client-library.md#package-sid)를 사용합니다.

6. 네이티브 응용 프로그램이 추가되면 **구성** 탭을 클릭합니다. **클라이언트 ID**를 찾고 이 값을 기록해 둡니다.

7. 페이지 아래의 **다른 응용 프로그램에 대한 권한** 섹션으로 스크롤하여 **응용 프로그램 추가**를 클릭합니다.

8. 이전에 등록한 웹 응용 프로그램을 검색하고 더하기 아이콘을 클릭합니다. 그런 다음 체크 표시를 클릭하여 대화 상자를 닫습니다. 웹 응용 프로그램 찾을 수 없는 경우 등록으로 이동하고 새 회신 URL(예: 현재 URL의 HTTP 버전)을 추가하며 저장을 클릭한 다음 이러한 단계를 반복합니다. 응용 프로그램은 목록에 표시되어야 합니다.

9. 방금 추가된 새 항목에서 **권한 위임** 드롭다운을 열고 **액세스(appName)**를 선택합니다. 그런 다음 **Save**를 클릭합니다.

이제 앱 서비스 응용 프로그램에 액세스할 수 있는 네이티브 클라이언트 응용 프로그램을 구성했습니다.

## <a name="related-content"> </a>관련 콘텐츠

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[Azure 포털]: https://portal.azure.com/
[Azure 클래식 포털]: https://manage.windowsazure.com/
[alternative method]: #advanced

<!---HONumber=AcomDC_0824_2016-->
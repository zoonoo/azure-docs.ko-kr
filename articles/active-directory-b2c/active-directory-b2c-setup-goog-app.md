<properties
	pageTitle="Azure Active Directory B2C 미리 보기: Google+ 구성 | Microsoft Azure"
	description="소비자에게 Azure Active Directory B2C를 사용하여 보안이 유지되는 응용 프로그램에서 Google+ 계정으로 등록 및 로그인 제공"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C 미리 보기: 고객에게 Google+ 계정으로 등록 및 로그인 제공

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Google+ 응용 프로그램 만들기

Azure Active Directory(AD) B2C에서 Google+을 ID 공급자로 사용하려면 먼저 Google+ 응용 프로그램을 만들고 올바른 매개 변수를 제공해야 합니다. 다음을 수행하려면 Google+ 계정이 필요합니다. 계정이 없다면 [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)에서 얻을 수 있습니다.

1. [Google 개발자 콘솔](https://console.developers.google.com/)로 이동하고 Google + 계정 자격 증명으로 로그인합니다.
2. **프로젝트 만들기**를 클릭하고 **프로젝트 이름**을 입력하며 서비스 계약에 동의하고 **만들기**를 클릭합니다.

    ![G+ - 시작하기](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![G+ - 새 프로젝트](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. **APIs & Auth**를 클릭한 다음 왼쪽 이동에서**Credentials**를 클릭합니다.
4. **OAuth 동의 화면**을 위쪽 탭에서 클릭합니다.

    ![G+ - 자격 증명](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. 유효한 **전자 메일 주소**를 선택하거나 지정하고 **제품 이름**을 제공하며 **저장**을 클릭합니다.

    ![G+ - OAuth 동의 화면](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. **응용 프로그램 형식**에서 **웹 응용 프로그램**을 선택합니다.
7. 응용 프로그램에 **이름**을 제공하고 **권한이 부여된 리디렉션 URI** 필드의 [https://login.microsoftonline.com](https://login.microsoftonline.com) 및 **권한이 부여된 리디렉션 URI** 필드의 [https://login.microsoftonline.com/te/{directory}/oauth2/authresp](https://login.microsoftonline.com/te/{directory}/oauth2/authresp)를 입력합니다. 해당 필드는 **{directory}**을 디렉터리의 이름으로 교체합니다.(예: contosob2c.onmicrosoft.com) **만들기**를 클릭합니다.

    > [AZURE.NOTE]**{directory}** 값은 대/소문자를 구분합니다.

    ![G+ - 클라이언트 ID 만들기](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

8. **클라이언트 ID** 및 **클라이언트 암호** 값을 복사합니다. 디렉터리에서 Google+를 ID 공급자로 구성하려면 둘 모두가 필요합니다.

    > [AZURE.NOTE]**클라이언트 암호**는 중요한 보안 자격 증명입니다.

    ![G+ - 클라이언트 암호](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## 디렉터리에서 Google+를 ID 공급자로 구성

1. [Azure Preview 포털의 B2C 기능 블레이드로 이동합니다.](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)
2. B2C 기능 블레이드에서 **소셜 ID 공급자**를 클릭합니다.
3. 블레이드의 위쪽에서 **+추가**를 클릭합니다.
4. ID 공급자 구성에 친숙한 **이름**을 제공합니다. 예를 들어 "G+"를 입력합니다.
5. **ID 공급자 형식**을 클릭하고 **Google**을 선택한 다음 **확인**을 클릭합니다.
6. **이 ID 공급자 설정**을 클릭하고 이전에 만든 Google+ 응용 프로그램의 **클라이언트 ID** 및 **클라이언트 암호**를 입력합니다.
7. **확인** 및 **만들기**를 차례로 클릭하여 Google+ 구성을 저장합니다.

<!---HONumber=Sept15_HO3-->
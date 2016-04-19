<properties
	pageTitle="Azure Active Directory B2C 미리 보기: Google+ 구성 | Microsoft Azure"
	description="소비자에게 Azure Active Directory B2C를 사용하여 보안이 유지되는 응용 프로그램에서 Google+ 계정으로 등록 및 로그인을 제공합니다."
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/04/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C 미리 보기: 고객에게 Google+ 계정으로 등록 및 로그인 제공

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Google+ 응용 프로그램 만들기

Azure AD(Azure Active Directory) B2C에서 Google+를 ID 공급자로 사용하려면 Google+ 응용 프로그램을 만들고 올바른 매개 변수를 제공해야 합니다. 이렇게 하려면 Google+ 계정이 필요합니다. 계정이 없으면 [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)에서 만들 수 있습니다.

1. [Google 개발자 콘솔](https://console.developers.google.com/)로 이동하고 Google + 계정 자격 증명으로 로그인합니다.
2. **프로젝트 만들기**를 클릭하고 **프로젝트 이름**을 입력한 다음 **만들기**를 클릭합니다.

    ![Google+ - 시작](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![Google+ - 새 프로젝트](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. **API 관리자**를 클릭한 다음 왼쪽 탐색에서**자격 증명**을 클릭합니다.
4. **OAuth 동의 화면**을 위쪽 탭에서 클릭합니다.

    ![Google+ - 자격 증명](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. 유효한 **메일 주소**를 선택하거나 지정하고 **제품 이름**을 제공하며 **저장**을 클릭합니다.

    ![Google+ - OAuth 동의 화면](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. **새 자격 증명**을 클릭한 다음 **OAuth 클라이언트 ID**를 선택합니다.

    ![Google+ - OAuth 동의 화면](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)

7. **응용 프로그램 형식**에서 **웹 응용 프로그램**을 선택합니다.

    ![Google+ - OAuth 동의 화면](./media/active-directory-b2c-setup-goog-app/google-web-app.png)

8. 응용 프로그램에 대한 **이름**을 제공하고 **권한이 부여된 JavaScript 원본** 필드에 `https://login.microsoftonline.com`을 입력하고 **권한이 부여된 리디렉션 URI** 필드에 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`를 입력합니다. **{tenant}**를 사용자의 테넌트 이름(예: contosob2c.onmicrosoft.com)으로 바꿉니다. **{tenant}** 값은 대/소문자를 구분합니다. **만들기**를 클릭합니다.

    ![Google+ - 클라이언트 ID 만들기](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

9. **클라이언트 ID** 및 **클라이언트 암호** 값을 복사합니다. 테넌트에서 Google+를 ID 공급자로 구성하려면 둘 모두가 필요합니다. **클라이언트 암호**는 중요한 보안 자격 증명입니다.

    ![Google+ - 클라이언트 암호](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## 테넌트에서 Google+를 ID 공급자로 구성

1. 다음 단계에 따라 [Azure 포털의 B2C 기능 블레이드로 이동합니다](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. B2C 기능 블레이드에서 **ID 공급자**를 클릭합니다.
3. 블레이드의 위쪽에서 **+추가**를 클릭합니다.
4. ID 공급자 구성에 친숙한 **이름**을 제공합니다. 예를 들어 "G+"를 입력합니다.
5. **ID 공급자 형식**을 클릭하고 **Google**을 선택한 다음 **확인**을 클릭합니다.
6. **이 ID 공급자 설정**을 클릭하고 이전에 만든 Google+ 응용 프로그램의 클라이언트 ID 및 클라이언트 암호를 입력합니다.
7. **확인**을 클릭한 다음 **만들기**를 클릭하여 Google+ 구성을 저장합니다.

<!---HONumber=AcomDC_0406_2016-->
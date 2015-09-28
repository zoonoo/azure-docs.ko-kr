<properties
	pageTitle="Azure Active Directory B2C 미리 보기: Facebook 구성 | Microsoft Azure"
	description="소비자에게 Azure Active Directory B2C를 사용하여 보안이 유지되는 응용 프로그램에서 Facebook 계정으로 등록 및 로그인 제공"
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

# Azure Active Directory B2C 미리 보기: 고객에게 Facebook 계정으로 등록 및 로그인 제공

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Facebook 응용 프로그램 만들기

Azure Active Directory(AD) B2C에서 Facebook을 ID 공급자로 사용하려면 먼저 Facebook 응용 프로그램을 만들고 올바른 매개 변수를 제공해야 합니다. 다음을 수행하려면 Facebook 계정이 필요합니다. 계정이 없다면 [https://www.facebook.com/](https://www.facebook.com/)에서 얻을 수 있습니다.

1. [Facebook 개발자 웹 사이트](https://developers.facebook.com/)로 이동한 다음 Facebook 계정 자격 증명으로 로그인합니다.
2. 아직 수행하지 않은 경우 **앱**, **개발자로 등록**을 차례로 클릭하고 정책에 동의한 후 등록 단계를 따릅니다.
3. **앱** 및 **새 앱 추가**를 차례로 클릭합니다. 그런 다음 **웹 사이트**를 플랫폼으로 선택하고 **앱 ID 건너뛰기 및 만들기**를 클릭합니다.

    ![FB - 새 앱 추가](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)

    ![FB - 새 앱, 웹 사이트를 추가합니다.](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)

    ![FB - 앱 ID 만들기](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)

4. 폼에서 **표시 이름**을 제공하고 적절한 **범주**를 선택하며 **앱 ID 만들기**를 클릭합니다. 참고: Facebook 플랫폼 정책을 수용하고 온라인 보안 검사를 완료해야 합니다.

    ![FB - 앱 ID 만들기](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)

5. 왼쪽 탐색에서 **설정**을 클릭합니다. 유효한 **전자 메일 연락처**를 입력합니다.
6. **+플랫폼 추가**를 클릭한 다음 **웹 사이트**를 선택합니다.

    ![FB - 설정](./media/active-directory-b2c-setup-fb-app/fb-settings.png)

    ![FB - 설정](./media/active-directory-b2c-setup-fb-app/fb-website.png)

7. **사이트 URL** 필드의 [https://login.microsoftonline.com/](https://login.microsoftonline.com/)를 입력한 다음 **변경 내용 저장**을 클릭합니다.
8. **앱 ID**의 값을 복사합니다. **표시**를 클릭하고 **앱 암호**의 값을 복사합니다. 디렉터리에서 Facebook을 ID 공급자로 구성하려면 둘 모두가 필요합니다.

    > [AZURE.NOTE]**앱 암호**는 중요한 보안 자격 증명입니다.

    ![FB - 사이트 URL](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

9. 위쪽에서 **고급** 탭을 클릭하고 (**보안** 섹션의)**유효한 OAuth 리디렉션 URI** 필드에서 [https://login.microsoftonline.com/te/{directory}/oauth2/authresp](https://login.microsoftonline.com/te/{directory}/oauth2/authresp)을 입력합니다. 여기서 **{directory}**는 디렉터리의 이름으로 교체됩니다.(예: contosob2c.onmicrosoft.com) 페이지 아래쪽에 있는 **변경 내용 저장**을 클릭합니다.

    ![FB- OAuth 리디렉션 URI](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)

10. Facebook 응용 프로그램을 Azure AD B2C에서 사용할 수 있도록 하려면 공개적으로 사용할 수 있도록 해야 합니다. 이렇게 하려면 왼쪽 탐색 창에서 **상태 및 검토**를 클릭하고 **예** 페이지의 위쪽에 스위치를 설정합니다. **확인**을 클릭합니다.

    ![FB- OAuth 리디렉션 URI](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## 디렉터리에서 Facebook을 ID 공급자로 구성

1. [Azure Preview 포털의 B2C 기능 블레이드로 이동합니다.](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)
2. B2C 기능 블레이드에서 **소셜 ID 공급자**를 클릭합니다.
3. 블레이드의 위쪽에서 **+추가**를 클릭합니다.
4. ID 공급자 구성에 친숙한 **이름**을 제공합니다. 예를 들어 "FB"를 입력합니다.
5. **ID 공급자 형식**을 클릭하고 **Facebook**을 선택한 다음 **확인**을 클릭합니다.
6. **이 ID 공급자 설정**을 클릭하고 이전에 **클라이언트 ID** 및 **클라이언트 암호** 필드에서 각각 만든 Facebook 응용 프로그램의 **앱 ID** 및 **앱 암호**를 입력합니다.
7. **확인** 및 **만들기**를 차례로 클릭하여 Facebook 구성을 저장합니다.

<!---HONumber=Sept15_HO3-->
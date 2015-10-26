<properties
	pageTitle="Azure Active Directory B2C 미리 보기: Amazon 구성 | Microsoft Azure"
	description="소비자에게 Azure Active Directory B2C를 사용하여 보안이 유지되는 응용 프로그램에서 LinkedIn 계정으로 등록 및 로그인 제공"
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
	ms.date="10/08/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C 미리 보기: 고객에게 Amazon 계정으로 등록 및 로그인 제공

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Amazon 응용 프로그램을 만듭니다.

Azure Active Directory(AD) B2C에서 Amazon을 ID 공급자로 사용하려면 먼저 Amazon 응용 프로그램을 만들고 올바른 매개 변수를 제공해야 합니다. 다음을 수행하려면 Amazon 계정이 필요합니다. 계정이 없다면 [http://www.amazon.com/](http://www.amazon.com/)에서 얻을 수 있습니다.

1. [Amazon 개발자 센터](https://login.amazon.com/)로 이동하여 Amazon 계정 자격 증명으로 로그인합니다.
2. 이미 수행한 경우 **등록**을 클릭하고 개발자 등록 단계를 수행하며 정책을 수락합니다.
3. **새 응용 프로그램 등록**을 클릭합니다.

    ![Amazon - 새 앱](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)

4. 응용 프로그램 정보를 제공하고(**이름**, **설명** 및 **개인 정보 알림 URL**) **저장**을 클릭합니다.

    ![Amazon - 앱 등록](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)

5. **웹 설정** 섹션에서 **클라이언트 ID** 및 **클라이언트 암호** 값을 복사합니다.(**비밀 표시** 버튼을 클릭하여 이를 확인해야 합니다) 디렉터리에서 Amazon을 ID 공급자로 구성하려면 둘 모두가 필요합니다. 섹션 아래쪽에서 **편집**을 클릭합니다.

    > [AZURE.NOTE]**클라이언트 암호**는 중요한 보안 자격 증명입니다.

    ![Amazon - 클라이언트 암호](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)

6. **허용된 JavaScript origin** 필드의 [https://login.microsoftonline.com](https://login.microsoftonline.com) 및 **허용된 반환 URL** 필드의 [https://login.microsoftonline.com/te/{directory}/oauth2/authresp](https://login.microsoftonline.com/te/{directory}/oauth2/authresp)를 입력합니다. 해당 필드는 **{directory}**을 디렉터리의 이름으로 교체합니다.(예: contoso.onmicrosoft.com) **Save**를 클릭합니다.

    > [AZURE.NOTE]**{directory}** 값은 대/소문자를 구분합니다.

    ![Amazon - URL](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## 디렉터리에서 Amazon을 ID 공급자로 구성

1. [Azure Preview 포털의 B2C 기능 블레이드로 이동합니다.](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)
2. B2C 기능 블레이드에서 **ID 공급자**를 클릭합니다.
3. 블레이드의 위쪽에서 **+추가**를 클릭합니다.
4. ID 공급자 구성에 친숙한 **이름**을 제공합니다. 예를 들어 "Amzn"을 입력합니다.
5. **ID 공급자 형식**을 클릭하고 **Amazon**을 선택한 다음 **확인**을 클릭합니다.
6. **이 ID 공급자 설정**을 클릭하고 이전에 만든 Amazon 응용 프로그램의 **클라이언트 ID** 및 **클라이언트 암호**를 입력합니다.
7. **확인** 및 **만들기**를 차례로 클릭하여 Amazon 구성을 저장합니다.

<!---HONumber=Oct15_HO3-->
<properties
	pageTitle="Azure Active Directory B2C 미리 보기: Microsoft 계정 구성 | Microsoft Azure"
	description="소비자는 Azure Active Directory B2C에 의해 보호되는 응용 프로그램에서 Microsoft 계정으로 등록하고 로그인할 수 있습니다."
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

# Azure Active Directory B2C 미리 보기: 고객에게 Microsoft 계정으로 등록 및 로그인 제공

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Microsoft 계정 응용 프로그램 만들기

Azure AD(Active Directory) B2C에서 Microsoft 계정을 ID 공급자로 사용하려면 먼저 Microsoft 계정 응용 프로그램을 만들고 올바른 매개 변수를 제공해야 합니다. 이렇게 하려면 Microsoft 계정이 필요합니다. 계정이 없는 경우 [https://www.live.com/](https://www.live.com/)에서 가져올 수 있습니다.

1. [Microsoft 계정 개발자 센터](https://account.live.com/developers/applications)로 이동하고 Microsoft 계정 자격 증명을 사용하여 로그인합니다.
2. **응용 프로그램 만들기**를 클릭합니다.

    ![Microsoft 계정 - 새 앱 추가](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)

3. **응용 프로그램 이름**을 입력하고 **동의함**을 클릭합니다. Microsoft 서비스 사용 약관에 동의해야 합니다.

    ![Microsoft 계정 - 앱 이름](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)

4. 왼쪽 탐색에서 **API 설정**을 클릭합니다. 유효한 **전자 메일 연락처**를 입력합니다.

    ![Microsoft 계정 - API 설정](./media/active-directory-b2c-setup-msa-app/msa-api-settings.png)

5. **URL 리디렉션** 필드에 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`를 입력합니다. **{tenant}**를 사용자의 테넌트 이름(예: contosob2c.onmicrosoft.com)으로 바꿉니다. 페이지 맨 아래에서 **저장**을 클릭합니다.

    ![Microsoft 계정 - URL 리디렉션](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)

6. 왼쪽 탐색에서 **앱 설정**을 클릭합니다. **클라이언트 ID** 및 **클라이언트 암호** 값을 복사합니다. 테넌트에서 Microsoft 계정을 ID 공급자로 구성하려면 둘 다 필요합니다. **클라이언트 암호**는 중요한 보안 자격 증명입니다.

    ![Microsoft 계정 - 클라이언트 암호](./media/active-directory-b2c-setup-msa-app/msa-client-secret.png)

## 테넌트에서 Microsoft 계정을 ID 공급자로 구성

1. 다음 단계에 따라 [Azure 포털의 B2C 기능 블레이드로 이동합니다](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. B2C 기능 블레이드에서 **ID 공급자**를 클릭합니다.
3. 블레이드의 위쪽에서 **+추가**를 클릭합니다.
4. ID 공급자 구성에 친숙한 **이름**을 제공합니다. 예를 들어 "MSA"를 입력합니다.
5. **ID 공급자 형식**을 클릭하고 **Microsoft 계정**을 선택하고 **확인**을 클릭합니다.
6. **이 ID 공급자 설정**을 클릭하고 이전에 만든 Microsoft 계정 응용 프로그램의 클라이언트 ID 및 클라이언트 암호를 입력합니다.
7. **확인**을 클릭한 다음 **만들기**를 클릭하여 Microsoft 계정 구성을 저장합니다.

<!---HONumber=AcomDC_0406_2016-->
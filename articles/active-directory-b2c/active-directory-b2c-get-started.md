<properties
	pageTitle="Azure Active Directory B2C 미리 보기: Azure Active Directory B2C 테넌트 만들기 | Microsoft Azure"
	description="Azure Active Directory B2C 테넌트를 만드는 방법에 대한 항목"
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
	ms.date="09/28/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C 미리 보기: Azure AD B2C 테넌트 만드는 방법

Azure Active Directory(AD) B2C를 사용하여 시작하려면 아래에 설명한 3단계를 수행합니다.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 1단계: Azure 구독에 등록

이미 Azure 구독이 있으면 다음 단계로 이동합니다. 그렇지 않은 경우 [Azure 구독](sign-up-organization.md)에 등록하고 Azure AD B2C에 액세스합니다.

> [AZURE.NOTE]Azure AD B2C Preview는 현재 무료로 사용하지만 제한이 있습니다(테넌트당 사용자 최대 5만명). Azure 구독은 [Azure 클래식 포털](http://manage.windowsazure.com/)에 액세스하는 데 필요합니다.

## 2단계: Azure AD B2C 테넌트 만들기

다음 단계에 따라 새 Azure AD B2C 테넌트를 만듭니다. 현재 B2C 기능은 기존 디렉터리가 있는 경우 해당 디렉터리에 설정할 수 없습니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com/)에 구독 관리자로 로그인합니다. 이것은 동일한 작업이거나 학교 계정 또는 Azure에 등록 하는데 사용한 동일한 Microsoft 계정입니다.
2. **새로 만들기** > **앱 서비스** > **Active Directory** > **디렉터리 ** > **사용자 지정 만들기**를 클릭합니다.

    ![테넌트 만들기](./media/active-directory-b2c-get-started/new-directory.png)

3. 테넌트에 대해 **이름**, **도메인 이름** 및 **국가 또는 지역**을 선택합니다.
4. "**B2C 디렉터리입니다**"라는 옵션을 확인합니다.
5. 확인 표시를 클릭하여 동작을 완료합니다.

    ![B2C 테넌트 만들기](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. 이제 테넌트가 만들어지고 Active Directory 확장에 표시됩니다. 또한 사용자는 테넌트의 전역 관리자가 되었습니다. 필요에 따라 다른 전역 관리자를 추가할 수 있습니다.

    > [AZURE.IMPORTANT]해당 테넌트를 만드는 데 최대 2분이 걸릴 수 있습니다. 테넌트를 만드는 동안 문제에 직면하는 경우 지침은 이 [문서](active-directory-b2c-support-create-directory.md)를 확인합니다.

## 3단계: Azure 포털의 B2C 기능 블레이드로 이동

1. 왼쪽의 탐색 모음에서 Active Directory 확장으로 이동합니다.
2. **디렉터리** 탭에서 테넌트를 찾고 클릭합니다.
3. **구성** 탭을 클릭합니다.
4. **B2C 관리** 섹션에서 **B2C 설정 관리** 링크를 클릭합니다.

    ![B2C 테넌트 만들기](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

4. B2C 기능 블레이드를 사용한 Azure 포털은 새 브라우저 탭 또는 창에서 열립니다.

    > [AZURE.IMPORTANT]이 페이지가 제대로 로드하지 않는다고 알려진 문제가 있습니다(소수의 테넌트에 해당). 브라우저를 새로 고치면 해결됩니다. 그렇지 않은 경우 고객 지원으로 문의하세요.

5. 쉽게 액세스할 수 있도록 이 블레이드(오른쪽 위 구석 확인)를 시작 보드에 고정합니다.

    ![B2C 기능 블레이드](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE][Azure 클래식 포털](https://manage.windowsazure.com/)에서 테넌트의 사용자와 그룹, 셀프 서비스 암호 재설정 구성 및 회사 브랜딩 기능을 관리할 수 있습니다.

## 다음 단계

[Azure AD B2C로 응용 프로그램 등록 및 빠른 시작 응용 프로그램 빌드](active-directory-b2c-app-registration.md)로 이동합니다.

<!---HONumber=AcomDC_0107_2016-->
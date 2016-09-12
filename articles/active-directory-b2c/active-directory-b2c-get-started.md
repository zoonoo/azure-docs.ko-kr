<properties
	pageTitle="Azure Active Directory B2C: Azure Active Directory B2C 테넌트 만들기 | Microsoft Azure"
	description="Azure Active Directory B2C 테넌트를 만드는 방법에 대한 항목"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.topic="article"
    ms.devlang="na"
	ms.date="08/30/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C: Azure AD B2C 테넌트 만들기

Microsoft Azure Active Directory(Azure AD) B2C를 사용하려면 이 문서에 요약한 세 단계를 따릅니다.

## 1단계: Azure 구독에 등록

Azure 구독이 이미 있는 경우 이 단계를 건너뜁니다. 그렇지 않은 경우 [Azure 구독](../active-directory/sign-up-organization.md)에 등록하고 Azure AD B2C에 액세스합니다.

## 2단계: Azure AD B2C 테넌트 만들기

다음 단계에 따라 새 Azure AD B2C 테넌트를 만듭니다. 현재 B2C 기능은 기존 테넌트에 설정할 수 없습니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com/)에 구독 관리자로 로그인합니다. 이는 동일한 직장이나 학교 계정 또는 Azure에 등록하는 데 사용한 동일한 Microsoft 계정입니다.
2. **새로 만들기** > **앱 서비스** > **Active Directory** > **디렉터리** > **사용자 지정 만들기**를 클릭합니다.

    ![테넌트 만들기 시작 스크린샷](./media/active-directory-b2c-get-started/new-directory.png)

3. 테넌트에 대해 **이름**, **도메인 이름** 및 **국가 또는 지역**을 선택합니다.
4. **B2C 디렉터리입니다**라는 옵션을 확인합니다.
5. 확인 표시를 클릭하여 동작을 완료합니다.

    ![B2C 디렉터리 만들기 확인 표시 스크린샷](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. 이제 테넌트가 만들어지고 Active Directory 확장에 표시됩니다. 또한 사용자는 테넌트의 전역 관리자가 되었습니다. 필요에 따라 다른 전역 관리자를 추가할 수 있습니다.

    > [AZURE.IMPORTANT]
	프로덕션 앱에 대해 B2C 테넌트를 사용하려는 경우 [프로덕션 규모와 B2C 테넌트 미리 보기 비교](active-directory-b2c-reference-tenant-type.md)의 문서를 참조하세요. 기존 B2C 테넌트를 삭제하고 동일한 도메인 이름으로 다시 만들어야 하는 경우 알려진 문제가 발생합니다. 다른 도메인 이름으로 B2C 테넌트를 만들어야 합니다.

## 3단계: Azure 포털의 B2C 기능 블레이드로 이동

1. 왼쪽의 탐색 모음에서 Active Directory 확장으로 이동합니다.
2. **디렉터리** 탭에서 테넌트를 찾고 클릭합니다.
3. **구성** 탭을 클릭합니다.
4. **B2C 관리** 섹션에서 **B2C 설정 관리** 링크를 클릭합니다.

    ![B2C를 위한 디렉터리 구성 스크린샷](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

5. 새 브라우저 탭 또는 창에 Azure 포털이 열리고 B2C 기능 블레이드가 표시됩니다.

    > [AZURE.IMPORTANT]
    테넌트가 Azure 포털에 액세스하는 데 최대 2-3분이 걸릴 수 있습니다. 시간이 지난 후에 이 단계를 다시 시도하면 이 문제가 해결됩니다. 그렇지 않은 경우 고객 지원으로 문의하세요.

6. 이 블레이드를 쉽게 액세스할 수 있도록 시작 보드에 고정합니다. (고정 도구는 기능 블레이드의 오른쪽 위 모서리에 빨간색으로 표시됩니다.)

    ![B2C 기능 블레이드 스크린샷](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]
    [Azure 클래식 포털](https://manage.windowsazure.com/)에서 테넌트의 사용자와 그룹, 셀프 서비스 암호 재설정 구성 및 회사 브랜딩 기능을 관리할 수 있습니다.

## Azure 포털의 B2C 기능 블레이드에 쉽게 액세스합니다.

검색 가능성을 개선하기 위해 Azure 포털에서 B2C 기능 블레이드에 바로 가기를 추가 했습니다.

1. B2C 테넌트의 전역 관리자로 Azure 포털에 로그인합니다. 이미 다른 테넌트로 로그인했다면 (오른쪽 위 모서리에서) 테넌트를 전환합니다.
2. 왼쪽 탐색 창에서 **찾아보기**를 클릭합니다.
3. B2C 기능 블레이드에 액세스하려면 **Azure AD B2C**를 클릭합니다.

    ![B2C 기능 블레이드로 이동 스크린샷](./media/active-directory-b2c-get-started/b2c-browse.png)

## 다음 단계

[Azure Active Directory B2C: 응용 프로그램 등록](active-directory-b2c-app-registration.md)을 읽어서 Azure AD B2C에 응용 프로그램을 등록하고 빠른 시작 응용 프로그램을 빌드하는 방법을 알아봅니다.

<!---HONumber=AcomDC_0831_2016-->
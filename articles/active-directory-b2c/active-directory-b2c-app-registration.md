<properties
	pageTitle="Azure Active Directory B2C 미리 보기: 응용 프로그램 등록 | Microsoft Azure"
	description="Azure Active Directory B2C로 응용 프로그램 등록하는 방법"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="mbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/22/2015"
	ms.author="swkrish"/>


# Azure Active Directory B2C 미리 보기: 응용 프로그램 등록

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 필수 요소

소비자 등록 및 로그인을 수락하는 응용 프로그램을 만들려면 먼저 Azure Active Directory B2C 테넌트를 사용하여 해당 응용 프로그램을 등록해야 합니다. [Azure AD B2C 테넌트 만들기](active-directory-b2c-get-started.md)에 요약한 단계를 사용하여 자신의 테넌트를 가져옵니다. 해당 문서의 모든 단계를 수행한 후 시작 보드에 고정된 B2C 기능 블레이드가 있을 것입니다.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## B2C 기능 블레이드로 이동

Azure 포털 또는 Azure 클래식 포털에서 B2C 기능 블레이드로 이동할 수 있습니다.

### 1\. Azure 포털에서 액세스

시작 보드에 고정된 B2C 기능 블레이드가 있다면 [Azure 포털](https://portal.azure.com/)에 B2C 테넌트의 전역 관리자 권한으로 로그인하는 즉시 해당 블레이드가 나타납니다.

또한 [Azure 포털](https://portal.azure.com/)의 왼쪽 탐색 창에서 **찾아보기**를 클릭한 다음 **Azure AD B2C**를 클릭하여 블레이드에 액세스할 수도 있습니다.

또한 [https://portal.azure.com/{tenant}.onmicrosoft.com/?#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/](https://portal.azure.com/{tenant}.onmicrosoft.com/?#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/)로 이동하여 블레이드에 직접 액세스할 수 있습니다. 여기서 **{tenant}**는 테넌트를 만들 때 사용된 이름(예: contosob2c)으로 바꿉니다. 나중에 사용할 경우를 위해 이 링크에 책갈피를 설정할 수 있습니다.

    > [AZURE.IMPORTANT]
    You need to be a Global Administrator of the B2C tenant to be able to access the B2C features blade. A Global Administrator from any other tenant or a user from any tenant cannot access it.

### 2\. Azure 클래식 포털에서 액세스

[Azure 클래식 포털](https://manage.windowsazure.com/)에 구독 관리자로 로그인합니다. (이것은 동일한 작업이거나 학교 계정 또는 Azure에 등록 하는데 사용한 동일한 Microsoft 계정입니다.) 왼쪽에서 Active Directory 확장으로 이동하고 B2C 테넌트를 클릭합니다. **빠른 시작** 탭(열린 첫 번째 탭)의 **관리**에서 **B2C 설정 관리**를 클릭합니다. 새 브라우저 창 또는 탭에서 B2C 기능 블레이드를 엽니다.

**구성** 탭의 **B2C 관리** 섹션에서 **B2C 설정 관리** 링크를 찾을 수도 있습니다.

## 응용 프로그램 등록

1. Azure 포털의 B2C 기능 블레이드에서 **응용 프로그램**을 클릭합니다.
2. 블레이드의 위쪽에서 **+추가**를 클릭합니다.
3. 소비자에게 응용 프로그램을 설명하는 응용 프로그램에 대한**이름**을 입력합니다. 예를 들어 "Contoso B2C 앱"을 입력할 수 있습니다.
4. 웹 기반 응용 프로그램을 작성하는 경우 **웹앱/웹 API 포함** 스위치를 **예**로 전환합니다. **회신 URL**은 Azure AD B2C에서 사용자 응용 프로그램이 요청한 토큰을 반환하는 끝점입니다. 예를 들어 `https://localhost:44321/`을 입력합니다. 응용 프로그램이 보안을 유지해야 하는 서버 쪽 구성 요소(API)를 포함하는 경우 **키 생성** 단추를 클릭하여 **응용 프로그램 암호**도 만들고 복사하는 것이 좋습니다.

    > [AZURE.NOTE]
    **응용 프로그램 암호**는 중요한 보안 자격 증명입니다.

5. 모바일 응용 프로그램을 작성하는 경우 **네이티브 클라이언트 포함** 스위치를 **예**로 전환합니다. 자동으로 만들어진 기본 **리디렉션 URI**을 적어둡니다.
6. **만들기**를 클릭하여 응용 프로그램을 등록합니다.
7. 방금 만든 응용 프로그램을 클릭하고, 나중에 코드에서 사용할 전역적으로 고유한 **응용 프로그램 ID**를 적어둡니다.

## 빠른 시작 응용 프로그램 빌드하기

Azure AD B2C로 등록된 응용 프로그램이 있다면 작동할 빠른 시작 자습서 중 하나를 완료할 수 있습니다. 몇가지 권장 사항입니다.

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

<!---HONumber=AcomDC_0224_2016-->
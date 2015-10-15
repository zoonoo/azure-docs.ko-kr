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
	ms.date="09/22/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C 미리 보기: 응용 프로그램 등록하는 방법

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 필수 구성 요소

소비자 등록 및 로그인을 수락하는 응용 프로그램을 만들려면 먼저 Azure Active Directory B2C 디렉터리를 사용하여 등록해야 합니다. [여기](active-directory-b2c-get-started.md)서 설명한 단계를 사용하여 고유의 디렉터리를 가져옵니다. 해당 문서의 모든 단계를 수행하면 시작 보드에 고정된 B2C 기능 블레이드가 있어야 합니다.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## B2C 기능 블레이드로 이동

다음 두 방법 중 하나를 사용하여 B2C 기능 블레이드로 이동할 수 있습니다.

### 1. Azure Preview 포털에서 직접

시작 보드에 고정된 B2C 기능 블레이드가 있다면 [Azure Preview 포털](https://portal.azure.com/)에 B2C 디렉터리의 전역 관리자 권한으로 로그인하는 즉시 나타납니다.

[https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft\_AAD\_B2CAdmin=true#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com](https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{directory}.onmicrosoft.com)(여기서 **{directory}**는 디렉터리를 만들 때 사용된 이름(예: contosob2c)으로 대체됨)으로 이동한 다음 B2C 디렉터리의 전역 관리자 권한으로 로그인하여 B2C 기능 블레이드에 직접 액세스할 수도 있습니다. 나중에 참조할 경우를 위해 이 링크에 책갈피를 설정할 수 있습니다.

### 2. Azure 포털을 통해 액세스할 수 있습니다.

[Azure 포털](https://manage.windowsazure.com/)에 구독 관리자 권한(Azure에 등록하는 데 사용한 것과 동일한 회사 또는 학교 계정이거나 동일한 Microsoft 계정임)으로 로그인합니다. 왼쪽에서 Active Directory 확장으로 이동하고 B2C 디럭터리를 클릭합니다. **빠른 시작** 탭(열린 첫 번째 탭)의 **관리**에서 **B2C 설정 관리**를 클릭합니다. 새 브라우저 창 또는 탭에서 B2C 기능 블레이드를 엽니다.

**구성** 탭의 **B2C 관리** 섹션에서 **B2C 설정 관리** 링크를 찾을 수도 있습니다.

## 응용 프로그램 등록

1. Azure Preview 포털의 B2C 기능 블레이드에서 **응용 프로그램**을 클릭합니다.
2. 블레이드의 위쪽에서 **+추가**를 클릭합니다.
3. 응용 프로그램 **이름**은 고객에게 응용 프로그램을 설명합니다. 예를 들어 "Contoso B2C 앱"을 입력합니다.
4. 웹 기반 응용 프로그램을 작성하는 경우 **웹앱/웹 API 포함** 스위치를 **예**로 전환합니다. **회신 URL**은 Azure AD B2C에서 사용자 응용 프로그램이 요청한 토큰을 반환하는 끝점입니다. 예를 들어 `https://localhost:44321/`을 입력합니다. 응용 프로그램이 보안을 유지해야 하는 서버 쪽 구성 요소(API)를 포함하는 경우 **키 생성** 단추를 클릭하여 **응용 프로그램 암호**도 만들고 복사하는 것이 좋습니다.

    > [AZURE.NOTE]
    **응용 프로그램 암호**는 중요한 보안 자격 증명입니다.

5. 모바일 응용 프로그램을 작성하는 경우 **네이티브 클라이언트 포함** 스위치를 **예**로 전환합니다. 자동으로 만들어진 기본 **리디렉션 URI**을 적어둡니다.
6. **만들기**를 클릭하여 응용 프로그램을 등록합니다.
7. 방금 만든 응용 프로그램을 클릭하고, 나중에 코드에서 사용할 전역적으로 고유한 **응용 프로그램 ID**를 적어둡니다.

## 빠른 시작 응용 프로그램 빌드하기

Azure AD B2C로 등록된 응용 프로그램이 있다면 작동할 빠른 시작 자습서 중 하나를 완료할 수 있습니다. 몇가지 권장 사항입니다.

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

<!----HONumber=Oct15_HO1-->
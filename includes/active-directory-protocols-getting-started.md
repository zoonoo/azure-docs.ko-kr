<properties
	pageTitle="Azure AD .NET 프로토콜 개요 | Microsoft Azure"
	description="Azure AD를 사용하여 테넌트에서 웹 응용 프로그램 및 Web API에 대한 액세스 권한을 부여하기 위해 HTTP 메시지를 사용하는 방법입니다."
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="priyamo"/>

<!--TODO: Introduction -->

## AD 테넌트에 응용 프로그램 등록

먼저, 응용 프로그램을 Active Directory 테넌트에 등록해야 합니다. 그러면 응용 프로그램에 대한 클라이언트 ID가 제공되며 토큰을 수신하는 데 사용할 수 있습니다.

- Azure 관리 포털에 로그인합니다.

- 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

- 응용 프로그램을 등록할 테넌트를 선택합니다.

- **응용 프로그램** 탭을 클릭하고 아래쪽 서랍에서 **추가**를 클릭합니다.

- 프롬프트에 따라 새 응용 프로그램을 만듭니다. 이 자습서에서는 웹 응용 프로그램이든지 네이티브 응용 프로그램이든지 상관 없지만 웹 응용 프로그램 또는 네이티브 응용 프로그램에 대한 특정 예제가 필요한 경우 [여기](../articles/active-directory/active-directory-developers-guide.md)에서 빠른 시작을 확인하세요.

- 웹 응용 프로그램의 경우 사용자가 로그인할 수 있는 앱의 기본 URL인 **로그온 URL**을 제공합니다(예: `http://localhost:12345`). **앱 ID URI**는 응용 프로그램의 고유 식별자입니다. 규칙은 `https://<tenant-domain>/<app-name>`(예: `https://contoso.onmicrosoft.com/my-first-aad-app`)을 사용하는 것입니다.

- 네이티브 응용 프로그램의 경우 Azure AD에서 토큰 응답을 반환하는 데 사용할 **리디렉션 URI**를 제공합니다. 응용 프로그램에 고유하게 해당되는 값을 입력합니다(예: `http://MyFirstAADApp`).

- 등록이 끝나면 AAD는 응용 프로그램에 고유한 클라이언트 식별자를 할당합니다. 이 값은 다음 섹션에서 필요하므로 응용 프로그램의 **구성** 탭에서 복사해둡니다.

<!---HONumber=AcomDC_0601_2016-->
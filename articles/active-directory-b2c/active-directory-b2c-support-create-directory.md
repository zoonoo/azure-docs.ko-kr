<properties
	pageTitle="Azure Active Directory: 테넌트 지원 항목 만들기 | Microsoft Azure"
	description="Azure Active Directory 테넌트 또는 Azure Active Directory B2C 테넌트 만들기 - 문제 및 해결 방법"
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
	ms.date="09/24/2015"
	ms.author="swkrish"/>

# Azure AD 테넌트 또는 Azure AD B2C 테넌트 만들기 - 문제 및 해결 방법

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Azure AD 테넌트 만들기

처음에 Azure AD 테넌트를 만들 수 없으면 다시 시도하세요. 문제가 지속되면 지원에 문의하세요.

## Azure AD B2C 테넌트(미리 보기) 만들기

[Azure AD B2C 테넌트를 만드는](active-directory-b2c-get-started.md) 동안 발생할 수 있다고 알려진 문제가 있습니다.

- Azure AD B2C 테넌트가 테넌트 목록에 표시되지 않으면 다시 시도하세요.
- Azure AD B2C 테넌트가 테넌트의 목록에 표시되었지만 다음과 같은 오류 메시지가 표시되는 경우가 있습니다. "B2C 테넌트 'contosob2c' 만들기를 완료하지 못했습니다. 자세한 지침은 이 [링크](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409)를 방문하세요." 그런 다음, 다음 중 하나를 수행합니다.
    - [https://portal.azure.com/{tenant}.onmicrosoft.com/?Microsoft\_AAD\_B2CAdmin=true#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/{tenant}.onmicrosoft.com](https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{tenant}.onmicrosoft.com) 링크를 사용(**{tenant}**는 테넌트를 만들 때 사용된 이름으로 대체(예: contosob2c))하여 Azure Preview 포털의 B2C 기능 블레이드로 이동하고 전역 관리자로 로그인합니다. 블레이드의 위쪽에서 **B2C 기능 사용**을 클릭하고 **확인**을 클릭합니다. Azure AD B2C 테넌트를 사용할 수 있어야 합니다!
	- 방금 만든 테넌트를 삭제하고 다시 시도합니다.
- 위 해결 방법이 작동하지 않으면 지원에 문의합니다. Azure AD B2C에 대한 지원 요청을 작성하는 방법은 [이 문서](active-directory-b2c-support.md)를 읽습니다.

<!---HONumber=Oct15_HO3-->
<properties
	pageTitle="그룹에 대해 동적 멤버 자격을 구성하는 간단한 규칙 만들기| Microsoft Azure"
	description="그룹에 대해 동적 멤버 자격을 구성하는 간단한 규칙을 만드는 방법을 설명합니다."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="curtand"/>


# 그룹에 대해 동적 멤버 자격을 구성하는 간단한 규칙 만들기

특정 그룹에 대해 동적 멤버 자격을 사용하려면 다음 단계를 수행하세요.

1. Azure 포털의 **그룹** 탭 아래에서 편집할 그룹을 선택하고 해당 그룹의 **구성** 탭에서 **동적 멤버 자격 사용** 스위치를 **예**로 설정합니다.

2. 이제 이 그룹에 대해 그룹의 동적 멤버 자격 작동 방식을 제어할 간단한 단일 규칙을 설정할 수 있습니다. **사용자 추가 위치** 옵션이 선택되어 있는지 확인하고 목록에서 사용자 속성(예: department, jobTitle 등)을 선택합니다.

3. 그런 다음, 조건(같지 않음, 같음, 다음으로 시작 안 함, 다음으로 시작, 포함 안 함, 포함, 일치하지 않음, 일치)을 선택하고 선택한 사용자 속성의 값을 지정합니다. 예를 들어 그룹이 SaaS 응용 프로그램에 할당되어 있고 **사용자 추가 위치**가 (-eq)Sales Rep과 같은 jobTitle로 설정된 규칙을 설정하여 이 그룹에 대해 동적 멤버 자격을 사용하도록 설정한 경우 Azure AD 디렉터리 내에서 직책이 Sales Rep으로 설정된 모든 사용자가 이 SaaS 응용 프로그램에 액세스할 수 있습니다. 4. 보안 그룹 또는 Office 그룹에서 동적 멤버 자격에 대한 규칙을 설정할 수 있습니다.

이러한 문서는 Azure Active Directory에 추가 정보를 제공합니다.

* [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](active-directory-manage-groups.md)
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
* [Azure Active Directory란?](active-directory-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0211_2016-->
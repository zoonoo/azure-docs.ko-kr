<properties
	pageTitle="Azure Active Directory의 전용 그룹 | Microsoft Azure"
	description="전용된 그룹의 Azure Active Directory를 사용한 작업 방식 및 작성된 방법의 개요입니다."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""
	/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/17/2015"
	ms.author="curtand"/>

# Azure Active Directory의 전용 그룹

Azure Active Directory에서는 자동으로 전용 그룹이 생성되며 전용 그룹에 대한 그룹 멤버 자격도 자동입니다. Azure 포털이나 Windows PowerShell cmdlet을 통해 또는 프로그래밍 방식으로 전용 그룹에 멤버를 추가하거나 전용 그룹에서 멤버를 제거할 수 없습니다. 전용 그룹을 사용하려면 Azure 포털의 구성 탭에서 **전용 그룹 사용 스위치를 예**로 설정합니다.

전용 그룹 사용 스위치가 **예**로 설정되면 **“모든 사용자” 그룹 사용** 스위치를 **예**로 설정하여 모든 사용자 전용 그룹을 자동으로 생성할 디렉터리를 추가로 사용하도록 설정할 수 있습니다. 그런 다음 **“모든 사용자” 그룹에 표시 이름** 필드에 입력하여 이 전용 그룹의 이름을 편집할 수도 있습니다.

디렉터리의 모든 사용자에게 동일한 사용 권한을 할당하려는 경우 모든 사용자 전용 그룹이 유용할 수 있습니다. 예를 들어 모든 사용자 전용 그룹에 대해 SaaS 응용 프로그램 액세스 권한을 할당하여 디렉터리의 모든 사용자에게 이 응용 프로그램에 대한 액세스를 부여할 수 있습니다.

이러한 문서는 Azure Active Directory에 추가 정보를 제공합니다.

* [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](active-directory-manage-groups.md)
* [Azure Active Directory란?](active-directory-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)

<!---HONumber=Nov15_HO4-->
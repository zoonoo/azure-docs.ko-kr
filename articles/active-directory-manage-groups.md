<properties 
	pageTitle="Azure AD에서 그룹 관리" 
	description="Azure AD에서 그룹을 관리하는 방법을 설명하는 항목입니다." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="Justinha"/>

# Azure AD에서 그룹 관리

그룹은 단일 단위로 관리할 수 있는 사용자 및 그룹의 컬렉션입니다. 특정 그룹에 속해 있는 사용자 및 그룹은 그룹 멤버라고 합니다. 그룹을 사용하면 계정별로 사용 권한을 할당하지 않고 한 번에 많은 계정에 일련의 사용 권한을 할당하여 관리를 간소화할 수 있습니다.

지금 현재 Azure AD에서는 보안 그룹만 만들 수 있습니다.

동일한 응용 프로그램에 많은 사용자를 할당해야 할 때 그룹을 사용하여 편리하게 응용 프로그램에 대한 액세스 권한을 사용자에게 할당할 수 있습니다. 또한 리소스에 대한 액세스를 제어하는 기타 온라인 서비스(예: SharePoint Online)의 액세스 관리를 구성할 때도 그룹을 사용할 수 있습니다.

디렉터리 동기화를 구성한 경우에는 로컬 온-프레미스 Windows Server Active Directory에서 동기화된 그룹이 표시되며 이 그룹의 ‘원본 위치' 속성 값은 ‘로컬 Active Directory'입니다. 이러한 그룹은 로컬 Active Directory에서 계속 관리해야 하며 Azure 관리 포털에서는 관리하거나 삭제할 수 없습니다.

Office 365가 있는 경우에는 Office 365 내의 Exchange 관리 센터에서 만들어져 관리되는 메일 사용이 가능한 보안 그룹 및 메일 그룹이 표시됩니다. 이러한 그룹의 '원본 위치' 속성 값은 'Office 365'이고 Exchange 관리 센터에서 계속 관리되어야 합니다.

액세스 패널을 통해 통합 그룹을 만들 수도 있습니다. 그룹 관리의 구성 탭에서 **사용자가 O365 그룹을 만들 수 있습니다** 위젯을 **예**로 설정하세요. 액세스 패널 또는 Office 365에서 생성된 Office 365 통합 그룹이 있는 경우 이러한 그룹의 '원본 위치' 속성은 'Azure Active Directory'로 설정되며 액세스 패널을 통해 관리할 수 있습니다.

사용자에 대해 셀프 서비스 그룹 관리를 설정한 경우(자세한 내용은 Azure AD에서 사용자를 위한 셀프 서비스 그룹 관리 참조) 테넌트 관리자로 Azure 관리 포털을 통해 이러한 그룹을 관리할 수도 있습니다. 그룹 멤버 및 그룹 소유자를 추가 및 제거할 수 있고, 그룹 속성을 편집할 수 있으며, 그룹 내에서 수행된 작업, 작업을 수행한 사람 및 시간을 표시하는 그룹의 기록 작업 보고서를 볼 수 있습니다.

> [AZURE.NOTE]응용 프로그램에 그룹을 할당하려면 Azure AD Premium을 사용해야 합니다. Azure AD Premium이 있는 경우에는 Azure AD와 통합되는 SaaS 응용 프로그램에 대한 액세스 권한을 할당하는 데도 그룹을 사용할 수 있습니다. 자세한 내용은 Azure AD에서 SaaS 응용 프로그램에 대한 액세스 권한을 그룹에 할당하기를 참조하세요.

## 다음 단계

- [Azure AD 관리](active-directory-administer.md)
- [Azure AD에서 사용자 만들기 또는 편집](active-directory-create-users.md)
- [Azure AD에서 암호 관리](active-directory-manage-passwords.md)

<!---HONumber=58-->
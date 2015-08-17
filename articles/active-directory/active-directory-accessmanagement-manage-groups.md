<properties

	pageTitle="Managing security groups in Azure Active Directory | Microsoft Azure"
	description="Covers how to sign up for Azure and first steps you can try with Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="femila"/>


#Azure Active Directory의 보안 그룹 관리


##보안 그룹을 만들고 관리하는 방법

**Azure 관리 포털에서 그룹을 만들려면**

1. 관리 포털에서 Active Directory를 클릭한 다음 조직의 디렉터리의 이름을 클릭합니다.
2. **그룹** 탭을 클릭합니다.
3. 그룹 페이지에서 **그룹 추가**를 클릭합니다.
4. **그룹 추가** 창에서 그룹의 이름 및 설명을 지정합니다.
5. 이 작업은 조직에서 구독한 서비스에 따라 Office 365 계정 포털, Windows Intune 계정 포털 또는 Azure 관리 포털을 사용하여 수행할 수 있습니다. 포털을 사용하여 Azure Active Directory를 관리하는 방법에 대한 자세한 내용은 Azure AD 디렉터리 관리를 참조하세요.

## 보안 그룹에서 사용자를 할당하거나 제거하는 방법

**Azure 관리 포털에서 그룹에 멤버를 추가하려면**

1. 관리 포털에서 Active Directory를 클릭한 다음 조직의 디렉터리의 이름을 클릭합니다.
2. **그룹** 탭을 클릭합니다.
3. **그룹** 페이지에서 멤버를 추가할 그룹의 이름을 클릭합니다. 기본적으로 선택된 그룹의 **멤버** 탭이 표시됩니다.
4. 해당 그룹의 페이지에서 **멤버 추가**를 클릭합니다.
5. **멤버 추가** 페이지에서 이 그룹의 멤버로 추가할 사용자 또는 그룹의 이름을 클릭하고 이 이름이 선택 항목 창에 추가되었는지 확인합니다.


**Azure 관리 포털을 통해 그룹에서 멤버를 제거하려면**

1. 관리 포털에서 Active Directory를 클릭한 다음 조직의 디렉터리의 이름을 클릭합니다.
2. **그룹** 탭을 클릭합니다.
3. 그룹 페이지에서 멤버를 제거할 그룹의 이름을 클릭합니다.
4. 해당 그룹의 페이지에서 **멤버** 탭을 클릭합니다.
5. 해당 그룹의 페이지에서, 이 그룹에서 제거할 멤버의 이름을 클릭하고 **제거**를 클릭합니다.
6. 작업 확인 질문에 대한 답변으로 **예**를 클릭하여 그룹에서 이 멤버를 제거할 것임을 확인합니다.


##규칙을 사용하여 동적으로 보안 그룹 멤버를 관리하는 방법
**특정 그룹에 대해 동적 멤버 자격을 사용하려면 다음 단계를 수행하세요.**

1. Azure 관리 포털의 **그룹** 탭 아래에서 편집할 그룹을 선택하고 해당 그룹의 **구성** 탭에서 **동적 멤버 자격 사용** 스위치를 **예**로 설정합니다.
2. 이제 이 그룹에 대해 그룹의 동적 멤버 자격 작동 방식을 제어할 간단한 단일 규칙을 설정할 수 있습니다. **사용자 추가 위치** 라디오 단추가 선택되어 있는지 확인하고, 풀다운 메뉴에서 사용자 속성(예: department, jobTitle 등)을 선택합니다. 
3. 그런 다음, 조건(같지 않음, 같음, 다음으로 시작 안 함, 다음으로 시작, 포함 안 함, 포함, 일치하지 않음, 일치)을 선택하고 선택한 사용자 속성의 값을 지정합니다.
4. 예를 들어 그룹이 SaaS 응용 프로그램에 할당되어 있고(자세한 내용은 Azure AD에서 그룹에 대해 SaaS 응용 프로그램 액세스 권한 할당 참조) 사용자 추가 위치가 (-eq)Sales Rep와 같은 jobTitle로 설정된 규칙을 설정하여 이 그룹에 대해 동적 멤버 자격을 사용하도록 설정한 경우 Azure AD 디렉터리 내에서 직책이 Sales Rep로 설정된 모든 사용자가 이 SaaS 응용 프로그램에 액세스할 수 있습니다.

다음은 Azure Active Directory에 대한 추가 정보를 제공하는 몇 가지 항목입니다.

* [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](active-directory-manage-groups.md)

* [Azure Active Directory란?](active-directory-whatis.md)

* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)

<!---HONumber=August15_HO6-->
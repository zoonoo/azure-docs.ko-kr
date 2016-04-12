<properties

	pageTitle="Managing security groups in Azure Active Directory | Microsoft Azure"
	description="How to create and manage security groups to manage Azure resource access using Azure Active Directory."
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
	ms.topic="get-started-article"
	ms.date="03/18/2016"
	ms.author="curtand"/>


# Azure Active Directory에서 그룹 관리

Azure AD(Azure Active Directory)의 주요 기능 중 하나는 리소스에 대한 액세스를 관리하는 기능입니다. 이러한 리소스는 디렉터리에 있는 개체이거나 SaaS 응용 프로그램, Azure 서비스, SharePoint 사이트 또는 온-프레미스 리소스와 같이디렉터리 외부에 있는 리소스일 수 있습니다. 또한 리소스 소유자는 Azure AD 그룹에 리소스에 대한 액세스를 할당할 수 있습니다. 이는 해당 그룹의 멤버에게 리소스에 대한 액세스 권한을 부여합니다. 그런 다음 그룹의 소유자는 그룹의 멤버 자격을 관리합니다. 실질적으로 리소스 소유자는 그룹의 소유자에게 권한을 위임하여 해당 리소스에 사용자를 할당합니다.

## 보안 그룹을 만드는 방법

**그룹 만들려면**

이 작업은 조직에서 구독한 서비스에 따라 Office 365 계정 포털, Windows Intune 계정 포털 또는 Azure 포털을 사용하여 완료될 수 있습니다. 포털을 사용하여 Azure Active Directory를 관리하는 방법에 대한 자세한 내용은 [Azure AD 디렉터리 관리](active-directory-administer.md)를 참조하세요.

1. [Azure 클래식 포털](https://manage.windowsazure.com)에서 **Active Directory**를 선택한 다음 조직의 디렉터리 이름을 선택합니다.

2. **그룹** 탭을 선택합니다.

3. **그룹 추가**를 선택합니다.

4. **그룹 추가** 창에서 그룹의 이름 및 설명을 지정합니다.


## 보안 그룹에서 개별 사용자를 추가하거나 제거하는 방법

**그룹에 개별 사용자를 추가하려면**

1. [Azure 클래식 포털](https://manage.windowsazure.com)에서 **Active Directory**를 선택한 다음 조직의 디렉터리 이름을 선택합니다.

2. **그룹** 탭을 선택합니다.

3. 구성원을 추가할 그룹을 엽니다. 기본적으로 선택된 그룹의 **멤버** 탭이 표시됩니다.

4. **멤버 추가**를 선택합니다.

5. **멤버 추가** 페이지에서 이 그룹의 멤버로 추가할 사용자 또는 그룹의 이름을 선택하고 이 이름이 **선택한** 창에 추가되었는지 확인합니다.


**그룹에서 개별 사용자를 제거하려면**

1. [Azure 클래식 포털](https://manage.windowsazure.com)에서 **Active Directory**를 선택한 다음 조직의 디렉터리 이름을 선택합니다.

2. **그룹** 탭을 선택합니다.

3. 멤버를 제거할 그룹을 엽니다.

4. **멤버** 탭을 선택하고 이 그룹에서 제거할 멤버의 이름을 선택한 다음 **제거**를 클릭합니다.

6. 프롬프트에서 이 멤버를 그룹에서 제거한다고 확인합니다.


## 그룹의 멤버 자격을 동적으로 관리하는 방법

Azure AD에서는 간단한 규칙을 매우 쉽게 설정하여(단일 비교를 수행하는 규칙) 그룹의 멤버가 될 사용자를 결정할 수 있습니다. 예를 들어 그룹이 SaaS 응용 프로그램에 할당되고 "판매 담당자"라는 직위를 가진 사용자를 추가하는 규칙을 설정한 경우 해당 직함을 가진 Azure AD 디렉터리 내의 모든 사용자는 이 SaaS 응용 프로그램에 액세스할 수 있습니다.

> [AZURE.NOTE] 보안 그룹 또는 Office 365 그룹에서 동적 멤버 자격에 대한 규칙을 설정할 수 있습니다. 중첩 그룹 구성원은 이번 응용 프로그램에 대한 그룹 기반 할당에서 지원되지 않습니다. 그룹의 동적 멤버 자격에는 할당된 Azure AD Premium 라이선스가 필요합니다.
>- 그룹의 규칙을 관리하는 관리자
>- 규칙에 따라 그룹의 멤버로 선택된 모든 사용자

**그룹에 동적 멤버 자격을 사용하려면**

1. [Azure 클래식 포털](https://manage.windowsazure.com)에서 **Active Directory**를 선택한 다음 조직의 디렉터리 이름을 선택합니다.

2. **그룹** 탭을 선택하고 편집할 그룹을 엽니다.

3. **구성** 탭을 선택한 다음 **동적 멤버 자격 사용**을 **예**로 설정합니다.

4. 이제 이 그룹에 대해 그룹의 동적 멤버 자격 작동 방식을 제어할 간단한 단일 규칙을 설정합니다. **사용자 추가 위치** 옵션이 선택되어 있는지 확인하고 목록에서 사용자 속성(예: department, jobTitle 등)을 선택합니다.

5. 그런 다음, 조건(같지 않음, 같음, 다음으로 시작 안 함, 다음으로 시작, 포함 안 함, 포함, 일치하지 않음, 일치)을 선택하고 선택한 사용자 속성의 값을 지정합니다.

동적 그룹 멤버 자격에 대한 *고급* 규칙(여러 비교를 포함할 수 있는 규칙)을 만드는 방법에 대해 자세히 알아보려면 [특성을 사용하여 고급 규칙 만들기](active-directory-accessmanagement-groups-with-advanced-rules.md)를 참조하세요.

## 추가 정보

이러한 문서는 Azure Active Directory에 대한 추가 정보를 제공합니다.

* [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](active-directory-manage-groups.md)

* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)

* [Azure Active Directory란?](active-directory-whatis.md)

* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0330_2016-->
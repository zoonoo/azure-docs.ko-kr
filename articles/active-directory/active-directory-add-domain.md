<properties
	pageTitle="Azure Active Directory에 사용자 지정 도메인 이름 추가 | Microsoft Azure"
	description="Azure Active Directory에 회사의 도메인 이름을 추가하는 방법 및 도메인 이름을 확인하는 방법."
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/20/2016"
	ms.author="curtand;jeffsta"/>

# Azure Active Directory에 사용자 지정 도메인 이름 추가

조직이 비즈니스를 수행하기 위해 사용하는 하나 이상의 도메인 이름이 있다면 조직의 사용자는 회사 도메인 이름을 사용하여 회사 네트워크에 로그인합니다. 이제 Azure Active Directory(Azure AD)를 사용 중이므로 Azure AD에도 회사 도메인 이름을 추가할 수 있습니다. 이렇게 하면 사용자에게 친숙한 ‘alice@contoso.com’과 같은 사용자 이름을 디렉터리에 할당할 수 있습니다. 프로세스는 간단합니다.

- Azure 클래식 포털에서 **도메인 추가**에 도메인 이름 추가

- Azure AD 클래식 포털 또는 Azure AD Connect 도구에서 DNS 항목 가져오기

- DNS 등록 기관을 위한 웹 사이트에서 도메인 이름에 대한 DNS 항목을 DNS 영역 파일에 추가

- Azure AD 클래식 포털 또는 Azure AD Connect 도구에서 도메인 이름 확인


사용자 지정 도메인 이름을 확인할 때까지는 디렉터리에 대한 초기 도메인 이름인 ‘alice@contoso.onmicrosoft.com’과 같은 사용자 이름으로 로그인해야 합니다. 사용자 지정 도메인 이름을 여러 개 사용해야 하는 경우(예: ‘contoso.com’ 및 ‘contosobank.com’), 최대 900개의 도메인에 해당 이름을 추가할 수 있습니다. 이 문서의 동일한 단계를 사용하여 각 도메인 이름을 추가합니다.

## 디렉터리에 사용자 지정 도메인 이름 추가

1. Azure AD 디렉터리의 전역 관리자인 사용자 계정으로 [Azure 클래식 포털](https://manage.windowsazure.com/)에 로그인합니다.

2. **Active Directory**에서 디렉터리를 열고 **도메인** 탭을 선택합니다.

3. 명령 모음에서 **추가**를 선택한 다음 'contoso.com'과 같은 사용자 지정 도메인의 이름을 입력합니다. .com, .net 또는 그 외 최상위 확장명을 포함해야 합니다.

4. 이 도메인을 온-프레미스 Active Directory의 [페더레이션된 로그인](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect)을 위해 구성하려면 해당 확인란을 선택합니다.

5. **추가**를 선택합니다.

이제 도메인 이름을 추가했으므로 Azure AD에서 조직이 해당 도메인 이름을 소유하고 있는지 확인해야 합니다. Azure AD에서 이러한 확인을 수행하기 전에 도메인 이름으로 DNS 영역 파일에 포함된 DNS 항목을 추가해야 합니다. 이 작업은 도메인 이름을 위한 도메인 이름 등록 기관의 웹 사이트에서 수행됩니다.

## 도메인 이름에 대한 DNS 항목 가져오기

DNS 항목은 온-프레미스 Windows Server Active Directory와 페더레이션하지 않은 경우 **도메인 추가** 마법사의 두 번째 페이지에 있습니다.

페더레이션을 위해 도메인을 구성하는 경우, Azure AD Connect 도구를 다운로드하라는 메시지가 표시됩니다. [도메인 이름 등록 기관에서 추가해야 하는 DNS 항목을 가져오려면](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation) Azure AD Connect 도구를 실행합니다. 또한 Azure AD Connect 도구에서도 Azure AD에 대한 도메인 이름을 확인합니다.

## DNS 영역 파일에 DNS 항목 추가

1.  도메인에 대한 도메인 이름 등록 기관에 로그인합니다. DNS 항목을 업데이트하기에 충분한 권한이 없으면, DNS 항목을 추가할 권한이 있는 사람이나 팀에게 요청합니다.

2.  Azure AD에서 제공되는 DNS 항목을 추가하여 도메인에 대한 DNS 영역 파일을 업데이트합니다. 이러한 DNS 항목을 통해 Azure AD에서 도메인에 대한 소유권을 확인할 수 있도록 합니다. DNS 항목은 메일 라우팅이나 웹 호스팅 같은 어떠한 동작도 변경하지 않습니다. DNS 레코드 전파에는 최대 한 시간 정도가 소요될 수 있습니다.

[널리 사용되는 DNS 등록 기관에서 DNS 항목을 추가하기 위한 지침](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## Azure AD에서 도메인 이름 확인

DNS 항목을 추가하고 나면 Azure AD에서 도메인 이름을 확인하는지 확인해야 합니다. 이는 성공을 위한 마지막 단계입니다.

**도메인 추가** 마법사가 여전히 열려있으면, 마법사의 세 번째 페이지에서 **확인**을 선택합니다. 확인하기 전에 DNS 항목이 전파되는 동안 한 시간 기다려야 합니다.

**도메인 추가** 마법사가 아직 열려있지 않으면, [Azure 클래식 포털](https://manage.windowsazure.com/)에서 도메인을 확인할 수 있습니다.

1.  Azure AD 디렉터리의 전역 관리자인 사용자 계정으로 로그인합니다.

2.  디렉터리를 열고 **도메인** 탭을 선택합니다.

3.  확인할 도메인을 선택합니다.

4.  명령 모음에서 **확인**을 선택한 다음 대화 상자에서 **확인**을 선택합니다.

성공을 축하합니다! 이제 [사용자 지정 도메인 이름을 포함하는 사용자 이름을 할당](active-directory-add-domain-add-users.md)할 수 있습니다. 도메인 이름을 확인하는 데 문제가 있는 경우 [문제 해결](#troubleshooting) 섹션을 참조하세요.

## 문제 해결
사용자 지정 도메인 이름을 확인할 수 없는 경우, 몇 가지 잠재적인 원인이 있을 수 있습니다. 가장 일반적인 것부터 시작하여, 가장 드문 것까지 알아보겠습니다.

- DNS 항목이 전파되기 전에 도메인 이름을 확인하려고 했습니다. 잠시 기다렸다가 다시 시도하세요.

- DNS 레코드가 전혀 입력되지 않았습니다. DNS 항목을 확인한 후 항목이 전파되기까지 기다렸다가 다시 시도하세요.

- 도메인 이름이 다른 디렉터리에서 이미 확인되었습니다. 다른 디렉터리에서 도메인 이름을 찾아서 삭제하고 다시 시도하세요.

- DNS 레코드에 오류가 포함되어 있습니다. 오류를 수정하고 다시 시도하세요.

- DNS 레코드를 업데이트할 수 있는 충분한 권한이 없습니다. 조직에서 액세스가 있는 팀이나 사람과 DNS 항목을 공유하고 DNS 항목을 추가하도록 요청하세요.


## 다음 단계

-   [사용자 지정 도메인 이름을 포함하는 사용자 이름 할당](active-directory-add-domain-add-users.md)
-   [사용자 지정 도메인 이름 관리](active-directory-add-manage-domain-names.md)
-   [Azure AD에서 도메인 관리 개념 알아보기](active-directory-add-domain-concepts.md)
-   [사용자 로그인 시 회사의 브랜딩 표시](active-directory-add-company-branding.md)
-   [PowerShell을 사용하여 Azure AD에서 도메인 이름 관리](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

<!---HONumber=AcomDC_0504_2016-->
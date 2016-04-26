<properties
	pageTitle="Azure Active Directory를 사용하여 로그인을 간소화하도록 사용자 지정 도메인 이름 추가 | Microsoft Azure"
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
	ms.date="04/19/2016"
	ms.author="curtand;jeffsta"/>

# Azure Active Directory를 사용하여 로그인을 간소화하도록 사용자 지정 도메인 이름 추가

Azure Active Directory에서 로그인 및 기타 사용자 환경을 개선하고 간소화하기 위해 자신만의 사용자 지정 도메인 이름 또는 이름을 사용할 수 있습니다. 예를 들어 조직이 'contoso.com'라는 도메인 이름을 소유하는 경우 사용자는 'joe@contoso.com'과 같이 친숙한 사용자 이름으로 로그인할 수 있습니다. Azure AD 디렉터리의 전역 관리자인 사용자 계정에 액세스할 수 있어야 합니다.

Azure Active Directory에 테넌트 디렉터리가 처음으로 생기면, 사용자는 ‘alice@contoso.onmicrosoft.com’ 같은 사용자 이름으로 로그인합니다. 이 예제에서는 contoso.onmicrosoft.com이 사용자 지정 도메인 이름을 확인할 때 까지 사용할 수 있는 초기 기본 제공 도메인 이름입니다 다음 단계 중 하나는 조직 소유의 사용자 지정 도메인 이름(예: ‘contoso.com’)을 추가하는 것입니다. 이를 통해, 사용자에게 친숙한 이름(예: ‘alice@contoso.com’)을 할당할 수 있습니다.

도메인 이름이 Azure AD에서 사용되는 방법에 대한 배경은 [Azure AD의 도메인 관리 개념](active-directory-add-domain-concepts.md)을 참조하세요. 대부분의 관리자는 Azure 클래식 포털을 사용하여 Azure AD의 도메인 이름 관리 작업을 수행합니다. 하지만 원한다면 [PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) 또는 [Graph API 미리 보기](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations) 를 사용하여 관리 작업을 수행할 수 있습니다.

## 디렉터리에 사용자 지정 도메인 이름 추가

디렉터리에 사용자 지정 도메인 이름을 추가하려면:

1. Azure AD 디렉터리의 전역 관리자인 사용자 계정으로 [Azure 클래식 포털](https://manage.windowsazure.com/)에 로그인합니다.

2. 왼쪽 탐색 모음에서 **Active Directory**를 선택합니다.

3. 디렉터리를 엽니다.

4. **도메인** 탭을 선택합니다.

5. 명령 모음에서 **추가**를 선택합니다.

6. 사용자 지정 도메인의 이름을 입력합니다(예: ‘contoso.com’). .com, .net 또는 그 외 최상위 확장명을 포함해야 합니다.

7. 이 도메인을 온-프레미스 Active Directory의 [페더레이션된 로그인](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect)을 위해 구성하려면, 해당 확인란을 선택합니다.

8. **추가**를 선택합니다.

사용자 지정 도메인 이름이 포함된 사용자 이름을 할당할 수 있으려면, 그 전에 Azure AD에서 해당 도메인 이름이 조직 소유인지를 확인해야 합니다. 이러한 확인을 수행하려면, 도메인에 대해 도메인 이름 등록 기관에서 DNS 항목을 업데이트해야 합니다.

## Azure AD가 도메인 이름 확인에 사용할 DNS 항목 가져오기

도메인을 추가할 때 페더레이션을 위해 도메인을 구성하도록 옵션을 선택한 경우에는 Azure AD Connect 도구 다운로드를 위한 지침이 표시됩니다. [도메인 이름 등록 기관에서 추가해야 하는 DNS 항목을 가져오려면](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation) Azure AD Connect 도구를 실행합니다.

Windows Server AD를 통한 페더레이션 로그인 옵션을 선택하지 않은 경우에는, **도메인 추가** 마법사의 두 번째 페이지에 DNS 항목이 표시됩니다.

## 도메인의 DNS 영역 파일에 DNS 항목 추가

Azure AD에 필요한 DNS 항목을 추가하려면:

1.  도메인에 대한 도메인 이름 등록 기관에 로그인합니다. 도메인에 대한 DNS 영역 파일을 업데이트하기에 충분한 권한이 없는 경우에는, 조직에서 해당 권한이 있는 사람이나 팀과 DNS 항목을 공유하고, 업데이트하도록 요청합니다.

2.  Azure AD에서 제공하는 DNS 항목을 추가하여 도메인에 대한 DNS 영역 파일을 업데이트합니다. 이러한 DNS 항목을 통해 Azure AD에서 도메인에 대한 소유권을 확인합니다. 메일 라우팅이나 웹 호스팅 같은 어떠한 동작도 변경하지 않습니다.

[널리 사용되는 DNS 등록 기관에서 DNS 항목을 추가하기 위한 지침](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## Azure AD에서 도메인 확인

DNS 항목을 추가하고 나면, Azure AD에서 도메인을 확인할 수 있습니다.

사용자 지정 도메인 이름을 페더레이션하도록 옵션을 선택한 경우에는, Azure AD Connect 도구에서 자동으로 확인이 수행됩니다. 필수 조건을 완료한 후에 도구를 실행합니다. 아니면, Azure 클래식 포털에서 도메인을 확인합니다. **도메인 추가** 마법사가 아직 열려있으면, 마법사의 세 번째 페이지에서 확인 단추를 클릭할 수 있습니다. DNS 레코드 전파에 몇 분 정도가 소요됩니다.

**도메인 추가** 마법사가 아직 열려있으면, [Azure 클래식 포털](https://manage.windowsazure.com/)에서 도메인을 확인할 수 있습니다.

1.  Azure AD 디렉터리의 전역 관리자인 사용자 계정으로 로그인합니다.

2.  왼쪽 탐색 모음에서 **Active Directory**를 선택합니다.

3.  디렉터리를 엽니다.

4.  **도메인** 탭을 선택합니다.

5.  도메인 목록에서 확인할 도메인을 선택합니다.

6.  명령 모음에서 **확인**을 선택합니다.

7.  대화 상자에서 **확인**을 선택합니다.

이제 사용자 정의 도메인 이름을 포함하는 사용자 이름을 할당할 수 있습니다.

## 사용자 지정 도메인 이름 더 추가하기

조직에서 사용하는 사용자 지정 도메인 이름이 둘 이상인 경우(예: ‘contoso.com’ 및 ‘contosobank.com’), Azure AD에 도메인을 최대 900개까지 추가할 수 있습니다. 위에 나열된 단계를 사용하여 그 다음 도메인 이름을 모두 추가합니다.

다음 단계

-   [사용자 이름과 사용자 지정 도메인 이름 할당](active-directory-add-domain-add-users.md)

-   [사용자 지정 도메인 이름 관리](active-directory-add-manage-domain-names.md)

-   [사용자 로그인 시 회사의 브랜딩 표시](active-directory-add-company-branding.md)

-   [Azure AD의 도메인 관리 개념](active-directory-add-domain-concepts.md)

<!---HONumber=AcomDC_0420_2016-->
<properties
	pageTitle="사용자 지정 도메인 이름을 사용하여 사용자의 로그인 환경 간소화 | Microsoft Azure"
	description="Azure Active Directory에 고유한 도메인 이름 및 기타 관련 정보를 추가하는 방법에 대해 설명합니다."
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
	ms.date="02/08/2016"
	ms.author="curtand;jeffsta"/>

# 사용자 지정 도메인 이름을 사용하여 사용자의 로그인 환경 간소화

Azure Active Directory에서 로그인 및 기타 사용자 환경을 개선하고 간소화하기 위해 자신 만의 사용자 지정 도메인 이름을 사용할 수 있습니다. 예를 들어 조직이 'contoso.com'라는 도메인 이름을 소유하는 경우 사용자는 'joe@contoso.com'과 같이 친숙한 사용자 이름으로 로그인할 수 있습니다.

Azure Active Directory의 모든 디렉터리는 'contoso.onmicrosoft.com' 형식의 기본 제공 도메인 이름을 함께 제공되어 Azure 또는 다른 Microsoft 서비스를 사용할 수 있게 해줍니다. [기본 제공 도메인에 대해 알아보세요](#built-in-domain-names-for-azure-active-directory).

## Azure AD와 사용자 지정 도메인 이름 사용

조직이 사용자에게 친숙한 사용자 지정 도메인 이름을 소유하고 있는 경우 Azure Active Directory와 사용자 지정 도메인 이름을 사용하는 것이 가장 좋습니다. Azure Active Directory와 함께 사용자 지정 도메인 이름을 사용하려면 다음을 수행합니다.

-   [사용자 지정 도메인 이름 추가 및 확인](active-directory-add-domain-add-verify-general.md)

-   [사용자 지정 도메인에 사용자 할당](active-directory-add-domain-add-users.md)

## Office 365 및 기타 서비스와 사용자 지정 도메인 이름 사용

Azure AD의 다른 리소스와 마찬가지로 추가하고 확인한 사용자 지정 도메인 이름은 Office 365, Intune 및 Azure AD를 사용하는 다른 응용 프로그램과 함께 사용할 수 있습니다. 예를 들어 Exchange Online과 함께 사용자 지정 도메인 이름을 사용하면 사용자가 joe@contoso.com처럼 친숙한 전자 메일 주소로 전자 메일을 주고 받을 수 있습니다. 사용자 지정 도메인을 사용하기 위해 다른 응용 프로그램을 사용하려면 응용 프로그램에 의해 설명된 대로 DNS 등록자의 DNS 항목을 추가해야 합니다.

-   [Office 365와 사용자 지정 도메인 사용](https://support.office.com/article/Add-your-users-and-domain-to-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611?ui=en-US&rs=en-US&ad=US)

-   [Intune과 사용자 지정 도메인 사용](https://technet.microsoft.com/library/dn646966.aspx#BKMK_DomainNames)

## Azure AD에서 도메인 관리

Azure Active Directory에서는 일반적으로 도메인 이름을 지속적으로 관리할 필요가 없습니다.

-   [Azure Active Directory에서 도메인 이름 목록 참조](active-directory-add-domain-add-users.md)

-   [사용자 지정 도메인 이름의 DNS 등록자를 변경 하는 경우 수행할 작업](active-directory-add-domain-change-registrar.md)

## 사용자 지정 도메인 이름 삭제

조직이 더 이상 해당 도메인 이름을 사용하지 않는 경우 또는 다른 Azure AD와 해당 도메인 이름을 사용해야 하는 경우, Azure AD에서 사용자 지정 도메인 이름을 삭제할 수 있습니다.

-   [사용자 지정 도메인 이름 삭제](#_Deleting_a_custom)

## Azure Active Directory의 기본 제공 도메인 이름

Azure Active Directory(Azure AD)의 기본 제공 도메인과 추가한 사용자 지정 도메인 간의 차이를 구분하세요.

-   기본 제공: Azure AD 디렉터리와 함께 제공되는 도메인으로 contoso.onmicrosoft.com 형태로 제공

-   사용자 지정: contoso.com 등 조직이 이미 소유하고 있는 사용자 지정 도메인 이름

## PowerShell 또는 Graph API를 사용하여 도메인 이름 관리

Azure Active Directory의 도메인 이름에 대한 대부분의 관리 작업은 Microsoft PowerShell을 사용하거나 프로그래밍 방식으로 Graph API를 사용하여 완료할 수도 있습니다.

-   [PowerShell을 사용하여 Azure AD에서 도메인 이름 관리](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Graph API를 사용하여 Azure AD(미리보기)에서 도메인 이름 관리](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)


## 다음 단계

Azure Active Directory에서 도메인 이름 사용을 이해하기 위한 추가 리소스가 필요한 경우, 다음을 시도해보십시오.

- [로그인 및 액세스 패널 페이지에 회사 브랜딩 추가하기](active-directory-add-company-branding.md)
- [Azure Active Directory에서 사용자 지정 도메인 이름 추가 및 확인](active-directory-add-domain-add-verify-general.md)
- [사용자 지정 도메인에 사용자 할당](active-directory-add-domain-add-users.md)
- [사용자 지정 도메인 이름의 DNS 등록자 변경](active-directory-add-domain-change-registrar.md)
- [Azure Active Directory에서 사용자 지정 도메인 삭제](active-directory-add-domain-delete-domain.md)

<!---HONumber=AcomDC_0211_2016-->
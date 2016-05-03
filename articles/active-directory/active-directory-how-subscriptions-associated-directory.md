<properties
	pageTitle="Azure 구독과 Azure Active Directory의 연관 관계 | Microsoft Azure"
	description="Microsoft Azure 로그인 및 Azure 구독과 Azure Active Directory 간의 관계와 같은 관련 문제입니다."
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
	ms.date="04/26/2016"
	ms.author="curtand"/>

# Azure 구독과 Azure Active Directory의 연관 관계

이 항목에서는 Microsoft Azure 로그인 및 Azure 구독과 Azure Active Directory(Azure AD) 간의 관계와 같은 관련 문제에 대한 정보를 제공합니다.

## 로그인에 사용할 수 있는 계정
로그인에 사용할 수 있는 계정부터 살펴보겠습니다. 두 가지 유형의 계정, 즉 Microsoft 계정(이전의 Microsoft Live ID)과 Azure AD에 저장되는 회사 또는 학교 계정이 있습니다.

 Microsoft 계정 | Azure AD 계정
	------------- | -------------
Microsoft에서 제공하는 소비자 ID 시스템 | Microsoft에서 제공하는 기업 ID 시스템
Hotmail, MSN과 같은 소비자 지향적인 서비스에 대한 인증 | Office 365와 같은 비즈니스 지향적인 서비스에 대한 인증
소비자가 자신의 Microsoft 계정을 생성(메일에 등록할 때 등) | 회사와 조직이 자신의 회사 또는 학교 계정을 만들고 관리함
ID가 만들어지고 Microsoft 계정 시스템에 저장됨 | Azure 또는 Office 365와 같은 다른 서비스를 사용하여 ID가 만들어지고 이 ID는 조직에 할당된 Azure AD 인스턴스에 저장됨

원래 Microsoft 계정 사용자만 Azure에 액세스할 수 있었으나 이제는 이 *두 가지* 시스템의 사용자도 액세스할 수 있습니다. 이렇게 하기 위해 모든 Azure 속성이 인증을 위해 Azure AD를 신뢰하도록 하고, Azure AD가 조직 사용자를 인증하도록 하며, 소비자 사용자를 인증하는 Microsoft 계정 소비자 ID 시스템을 Azure AD가 신뢰하는 위치에 페더레이션 관계를 만들었습니다. 그 결과, Azure AD가 "기본" Azure AD 계정 뿐만 아니라 "게스트" Microsoft 계정도 인증할 수 있습니다.

예를 들어 Microsoft 계정을 가진 사용자가 Azure 클래식 포털에 로그인합니다.

> [AZURE.NOTE]
Azure 클래식 포털에 로그인하려면 msmith@hotmail.com에 Azure 구독이 있어야 합니다. 구독의 공동 관리자 또는 서비스 관리자 계정이어야 합니다.

![][1]

이 Hotmail 주소는 소비자 계정이므로 Microsoft 계정 소비자 ID 시스템에서 로그인을 인증했습니다. Azure AD ID 시스템은 Microsoft 계정 시스템에서 수행한 인증을 신뢰하고 Azure 서비스에 액세스할 수 있는 토큰을 발급합니다.

## Azure 구독과 Azure AD의 연관 관계

모든 Azure 구독은 Azure AD 인스턴스와 트러스트 관계가 있습니다. 이는 Azure 구독이 사용자, 서비스, 장치를 인증하는 해당 디렉터리를 신뢰함을 의미합니다. 여러 구독에서 동일한 디렉터리를 신뢰할 수 있지만 구독은 하나의 디렉터리만 신뢰합니다. 설정 탭에서 구독이 신뢰하는 디렉터리를 확인할 수 있습니다. 신뢰하는 디렉터리를 변경하도록 [구독 설정을 편집](active-directory-understanding-resource-access.md)할 수 있습니다.

구독이 디렉터리와 갖는 이 트러스트 관계는 구독이 Azure의 다른 모든 리소스(웹 사이트, 데이터베이스 등)와 갖는 관계와 다르며 구독의 하위 리소스와 더 유사합니다. 구독이 만료되면 구독과 연결된 다른 리소스에 대한 액세스도 중지됩니다. 하지만 디렉터리는 Azure에 남아 있으며 해당 디렉터리와 다른 구독을 연결하여 디렉터리 사용자를 계속 관리할 수 있습니다.

마찬가지로, 구독에 표시되는 Azure AD 확장은 Azure 클래식 포털의 다른 확장처럼 작동하지 않습니다. Azure 클래식 포털의 다른 확장은 Azure 구독으로 범위가 제한됩니다. Azure AD 확장에 표시되는 내용은 구독에 따라 달라지지 않고 로그인한 사용자에 따라 디렉터리만 표시됩니다.

모든 사용자는 자신을 인증하는 단일 홈 디렉터리를 가지고 있지만 다른 디렉터리의 게스트가 될 수도 있습니다. Azure AD 확장에 사용자 계정이 구성원인 디렉터리가 모두 표시됩니다. 계정이 구성원이 아닌 디렉터리는 표시되지 않습니다. 디렉터리는 Azure AD의 회사 또는 학교 계정을 위한 토큰이나 Microsoft 계정 사용자(Azure AD가 Microsoft 계정 시스템과 페더레이션되므로)를 위한 토큰을 발급할 수 있습니다.

이 다이어그램에서는 Michael Smith란 사람이 Contoso의 회사 계정을 사용하여 등록한 후의 구독을 보여 줍니다.

![][2]

## 구독 및 디렉터리를 관리하는 방법
Azure 구독의 관리 역할은 Azure 구독에 연결된 리소스를 관리합니다. 이러한 역할 및 구독 관리 모범 사례는 [Azure Active Directory의 관리자 역할 할당](active-directory-assign-admin-roles.md)에서 다룹니다.

등록할 때 기본적으로 서비스 관리자 역할이 할당됩니다. 다른 사용자가 동일한 구독을 사용하여 로그인하고 서비스에 액세스해야 하는 경우 공동 관리자로 추가할 수 있습니다. 서비스 관리자와 공동 관리자는 Microsoft 계정이거나 Azure 구독이 연결된 디렉터리의 회사 또는 학교 계정일 수 있습니다.

Azure AD는 디렉터리 및 ID 관련 기능을 관리하는 다른 관리 역할 집합을 가지고 있습니다. 예를 들어 디렉터리의 전역 관리자는 디렉터리에 사용자 및 그룹을 추가하거나 사용자에 대해 다단계 인증을 요구할 수 있습니다. 디렉터리를 만든 사용자는 전역 관리자 역할에 할당되어 다른 사용자에게 관리자 역할을 할당할 수 있습니다.

구독 관리자와 마찬가지로 Azure AD 관리 역할은 Microsoft 계정이거나 회사 또는 학교 계정일 수 있습니다. 또한 Azure AD 관리 역할은 Office 365 및 Microsoft Intune과 같은 다른 서비스에서도 사용됩니다. 자세한 내용은 [관리자 역할 할당](active-directory-assign-admin-roles.md)을 참조하세요.

하지만 여기서 중요한 것은 Azure 구독 관리자와 Azure AD 디렉터리 관리자는 서로 다른 개념이라는 점입니다. Azure 구독 관리자는 Azure에서 리소스를 관리할 수 있으며 Azure 클래식 포털이 Azure 리소스이므로 Azure 클래식 포털에서 Active Directory 확장을 볼 수 있습니다. 디렉터리 관리자는 디렉터리에서 속성을 관리할 수 있습니다.

역할을 모두 담당할 수 있지만 그럴 필요는 없습니다. 사용자는 디렉터리 전역 관리자 역할에 할당될 수 있지만 Azure 구독의 공동 관리자 또는 서비스 관리자로 할당될 수는 없습니다. 구독의 관리자가 아니어도 사용자는 Azure 클래식 포털에 로그인할 수 없습니다. 하지만 Azure AD PowerShell 또는 Office 365 관리 센터와 같은 다른 도구를 사용하여 디렉터리 관리 작업을 수행할 수 있습니다.

## 현재 사용자 계정으로 디렉터리를 관리할 수 없는 이유

사용자가 Azure 구독에 등록하기 전에 회사 또는 학교 계정을 사용하여 Azure 클래식 포털에 로그인할 경우도 있습니다. 이 경우에 사용자는 해당 계정에 대한 구독이 없다는 메시지를 받게 됩니다. 메시지에는 무료 평가판 구독을 시작할 수 있는 링크가 포함됩니다.

무료 평가판에 등록한 후 사용자는 Azure 클래식 포털에서 조직의 디렉터리를 볼 수 있지만 디렉터리 전역 관리자가 아니므로 디렉터리를 관리할 수는 없습니다. 즉, 사용자를 추가하거나 기존 사용자 속성을 편집할 수 없습니다. 구독을 통해 사용자는 Azure 클래식 포털을 사용하고 Azure Active Directory 확장을 볼 수 있지만 디렉터리를 관리하려면 전역 관리자 권한이 추가로 필요합니다.

## Microsoft 계정을 사용하여 만든 Azure 구독을 회사 또는 학교 계정을 사용하여 관리

[조직으로 Azure에 등록](sign-up-organization.md)하고 회사 또는 학교 계정을 사용하여 Azure에서 리소스를 관리하는 것이 가장 좋습니다. 회사 또는 학교 계정은 발급한 조직이 중앙에서 관리하고, Microsoft 계정보다 많은 기능을 갖고 있으며, Azure AD에서 직접 인증하므로 선호됩니다. 동일한 계정은 기업 및 조직에 제공된 Office 365 또는 Microsoft Intune 등의 다른 Microsoft 온라인 서비스에 대한 액세스를 제공합니다. 이러한 다른 속성을 사용하는 계정이 이미 있는 경우 Azure로 동일한 계정을 사용하려는 경우가 많습니다. 또한 Azure 구독을 신뢰하는 속성을 지원하는 Active Directory 인스턴스도 갖게 됩니다.

회사 또는 학교 계정은 Microsoft 계정보다 많은 방법으로 관리할 수도 있습니다. 예를 들어 관리자는 회사 또는 학교 계정의 암호를 재설정하거나 계정에 대한 다단계 인증을 요구할 수 있습니다.

경우에 따라 조직의 사용자가 소비자 Microsoft 계정의 Azure 구독에 연결된 리소스를 관리할 수도 있습니다. 다른 계정이 구독 또는 디렉터리를 관리하도록 전환하는 방법에 대한 자세한 내용은 [Azure에서 Office 365 구독의 디렉터리 관리](#manage-the-directory-for-your-office-365-subscription-in-azure)를 참조하세요.


## Microsoft 계정에 업무용 메일을 사용한 경우의 로그인

지난 번에 업무용 메일을 사용자 ID로 사용하여 소비자 Microsoft 계정을 만들었으면 Microsoft Azure 계정 시스템 또는 Microsoft 계정 시스템에서 선택하라는 페이지가 표시될 수 있습니다.

![][3]

이름이 같은 사용자 계정이 있으며, 하나는 Azure AD에 있고 다른 하나는 소비자 Microsoft 계정 시스템에 있습니다. 사용하려는 Azure 구독과 연결된 계정을 선택해야 합니다. 이 사용자에 대한 구독이 없다는 오류가 발생하면 잘못 선택한 것입니다. 로그아웃하고 다시 시도하세요. 로그인을 방해할 수 있는 오류에 대한 자세한 내용은 ["계정과 연결된 구독을 찾지 못했습니다" 오류 문제 해결](https://social.msdn.microsoft.com/Forums/ko-KR/f952f398-f700-41a1-8729-be49599dd7e2/troubleshooting-we-were-unable-to-find-any-subscriptions-associated-with-your-account-errors-in?forum=windowsazuremanagement)을 참조하세요.

## Azure에서 Office 365 구독의 디렉터리 관리

Azure에 등록하기 전에 Office 365에 등록했다고 가정해 보겠습니다. 이제 Azure 클래식 포털에서 Office 365 구독의 디렉터리를 관리하려고 합니다. 이 작업을 수행하는 방법에는 Azure 등록 여부에 따라 두 가지가 있습니다.

### Azure에 대한 구독이 없는 경우

이 경우 Office 365에 로그인하는 데 사용하는 것과 동일한 회사 또는 학교 계정을 사용하여 [Azure에 등록](sign-up-organization.md)하기만 하면 됩니다. Office 365 계정의 관련 정보가 Azure 등록 양식에 미리 채워집니다. 계정이 구독의 서비스 관리자 역할에 할당됩니다.

### Azure에 대해 Microsoft 계정을 사용하는 구독이 있는 경우

회사 또는 학교 계정을 사용하여 Office 365에 등록한 다음 Microsoft 계정을 사용하여 Azure에 등록한 경우에는 두 개의 디렉터리가 있습니다. 하나는 회사 또는 학교용 디렉터리이고, 다른 하나는 Azure에 등록할 때 생성된 기본 디렉터리입니다.

이 두 디렉터리를 모두 Azure 클래식 포털에서 관리하려면 다음 단계를 완료하세요.

> [AZURE.NOTE]
이 단계는 사용자가 Microsoft 계정으로 로그인한 경우에만 완료할 수 있습니다. 사용자가 회사 또는 학교 계정으로 로그인했으면 홈 디렉터리(즉, 회사 또는 학교 계정이 저장된 디렉터리 및 회사 또는 학교가 소유한 디렉터리)만 회사 또는 학교 계정을 인증할 수 있으므로 **기존 디렉터리 사용** 옵션을 사용할 수 없습니다.

1. Microsoft 계정을 사용하여 Azure 클래식 포털에 로그인합니다.

2. **새로 만들기** > **앱 서비스** > **Active Directory** > **디렉터리** > **사용자 지정 만들기**를 클릭합니다.

3. **기존 디렉터리 사용**을 클릭하고 **지금 로그아웃할 준비가 되었습니다**를 선택하고 확인 표시를 클릭하여 작업을 완료합니다.

4. 회사 또는 학교 디렉터리에 대한 전역 관리자 권한이 있는 계정을 사용하여 Azure 클래식 포털에 로그인합니다.

5. **Azure로 Contoso 디렉터리를 사용할까요?**란 메시지가 나타나면 **계속**을 클릭합니다.

6. **지금 로그아웃**을 클릭합니다.

7. Microsoft 계정을 사용하여 Azure 클래식 포털에 다시 로그인합니다. Active Directory 확장에 두 디렉터리가 모두 표시됩니다.


## 다음 단계

- Azure 구독에 대한 관리자를 변경하는 방법을 자세히 알아보려면 [Azure 관리자 역할을 추가 또는 변경하는 방법](../billing-add-change-azure-subscription-administrator.md)을 참조하세요.

- Microsoft Azure에서 리소스 액세스를 제어하는 방법을 자세히 알아보려면 [Azure에서 리소스 액세스 이해](active-directory-understanding-resource-access.md)를 참조하세요.

- Azure AD에서 역할을 할당하는 방법에 대한 자세한 내용은 [Azure Active Directory에서 관리자 역할 할당](active-directory-assign-admin-roles.md)을 참조하세요.

- [조직으로 Azure에 등록](sign-up-organization.md)


<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG

<!---HONumber=AcomDC_0427_2016-->
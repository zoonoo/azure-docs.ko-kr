<properties
	pageTitle="Azure Active Directory에서 사용자 지정 도메인 이름 추가 및 확인 | Microsoft Azure"
	description="Azure AD를 시작할 때 기존 도메인을 Azure Active Directory에 추가하는 방법 사용자 계정 정보를 온-프레미스 ID 인프라와 동기화하도록 사용자 지정 도메인을 설정합니다."
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
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="curtand;jeffsta"/>

# Azure Active Directory에서 사용자 지정 도메인 이름 추가 및 확인

사용자 지정 도메인 이름을 추가하고 Azure Active Directory에서 사용할 수 있는지 확인하려면 다음 단계를 완료해야 합니다.

1.  다음 중 하나를 수행합니다.

    -   [온-프레미스 Active Directory와 페더레이션될 사용자 지정 도메인 이름을 추가합니다.](#add-a-custom-domain-name-that-will-be-federated)

    -   [온-프레미스 Active Directory와 페더레이션되지 않을 사용자 지정 도메인 이름을 추가합니다.](#add-a-custom-domain-name-that-will-not-be-federated)

2.  사용자 지정 도메인 이름을 확인합니다.

    -   Azure AD가 조직이 해당 도메인을 위한 도메인 이름 등록 기관에서 사용자 지정 도메인 이름을 소유하는지 확인하기 위해 사용하는 DNS 항목을 추가합니다.

    -   Azure AD의 도메인을 확인합니다.

## 페더레이션될 사용자 지정 도메인 이름 추가

Azure AD Connect를 사용하는 온-프레미스 디렉터리 통합에 대한 자세한 내용은 다음을 참조하세요.

**Azure AD 디렉터리에 사용자 지정 도메인 이름을 추가하려면**

1.  Azure AD에서 전역 관리자 권한이 있는 계정을 사용하여 [Azure 클래식 포털](https://manage.windowsazure.com/)에 로그인합니다.

2.  Active Directory를 선택합니다.

3.  디렉터리를 엽니다.

4.  **도메인** 탭을 선택합니다.

5.  명령 모음에서 **추가**를 선택합니다.

6.  사용자 지정 도메인의 이름을 입력합니다. .com 확장명을 포함해야 합니다.

7.  **내 로컬 Active Directory를 사용하여 이 도메인을 Single Sign-On용으로 구성할 계획입니다** 확인란을 선택합니다.

8.  **추가**를 클릭합니다.

## 페더레이션되지 않을 사용자 지정 도메인 이름을 추가합니다.

대부분의 사용자 지정 도메인 이름은 두 번째 수준 도메인 "contoso.com"입니다.

**Azure AD 디렉터리에 사용자 지정 도메인 이름을 추가하려면**

1.  Azure AD에서 전역 관리자 권한이 있는 계정을 사용하여 [Azure 클래식 포털](https://manage.windowsazure.com/)에 로그인합니다.

2.  Active Directory를 선택합니다.

3.  디렉터리를 엽니다.

4.  **도메인** 탭을 선택합니다.

5.  명령 모음에서 **추가**를 선택합니다.

6.  사용자 지정 도메인의 이름을 입력합니다. .com 확장명을 포함해야 합니다.

7.  **내 로컬 Active Directory를 사용하여 이 도메인을 Single Sign-On용으로 구성할 계획입니다** 확인란이 선택 취소되었는지 확인합니다.

8.  **추가**를 선택합니다.

9.  도메인이 디렉터리에 추가된 것을 확인합니다.

## 도메인 이름 등록 기관에서 도메인 확인

도메인을 확인하려면 도메인 이름 등록 기관이나 DNS가 호스트되는 모든 위치에서 DNS 레코드를 만듭니다. 그러면 Azure AD에서 해당 레코드를 사용하여 도메인을 소유하고 있는지 확인합니다. [널리 사용되는 DNS 등록 기관에서 DNS 항목을 추가하기 위한 지침](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

이미 도메인 이름 등록 기관에 등록된 도메인이 있는 경우 필요한 DNS 레코드가 이미 있습니다.

사용자 지정 도메인을 추가했지만 도메인이 아직 확인되지 않은 경우 상태에 **확인되지 않음**이 표시됩니다.

## 온-프레미스 디렉터리와 페더레이션되지 않을 사용자 지정 도메인 이름을 확인합니다.
도메인에 대해 만든 레코드가 도메인 등록 기관의 DNS 시스템을 통해 성공적으로 추가된 경우 다음을 수행합니다.

1.  [Azure 클래식 포털](https://manage.windowsazure.com/)의 Azure Active Directory에서 **도메인**을 클릭합니다.

2.  **도메인** 목록에서 확인할 도메인을 찾은 다음 사용하고 있는 포털에 따라 **도메인을 확인하려면 클릭** 또는 **확인**을 클릭합니다.

3.  제공된 지침에 따라 확인 프로세스를 완료합니다.

    -   도메인 확인에 성공하면 도메인이 계정에 추가되었다는 알림이 표시됩니다.

    -   도메인 확인에 실패하면 도메인 등록 기관에서 변경한 내용을 전파하는 데 더 많은 시간이 필요할 수 있습니다. 확인을 취소하고 나중에 다시 시도하세요.

도메인을 변경한 이후 72시간 이상 경과되었으면 도메인 등록 기관 웹 사이트에 로그인하여 별칭 정보를 올바르게 입력했는지 확인하세요. 정보를 잘못 입력한 경우 잘못된 DNS 레코드를 제거하고 올바른 정보로 새 레코드를 만들어야 합니다.

## 온-프레미스 디렉터리와의 페더레이션을 위해 사용자 지정 도메인 확인

1.  Azure AD Connect 다운로드 및 실행 Azure AD Connect 도구가 [제공하는 DNS 항목을 추가](active-directory-aadconnect-get-started-custom.md#select-the-azure-ad-domain-that-you-wish-to-federate)하라는 메시지를 표시합니다.

## 세 번째 수준 도메인 이름

Azure AD에서는 "europe.contoso.com"과 같은 세 번째 수준 도메인을 사용할 수 있습니다. 세 번째 수준 도메인을 추가하고 사용하려면

1.  두 번째 수준 도메인 "contoso.com"을 추가하고 확인합니다.

2.  "europe.contoso.com"과 같은 하위 도메인을 Azure AD에 추가합니다. 확인된 두 번째 수준 도메인의 하위 도메인을 추가할 때 Azure AD에서 자동으로 세 번째 수준 도메인을 확인합니다. DNS 항목을 더 이상 추가할 필요가 없습니다.

PowerShell 및 Graph를 사용하여 이러한 단계를 완료할 수도 있습니다.

도메인을 확인한 후에는 계정에 사용하도록 도메인을 구성할 수 있습니다.

## 다음 단계

- [사용자 지정 도메인 이름을 사용하여 사용자의 로그인 환경 간소화](active-directory-add-domain.md)
- [로그인 및 액세스 패널 페이지에 회사 브랜딩 추가하기](active-directory-add-company-branding.md)
- [사용자 지정 도메인에 사용자 할당](active-directory-add-domain-add-users.md)
- [사용자 지정 도메인 이름의 DNS 등록 기관 변경](active-directory-add-domain-change-registrar.md)
- [Azure Active Directory에서 사용자 지정 도메인 삭제](active-directory-add-domain-delete-domain.md)

<!---HONumber=AcomDC_0211_2016-->
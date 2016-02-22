<properties
	pageTitle="Azure Active Directory에서 사용자 지정 도메인 삭제 | Microsoft Azure"
	description="Azure Active Directory에서 사용자 지정 도메인을 삭제하는 방법"
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


# 사용자 지정 도메인 이름 삭제

Azure Active Directory에서 더 이상 사용할 필요가 없는 사용자 지정 도메인 이름을 삭제할 수 있습니다. 예를 들어 새 회사 이름이 있는 경우, 다른 Azure Active Directory가 있는 경우 등입니다. 또한 이름을 잘못 입력한 도메인을 추가하고 나중에 발견한 경우, 도메인의 페더레이션 여부에 대해 값을 올바로 설정하지 않은 경우와 같이 확인되지 않은 도메인을 삭제할 수도 있습니다.

## 시작하기 전에

도메인 이름을 제거하기 전에 다음 정보를 검토하는 것이 좋습니다.

- 등록할 때 디렉터리에 대해 제공된 원래 contoso.onmicrosoft.com 도메인 이름은 제거할 수 없습니다.
- 연결된 하위 도메인이 있는 모든 최상위 도메인은 하위 도메인이 제거된 다음에야 제거할 수 있습니다. 예를 들어 corp.adatum.com이나 최상위 도메인 이름을 사용하는 다른 하위 도메인이 있는 경우adatum.com을 제거할 수 없습니다. 자세한 내용은 지원 문서 [Office 365에서 도메인을 제거하려고 할 때 "도메인에 연결된 하위 도메인이 있습니다." 또는 "하위 도메인이 있는 도메인을 제거할 수 없습니다." 오류](https://support.microsoft.com/kb/2787792/)를 참조하세요.
- 디렉터리 동기화를 활성화한 경우 도메인이 계정에 자동으로 추가되고 contoso.mail.onmicrosoft.com과 같이 표시됩니다. 이 도메인 이름은 제거할 수 없습니다.
- 도메인 이름을 제거하려면 먼저 해당 도메인과 연결된 모든 사용자 또는 메일 계정에서 도메인 이름을 제거해야 합니다. 모든 계정을 제거하거나, 사용자 계정을 대량 편집하여 도메인 이름 정보 및 메일 주소를 변경할 수 있습니다. 자세한 내용은 [Azure AD에서 사용자 만들기 또는 편집](active-directory-create-users.md)을 참조하세요. 다음 항목은 제거해야 합니다.

	-   사용자 이름 또는 메일 주소에 도메인이 있는 사용자

	-   메일 주소에 도메인이 있는 메일 사용 그룹

	-   회신 URL의 일부에 도메인이 있는 응용 프로그램

- SharePoint Online 사이트 모음에 사용되고 있는 도메인 이름에서 SharePoint Online 사이트를 호스트하는 경우 도메인 이름을 제거하려면 먼저 사이트 모음을 삭제해야 합니다.

## 도메인을 삭제하려면

1.  해당 디렉터리에 대한 전역 관리자 권한이 있는 계정을 사용하여 Azure 클래식 포털에 로그인합니다.

2.  디렉터리를 열고 **도메인**을 선택합니다.

3.  도메인을 선택하고 삭제를 클릭합니다.

## 다음 단계

- [사용자 지정 도메인 이름을 사용하여 사용자의 로그인 환경 간소화](active-directory-add-domain.md)
- [로그인 및 액세스 패널 페이지에 회사 브랜딩 추가하기](active-directory-add-company-branding.md)
- [Azure Active Directory에서 사용자 지정 도메인 이름 추가 및 확인](active-directory-add-domain-add-verify-general.md)
- [사용자 지정 도메인에 사용자 할당](active-directory-add-domain-add-users.md)
- [사용자 지정 도메인 이름의 DNS 등록 기관 변경](active-directory-add-domain-change-registrar.md)

<!---HONumber=AcomDC_0211_2016-->
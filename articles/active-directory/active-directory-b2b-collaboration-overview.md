<properties
   pageTitle="Azure Active Directory B2B 공동 작업 | Microsoft Azure"
   description="Azure Active Directory B2B 공동 작업을 사용하면 비즈니스 파트너가 각 사용자를 단일 Azure AD 계정으로 나타내어 회사 응용 프로그램에 액세스할 수 있습니다."
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="02/09/2016"
   ms.author="curtand"/>

# Azure Active Directory(Azure AD) B2B 공동 작업

Azure AD B2B 공동 작업은 파트너 관리되는 ID에서 회사 응용 프로그램에 액세스를 사용하도록 설정할 수 있습니다. 리소스에 액세스하는 파트너 회사의 사용자를 초대하고 권한을 부여하여 회사 간 관계를 만들 수 있습니다. 각 회사가 Azure Active Directory로 한 번 페더레이션하고 각 사용자가 단일 Azure AD 계정에서 나타나기 때문에 복잡성이 감소합니다. 파트너 사용자가 자신의 조직에서 종료되는 경우 액세스가 취소되고 내부 디렉터리의 멤버 자격을 통해 의도하지 않은 액세스가 차단되기 때문에 보안이 향상됩니다. Azure AD가 없는 비즈니스 파트너의 경우 B2B 공동 작업은 간소화된 등록 환경으로 비즈니스 파트너에게 Azure AD 계정을 제공합니다.

-   비즈니스 파트너는 고유한 로그인 자격 증명을 사용하여 사용자를 외부 파트너 디렉터리의 관리에서 해제하고 사용자가 파트너 조직을 떠날 때 액세스 권한을 제거하지 않게 합니다.

-   비즈니스 파트너의 계정 수명 주기와 독립적으로 앱에 대한 액세스를 관리합니다. 즉, 예를 들어 작업을 수행할 비즈니스 파트너의 IT 부서에 요청하지 않고 액세스를 취소할 수 있습니다.

## 기능

B2B 공동 작업은 관리를 단순화하고 Office 365, Salesforce, Azure 서비스 및 모든 모바일과 같은 SaaS 앱, 클라우드 및 온-프레미스 클레임 인식 응용 프로그램을 포함하는 회사 리소스에 대한 파트너 액세스의 보안을 향상시킵니다. B2B 공동 작업을 사용하면 파트너는 자신의 계정을 관리하고 기업은 파트너 액세스에 보안 정책을 적용할 수 있습니다.

Azure Active Directory B2B 공동 작업은 전자 메일 확인 프로세스를 통해 고유의 Azure Active Directory를 가지고 있지 않은 경우라도 모든 규모의 파트너에 대해 간소화된 등록으로 구성하기 쉽습니다. 또한 외부 디렉터리가 없거나 파트너 당 페더레이션 구성으로 유지 관리하기 쉽습니다.

프로세스:

1. Azure AD B2B 공동 작업을 사용하면 B2B 공동 작업 포털에 2000개 이하 줄인 쉼표로 구분된 값(CSV) 파일을 업로드하여 회사 관리자가 외부 사용자의 집합을 초대하고 권한을 부여할 수 있습니다.

  ![CSV 파일 업로드 대화 상자](./media/active-directory-b2b-collaboration-overview/upload-csv.png)

2. 포털은 이러한 외부 사용자에게 전자 메일 초대를 전송합니다.

3. 초대된 사용자는 Microsoft를 사용하여 기존 작업 계정에 로그인하거나(Azure AD에서 관리됨) Azure AD에서 새 작업 계정을 가져옵니다.

4. 일단 로그인하면 사용자와 공유하는 앱으로 사용자가 리디렉션됩니다.

소비 전자 메일 주소로 초대하기(예: Gmail 또는 [*comcast.net*](http://comcast.net/))는 현재 지원되지 않습니다.

B2B 공동 작업의 작동 원리에 대해 자세한 것은 [이 비디오](http://aka.ms/aadshowb2b)를 확인하세요.

## CSV 파일 형식

CSV 파일은 다음과 같은 형식을 따릅니다. 하나 이상의 옵션을 지정하지 않는 경우에도 필요한 모든 쉼표를 추가합니다.

**전자 메일:** 초대된 사용자에 대한 전자 메일 주소입니다.<br/> **DisplayName:** 초대된 사용자에 대 한 표시 이름입니다.(일반적으로 성 및 이름)<br/> **InviteAppID:** 전자 메일 초대 및 수락 페이지를 브랜딩하기 위해 사용하는 응용 프로그램에 대한 ID입니다.<br/> **InviteReplyURL:** 초대 수락 후에 초대된 사용자를 안내하는 URL입니다. 회사 관련 URL(예: [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/))이어야 합니다. 이 선택적 필드를 지정하지 않으면 초대 회사의 액세스 패널 URL이 생성됩니다(이 URL은 `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>` 형식임).<br/> **InviteAppResources:** 응용 프로그램이 사용자를 할당할 수 있는 AppID입니다. AppID는 사용자를 추가하려는 그룹에 대한 `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`<br/> **InviteGroupResources:** ObjectID를 호출하여 검색할 수 있습니다. 초대받은 사용자가 조직에 문의하려는 경우 메일 초대에 포함할 `Get-MsolGroup | fl DisplayName, ObjectId`<br/> **InviteContactUsUrl:** "문의처" URL을 호출하여 ObjectID를 검색할 수 있습니다.<br/>

## 샘플 CSV 파일
다음은 용도에 맞게 수정할 수 있는 샘플 CSV입니다. 선호하는 파일 이름으로 저장하되 '.csv' 파일 확장이 있는지 확인합니다.

```
Email,DisplayName,InviteAppID,InviteReplyUrl,InviteAppResources,InviteGroupResources,InviteContactUsUrl
wharp@contoso.com,Walter Harp,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
jsmith@contoso.com,Jeff Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
bsmith@contoso.com,Ben Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
```
## 다음 단계
Azure B2B 공동 작업에 대한 다른 기사 찾아보기

- [Azure AD B2B 공동 작업이란?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [작동 방법](active-directory-b2b-how-it-works.md)
- [자세한 연습](active-directory-b2b-detailed-walkthrough.md)
- [CSV 파일 형식 참조](active-directory-b2b-references-csv-file-format.md)
- [외부 사용자 토큰 형식](active-directory-b2b-references-external-user-token-format.md)
- [외부 사용자 개체 특성 변경 사항](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [현재 미리 보기 제한 사항](active-directory-b2b-current-preview-limitations.md)
- [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)

<!---HONumber=AcomDC_0211_2016-->
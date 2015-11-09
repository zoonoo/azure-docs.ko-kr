<properties
   pageTitle="Azure Active Directory B2B 공동 작업 미리 보기를 위한 CSV 파일 형식 | Microsoft Azure"
   description="Azure Active Directory B2B 공동 작업은 비즈니스 파트너가 선택적으로 회사 응용 프로그램에 액세스할 수 있게 함으로써 회사 간 관계를 지원합니다."
   services="active-directory"
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="10/27/2015"
   ms.author="viviali"/>

# Azure AD(Azure Active Directory) B2B 공동 작업 미리 보기를 위한 CSV 파일 형식

Azure AD B2B 공동 작업의 미리 보기 버전에는 Azure AD 포털을 통해 업로드할 파트너 사용자 정보가 지정된 CSV 파일이 필요합니다. CSV 파일은 아래의 필수 레이블 및 필요에 따라 선택적 필드를 포함해야 합니다. 첫 번째 행의 레이블 철자를 변경하거나 열을 다시 정렬하지 말고 아래의 샘플 CSV 파일을 수정하세요.

>[AZURE.NOTE]레이블의 첫 번째 행(예: Email, DisplayName...)은 CSV 파일을 성공적으로 구문 분석하기 위해 필요합니다. 철자는 아래 지정된 필드와 일치해야 합니다. 이러한 레이블은 그 아래의 콘텐츠를 식별합니다. 필요하지 않은 선택적 필드에 대한 레이블은 CSV 파일에서 제거할 수 있습니다. 전체 열을 비워 둘 수 있습니다.

## 필수 필드: <br/>
**Email:** 초대한 사용자의 메일 주소입니다. <br/> **DisplayName:** 초대한 사용자에 대한 표시 이름(일반적으로 성 및 이름)입니다.<br/> **InviteContactUsUrl:** 초대한 사용자가 조직에게 문의하려는 경우 사용할 URL로, 메일 초대에 포함됩니다.<br/>

## 선택적 필드: <br/>
**InviteAppID:** 메일 초대 및 수락 페이지를 브랜딩하기 위해 사용할 응용 프로그램에 대한 ID입니다.<br/> **InviteAppResources:** 사용자를 할당할 회사 응용 프로그램에 대한 AppID입니다. AppID는 사용자를 추가하려는 그룹에 대한 `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`<br/> **InviteGroupResources:** ObjectID를 호출하여 PowerShell에서 검색할 수 있습니다. ObjectID는 초대 수락 후 초대한 사용자를 연결하기 위해 `Get-MsolGroup | fl DisplayName, ObjectId`<br/> **InviteReplyURL:** URL을 호출하여 PowerShell에서 검색할 수 있습니다. 회사 관련 URL(예: [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/))이어야 합니다. 이 선택적 필드를 지정하지 않으면 초대한 사용자가 선택한 회사 앱을 탐색할 수 있는 앱 액세스 패널로 연결됩니다. 앱 액세스 패널 URL의 형식은 `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`입니다.<br/> **Language:** 초대 메일 및 사용 환경에 사용하는 언어로, 지정되지 않은 경우 기본값으로 영어가 사용됩니다. 기타 지원되는 10가지 언어 코드는 다음과 같습니다.<br/> 1. de: 독일어<br/> 2. es: 스페인어<br/> 3. fr: 프랑스어<br/> 4. it: 이탈리아어<br/> 5. ja: 일본어<br/> 6. ko: 한국어<br/> 7. PT-BR: 포르투갈어(브라질)<br/> 8. ru: 러시아어<br/> 9. ZH-HANS: 중국어 간체<br/> 10. ZH-HANT: 중국어 번체<br/>

## 샘플 CSV 파일
다음은 수정할 수 있는 샘플 CSV입니다.

>[AZURE.NOTE]이 파일을 메모장에 복사하여 붙여넣고 '.csv' 파일 확장명을 사용하여 저장합니다. 그런 다음 Excel에서 이 파일을 편집합니다. 그러면 첫 번째 행에 레이블이 표시된 테이블로 표시됩니다.

>[AZURE.NOTE]Excel에서 레이블을 지정하고 그 아래 열을 채워 더 많은 선택적 필드를 추가할 수 있습니다.

```
Email,DisplayName,InviteAppID,InviteReplyUrl,InviteAppResources,InviteGroupResources,InviteContactUsUrl
wharp@contoso.com,Walter Harp,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
jsmith@contoso.com,Jeff Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
bsmith@contoso.com,Ben Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
```

## 관련된 문서
Azure B2B 공동 작업에 대한 다른 문서 찾아보기

- [Azure AD B2B 공동 작업이란?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [작동 방법](active-directory-b2b-how-it-works.md)
- [자세한 연습](active-directory-b2b-detailed-walkthrough.md)
- [외부 사용자 토큰 형식](active-directory-b2b-references-external-user-token-format.md)
- [외부 사용자 개체 특성 변경 사항](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [현재 미리 보기 제한 사항](active-directory-b2b-current-preview-limitations.md)

<!---HONumber=Nov15_HO1-->
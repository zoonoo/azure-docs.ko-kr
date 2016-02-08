<properties
   pageTitle="Azure Active Directory 감사 보고서 이벤트 | Microsoft Azure"
   description="Azure Active Directory에서 확인하고 다운로드할 수 있는 감사된 이벤트"
   services="active-directory"
   documentationCenter=""
   authors="kenhoff"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/07/2015"
   ms.author="kenhoff"/>

# Azure Active Directory 감사 보고서 이벤트

*이 설명서는 [Azure Active Directory Reporting 가이드](active-directory-reporting-guide.md)의 일부입니다.*

Azure Active Directory 감사 보고서를 통해 고객은 자신의 Azure Active Directory에서 발생한 권한 있는 작업을 식별할 수 있습니다. 권한 있는 작업에는 권한 상승 변경(예: 역할 만들기 또는 암호 재설정), 정책 구성 변경(예: 암호 정책) 또는 디렉터리 구성 변경(예: 도메인 페더레이션 설정 변경)이 포함됩니다. 보고서는 이벤트 이름의 감사 레코드, 작업을 수행한 행위자, 변경으로 인한 영향을 받은 대상 리소스, 날짜 및 시간(UTC)을 제공합니다. 고객은 [액세스 및 사용 보고서 보기](active-directory-view-access-usage-reports.md)에서 설명한 대로 [Azure 관리 포털](https://manage.windowsazure.com/)을 통해 Azure Active Directory의 감사 이벤트 목록을 검색할 수 있습니다.


## 감사 보고서 이벤트 목록
<!--- audit event descriptions should be in the past tense --->

이벤트 | 이벤트 설명
------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------
**사용자 이벤트** |
사용자 추가 | 디렉터리에 사용자를 추가합니다.
사용자 삭제 | 디렉터리에서 사용자를 삭제합니다.
라이선스 속성 설정 | 디렉터리에 있는 사용자의 라이선스 속성을 설정합니다.
사용자 암호 재설정 | 디렉터리의 사용자에 대한 암호를 다시 설정합니다.
사용자 암호 변경 | 디렉터리에 있는 사용자의 암호를 변경합니다.
사용자 라이선스 변경 | 디렉터리의 사용자에게 할당된 라이선스를 변경합니다. 어떤 라이선스가 업데이트되었는지를 확인하려면 이 이벤트 직전 또는 직후에 "업데이트 사용자" 이벤트를 봅니다.
사용자 업데이트 | 디렉터리의 사용자를 업데이트합니다. 업데이트할 수 있는 특성은 [아래에서 참조](#quotupdate-userquot-attributes)합니다.
사용자 암호 강제 변경 설정 | 로그인 시 사용자가 강제로 암호를 변경하도록 하는 속성을 설정합니다.
**그룹 이벤트** |
그룹 추가 | 디렉터리에 그룹 생성됨.
그룹 업데이트 | 디렉터리에 그룹 업데이트됨.
그룹 삭제 | 디렉터리에서 그룹 삭제됨.
그룹에 멤버 추가 | 디렉터리에서 그룹에 멤버 추가됨.
그룹에서 멤버 제거 | 디렉터리의 그룹에서 멤버 제거됨.
**응용 프로그램 이벤트** |
서비스 주체 추가 | 디렉터리에 서비스 주체를 추가합니다.
서비스 주체 제거 | 디렉터리에서 서비스 주체를 제거합니다.
서비스 주체 자격 증명 추가 | 서비스 주체에 자격 증명을 추가합니다.
서비스 주체 자격 증명 제거 | 서비스 주체에서 자격 증명을 제거합니다.
위임 항목 추가 | 디렉터리에서 [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity)를 만들었습니다.
위임 항목 설정 | 디렉터리에서 [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity)를 업데이트했습니다.
위임 항목 제거 | 디렉터리에서 [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity)를 삭제했습니다.
**역할 이벤트** |
역할에 역할 멤버 추가 | 디렉터리 역할에 사용자를 추가합니다.
역할에서 역할 멤버 제거 | 디렉터리 역할에서 사용자를 제거합니다.
회사 연락처 정보 설정 | 회사 수준 연락처 기본 설정을 지정합니다. 여기에는 Microsoft 온라인 서비스에 대한 기술 관련 알림 및 마케팅을 위한 메일 주소가 포함됩니다.
**B2B 이벤트** |
배치 초대가 업로드되었습니다. | 관리자는 초대를 포함하는 파일을 업로드하여 파트너 사용자에게 보냅니다.
배치 초대가 처리되었습니다. | 파트너 사용자에 보내는 초대를 포함하는 파일이 처리되었습니다.
외부 사용자를 초대합니다. | 외부 사용자를 디렉터리에 초대했습니다.
외부 사용자 초대를 충전합니다. | 외부 사용자가 자신의 초대를 디렉터리에 충전했습니다.
외부 사용자를 그룹에 추가합니다. | 외부 사용자가 디렉터리의 그룹에 멤버 자격을 할당받았습니다.
외부 사용자를 응용 프로그램에 할당합니다. | 외부 사용자는 응용 프로그램에 대한 직접 액세스에 할당되었습니다.
바이럴 테넌트 만들기입니다. | 새 테넌트는 초대 상환에 의해 Azure AD에서 만들어졌습니다.
바이럴 사용자 만들기입니다. | 사용자는 초대 상환에 의해 Azure AD의 기존 테넌트에서 만들어졌습니다.
**Directory 이벤트** |
회사에 파트너 추가 | 디렉터리에 파트너를 추가합니다.
회사에서 파트너 제거 | 디렉터리에서 파트너를 제거합니다.
회사에 도메인 추가 | 디렉터리에 도메인을 추가합니다.
회사에서 도메인 제거 | 디렉터리에서 도메인을 제거합니다.
도메인 업데이트 | 디렉터리에서 도메인을 업데이트합니다.
도메인 인증 설정 | 회사의 기본 도메인 설정을 변경합니다.
도메인에 페더레이션 설정 지정 | 도메인의 페더레이션 설정을 업데이트합니다.
도메인 확인 | 디렉터리의 도메인을 확인합니다.
메일로 확인된 도메인 확인 | 메일 확인을 사용하여 디렉터리의 도메인을 확인합니다.
회사에 DirSyncEnabled 플래그 설정 | Azure AD Sync에 디렉터리를 사용하도록 하는 속성을 설정합니다.
암호 정책 설정 | 사용자 암호의 길이 및 문자 제약 조건을 설정합니다.
회사 정보 설정 | 회사 수준 정보를 업데이트합니다. 자세한 내용은 [Get-msolcompanyinformation](https://msdn.microsoft.com/library/azure/dn194126.aspx) PowerShell cmdlet을 참조하세요.

<!---

List of events that still need descriptions:

Restore Application
Set String Auth Policy
Promote tenant to partner

--->

## 감사 보고서 보존
Azure AD 감사 보고서의 이벤트는 180일 동안 보존됩니다. 보고서에 보존하는 방법에 대한 자세한 내용은 [Azure Active Directory 보고서 보존 정책](active-directory-reporting-retention.md)을 참조하세요.

더 긴 보존 기간 동안 감사 이벤트를 저장하려는 고객은 Reporting API를 사용하여 별도의 데이터 저장소에 정기적으로 감사 이벤트를 저장할 수 있습니다. 자세한 내용은 [Reporting API 시작](active-directory-reporting-api-getting-started.md)을 참조하세요.

## 각 감사 이벤트에 포함된 속성

속성 | 설명
------------- | --------------------------------------------------------------
날짜 및 시간 | 감사 이벤트가 발생한 날짜 및 시간
행위자 | 작업을 수행한 사용자 또는 서비스 주체
작업 | 수행된 작업
대상 | 작업이 수행된 사용자 또는 서비스 주체


## "업데이트 사용자" 특성
"사용자 업데이트" 감사 이벤트에는 업데이트된 사용자 특성에 대한 추가 정보가 포함됩니다. 특성마다 이전 값과 새 값이 모두 포함됩니다.

특성 | 설명
------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled | 사용자가 로그인할 수 있습니다.
AssignedLicense | 사용자에게 할당된 모든 라이선스입니다.
AssignedPlan | 사용자에게 할당된 라이선스에서 발생한 서비스 계획입니다.
LicenseAssignmentDetail | 사용자에게 할당된 라이선스의 세부 정보입니다. 예를 들어, 그룹 기반 라이선스가 관련되어 있으면 라이선스를 부여한 그룹이 포함됩니다.
모바일 | 사용자의 휴대폰입니다.
OtherMail | 사용자의 암호 확인용 메일입니다.
OtherMobile | 사용자의 대체 휴대폰입니다.
StrongAuthenticationMethod | Multi-Factor Authentication(예: 모바일 앱으로부터의 음성 통화, SMS 또는 확인 코드)을 위해 사용자가 구성한 검증 방법의 목록입니다.
StrongAuthenticationRequirement | 이 사용자에 대한 Multi-Factor Authentication의 강제 적용, 사용 또는 사용 안 함의 여부입니다.
StrongAuthenticationUserDetails | Multi-Factor Authentication 및 암호 재설정 확인에 사용된 사용자 전화 번호, 대체 전화 번호, 메일 주소입니다.
TelephoneNumber | 사용자의 전화 번호입니다.

감사 레코드는 많은 규정 준수 규칙의 필수 컨트롤입니다. Azure Active Directory 감사 보고서를 사용하여 규정 준수 규칙을 충족하려는 고객은 보고서 세부 정보를 설명하는 데 도움이 되도록 내보낸 고객 감사 보고서의 복사본과 함께 이 도움말 항목의 복사본을 제출하는 것이 좋습니다. Azure에서 현재 충족하는 규정 준수 규칙을 알려고 하는 감사자는 직접 Microsoft Azure 보안 센터의 [규정 준수 페이지](https://azure.microsoft.com/support/trust-center/compliance/)로 안내하세요.

<!---HONumber=AcomDC_0128_2016-->
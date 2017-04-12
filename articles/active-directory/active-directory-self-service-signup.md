---
title: "Azure의 셀프 서비스 등록이란? | Microsoft Docs"
description: "Azure의 셀프 서비스 등록, 등록 프로세스 관리 방법 및 DNS 도메인 이름 인수 방법에 대한 개요입니다."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: b9f01876-29d1-4ab8-8b74-04d43d532f4b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: d517f434fe81617061d37a5b60f4e49806948005


---
# <a name="what-is-self-service-signup-for-azure"></a>Azure의 셀프 서비스 등록이란?
이 항목에서는 셀프 서비스 등록 프로세스 및 DNS 도메인 이름을 인수하는 방법을 설명합니다.  

## <a name="why-use-self-service-signup"></a>셀프 서비스 등록을 사용하는 이유
* 고객이 원하는 서비스를 더욱 빠르게 제공합니다.
* 서비스에 대한 메일 기반 제공 사항을 만듭니다.
* 사용자가 기억하기 쉬운 업무용 메일 별칭을 사용하여 신속하게 ID를 만들 수 있도록 하는 메일 기반 등록 플로를 만듭니다.
* 관리되지 않는 Azure 디렉터리를 나중에 관리되는 디렉터리에 만들 수 있으며 다른 서비스에 다시 사용할 수 있습니다.

## <a name="terms-and-definitions"></a>용어 및 정의
* **셀프 서비스 등록**: 사용자가 클라우드 서비스에 등록하고 해당 메일 도메인을 기반으로 Azure AD(Active Directory)에 ID가 자동으로 생성되게 하는 방법입니다.
* **관리되지 않는 Azure 디렉터리**: ID가 생성되는 디렉터리입니다. 관리되지 않는 디렉터리는 전역 관리자가 없는 디렉터리입니다.
* **메일로 확인된 사용자**: Azure AD의 사용자 계정의 한 유형입니다. 셀프 서비스 제공 사항에 등록한 후 자동으로 생성된 ID를 갖는 사용자를 메일로 확인된 사용자라고 합니다. 메일로 확인된 사용자는 creationmethod=EmailVerified로 태그가 지정된 디렉터리의 일반 멤버입니다.

## <a name="user-experience"></a>사용자 환경
예를 들어 있는 메일이 Dan@BellowsCollege.com 인 사용자가 메일을 통해 중요한 파일을 받는다고 가정해 보겠습니다. 파일은 Azure Rights Management(Azure RMS)로 보호되었습니다. 하지만 Dan의 조직인 Bellows College는 Azure RMS에 등록하지 않았으며 Active Directory RMS도 배포하지 않았습니다. 이 경우 Dan은 보호된 파일을 읽기 위해 개인용 RMS에 대한 무료 구독에 등록할 수 있습니다.

Dan이 이 셀프 서비스 제공 사항에 등록하는 BellowsCollege.com 메일 주소를 가진 첫 번째 사용자인 경우에는 Azure AD에서 BellowsCollege.com에 대해 관리되지 않는 디렉터리가 생성됩니다. BellowsCollege.com 도메인의 다른 사용자가 이 제공 사항 또는 비슷한 셀프 서비스 제공 사항에 등록하는 경우 이 사용자도 Azure에서 동일한 관리되지 않는 디렉터리에 생성된 메일로 확인된 사용자 계정을 갖게 됩니다.

## <a name="admin-experience"></a>관리자 환경
관리되지 않는 Azure 디렉터리의 DNS 도메인 이름을 소유한 관리자는 소유권을 증명한 후 디렉터리를 인수하거나 병합할 수 있습니다. 다음 섹션에서 관리자 환경을 보다 자세히 설명하지만 요약 내용은 다음과 같습니다.

* 관리되지 않는 Azure 디렉터리를 인수하면 관리되지 않는 디렉터리의 전역 관리자가 됩니다. 이를 내부 인수라고도 합니다.
* 관리되지 않는 Azure 디렉터리를 병합하고 관리되지 않는 디렉터리의 DNS 도메인 이름을 관리되는 Azure 디렉터리에 추가하여 사용자에서 리소스로 매핑되면 사용자가 중단 없이 서비스에 계속 액세스할 수 있습니다. 이를 외부 인수라고도 합니다.

## <a name="what-gets-created-in-azure-active-directory"></a>Azure Active Directory에 생성되는 것은 무엇인가요?
#### <a name="directory"></a>디렉터리
* 도메인에 대한 Azure Active Directory 디렉터리가 도메인당 디렉터리 하나씩 생성됩니다.
* Azure AD 디렉터리에는 전역 관리자가 없습니다.

#### <a name="users"></a>사용자
* 등록하는 사용자마다 Azure AD 디렉터리에 사용자 개체가 생성됩니다.
* 각 사용자 개체는 외부로 표시됩니다.
* 각 사용자는 등록한 서비스에 대한 액세스 권한을 받습니다.

### <a name="how-do-i-claim-a-self-service-azure-ad-directory-for-a-domain-i-own"></a>소유한 도메인에 대한 셀프 서비스 Azure AD 디렉터리를 어떻게 요구하나요?
도메인 유효성 검사를 수행하여 셀프 서비스 Azure AD 디렉터리를 요구할 수 있습니다. 도메인 유효성 검사는 DNS 레코드를 만들어 도메인 소유권을 증명합니다.

Azure AD 디렉터리의 DNS 인수를 수행하는 방법에는 다음 두 가지가 있습니다.

* 내부 인수(관리자가 관리되지 않는 Azure 디렉터리를 검색하여 관리되는 디렉터리로 바꾸려고 함)
* 외부 인수(관리자가 새 도메인을 관리되는 Azure 디렉터리에 추가하려고 함)

사용자의 셀프 서비스 등록 후 관리되지 않는 디렉터리를 인수하는 중이므로 도메인 소유권을 확인하고 싶거나 관리되는 기존 디렉터리에 새 도메인을 추가할 수 있습니다. 예를 들어 contoso.com이라는 이름의 도메인을 가지고 있으며 contoso.co.uk 또는 contoso.uk라는 이름의 새 도메인을 추가하려고 합니다.

## <a name="what-is-domain-takeover"></a>도메인 인수란?
이 섹션에서는 도메인 소유권을 확인하는 방법을 다룹니다.

### <a name="what-is-domain-validation-and-why-is-it-used"></a>도메인 유효성 검사란 무엇이며 사용해야 하는 이유는?
Azure AD를 사용하여 디렉터리에서 작업을 수행하려면 DNS 도메인의 소유권이 있어야 합니다.  도메인의 유효성 검사를 사용하면 디렉터리를 요구하거나 셀프 서비스 디렉터리를 관리되는 디렉터리로 승격시키거나 관리되는 기존 디렉터리에 병합할 수 있습니다.

## <a name="examples-of-domain-validation"></a>도메인 유효성 검사 예
디렉터리의 DNS 인수를 수행하는 데는 다음 두 가지 방법이 있습니다.

* 내부 인수(예: 관리자가 셀프 서비스와 관리되지 않는 디렉터리를 검색하여 관리되는 디렉터리로 바꾸려고 함)
* 외부 인수(예: 관리자가 새 도메인을 관리되는 디렉터리에 추가하려고 함)

### <a name="internal-takeover---promote-a-self-service-unmanaged-directory-to-be-a-managed-directory"></a>내부 인수 - 셀프 서비스, 관리되지 않는 디렉터리를 관리되는 디렉터리로 승격
내부 인수를 수행하면 관리되지 않는 디렉터리가 관리되는 디렉터리로 변환됩니다. DNS 도메인 이름 유효성 검사를 완료해야 합니다. DNS 영역에 MX 레코드 또는 TXT 레코드를 만드는 경우 다음을 수행합니다.

* 도메인 소유 확인
* 관리되는 디렉터리 만들기
* 디렉터리의 전역 관리자가 됨

Bellows College의 IT 관리자가 셀프 서비스 제공 사항에 등록한 교내 사용자를 찾았다고 가정해 보겠습니다. IT 관리자는 BellowsCollege.com이란 DNS 이름의 등록된 소유자로서 Azure에서 DNS 이름의 소유권을 확인한 다음 관리되지 않는 디렉터리를 인수할 수 있습니다. 그러면 이 디렉터리는 관리되는 디렉터리가 되며 IT 관리자에게 BellowsCollege.com 디렉터리에 대한 전역 관리자 역할이 할당됩니다.

### <a name="external-takeover---merge-a-self-service-directory-into-an-existing-managed-directory"></a>외부 인수 - 셀프 서비스 디렉터리를 관리되는 기존 디렉터리에 병합
외부 인수의 경우 이미 관리되는 디렉터리가 있으며 두 개의 별도의 디렉터리를 소유하는 대신 관리되지 않는 디렉터리의 모든 사용자 및 그룹을 관리되는 디렉터리에 결합시키려고 합니다.

관리되는 디렉터리의 관리자는 도메인을 추가하며 이 도메인은 연결된 관리되지 않는 디렉터리를 가지게 됩니다.

예를 들어 IT 관리자이며 조직에 등록된 도메인 이름인 Contoso.com의 관리되는 디렉터리를 가지고 있다고 가정해 보겠습니다. 조직이 소유한 다른 도메인 이름인 user@contoso.co.uk,라는 메일 도메인 이름을 사용하여 셀프 서비스 제공 사항에 등록한 조직 사용자를 발견합니다. 이 사용자는 현재 contoso.co.uk의 관리되지 않는 디렉터리에 계정이 있습니다.

두 개의 디렉터리를 별도로 관리하고 싶지 않으므로 contoso.co.uk의 관리되지 않는 디렉터리를 contoso.com의 IT 관리되는 기존 디렉터리에 병합합니다.

외부 인수는 내부 인수와 동일한 DNS 유효성 검사 프로세스를 따릅니다.  차이: 사용자와 서비스는 IT 관리되는 디렉터리에 다시 매핑됩니다.

#### <a name="whats-the-impact-of-performing-an-external-takeover"></a>외부 인수 수행 효과는 무엇인가요?
외부 인수를 통해 사용자에서 리소스로 매핑되어 사용자가 중단 없이 서비스에 계속 액세스할 수 있습니다. 개인용 RMS를 포함한 많은 응용 프로그램이 사용자에서 리소스로의 매핑을 잘 처리하므로 사용자가 변경하지 않고 이러한 서비스에 계속 액세스할 수 있습니다. 응용 프로그램이 사용자에서 리소스로의 매핑을 효과적으로 처리하지 않을 경우 외부 인수는 사용자에게 만족스러운 환경을 제공할 수 없습니다.

#### <a name="directory-takeover-support-by-service"></a>디렉터리 인수 지원 서비스
현재 인수를 지원하는 서비스:

* RMS

인수를 곧 지원할 예정인 서비스:

* PowerBI

다음 서비스는 외부 인수 후 사용자 데이터를 마이그레이션하기 위한 관리자 추가 작업이 필요 없습니다.

* SharePoint/OneDrive

## <a name="how-to-perform-a-dns-domain-name-takeover"></a>DNS 도메인 이름 인수를 수행하는 방법
도메인 유효성 검사를 수행하는 방법으로 다음과 같은 몇 가지 옵션이 있습니다(원하는 경우 인수 수행).

1. Azure 관리 포털

   도메인을 추가하면 인수가 트리거됩니다.  해당 도메인에 대한 디렉터리가 이미 존재하면 외부 인수를 수행할 수 있습니다.

   자격 증명을 사용하여 Azure 포털에 로그인합니다.  기존 디렉터리로 이동한 다음 **도메인 추가**로 이동합니다.
2. Office 365

   Office 365의 [도메인 관리](https://support.office.com/article/Navigate-to-the-Office-365-Manage-domains-page-026af1f2-0e6d-4f2d-9b33-fd147420fac2/) 페이지에 있는 옵션을 사용하여 도메인 및 DNS 레코드를 사용할 수 있습니다. [Office 365에서 도메인 확인](https://support.office.com/article/Verify-your-domain-in-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611/)을 참조하세요.
3. Windows PowerShell

   Windows PowerShell을 사용하여 유효성 검사를 수행하려면 다음 단계가 필요합니다.

   | 단계 | 사용할 cmdlet |
   | --- | --- |
   | 자격 증명 개체 만들기 |Get-Credential |
   | Azure에 연결 |Connect-MsolService |
   | 도메인 목록 가져오기 |Get-MsolDomain |
   | 챌린지 만들기 |Get-MsolDomainVerificationDns |
   | DNS 레코드 만들기 |DNS 서버에서 수행 |
   | 챌린지 확인 |Confirm-MsolEmailVerifiedDomain |

예:

1. 셀프 서비스 제공 사항에 응답하는 데 사용된 자격 증명을 사용하여 Azure AD에 연결합니다.

        import-module MSOnline
        $msolcred = get-credential
        connect-msolservice -credential $msolcred
2. 도메인 목록을 가져옵니다.

    Get-MsolDomain
3. 그런 다음 Get-MsolDomainVerificationDns cmdlet을 실행하여 챌린지를 만듭니다.

    Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord

    예:

    Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
4. 이 명령에서 반환되는 값(챌린지)을 복사합니다.

    예:

    MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
5. 공용 DNS 네임스페이스에서 이전 단계에서 복사한 값이 포함된 DNS txt 레코드를 만듭니다.

    이 레코드의 이름은 부모 도메인의 이름이므로 Windows Server의 DNS 역할을 사용하여 이 리소스 레코드를 만드는 경우 레코드 이름은 비워두고 값을 텍스트 상자에 붙여넣기만 하세요.
6. Confirm-MsolDomain cmdlet을 실행하여 챌린지를 확인합니다.

    Confirm-MsolEmailVerifiedDomain -DomainName *your_domain_name*

    예:

    Confirm-MsolEmailVerifiedDomain -DomainName contoso.com

챌린지가 성공하면 오류 없이 프롬프트로 돌아갑니다.

## <a name="how-do-i-control-self-service-settings"></a>셀프 서비스 설정을 제어하는 방법
관리자는 현재 두 개의 셀프 서비스 컨트롤을 보유하며 다음을 제어할 수 있습니다.

* 사용자가 메일을 통해 디렉터리를 결합할 수 있는지 여부
* 응용 프로그램 및 서비스를 사용자 자신이 사용하도록 허가할 수 있는지 여부

### <a name="how-can-i-control-these-capabilities"></a>이러한 기능은 어떻게 제어할 수 있나요?
관리자는 Azure AD cmdlet인 Set-MsolCompanySettings 매개 변수를 사용하여 이러한 기능을 구성할 수 있습니다.

* **AllowEmailVerifiedUsers** 는 관리되지 않는 디렉터리를 만들거나 결합시킬 수 있는지 여부를 제어합니다. 이 매개 변수를 $false로 설정하면 메일로 확인된 사용자가 디렉터리를 결합시킬 수 없습니다.
* **AllowAdHocSubscriptions** 는 사용자의 셀프 서비스 등록 수행을 제어합니다. 이 매개 변수를 $false로 설정하면 사용자가 셀프 서비스 등록을 수행할 수 없습니다.

### <a name="how-do-the-controls-work-together"></a>컨트롤이 어떻게 작동하나요?
이 두 매개 변수를 함께 사용하여 정의하면 셀프 서비스 등록을 더욱 세밀하게 제어할 수 있습니다. 예를 들어 Azure AD에 이미 계정이 있는 사용자만 다음 명령을 사용하여 셀프 서비스 등록을 수행할 수 있습니다. 즉, 메일로 확인된 계정을 만들어야 하는 사용자는 셀프 서비스 등록을 수행할 수 없습니다.

    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true

다음 순서도는 디렉터리와 셀프 서비스 등록에 대한 이러한 매개 변수 및 그 결과 조건의 다양한 모든 조합을 설명합니다.

![][1]

매개 변수 사용 방법에 대한 자세한 내용 및 예는 [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx)를 참조하세요.

## <a name="see-also"></a>참고 항목
* [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azureps-cmdlets-docs)
* [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
* [Azure Cmdlet 참조](https://msdn.microsoft.com/library/azure/jj554330.aspx)
* [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png



<!--HONumber=Feb17_HO2-->



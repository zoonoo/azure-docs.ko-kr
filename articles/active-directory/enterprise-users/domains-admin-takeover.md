---
title: 관리 되지 않는 디렉터리의 관리자 인수-Azure AD | Microsoft Docs
description: 관리 되지 않는 Azure AD 조직 (섀도 테 넌 트)에서 DNS 도메인 이름을 사용 하는 방법입니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0472b2adb3213338b9fbc4e3a17a2c3444eb113
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647582"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Microsoft Azure Active Directory에서 관리자로서 관리되지 않는 디렉터리 인수

이 문서에서는 Azure Active Directory (Azure AD)에서 관리 되지 않는 디렉터리의 DNS 도메인 이름을 인수할 두 가지 방법을 설명합니다. 셀프 서비스 사용자가 Microsoft AD를 사용하는 클라우드 서비스에 등록할 때, 전자 메일 도메인에 기반하여 관리되지 않는 Microsoft Azure AD 디렉토리에 추가됩니다. 셀프 서비스 또는 서비스에 대 한 "바 이럴" 등록에 대 한 자세한 내용은 [Azure Active Directory에 대 한 셀프 서비스 등록 이란?](directory-self-service-signup.md) 을 참조 하세요.

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>관리되지 않는 디렉터리를 인수하고자 하는 방법을 결정합니다.
관리자 인수 과정 중에 [Microsoft Azure AD에 사용자 지정 도메인 이름 추가](../fundamentals/add-custom-domain.md)에서 설명된 대로 소유권을 증명할 수 있습니다. 다음 섹션에서 관리자 환경을 보다 자세히 설명하지만 요약 내용은 다음과 같습니다.

* 관리 되지 않는 Azure 디렉터리의 ["내부" 관리자 인수](#internal-admin-takeover) 를 수행 하는 경우 관리 되지 않는 디렉터리의 전역 관리자로 추가 됩니다. 어떤 사용자, 도메인 또는 서비스 계획도 새 관리자가 관리하는 다른 디렉터리로 마이그레이션되지 않습니다.

* 관리 되지 않는 Azure 디렉터리의 ["외부" 관리자 인수](#external-admin-takeover) 를 수행 하는 경우 관리 되지 않는 디렉터리의 DNS 도메인 이름을 관리 되는 azure 디렉터리에 추가 합니다. 도메인 이름을 추가하면 사용자에서 리소스로 매핑이 관리되는 Azure 디렉터리에 생성되어 사용자가 중단 없이 서비스에 계속 액세스할 수 있습니다. 

## <a name="internal-admin-takeover"></a>내부 관리자 인수

Microsoft 365와 같이 SharePoint 및 OneDrive를 포함 하는 일부 제품은 외부 인수을 지원 하지 않습니다. 사용자의 시나리오 이거나 관리자 이며 셀프 서비스 등록을 사용 하는 사용자가 만든 관리 되지 않거나 "섀도" Azure AD 조직을 사용 하려는 경우에는 내부 관리자 인수을 사용 하 여이 작업을 수행할 수 있습니다.

1. Power BI에 등록 하 여 관리 되지 않는 조직에서 사용자 컨텍스트를 만듭니다. 예제의 편의를 위해 이러한 단계는 해당 경로를 가정합니다.

2. [Power BI 사이트](https://powerbi.com)를 열고 **무료로 시작** 을 선택합니다. 조직에 대한 도메인 이름을 사용하는 사용자 계정을 입력합니다. 예: `admin@fourthcoffee.xyz`. 확인 코드를 입력한 후 인증 코드에 대한 전자 메일을 확인합니다.

3. Power BI에서 온 확인 전자 메일에서 **예, 바로 저입니다** 를 선택합니다.

4. Power BI 사용자 계정을 사용 하 여 [Microsoft 365 관리 센터](https://portal.office.com/admintakeover) 에 로그인 합니다. 관리 되지 않는 조직에서 이미 확인 된 도메인 이름의 **관리자** 가 되도록 지시 하는 메시지가 표시 됩니다. **예, 관리자가 되고 싶습니다** 를 선택합니다.
  
   ![관리자 되기에 대한 첫 번째 스크린샷](./media/domains-admin-takeover/become-admin-first.png)
  
5. 도메인 이름 등록자에서 도메인 이름 **fourthcoffee.xyz** 을 소유하고 있음을 증명하기 위해 TXT 레코드를 추가합니다. 이 예제에서는 GoDaddy.com입니다.
  
   ![도메인 이름에 대한 TXT 레코드 추가](./media/domains-admin-takeover/become-admin-txt-record.png)

DNS TXT 레코드를 도메인 이름 등록 기관에서 확인 하는 경우 Azure AD 조직을 관리할 수 있습니다.

앞의 단계를 완료 하면 Microsoft 365에서 네 번째 커피 조직의 전역 관리자가 됩니다. 도메인 이름을 다른 Azure 서비스와 통합 하려면 Microsoft 365에서 제거 하 여 Azure의 다른 관리 되는 조직에 추가 합니다.

### <a name="adding-the-domain-name-to-a-managed-organization-in-azure-ad"></a>Azure AD에서 관리 되는 조직에 도메인 이름 추가

1. [Microsoft 365 관리 센터](https://admin.microsoft.com)를 엽니다.
2. 사용자 **탭을** 선택 하 고 사용자 지정 도메인 이름을 사용 하지 않는 *사용자 \@ fourthcoffeexyz.onmicrosoft.com* 같은 이름으로 새 사용자 계정을 만듭니다. 
3. 새 사용자 계정에 Azure AD 조직에 대 한 전역 관리자 권한이 있는지 확인 합니다.
4. Microsoft 365 관리 센터에서 **도메인** 탭을 열고 도메인 이름을 선택한 다음 **제거** 를 선택 합니다. 
  
   ![Microsoft 365에서 도메인 이름 제거](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. 제거 된 도메인 이름을 참조 하는 Microsoft 365의 사용자 또는 그룹이 있는 경우 해당 이름을 onmicrosoft.com 도메인으로 변경 해야 합니다. 강제로 도메인 이름을 삭제 하면이 예제에서 *사용자 \@ fourthcoffeexyz.onmicrosoft.com* 에 모든 사용자의 이름이 자동으로 바뀝니다.
  
6. Azure ad 조직에 대 한 전역 관리자 인 계정으로 [AZURE ad 관리 센터](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 에 로그인 합니다.
  
7. **사용자 지정 도메인 이름** 을 선택하고 도메인 이름을 추가합니다. 도메인 이름의 소유권을 확인하려면 DNS TXT 레코드를 입력해야 합니다. 
  
   ![Azure AD에 추가 된 것으로 확인 된 도메인](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Microsoft 365 조직에 할당 된 라이선스가 있는 Power BI 또는 Azure Rights Management 서비스의 사용자는 도메인 이름을 제거 하는 경우 해당 대시보드를 저장 해야 합니다. 사용자 *\@ fourthcoffee* 대신 사용자 *\@ fourthcoffeexyz.onmicrosoft.com* 같은 사용자 이름으로 로그인 해야 합니다.

## <a name="external-admin-takeover"></a>외부 관리자 인수

이미 Azure 서비스 또는 Microsoft 365를 사용 하 여 조직을 관리 하는 경우 다른 Azure AD 조직에서 이미 확인 된 경우 사용자 지정 도메인 이름을 추가할 수 없습니다. 그러나 Azure AD의 관리 되는 조직에서는 외부 관리자 인수 관리 되지 않는 조직을 사용할 수 있습니다. 일반적인 절차는 [Microsoft Azure AD에 사용자 지정 도메인 추가](../fundamentals/add-custom-domain.md) 문서를 따릅니다.

도메인 이름의 소유권을 확인 하는 경우 Azure AD는 관리 되지 않는 조직에서 도메인 이름을 제거 하 고 기존 조직으로 이동 합니다. 관리되지 않는 디렉터리의 외부 관리자 인수를 하려면 내부 관리자 인수와 동일한 DNS TXT 유효성 검사 프로세스를 필요로 합니다. 차이점은 또한 다음을 도메인 이름과 함께 이동할 수 있습니다.

- 사용자
- 구독
- 라이선스 할당

### <a name="support-for-external-admin-takeover"></a>외부 관리자 인수에 대한 지원
외부 관리자 인수는 다음과 같은 온라인 서비스에서 지원합니다.

- Azure Rights Management
- Exchange Online

지원되는 서비스 계획은 다음과 같습니다.

- PowerApps Free
- PowerFlow Free
- 개인용 RMS
- Microsoft Stream
- Dynamics 365 평가판

SharePoint, OneDrive 또는 비즈니스용 Skype를 포함 하는 서비스 계획이 있는 서비스에 대해서는 외부 관리자 인수 지원 되지 않습니다. 예를 들어, Office 무료 구독을 사용 합니다. 

필요에 따라 [ **ForceTakeover** 옵션](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) 을 사용 하 여 관리 되지 않는 조직에서 도메인 이름을 제거 하 고 원하는 조직에서 도메인 이름을 확인할 수 있습니다. 

#### <a name="more-information-about-rms-for-individuals"></a>개인용 RMS에 대한 자세한 내용

개인용 [RMS](/azure/information-protection/rms-for-individuals)의 경우 관리 되지 않는 조직이 소유 하 고 있는 조직과 동일한 지역에 있는 경우 자동으로 생성 된 [Azure Information Protection 조직 키](/azure/information-protection/plan-implement-tenant-key) 와 [기본 보호 템플릿이](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) 도메인 이름으로 추가로 이동 됩니다.

관리 되지 않는 조직이 다른 지역에 있으면 키와 템플릿이 이동 하지 않습니다. 예를 들어 관리 되지 않는 조직이 유럽에 있고 소유 하 고 있는 조직이 북아메리카 경우입니다.

개인용 RMS는 보호된 콘텐츠를 열기 위한 Azure AD 인증을 지원하도록 디자인되었지만, 사용자의 콘텐츠 보호를 방지하지 못 합니다. 사용자가 개인용 RMS 구독을 사용 하 여 콘텐츠를 보호 하 고 키와 템플릿을 이동 하지 않은 경우 도메인 인수 후 해당 콘텐츠에 액세스할 수 없습니다.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>ForceTakeover 옵션에 대한 Microsoft Azure AD PowerShell cmdlets
[PowerShell 예](#powershell-example)에서 사용되는 이러한 cmdlet을 참조할 수 있습니다.

Cmdlet | 사용량
------- | -------
`connect-msolservice` | 메시지가 표시 되 면 관리 되는 조직에 로그인 합니다.
`get-msoldomain` | 현재 조직과 연결 된 도메인 이름을 표시 합니다.
`new-msoldomain –name <domainname>` | 조직에 도메인 이름을 확인 되지 않음으로 추가 합니다 (DNS 확인이 아직 수행 되지 않음).
`get-msoldomain` | 이제 도메인 이름이 관리 되는 조직과 연결 된 도메인 이름 목록에 포함 되어 있지만 확인 되지 **않음으로 표시 됩니다.**
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | 도메인에 대해 새 DNS TXT 레코드를 저장할 정보 제공 (MS = xxxxx). TXT 레코드가 전파되는 데 약간의 시간이 걸리므로 확인이 즉시 이뤄지지 않을 수도 있습니다. 따라서 **-ForceTakeover** 옵션을 고려하기 전에 몇 분 정도 기다리십시오. 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>여전히 도메인 이름이 확인되지 않는 경우 **-ForceTakeover** 옵션을 사용하여 진행할 수 있습니다. TXT 레코드가 만들어졌는지 확인하고 인수 프로세스를 시작합니다.<li>**ForceTakeover** 옵션은 관리 되지 않는 조직에 인수를 차단 하는 Microsoft 365 서비스가 있는 경우와 같이 외부 관리자 인수을 적용 하는 경우에만 cmdlet에 추가 해야 합니다.
`get-msoldomain` | 이제 도메인 목록은 도메인 이름을 **확인됨** 으로 표시합니다.

> [!NOTE]
> 관리 되지 않는 Azure AD 조직은 external 인수 force 옵션을 실행 한 후 10 일 후에 삭제 됩니다.

### <a name="powershell-example"></a>PowerShell 예제

1. 셀프 서비스 제공 사항에 응답하는 데 사용된 자격 증명을 사용하여 Azure AD에 연결합니다.
   ```powershell
   Install-Module -Name MSOnline
   $msolcred = get-credential
    
   connect-msolservice -credential $msolcred
   ```
2. 도메인 목록을 가져옵니다.
  
   ```powershell
   Get-MsolDomain
   ```
3. Get-MsolDomainVerificationDns cmdlet을 실행하여 챌린지를 만듭니다.
   ```powershell
   Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
   ```
    예를 들어:
   ```
   Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. 이 명령에서 반환되는 값(챌린지)을 복사합니다. 예를 들어:
   ```powershell
   MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. 공용 DNS 네임스페이스에서 이전 단계에서 복사한 값이 포함된 DNS txt 레코드를 만듭니다. 이 레코드의 이름은 부모 도메인의 이름이므로 Windows Server의 DNS 역할을 사용하여 이 리소스 레코드를 만드는 경우 레코드 이름은 비워두고 값을 텍스트 상자에 붙여넣기만 하세요.
6. Confirm-MsolDomain cmdlet을 실행하여 챌린지를 확인합니다.
  
   ```powershell
   Confirm-MsolDomain –DomainName *your_domain_name* –ForceTakeover Force
   ```
  
   예를 들어:
  
   ```powershell
   Confirm-MsolDomain –DomainName contoso.com –ForceTakeover Force
   ```

챌린지가 성공하면 오류 없이 프롬프트로 돌아갑니다.

## <a name="next-steps"></a>다음 단계

* [Microsoft Azure AD에 사용자 지정 도메인 이름 추가](../fundamentals/add-custom-domain.md)
* [Azure PowerShell 설치 및 구성하는 방법](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Azure Cmdlet 참조](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0&preserve-view=true)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png

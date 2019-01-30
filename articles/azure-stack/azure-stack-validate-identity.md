---
title: Azure Stack에 대 한 Azure Id 유효성 검사 | Microsoft Docs
description: Azure Stack 준비 검사를 사용 하 여 Azure id를 확인 합니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/04/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/04/2018
ms.openlocfilehash: 4fb636a91389309b44f2308efec1a6c257c41078
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242589"
---
# <a name="validate-azure-identity"></a>Azure id의 유효성을 검사합니다 
Azure Active Directory (Azure AD) Azure Stack과 함께 사용할 준비가 되었는지 유효성을 검사 하려면 (AzsReadinessChecker) Azure Stack 준비 검사기 도구를 사용 합니다. Azure Stack 배포를 시작 하기 전에 Azure id 솔루션의 유효성을 검사 합니다.  

준비 상태 검사기의 유효성을 검사 합니다.
 - Azure Active Directory (Azure AD)를 id 공급자로 Azure Stack에 대 한 합니다.
 - 사용 하려는 Azure AD 계정을 Azure Active Directory의 전역 관리자로 로그온 수 있습니다. 

유효성 검사에 환경이 Azure AD에서 사용자, 응용 프로그램, 그룹 및 Azure Stack에서 서비스 주체에 대 한 정보를 저장할 Azure Stack에 대 한 준비가 되었는지 확인 합니다.

## <a name="get-the-readiness-checker-tool"></a>준비 검사 도구
(AzsReadinessChecker) Azure Stack 준비 검사 도구의 최신 버전을 다운로드 합니다 [PSGallery](https://aka.ms/AzsReadinessChecker)합니다.  

## <a name="prerequisites"></a>필수 조건
다음 필수 구성 요소가 준비 되어야 합니다.

**도구가 실행 되는 컴퓨터:**
 - Windows 10 또는 Windows Server 2016에서는 인터넷에 연결 합니다.
 - PowerShell 5.1 이상 버전을 확인 하려면 다음 PowerShell cmd를 실행 하 고 검토 합니다 *주요* 버전 및 *부* 버전:  

   > `$PSVersionTable.PSVersion`
 - 구성할 [Azure Stack 용 PowerShell](azure-stack-powershell-install.md)합니다. 
 - 최신 버전의 [Microsoft Azure Stack 준비 검사](https://aka.ms/AzsReadinessChecker) 도구입니다.

**Azure Active Directory 환경:**
 - Azure Stack에 대 한 사용 되며, Azure Active Directory 전역 관리자가 Azure AD 계정을 식별 합니다.
 - Azure AD 테 넌 트 이름을 식별 합니다. 테 넌 트 이름 이어야 합니다는 *기본* Azure Active Directory에 대 한 도메인 이름입니다. 예를 들어 *contoso.onmicrosoft.com*합니다. 
 - 사용 하 여 AzureEnvironment를 식별 합니다. 환경 이름 매개 변수에 대해 지원 되는 값 AzureCloud, AzureChinaCloud, AzureUSGovernment를 사용 하는 Azure 구독에 따라 됩니다.

## <a name="validate-azure-identity"></a>Azure id의 유효성을 검사합니다 
1. 필수 구성 요소를 충족 하는 컴퓨터에서 관리자 PowerShell 프롬프트를 열고 AzsReadinessChecker를 설치 하려면 다음 명령을 실행:  

   > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. PowerShell 프롬프트에서 설정 하려면 다음을 실행 *$serviceAdminCredential* 서비스 관리자 Azure AD 테 넌 트에 대 한 합니다.  바꿉니다 *serviceadmin@contoso.onmicrosoft.com* 계정 및 테 넌 트입니다. 
   > `$serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant"` 

3. PowerShell 프롬프트에서 Azure AD의 유효성 검사를 시작 하려면 다음을 실행 합니다. 
   - AzureEnvironment 환경 이름 값을 지정 합니다. 환경 이름 매개 변수에 대해 지원 되는 값 AzureCloud, AzureChinaCloud, AzureUSGovernment를 사용 하는 Azure 구독에 따라 됩니다.  
   - Azure Active Directory 테 넌 트 이름을 바꾸려면 지정할 *contoso.onmicrosoft.com*합니다. 

   > `Invoke-AzsAzureIdentityValidation -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment <environment name> -AADDirectoryTenantName contoso.onmicrosoft.com`
4. 도구를 실행 한 후 출력을 검토 합니다. 상태 확인 **확인** 설치 요구 사항에 대 한 합니다. 다음 이미지와 같이 유효성 검사를 성공적으로 표시 됩니다. 
 
```PowerShell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: OK

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```


## <a name="report-and-log-file"></a>보고서 및 로그 파일
각 시간 유효성 검사 실행, 결과를 기록 **AzsReadinessChecker.log** 하 고 **AzsReadinessCheckerReport.json**합니다. PowerShell에서 유효성 검사 결과 사용 하 여 이러한 파일의 위치를 표시합니다.

이러한 파일 도움이 유효성 검사 문제를 조사 하거나 Azure Stack을 배포 하기 전에 유효성 검사 상태를 공유 합니다.  두 파일의 각 후속 유효성 검사 결과 유지합니다. 보고서 id 구성의 배포 팀 확인을 제공합니다. 로그 파일에는 유효성 검사 문제를 조사 하는 배포 또는 지원의 팀원 데 도움이 됩니다. 

기본적으로 두 파일에 기록 됩니다 *C:\Users\<사용자 이름 > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*합니다.  
 - 사용 된 **-OutputPath** ***&lt;경로&gt;*** 끝에 다른 보고서 위치를 지정 하려면 실행된 명령줄 매개 변수입니다.   
 - 사용 된 **-CleanReport** 매개 변수에서 정보를 지우려면 실행 명령의 끝 *AzsReadinessCheckerReport.json*합니다.  이전에 대 한 도구를 실행합니다. 

자세한 내용은 [Azure Stack 유효성 검사 보고서](azure-stack-validation-report.md)합니다.

## <a name="validation-failures"></a>유효성 검사 실패
유효성 검사에 실패 하는 경우 PowerShell 창에서 오류에 대 한 세부 정보 표시. 또한 도구는 AzsReadinessChecker.log에 정보를 기록합니다.

다음 예제에서는 일반적인 유효성 검사 오류에 지침을 제공 합니다.

### <a name="expired-or-temporary-password"></a>만료 된 또는 임시 암호 
 
```PowerShell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail 
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The password for account  has expired or is a temporary password that needs to be reset before continuing. Run Login-AzureRMAccount, login with  credentials and follow the prompts to reset.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```
**원인** -계정 로그온 없습니다 암호는 만료 되었거나 일시적입니다.     

**해상도** -PowerShell에서 다음을 실행 하 고 다음 지시에 따라 암호를 다시 설정 합니다.  
> `Login-AzureRMAccount`

에 로그인 또는 https://portal.azure.com 계정 및 사용자 암호를 변경 하려면 적용할 수 됩니다.
### <a name="unknown-user-type"></a>알 수 없는 사용자 유형 
 
```PowerShell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail 
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
Unknown user type detected. Check the account  is valid for AzureChinaCloud
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```
**원인** -계정이 지정된 된 Azure Active Directory (AADDirectoryTenantName)에 로그온 할 수 있습니다. 이 예에서 *AzureChinaCloud* 로 지정 합니다 *AzureEnvironment*합니다.

**해상도** -지정 된 Azure 환경에 대 한 유효한 계정 인지 확인 합니다. PowerShell에서 특정 환경에 대 한 계정이 올바른지 확인 하려면 다음을 실행 합니다.   Login-azurermaccount-EnvironmentName AzureChinaCloud 
### <a name="account-is-not-an-administrator"></a>계정은 관리자가 아닙니다. 
 
```PowerShell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail 
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The Service Admin account you entered 'admin@contoso.onmicrosoft.com' is not an administrator of the Azure Active Directory tenant 'contoso.onmicrosoft.com'.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**원인** -계정이 Azure Active Directory (AADDirectoryTenantName)의 관리자가 아닌 계정 로그온 할 수 있지만.  

**해상도** -로그인 https://portal.azure.com 계정으로 이동 **Azure Active Directory** > **사용자가** > *사용자선택*  >  **디렉터리 역할**, 사용자가 확인 된 **전역 관리자**합니다.  계정 사용자 인 경우 이동할 **Azure Active Directory** > **사용자 지정 도메인** 이름, 및에 대 한 제공 이름 확인 *AADDirectoryTenantName* 됩니다 이 디렉터리에 대 한 주 도메인 이름으로 표시 합니다.  이 예에서 *contoso.onmicrosoft.com*합니다. 

Azure Stack 도메인 이름을 주 도메인 이름이 필요 합니다.

## <a name="next-steps"></a>다음 단계
[Azure 등록 유효성 검사](azure-stack-validate-registration.md)  
[준비 보고서를 보려면](azure-stack-validation-report.md)  
[일반 Azure Stack 통합 고려 사항](azure-stack-datacenter-integration.md)  


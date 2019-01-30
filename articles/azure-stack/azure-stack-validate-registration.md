---
title: Azure Stack에 대 한 Azure 등록 유효성 검사 | Microsoft Docs
description: Azure Stack 준비 검사를 사용 하 여 Azure 등록의 유효성을 검사 합니다.
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
ms.openlocfilehash: 0281186be4fae71c56b65327e0c67da3f252a33c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239522"
---
# <a name="validate-azure-registration"></a>Azure 등록 유효성 검사
 
Azure 구독의 Azure Stack과 함께 사용할 준비가 되었는지 유효성을 검사 하려면 (AzsReadinessChecker) Azure Stack 준비 검사기 도구를 사용 합니다. Azure Stack 배포를 시작 하기 전에 등록을 확인 합니다. 준비 검사 하는지 확인 합니다.

- 사용할 Azure 구독에 지원 되는 형식입니다. 클라우드 서비스 공급자 (CSP) 또는 EA (기업 계약)를 구독 해야 합니다. 
- Azure 구독을 등록 하는 데 사용할 계정 Azure에 로그인 할 수 이며 구독 소유자입니다. 

Azure Stack 등록에 대 한 자세한 내용은 참조 하세요. [Azure 사용 하 여 Azure Stack 등록](azure-stack-registration.md)합니다. 

## <a name="get-the-readiness-checker-tool"></a>준비 검사 도구

(AzsReadinessChecker) Azure Stack 준비 검사 도구의 최신 버전을 다운로드 합니다 [PowerShell 갤러리](https://aka.ms/AzsReadinessChecker)합니다.  

## <a name="prerequisites"></a>필수 조건

다음 필수 구성 요소가 준비 되어야 합니다.

**도구가 실행 되는 컴퓨터:**
 - Windows 10 또는 Windows Server 2016에서는 인터넷에 연결 합니다.
 - PowerShell 5.1 이상 버전을 확인 하려면 다음 PowerShell cmdlet을 실행 하 고 검토 합니다 *주요* 하 고 *부* 버전:  

    ```powershell
    $PSVersionTable.PSVersion
    ``` 
 - 구성할 [Azure Stack 용 PowerShell](azure-stack-powershell-install.md)합니다. 
 - 최신 버전을 다운로드 합니다 [Microsoft Azure Stack 준비 검사](https://aka.ms/AzsReadinessChecker) 도구입니다.  

**Azure Active Directory 환경:**
 - 사용자 이름 및 Azure Stack과 함께 사용 하 여 Azure 구독에 대 한 소유자가 계정의 암호를 식별 합니다.  
 - 사용 하 여 Azure 구독에 대 한 구독 ID를 식별 합니다. 
 - 식별 된 **AzureEnvironment** 사용 합니다. 환경 name 매개 변수에 대해 지원 되는 값은 **AzureCloud**, **AzureChinaCloud** 하거나 **AzureUSGovernment**, 사용 중인 Azure 구독에 따라 합니다.

## <a name="validate-azure-registration"></a>Azure 등록 유효성 검사

1. 필수 구성 요소를 충족 하는 컴퓨터를 관리 PowerShell 프롬프트를 열고 설치 하려면 다음 명령을 실행 합니다 **AzsReadinessChecker**합니다.

    ```powershell
    Install-Module Microsoft.AzureStack.ReadinessChecker -Force
    ```

2. PowerShell 프롬프트에서 설정 하려면 다음을 실행 `$registrationCredential` 구독 소유자 인 계정으로 합니다. 대체 `subscriptionowner@contoso.onmicrosoft.com` 계정 및 테 넌 트: 
   ```powershell
   $registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"
   ```

3. PowerShell 프롬프트에서 설정 하려면 다음을 실행 `$subscriptionID` 사용 하 여 Azure 구독으로 합니다. 대체 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 사용자 고유의 구독 id:
   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ``` 

4. PowerShell 프롬프트에서 구독의 유효성 검사를 시작 하려면 다음을 실행 합니다. 
   - AzureEnvironment으로 값을 지정 **AzureCloud**를 **AzureGermanCloud**, 또는 **AzureChinaCloud**합니다.  
   - Azure Active Directory 관리자와 Azure Active Directory 테 넌 트 이름을 제공 합니다. 

   ```powershell
   Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID
   ```

5. 도구를 실행 한 후 출력을 검토 합니다. 상태는 로그온과 등록 요구 사항이 모두에 대 한 확인을 확인 합니다. 유효성 검사를 성공적으로 다음 예제와 유사합니다.
  
   ```shell
   Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
   Checking Registration Requirements: OK
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```

## <a name="report-and-log-file"></a>보고서 및 로그 파일

각 시간 유효성 검사 실행, 결과를 기록 **AzsReadinessChecker.log** 하 고 **AzsReadinessCheckerReport.json**합니다. PowerShell에서 유효성 검사 결과 사용 하 여 이러한 파일의 위치를 표시합니다. 

이러한 파일 도움이 유효성 검사 문제를 조사 하거나 Azure Stack을 배포 하기 전에 유효성 검사 상태를 공유 합니다. 두 파일의 각 후속 유효성 검사 결과 유지합니다. 보고서 id 구성의 배포 팀 확인을 제공합니다. 로그 파일에는 유효성 검사 문제를 조사 하는 배포 또는 지원의 팀원 데 도움이 됩니다. 

기본적으로 두 파일에 기록 됩니다 *C:\Users\<사용자 이름 > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*합니다.  
 - 사용 된 **-OutputPath** ***&lt;경로&gt;*** 끝에 다른 보고서 위치를 지정 하려면 실행된 명령줄 매개 변수입니다.   
 - 사용 된 **-CleanReport** 매개 변수에서 정보를 지우려면 실행 명령의 끝 *AzsReadinessCheckerReport.json*합니다.  이전에 대 한 도구를 실행합니다. 자세한 내용은 [Azure Stack 유효성 검사 보고서](azure-stack-validation-report.md)합니다.

## <a name="validation-failures"></a>유효성 검사 실패
유효성 검사에 실패 하는 경우 PowerShell 창에서 오류에 대 한 세부 정보 표시. 이 도구는 또한 AzsReadinessChecker.log 파일에 정보를 기록합니다.

다음 예제에서는 일반적인 유효성 검사에 실패 한 지침을 제공 합니다.

### <a name="user-must-be-an-owner-of-the-subscription"></a>사용자 구독 소유자 여야 합니다.   

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
The user admin@contoso.onmicrosoft.com is role(s) Reader for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d. User must be an owner of the subscription to be used for registration.
Additional help URL https://aka.ms/AzsRemediateRegistration

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**원인** -계정이 Azure 구독 관리자가 아닙니다.   

**해상도** -Azure Stack 배포의 사용량에 대 한 청구는 Azure 구독의 관리자 인 계정을 사용 합니다.

### <a name="expired-or-temporary-password"></a>만료 된 또는 임시 암호
 
```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription [subscription ID] using account admin@contoso.onmicrosoft.com failed with AADSTS50055: Force Change P
assword.
Trace ID: [Trace ID]
Correlation ID: [Correlation ID]
Timestamp: 2018-10-22 11:16:56Z: The remote server returned an error: (401) Unauthorized.

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**원인** -계정 로그온 없습니다 암호는 만료 되었거나 일시적입니다.     

**해상도** -PowerShell에서를 실행 및 지시에 따라 암호를 다시 설정 합니다. 

```powershell
Login-AzureRMAccount
``` 

에 로그인 또는 https://portal.azure.com 계정 및 사용자 암호를 변경 하려면 적용할 수 됩니다.

### <a name="unknown-user-type"></a>알 수 없는 사용자 유형  

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription <subscription ID> using <account> failed with unknown_user_type: Unknown User Type

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**원인** -계정이 지정 된 Azure Active Directory 환경에 로그온 할 수 없습니다. 이 예에서 *AzureChinaCloud* 로 지정 합니다 *AzureEnvironment*합니다.  

**해상도** -지정 된 Azure 환경에 대 한 유효한 계정 인지 확인 합니다. PowerShell에서 특정 환경에 대 한 계정이 올바른지 확인 하려면 다음을 실행 합니다.
     
```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

## <a name="next-steps"></a>다음 단계

- [Azure id의 유효성을 검사합니다](azure-stack-validate-identity.md)
- [준비 보고서를 보려면](azure-stack-validation-report.md)
- [일반 Azure Stack 통합 고려 사항](azure-stack-datacenter-integration.md)


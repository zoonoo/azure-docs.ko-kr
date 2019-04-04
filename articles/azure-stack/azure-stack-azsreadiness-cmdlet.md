---
title: 시작-AzsReadinessChecker cmdlet 참조 | Microsoft Docs
description: Azure Stack 준비 검사 모듈에 대 한 PowerShell cmdlet 도움말입니다.
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
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/04/2018
ms.openlocfilehash: f920059f97f43a2ac3c48dad1c8f999833f6add1
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757777"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>시작-AzsReadinessChecker cmdlet 참조

모듈: **Microsoft.AzureStack.ReadinessChecker**

이 모듈에는 단일 cmdlet을 들어 있습니다. Cmdlet은 Azure Stack에 대 한 하나 이상의 배포 전 또는 미리 서비스 함수를 수행합니다.

## <a name="syntax"></a>구문

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       -Subject <OrderedDictionary>
       -RequestType <String>
       [-IncludePaaS]
       -OutputRequestPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -AADDirectoryTenantName <String>
       -IdentitySystem <String>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -ReportPath <String>
       [-ReportSections <String>]
       [-Summary]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

## <a name="description"></a>설명

합니다 **시작 AzsReadinessChecker** cmdlet은 인증서, Azure 계정, Azure 구독 및 Azure Active 디렉터리의 유효성을 검사 합니다. Azure Stack을 배포 또는 Azure Stack 비밀 회전 등의 작업을 처리 하기 전에 유효성 검사를 실행 합니다. Cmdlet는 인프라 인증서에 대 한 요청을 서명 인증서 및 필요에 따라 PaaS 인증서 생성 하려면 데도 사용할 수 있습니다. 마지막으로, cmdlet 일반적인 패키징 문제를 해결 하려면 PFX 인증서 다시 패키지 있습니다.

## <a name="examples"></a>예

### <a name="example-generate-certificate-signing-request"></a>예제: 인증서 서명 요청 생성

```powershell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

이 예에서 `Start-AzsReadinessChecker` 의 지역 이름으로는 AD FS Azure Stack 배포에 적합 한 인증서에 대 한 여러 인증서 서명 요청 (Csr)을 생성 **동부** 과의 외부 FQDN  **azurestack.contoso.com**합니다.

### <a name="example-validate-certificates"></a>예제: 인증서의 유효성 검사

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

이 예제에서는 PFX 암호를 보안을 요구 하 고 `Start-AzsReadinessChecker` 상대 폴더를 확인 **인증서** 지역 이름 사용 하 여 AAD 배포를 위한 올바른 인증서에 대 한 **동부** 및 외부 FQDN **azurestack.contoso.com**합니다.

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>예: 배포 데이터 (배포 및 지원)를 사용 하 여 인증서의 유효성을 검사합니다

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

이 예에서 배포 및 지원 PFX 암호를 보안을 요구 하 고 `Start-AzsReadinessChecker` 상대 폴더를 확인 **인증서** id, 지역 및 외부 FQDN이 있는 배포에 대 한 올바른 인증서에 대 한 배포에 대해 생성 된 배포 데이터 JSON 파일에서 읽습니다.

### <a name="example-validate-paas-certificates"></a>예: PaaS 인증서의 유효성 검사

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates – RegionName east -FQDN azurestack.contoso.com
```

이 예제에서는 해시 테이블은 경로 및 각 PaaS 인증서 암호를 사용 하 여 생성 됩니다. 인증서를 생략할 수 있습니다. `Start-AzsReadinessChecker` 각 PFX 경로 있는지 확인에 한 영역을 사용 하 여 유효성을 검사 **동부** 및 외부 FQDN **azurestack.contoso.com**합니다.

### <a name="example-validate-paas-certificates-with-deployment-data"></a>예: 배포 데이터를 사용 하 여 PaaS 인증서의 유효성을 검사합니다

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

이 예제에서는 해시 테이블은 경로 및 각 PaaS 인증서 암호를 사용 하 여 생성 됩니다. 인증서를 생략할 수 있습니다. `Start-AzsReadinessChecker` 각 PFX 경로 있고 영역을 사용 하 여 유효성을 검사 및 배포에 대해 생성 된 배포 데이터 JSON 파일에서 읽은 외부 FQDN을 확인 합니다.

### <a name="example-validate-azure-identity"></a>예: Azure id의 유효성을 검사합니다

```powershell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

이 예제에서는 서비스 관리자 계정 자격 증명은 보안을 위해 필요 하 고 `Start-AzsReadinessChecker` Azure 계정 및 Azure Active Directory의 테 넌 트 디렉터리 이름으로 AAD 배포 유효성을 검사  **azurestack.contoso.com**합니다.

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>예: 배포 데이터 (배포 지원)를 사용 하 여 Azure id의 유효성을 검사합니다

```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

이 예제에서는 서비스 관리자 계정 자격 증명은 보안을 위해 필요 하 고 `Start-AzsReadinessChecker` Azure 계정 및 Azure Active Directory는 AAD 배포에 대 한 유효성을 검사 위치 **AzureCloud** 및 **TenantName** 배포에 대해 생성 된 배포 데이터 JSON 파일에서 읽힙니다.

### <a name="example-validate-azure-registration"></a>예: Azure 등록의 유효성을 검사합니다

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

이 예제에서는 구독 소유자 자격 증명이 보안을 위해 필요 하 고 `Start-AzsReadinessChecker` 다음 지정 된 계정 및 Azure Stack 등록을 위해 사용할 수 있는지 확인 하려면 구독에 대 한 유효성 검사를 수행 합니다.

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>예: 배포 데이터 (배포 팀)를 사용 하 여 Azure 등록의 유효성을 검사합니다

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

이 예제에서는 구독 소유자 자격 증명은 보안을 위해 필요한 및 `Start-AzsReadinessChecker` 다음 구독에서 추가 세부 정보는 읽을 대상 Azure Stack 등록을 위해 사용할 수 있는지 확인 하 고 지정 된 계정에 대 한 유효성 검사를 수행 합니다 배포에 대해 생성 된 배포 데이터 JSON 파일입니다.

### <a name="example-importexport-pfx-package"></a>예: 가져오기/내보내기 PFX 패키지

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

이 예제에서는 PFX 암호가 보안을 위해 필요 합니다. Ssl.pfx 파일을 로컬 컴퓨터 인증서 저장소로 가져올에 같은 암호를 사용 하 여 다시 내보낼 및 Ssl_new.pfx로 저장 합니다. 인증서 유효성 검사 플래그가 개인 키에 없는지를 지정 하는 경우이 프로시저를 사용 합니다 **로컬 컴퓨터** 인증서 체인이 끊어집니다, 관련이 없는 인증서가 PFX에 있는지 또는 인증서 체인을 특성 집합 잘못 된 순서입니다.

### <a name="example-view-validation-report-deployment-support"></a>예: 보기 유효성 검사 보고서 (배포 지원)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

이 예제에서는 배포 또는 지원 팀 받은 준비 보고서를 고객 (Contoso)에서 사용 하 여 `Start-AzsReadinessChecker` Contoso 수행 된 유효성 검사 실행의 상태를 볼 수 있습니다.

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>예제: 인증서 유효성 검사 전용 (배포 및 지원)에 대 한 요약 유효성 검사 보고서를 보려면

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

이 예제에서는 배포 또는 지원 팀 받은 준비 보고서를 고객 (Contoso)에서 사용 하 여 `Start-AzsReadinessChecker` Contoso 수행 되는 인증서 유효성 검사 실행의 요약 된 상태를 볼 수 있습니다.

## <a name="required-parameters"></a>필수 매개 변수

### <a name="-regionname"></a>-RegionName

Azure Stack 배포 지역 이름을 지정합니다.

|  |  |
|----------------------------|--------------|
|형식:                       |문자열        |
|위치:                   |named         |
|기본값:              |없음          |
|파이프라인 입력 허용:      |False         |
|와일드 카드 문자를 허용 합니다. |False         |

### <a name="-fqdn"></a>-FQDN

Azure Stack 배포를 지정 외부 FQDN도 별칭이 **ExternalFQDN** 하 고 **ExternalDomainName**합니다.

|  |  |
|----------------------------|--------------|
|형식:                       |문자열        |
|위치:                   |named         |
|기본값:              |ExternalFQDN, ExternalDomainName |
|파이프라인 입력 허용:      |False         |
|와일드 카드 문자를 허용 합니다. |False         |

### <a name="-identitysystem"></a>-IdentitySystem

Azure Active Directory 및 Active Directory Federated Services, Azure Stack 배포 id 시스템 올바른 값, AAD 또는 ADFS를 각각 지정합니다.

|  |  |
|----------------------------|--------------|
|형식:                       |문자열        |
|위치:                   |named         |
|기본값:              |없음          |
|유효한 값:                |' AAD', 'ADFS'  |
|파이프라인 입력 허용:      |False         |
|와일드 카드 문자를 허용 합니다. |False         |

### <a name="-pfxpassword"></a>-PfxPassword

PFX 인증서 파일을 사용 하 여 연결 된 암호를 지정 합니다.

|  |  |
|----------------------------|---------|
|형식:                       |SecureString |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

### <a name="-paascertificates"></a>-PaaSCertificates

경로 및 PaaS 인증서 암호를 포함 하는 해시 테이블을 지정 합니다.

|  |  |
|----------------------------|---------|
|형식:                       |해시 테이블 |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

### <a name="-deploymentdatajsonpath"></a>-DeploymentDataJSONPath

Azure Stack 배포 데이터 JSON 구성 파일을 지정합니다. 이 파일은 배포에 대 한 생성 됩니다.

|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

### <a name="-pfxpath"></a>-PfxPath

이 도구에서 인증서 유효성 검사에 표시 된 대로 해결 하는 가져오기/내보내기 루틴을 필요로 하는 문제가 있는 인증서의 경로를 지정 합니다.

|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

### <a name="-exportpfxpath"></a>-ExportPFXPath  

가져오기/내보내기 루틴에서 결과 PFX 파일에 대 한 대상 경로 지정합니다.  

|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

### <a name="-subject"></a>-Subject

인증서 요청 생성에 대 한 정렬된 된 사전을 주체를 지정합니다.

|  |  |
|----------------------------|---------|
|형식:                       |OrderedDictionary   |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

### <a name="-requesttype"></a>-RequestType

인증서 요청의 SAN 형식을 지정합니다. 유효한 값은 **MultipleCSR**하십시오 **SingleCSR**합니다.

- **MultipleCSR** 각 서비스에 대해 하나씩 여러 인증서 요청을 생성 합니다.
- **SingleCSR** 모든 서비스에 대 한 인증서 요청을 생성 합니다.

|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |없음     |
|유효한 값:                |'MultipleCSR','SingleCSR' |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

### <a name="-outputrequestpath"></a>-OutputRequestPath

인증서 요청 파일에 대 한 대상 경로 지정합니다. 디렉터리가 이미 있어야 합니다.

|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

### <a name="-aadserviceadministrator"></a>-AADServiceAdministrator

Azure Stack 배포에 사용할 Azure Active Directory 서비스 관리자를 지정 합니다.

|  |  |
|----------------------------|---------|
|형식:                       |PSCredential   |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

### <a name="-aaddirectorytenantname"></a>-AADDirectoryTenantName

Azure Stack 배포에 사용할 Azure Active Directory 이름을 지정 합니다.

|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

### <a name="-azureenvironment"></a>-AzureEnvironment

Azure Stack 배포 및 등록에 사용할 계정, 디렉터리 및 구독에 포함 된 Azure 서비스의 인스턴스를 지정 합니다.

|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |없음     |
|유효한 값:                |'AzureCloud','AzureChinaCloud','AzureUSGovernment' |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

### <a name="-registrationaccount"></a>-RegistrationAccount

Azure Stack 등록에 사용할 등록 계정을 지정 합니다.

|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

### <a name="-registrationsubscriptionid"></a>-RegistrationSubscriptionID

Azure Stack 등록에 사용할 등록 구독 ID를 지정 합니다.

|  |  |
|----------------------------|---------|
|형식:                       |Guid     |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

### <a name="-reportpath"></a>-ReportPath

현재 디렉터리 및 기본 보고서 이름을 기본값으로 준비 보고서에 대 한 경로 지정 합니다.

|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |모두      |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

## <a name="optional-parameters"></a>선택적 매개 변수

### <a name="-certificatepath"></a>-CertificatePath

인증서만 필요한 인증서 폴더를 사용할 수 있는 경로 지정 합니다.

Azure Active Directory id 시스템을 사용 하 여 Azure Stack 배포에 대 한 필요한 폴더는:

ACSBlob ACSQueue, ACSTable, 관리 포털, ARM 관리자 ARM 공용, KeyVault KeyVaultInternal, 공용 포털

Active Directory Federation Services id 시스템을 사용 하 여 배포 된 Azure Stack에 대 한 폴더를 해야 합니다.

ACSBlob, ACSQueue, ACSTable, ADFS, Admin Portal, ARM Admin, ARM Public, Graph, KeyVault, KeyVaultInternal, Public Portal

|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |. \Certificates |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

### <a name="-includepaas"></a>-IncludePaaS  

PaaS 서비스/호스트 이름이 인증서 요청을 추가할지 여부를 지정 합니다.

|  |  |
|----------------------------|------------------|
|형식:                       |SwitchParameter   |
|위치:                   |named             |
|기본값:              |False             |
|파이프라인 입력 허용:      |False             |
|와일드 카드 문자를 허용 합니다. |False             |

### <a name="-reportsections"></a>-ReportSections

세부 정보 생략 요약 보고서 표시 여부를 지정 합니다.

|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |모두      |
|유효한 값:                |'Certificate','AzureRegistration','AzureIdentity','Jobs','All' |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

### <a name="-summary"></a>-요약

세부 정보 생략 요약 보고서 표시 여부를 지정 합니다.

|  |  |
|----------------------------|------------------|
|형식:                       |SwitchParameter   |
|위치:                   |named             |
|기본값:              |False             |
|파이프라인 입력 허용:      |False             |
|와일드 카드 문자를 허용 합니다. |False             |

### <a name="-cleanreport"></a>-CleanReport

이전 실행 및 유효성 검사 기록을 제거 하 고 새 보고서 유효성 검사를 씁니다.

|  |  |
|----------------------------|------------------|
|형식:                       |SwitchParameter   |
|별칭:                    |cf                |
|위치:                   |named             |
|기본값:              |False             |
|파이프라인 입력 허용:      |False             |
|와일드 카드 문자를 허용 합니다. |False             |

### <a name="-outputpath"></a>-OutputPath

준비 JSON 보고서와 자세한 정보 표시 로그 파일을 저장 하기 위한 사용자 지정 경로 지정 합니다. 경로가 없는 경우이 명령은 디렉터리를 만들려고 시도 합니다.

|  |  |
|----------------------------|------------------|
|형식:                       |문자열            |
|위치:                   |named             |
|기본값:              |$ENV:TEMP\AzsReadinessChecker  |
|파이프라인 입력 허용:      |False             |
|와일드 카드 문자를 허용 합니다. |False             |

### <a name="-confirm"></a>-Confirm

cmdlet를 실행하기 전에 확인 메시지를 표시합니다.

|  |  |
|----------------------------|------------------|
|형식:                       |SwitchParameter   |
|별칭:                    |cf                |
|위치:                   |named             |
|기본값:              |False             |
|파이프라인 입력 허용:      |False             |
|와일드 카드 문자를 허용 합니다. |False             |

### <a name="-whatif"></a>-WhatIf

cmdlet을 실행할 경우 발생하는 일을 표시합니다. cmdlet은 실행되지 않습니다.

|  |  |
|----------------------------|------------------|
|형식:                       |SwitchParameter   |
|별칭:                    |wi                |
|위치:                   |named             |
|기본값:              |False             |
|파이프라인 입력 허용:      |False             |
|와일드 카드 문자를 허용 합니다. |False             |

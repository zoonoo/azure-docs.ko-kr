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
ms.date: 12/04/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/04/2018
ms.openlocfilehash: 63de39fa97850c1df72bdcd964ed670e4e8b0608
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57767195"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>시작-AzsReadinessChecker cmdlet 참조

모듈: Microsoft.AzureStack.ReadinessChecker

이 모듈에는 단일 cmdlet을 들어 있습니다.  이 cmdlet은 Azure Stack에 대 한 하나 이상의 배포 전 또는 미리 서비스 함수를 수행합니다.

## <a name="syntax"></a>구문

```PowerShell
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

```PowerShell
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

```PowerShell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
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

```PowerShell
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

```PowerShell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
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

```PowerShell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
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

```PowerShell
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

```PowerShell
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

합니다 **시작 AzsReadinessChecker** cmdlet은 인증서, Azure 계정, Azure 구독 및 Azure Active 디렉터리의 유효성을 검사 합니다. Azure Stack을 배포 또는 Azure Stack 암호 회전 등의 작업을 처리 하기 전에 유효성 검사를 실행 합니다. Cmdlet는 인프라 인증서 및 필요에 따라 PaaS 인증서에 대 한 인증서 서명 요청을 생성 하려면 데도 사용할 수 있습니다.  마지막으로, cmdlet 일반적인 패키징 문제를 해결 하려면 PFX 인증서 다시 패키지 있습니다.

## <a name="examples"></a>예

### <a name="example-generate-certificate-signing-request"></a>예제: 인증서 서명 요청을 생성 합니다.

```PowerShell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

이 예제에서는 시작 AzsReadinessChecker 여러 서명 요청 (CSR (인증서) "동부"의 지역 이름으로는 ADFS Azure Stack 배포에 적합 한 인증서에 대 한 및 "azurestack.contoso.com" 외부 FQDN을 생성

### <a name="example-validate-certificates"></a>예제: 인증서의 유효성 검사

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

이 예제에서는 PFX 암호를 묻는 메시지가 안전 하 게 하 고 시작 AzsReadinessChecker "동부" 및 "azurestack.contoso.com" 외부 FQDN으로 지역 이름과 AAD 배포를 위한 올바른 인증서에 대 한 상대 폴더 "인증서"를 확인 합니다.

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>예제: 배포 데이터 (배포 및 지원)를 사용 하 여 인증서 유효성 검사

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

이 예에서 배포 및 지원 PFX 암호를 묻는 안전 하 게 하 고 시작 AzsReadinessChecker id, 지역 및 외부 FQDN에서 읽기는 배포에 대 한 올바른 인증서에 대 한 상대 폴더 "인증서"를 확인 합니다 배포에 대해 생성 된 배포 데이터 JSON 파일입니다. 

### <a name="example-validate-paas-certificates"></a>예제: PaaS 인증서의 유효성 검사

```PowerShell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates – RegionName east -FQDN azurestack.contoso.com
```

이 예제에서는 해시 테이블은 경로 및 각 PaaS 인증서 암호를 사용 하 여 생성 됩니다. 인증서를 생략할 수 있습니다. 시작-AzsReadinessChecker 각 PFX 경로 존재 확인 '동부' 영역을 사용 하 고 외부 FQDN 'azurestack.contoso.com'의 유효성을 검사 합니다.

### <a name="example-validate-paas-certificates-with-deployment-data"></a>예제: 배포 데이터를 사용 하 여 PaaS 인증서의 유효성 검사

```PowerShell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

이 예제에서는 해시 테이블은 경로 및 각 PaaS 인증서 암호를 사용 하 여 생성 됩니다. 인증서를 생략할 수 있습니다. 시작-AzsReadinessChecker 외부 FQDN에서 배포에 대해 생성 된 배포 데이터 JSON 파일 읽고 각 PFX 경로가 있는지와 지역을 사용 하 여 유효성을 검사를 확인 합니다. 

### <a name="example-validate-azure-identity"></a>예제: Azure id의 유효성을 검사합니다

```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

이 예제에서는 서비스 관리자 계정 자격 증명을 묻는 메시지가 안전 하 게 하 고 시작 AzsReadinessChecker Azure 계정 및 Azure Active Directory 유효성 검사 "azurestack.contoso.com" 테 넌 트 디렉터리 이름으로 AAD 배포

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>예제: 배포 데이터 (배포 지원)를 사용 하 여 Azure id의 유효성을 검사합니다

```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

이 예제에서는 서비스 관리자 계정 자격 증명을 묻는 메시지가 안전 하 게 하 고 시작 AzsReadinessChecker Azure 계정 및 Azure Active Directory 유효성 검사는 AAD 배포용 AzureCloud 및 TenantName 배포 데이터에서 읽기는 배포에 대해 생성 된 JSON 파일입니다.

### <a name="example-validate-azure-registration"></a>예제: Azure 등록 유효성 검사

```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

이 예제에서는 구독 소유자 자격 증명을 묻는 메시지가 안전 하 게 시작 AzsReadinessChecker 다음 지정된 된 계정에 대 한 유효성 검사를 수행 하 고 Azure Stack 등록에 확인 하기 위해 구독을 사용할 수 있습니다. 

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>예제: 배포 데이터 (배포 팀)를 사용 하 여 Azure 등록 유효성 검사

```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

이 예제에서는 구독 소유자 자격 증명을 묻는 메시지가 안전 하 게 시작 AzsReadinessChecker 다음 지정된 된 계정에 대 한 유효성 검사를 수행 및 확인 하기 위해 구독 추가 세부 정보는 여기서 하는 Azure Stack 등록에 사용할 수 있습니다. 배포에 대해 생성 된 배포 데이터 JSON 파일에서 읽습니다.

### <a name="example-importexport-pfx-package"></a>예제: Import/Export PFX 패키지

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

이 예제에서는 PFX 암호가 입력에 대 한 안전 하 게 합니다. Ssl.pfx 파일을 로컬 컴퓨터 인증서 저장소로 가져올 및 동일한 암호를 사용 하 여 다시 내보낸 되며 ssl_new.pfx로 저장 합니다.  이 절차는 개인 키를 로컬 컴퓨터 특성 집합, 인증서 체인이 끊어집니다, 관련이 없는 인증서는 PFX에 없거나 잘못 된 순서로 인증서 체인을 인증서 유효성 검사에 플래그가 지정 된 경우 사용 하 여입니다.

### <a name="example-view-validation-report-deployment-support"></a>예제: 유효성 검사 보고서 보기 (배포 지원)

```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

이 예제에서는 배포 또는 지원 팀 고객 (Contoso)에서 준비 보고서를을 받고 시작 AzsReadinessChecker를 사용 하 여 Contoso 수행 된 유효성 검사 실행의 상태를 확인 합니다.

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>예제: 인증서 유효성 검사 전용 (배포 및 지원)에 대 한 요약 유효성 검사 보고서 보기

```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

이 예제에서는 배포 또는 지원 팀 Contoso 고객 으로부터 준비 보고서를을 받고 시작 AzsReadinessChecker를 사용 하 여 Contoso 수행 되는 인증서 유효성 검사 실행의 요약 된 상태를 확인 합니다.

## <a name="required-parameters"></a>필수 매개 변수

> -RegionName

Azure Stack 배포의 지역 이름을 지정합니다.
|  |  |
|----------------------------|--------------|
|형식:                       |문자열        |
|위치:                   |named         |
|기본값:              |없음          |
|파이프라인 입력 허용:      |False         |
|와일드 카드 문자를 허용 합니다. |False         |

> -FQDN

또한 별칭이 ExternalFQDN 및 ExternalDomainName Azure Stack 배포의 외부 FQDN을 지정합니다.
|  |  |
|----------------------------|--------------|
|형식:                       |문자열        |
|위치:                   |named         |
|기본값:              |ExternalFQDN, ExternalDomainName |
|파이프라인 입력 허용:      |False         |
|와일드 카드 문자를 허용 합니다. |False         |

> -IdentitySystem

Azure Stack 배포의 Id 시스템 유효한 값, AAD 또는 ADFS, Azure Active Directory 및 Active Directory Federated Services 각각 지정합니다.
|  |  |
|----------------------------|--------------|
|형식:                       |문자열        |
|위치:                   |named         |
|기본값:              |없음          |
|유효한 값:                |' AAD', 'ADFS'  |
|파이프라인 입력 허용:      |False         |
|와일드 카드 문자를 허용 합니다. |False         |

> -PfxPassword

PFX 인증서 파일을 사용 하 여 연결 된 암호를 지정 합니다.
|  |  |
|----------------------------|---------|
|형식:                       |SecureString |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

> -PaaSCertificates

경로 및 PaaS 인증서 암호를 포함 하는 해시 테이블을 지정 합니다.
|  |  |
|----------------------------|---------|
|형식:                       |해시 테이블 |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

> -DeploymentDataJSONPath

Azure Stack 배포 데이터 JSON 구성 파일을 지정합니다. 이 파일은 배포에 대 한 생성 됩니다.
|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

> -PfxPath

이 도구에서 인증서 유효성 검사에 표시 된 대로 가져오기/내보내기 루틴을 해결 하려면 필요한 문제가 있는 인증서의 경로를 지정 합니다.
|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

> -ExportPFXPath  

가져오기/내보내기 루틴에서 결과 PFX 파일에 대 한 대상 경로 지정합니다.  
|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

> -Subject

인증서 요청 생성에 대 한 정렬된 된 사전을 주체를 지정합니다.
|  |  |
|----------------------------|---------|
|형식:                       |OrderedDictionary   |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

> -RequestType

인증서 요청의 SAN 형식을 지정합니다. 유효한 값 MultipleCSR, SingleCSR입니다.

- *MultipleCSR* 각 서비스에 대해 하나씩 여러 인증서 요청을 생성 합니다.
- *SingleCSR* 모든 서비스에 대 한 인증서 요청을 생성 합니다.

|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |없음     |
|유효한 값:                |'MultipleCSR','SingleCSR' |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

> -OutputRequestPath

대상 경로 지정 합니다. 인증서 요청 파일을 디렉터리 이미 존재 해야 합니다.
|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

> -AADServiceAdministrator

Azure Stack 배포에 사용할 Azure Active Directory 서비스 관리자를 지정 합니다.
|  |  |
|----------------------------|---------|
|형식:                       |PSCredential   |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

> -AADDirectoryTenantName

Azure Stack 배포에 사용할 Azure Active Directory 이름을 지정 합니다.
|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

> -AzureEnvironment

Azure Stack 배포 및 등록에 사용할 계정, 디렉터리 및 구독에 포함 된 Azure 서비스의 인스턴스를 지정 합니다.
|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |없음     |
|유효한 값:                |'AzureCloud','AzureChinaCloud','AzureUSGovernment' |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

> -RegistrationAccount

Azure Stack 등록에 사용할 등록 계정을 지정 합니다.
|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

> -RegistrationSubscriptionID

Azure Stack 등록에 사용할 등록 구독 ID를 지정 합니다.
|  |  |
|----------------------------|---------|
|형식:                       |Guid     |
|위치:                   |named    |
|기본값:              |없음     |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

> -ReportPath

현재 디렉터리 및 기본 보고서 이름을 기본값으로 준비 보고서에 대 한 경로 지정 합니다.
|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |모두      |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

## <a name="optional-parameters"></a>선택적 매개 변수

> -CertificatePath

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

> -IncludePaaS  

PaaS 서비스/호스트 이름이 인증서 요청에 추가 해야 하는 경우를 지정 합니다.

|  |  |
|----------------------------|------------------|
|형식:                       |SwitchParameter   |
|위치:                   |named             |
|기본값:              |False             |
|파이프라인 입력 허용:      |False             |
|와일드 카드 문자를 허용 합니다. |False             |

> -ReportSections

세부 정보 생략 요약 보고서 표시 여부를 지정 합니다.
|  |  |
|----------------------------|---------|
|형식:                       |문자열   |
|위치:                   |named    |
|기본값:              |모두      |
|유효한 값:                |'Certificate','AzureRegistration','AzureIdentity','Jobs','All' |
|파이프라인 입력 허용:      |False    |
|와일드 카드 문자를 허용 합니다. |False    |

> -요약

세부 정보 생략 요약 보고서 표시 여부를 지정 합니다.
|  |  |
|----------------------------|------------------|
|형식:                       |SwitchParameter   |
|위치:                   |named             |
|기본값:              |False             |
|파이프라인 입력 허용:      |False             |
|와일드 카드 문자를 허용 합니다. |False             |

> -CleanReport

이전 실행 및 유효성 검사 기록을 제거 하 고 새 보고서 유효성 검사를 씁니다.
|  |  |
|----------------------------|------------------|
|형식:                       |SwitchParameter   |
|별칭:                    |cf                |
|위치:                   |named             |
|기본값:              |False             |
|파이프라인 입력 허용:      |False             |
|와일드 카드 문자를 허용 합니다. |False             |

> -OutputPath

사용자 지정 준비 JSON 보고서와 자세한 정보 표시 로그 파일을 저장할 경로 지정 합니다.  경로가 없는 경우 도구 디렉터리를 만들 하려고 합니다.

|  |  |
|----------------------------|------------------|
|형식:                       |문자열            |
|위치:                   |named             |
|기본값:              |$ENV:TEMP\AzsReadinessChecker  |
|파이프라인 입력 허용:      |False             |
|와일드 카드 문자를 허용 합니다. |False             |

> -Confirm

cmdlet를 실행하기 전에 확인 메시지를 표시합니다.
|  |  |
|----------------------------|------------------|
|형식:                       |SwitchParameter   |
|별칭:                    |cf                |
|위치:                   |named             |
|기본값:              |False             |
|파이프라인 입력 허용:      |False             |
|와일드 카드 문자를 허용 합니다. |False             |

> -WhatIf

cmdlet을 실행할 경우 발생하는 일을 표시합니다. cmdlet은 실행되지 않습니다.
|  |  |
|----------------------------|------------------|
|형식:                       |SwitchParameter   |
|별칭:                    |wi                |
|위치:                   |named             |
|기본값:              |False             |
|파이프라인 입력 허용:      |False             |
|와일드 카드 문자를 허용 합니다. |False             |

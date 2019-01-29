---
title: Azure Stack 통합 시스템 배포에 대 한 Azure Stack 공개 키 인프라 인증서의 유효성 검사 | Microsoft Docs
description: Azure Stack 통합 시스템에 대 한 Azure Stack PKI 인증서의 유효성을 검사 하는 방법에 설명 합니다. Azure Stack 인증서 검사기 도구를 사용 하 여 설명 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/08/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 6b2b72403365dc0f6cc61232e0b70801b57a29e2
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55095542"
---
# <a name="validate-azure-stack-pki-certificates"></a>Azure Stack PKI 인증서의 유효성 검사

이 문서에 설명 된 Azure Stack 준비 상태 검사기 도구를 사용할 수 [PowerShell 갤러리에서](https://aka.ms/AzsReadinessChecker)합니다. 도구를 사용 하 여 유효성을 검사 하는 [PKI 인증서를 생성](azure-stack-get-pki-certs.md) 전 배포에 적합 합니다. 충분 한 시간을 테스트 하 고 필요한 경우 인증서를 다시 실행 하 여 인증서의 유효성을 검사 합니다.

준비 검사 도구는 다음 인증서 유효성 검사를 수행합니다.

- **PFX 읽기**  
    올바른 PFX 파일에서 올바른 암호를 확인 하 고 공개 정보는 암호로 보호 되어 있지 않으면 여부. 
- **서명 알고리즘**  
    서명 알고리즘을 s h a 1 없습니다 확인 합니다.
- **개인 키**  
    개인 키가 로컬 컴퓨터 특성을 사용 하 여 내보낸 확인 합니다. 
- **인증서 체인**  
    확인 인증서 체인이 자체 서명 된 인증서에 대 한 검사를 포함 하 여 그대로 유지 됩니다.
- **DNS 이름**  
    각 끝점에 대 한 관련 DNS 이름을 포함 하는 SAN 또는 와일드 카드를 지 원하는 경우는 확인 합니다.
- **키 사용**  
    디지털 서명 및 키 암호화 키 용도가 포함 되 고 서버 인증과 클라이언트 인증을 포함 하는 향상 된 키 사용을 확인 합니다.
- **키 크기**  
    키 크기가 2048 이상 인지 확인 합니다.
- **체인 순서**  
    순서 올바른지 유효성을 검사 하는 다른 인증서의 순서를 확인 합니다.
- **다른 인증서**  
    다른 인증서가 없습니다. 관련 리프 인증서 및 해당 체인 이외의 PFX에 패키지 된를 확인 합니다.
- **프로필이 없습니다.**  
    새 사용자 데이터를 로드할 수 PFX가 사용자 프로필 로드 없이 인증서를 서비스 하는 동안 gMSA 계정의 동작을 모방을 확인 합니다.

> [!IMPORTANT]  
> PKI 인증서는 PFX 파일 및 암호는 중요 한 정보로 간주 해야 합니다.

## <a name="prerequisites"></a>필수 조건

시스템은 Azure Stack 배포를 위한 PKI 인증서의 유효성을 검사 하기 전에 다음 필수 구성 요소를 충족 해야 합니다.

- Microsoft Azure Stack 준비 검사
- 다음 내보낼 SSL 인증서는 [준비 지침](azure-stack-prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 또는 Windows Server 2016

## <a name="perform-core-services-certificate-validation"></a>핵심 서비스 인증서 유효성 검사를 수행 합니다.

배포 및 비밀 회전에 대 한 Azure Stack PKI 인증서의 유효성 검사를 준비 하려면 다음이 단계를 사용 합니다.

1. 설치할 **AzsReadinessChecker** PowerShell 프롬프트에서 (5.1 이상)에서 다음 cmdlet을 실행 합니다.

    ```PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ```

2. 인증서 디렉터리 구조를 만듭니다. 아래 예제에서는 변경할 수 있습니다 `<c:\certificates>` 를 새 디렉터리 경로로 세요.
    ```PowerShell  
    New-Item C:\Certificates -ItemType Directory
    
    $directories = 'ACSBlob','ACSQueue','ACSTable','Admin Portal','ARM Admin','ARM Public','KeyVault','KeyVaultInternal','Public Portal','Admin Extension Host','Public Extension Host'
    
    $destination = 'c:\certificates'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```
    
    > [!Note]  
    > AD FS 및 그래프는 id 시스템으로 AD FS를 사용 하는 경우 필요 합니다.
    
     - 이전 단계에서 만든 적절 한 디렉터리에서 사용자 인증서를 배치 합니다. 예:   
        - `c:\certificates\ACSBlob\CustomerCertificate.pfx`
        - `c:\certificates\Admin Portal\CustomerCertificate.pfx`
        - `c:\certificates\ARM Admin\CustomerCertificate.pfx`

3. PowerShell 창에서 값을 변경할 **RegionName** 하 고 **FQDN** Azure Stack 환경에 적합 하 고 다음을 실행 합니다.

    ```PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Invoke-AzsCertificateValidation -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD  
    ```

4. 모든 테스트를 통과 하는 출력 및 모든 인증서를 확인 합니다. 예: 

```PowerShell
Invoke-AzsCertificateValidation v1.1809.1005.1 started.
Testing: ARM Public\ssl.pfx
Thumbprint: 7F6B27****************************E9C35A
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK
Testing: Admin Extension Host\ssl.pfx
Thumbprint: A631A5****************************35390A
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK
Testing: Public Extension Host\ssl.pfx
Thumbprint: 4DBEB2****************************C5E7E6
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsCertificateValidation Completed
```

### <a name="known-issues"></a>알려진 문제

**증상**: 테스트를 건너뜁니다.

**원인**: 종속성이 충족 되지 않으면 AzsReadinessChecker 특정 테스트를 건너뜁니다.

 - 다른 인증서는 인증서 체인에서 실패할 경우 건너뜁니다.

    ```PowerShell  
    Testing: ACSBlob\singlewildcard.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: Fail
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: Skipped
    Details:
    The certificate records '*.east.azurestack.contoso.com' do not contain a record that is valid for '*.blob.east.azurestack.contoso.com'. Please refer to the documentation for how to create the required certificate file.
    The Other Certificates check was skipped because Cert Chain and/or DNS Names failed. Follow the guidance to remediate those issues and recheck. 
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsCertificateValidation Completed
    ```

**해결 방법**: 각 인증서에 대 한 테스트의 각 집합 세부 정보 섹션에서이 도구의 지침을 따릅니다.

## <a name="perform-platform-as-a-service-certificate-validation"></a>플랫폼 서비스 인증서 유효성 검사 수행

SQL/MySQL 또는 App Services 배포 계획 된 경우 준비 하 고 플랫폼에 대 한 Azure Stack PKI 인증서는 서비스 (PaaS) 인증서로 유효성을 검사 하려면 다음이 단계를 사용 합니다.

1.  설치할 **AzsReadinessChecker** PowerShell 프롬프트에서 (5.1 이상)에서 다음 cmdlet을 실행 합니다.

    ```PowerShell  
      Install-Module Microsoft.AzureStack.ReadinessChecker -force
    ```

2.  경로 및 각 PaaS 인증서 유효성 검사를 필요로 하는 데 암호를 포함 하는 중첩 된 해시 테이블을 만듭니다. PowerShell 창의 실행:

    ```PowerShell  
        $PaaSCertificates = @{
        'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        }
    ```

3.  값을 변경할 **RegionName** 하 고 **FQDN** 유효성 검사를 시작 하려면 Azure Stack 환경과 일치 하도록 합니다. 그런 후 다음을 실행합니다.

    ```PowerShell  
    Invoke-AzsCertificateValidation -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com 
    ```
4.  출력 하 고 있는 모든 인증서 모든 테스트를 통과 하는 확인 합니다.

    ```PowerShell
    Invoke-AzsCertificateValidation v1.0 started.
    Thumbprint: 95A50B****************************FA6DDA
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: EBB011****************************59BE9A
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: 76AEBA****************************C1265E
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: 8D6CCD****************************DB6AE9
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
    ```

## <a name="certificates"></a>인증서

| 디렉터리 | 인증서 |
| ---    | ----        |
| acsBlob | wildcard_blob_\< 지역 >\< externalFQDN > |
| ACSQueue  |  wildcard_queue\< 지역 >\< externalFQDN > |
| ACSTable  |  wildcard_table\< 지역 >\< externalFQDN > |
| 관리 확장 호스트  |  wildcard_adminhosting\< 지역 >\< externalFQDN > |
| 관리 포털  |  adminportal\< 지역 >\< externalFQDN > |
| ARM 관리  |  adminmanagement\< 지역 >\< externalFQDN > |
| ARM 공용  |  관리\< 지역 >\< externalFQDN > |
| KeyVault  |  wildcard_vault\< 지역 >\< externalFQDN > |
| KeyVaultInternal  |  wildcard_adminvault\< 지역 >\< externalFQDN > |
| 공용 확장 호스트  |  wildcard_hosting\< 지역 >\< externalFQDN > |
| 공용 포털  |  포털\< 지역 > _\< externalFQDN > |

## <a name="using-validated-certificates"></a>유효성이 검사 된 인증서를 사용 하 여

인증서는 AzsReadinessChecker에서 검증 된, 일단 Azure Stack 비밀 회전 또는 Azure Stack 배포에 사용할 준비가 된 것입니다. 

 - 배포에 대 한 안전 하 게 전송 인증서 배포 담당 엔지니어에 지정 된 배포 호스트에 복사할 수 있습니다 이러한 합니다 [Azure Stack PKI 요구 사항 설명서](azure-stack-pki-certs.md)합니다.
 - 비밀 회전을 수행 하 여 Azure Stack 환경과 공용 인프라 끝점에 대 한 이전 인증서를 업데이트 하는 인증서를 사용할 수 있습니다 합니다 [Azure Stack 암호 회전 설명서](azure-stack-rotate-secrets.md)합니다.
 - PaaS 서비스에 대해 수행 하 여 Azure Stack에서 SQL, MySQL 및 앱 서비스 리소스 공급자를 설치 하는 인증서를 사용할 수 있습니다 합니다 [Azure Stack 설명서에는 서비스 제공 개요](azure-stack-offer-services-overview.md)합니다.

## <a name="next-steps"></a>다음 단계

[데이터 센터 id 통합](azure-stack-integrate-identity.md)

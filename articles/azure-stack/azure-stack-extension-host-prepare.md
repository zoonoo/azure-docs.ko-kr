---
title: Azure Stack에 대 한 확장 호스트에 대 한 준비 | Microsoft Docs
description: 향후 Azure 스택 업데이트 패키지를 통해 자동으로 사용할 수 있는 확장 호스트를 준비 하는 방법을 알아봅니다.
services: azure-stack
keywords: ''
author: mattbriggs
ms.author: mabrigg
ms.date: 11/27/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: thoroet
manager: femila
ms.openlocfilehash: fcd5137792e573c3077a4b9d5e815b9bf20774f6
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54155073"
---
# <a name="prepare-for-extension-host-for-azure-stack"></a>Azure Stack에 대 한 확장 호스트 준비

확장 호스트 필요한 TCP/IP 포트의 수를 줄여 Azure Stack을 보호 합니다. 이 문서에서는 Azure Stack 1808 업데이트 후 Azure Stack 업데이트 패키지를 통해 자동으로 사용할 수 있는 확장 호스트에 대 한 준비 살펴봅니다.

## <a name="certificate-requirements"></a>인증서 요구 사항

확장 호스트 각 포털 확장에 대 한 고유한 호스트 항목을 보장 하기 위해 두 개의 새 도메인 네임 스페이스를 구현 합니다. 새 도메인 네임 스페이스 안전한 통신을 위해 두 개의 추가 와일드 카드 인증서가 필요 합니다.

다음 표에서 새 네임 스페이스와 연결 된 인증서를 보여줍니다.

| 배포 폴더 | 필요한 인증서 주체 및 주체 대체 이름 (SAN) | 범위 (지역당) | 하위 네임 스페이스 |
|-----------------------|------------------------------------------------------------------|-----------------------|------------------------------|
| 관리 확장 호스트 | *.adminhosting 합니다. \<지역 >. \<fqdn > (와일드 카드 SSL 인증서) | 관리 확장 호스트 | adminhosting 합니다. \<지역 >. \<fqdn > |
| 공용 확장 호스트 | *.hosting 합니다. \<지역 >. \<fqdn > (와일드 카드 SSL 인증서) | 공용 확장 호스트 | 호스팅. \<지역 >. \<fqdn > |

자세한 인증서 요구 사항에서 찾을 수 있습니다 합니다 [Azure Stack 공개 키 인프라 인증서 요구 사항](azure-stack-pki-certs.md) 문서.

## <a name="create-certificate-signing-request"></a>인증서 서명 요청 만들기

Azure Stack 준비 검사 도구를 두 개의 새, 필요한 SSL 인증서에 대 한 요청을 서명 인증서를 만드는 기능을 제공 합니다. 문서의 단계를 수행 [Azure Stack 인증서 서명 요청 생성](azure-stack-get-pki-certs.md)합니다.

> [!Note]  
> SSL 인증서를 요청 하는 방법에 따라이 단계를 건너뛸 수 있습니다.

## <a name="validate-new-certificates"></a>새 인증서의 유효성 검사

1. 하드웨어 수명 주기 호스트 또는 Azure Stack 관리 워크스테이션에서 관리자 권한으로 PowerShell을 엽니다.
2. Azure Stack 준비 상태 검사기 도구를 설치 하려면 다음 cmdlet을 실행 합니다.

    ```PowerShell  
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

3. 필요한 폴더 구조를 만들려면 다음 스크립트를 실행 합니다.

    ```PowerShell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','Admin Portal','ARM Admin','ARM Public','KeyVault','KeyVaultInternal','Public Portal', 'Admin extension host', 'Public extension host'

    $destination = 'c:\certificates'

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```

    > [!Note]  
    > Azure Active Directory Federated Services (AD FS)를 사용 하 여 배포 하는 경우 다음 디렉터리에 추가 되어야 합니다 **$directories** 스크립트에서: `ADFS`, `Graph`합니다.

4. 인증서 확인을 시작 하려면 다음 cmdlet을 실행 합니다.

    ```PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ```

5. 해당 디렉터리에 사용자 인증서를 배치 합니다.

6. 모든 테스트를 통과 하는 출력 및 모든 인증서를 확인 합니다.


## <a name="import-extension-host-certificates"></a>확장 호스트 인증서를 가져옵니다.

다음 단계에 대 한 권한 있는 Azure Stack 끝점에 연결할 수 있는 컴퓨터를 사용 합니다. 해당 컴퓨터에서 새 인증서 파일에 액세스할 수 있는지 확인 합니다.

1. 다음 단계에 대 한 권한 있는 Azure Stack 끝점에 연결할 수 있는 컴퓨터를 사용 합니다. 해당 컴퓨터에서 새 인증서 파일에 액세스 해야 합니다.
2. 다음 스크립트 블록을 실행 하려면 PowerShell ISE를 열려면
3. 끝점 호스트 관리에 대 한 인증서를 가져옵니다.

    ```PowerShell  

    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$AdminHostingCertContent = [Byte[]](Get-Content c:\certificate\myadminhostingcertificate.pfx -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($AdminHostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($AdminHostingCertContent, $CertPassword)
            Import-AdminHostingServiceCert $AdminHostingCertContent $certPassword
    }
    ```
4. 호스팅 끝점에 대 한 인증서를 가져옵니다.
    ```PowerShell  
    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$HostingCertContent = [Byte[]](Get-Content c:\certificate\myhostingcertificate.pfx  -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($HostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($HostingCertContent, $CertPassword)
            Import-UserHostingServiceCert $HostingCertContent $certPassword
    }
    ```

### <a name="update-dns-configuration"></a>DNS 구성 업데이트

> [!Note]  
> DNS 통합에 대 한 Dns 위임을 사용 하는 경우에이 단계가 필요 하지 않습니다.
Azure Stack 끝점을 게시 하려면 개별 호스트 A 레코드를 구성한 경우에 두 개의 추가 호스트 A 레코드를 만들 해야 합니다.

| IP | 호스트 이름 | type |
|----|------------------------------|------|
| \<IP &GT; | *. Adminhosting 합니다. \<지역 >. \<FQDN > | A |
| \<IP &GT; | *. 호스팅. \<지역 >. \<FQDN > | A |

Cmdlet을 실행 하 여 권한 있는 끝점을 사용 하 여 할당 된 Ip를 검색할 수 있습니다 **Get AzureStackStampInformation**합니다.

### <a name="ports-and-protocols"></a>포트 및 프로토콜

문서를 [Azure Stack 데이터 센터 통합-끝점을 게시](azure-stack-integrate-endpoints.md), 포트 및 프로토콜 확장 호스트 출시 하기 전에 Azure Stack을 게시 하려면 인바운드 통신을 필요로 하는 방법을 다룹니다.

### <a name="publish-new-endpoints"></a>새 끝점을 게시

방화벽을 통해 게시 하는 데 필요한 두 개의 새로운 끝점 있습니다. 에 Azure Stack을 통해 실행 해야 하는 다음 코드를 사용 하 여 공용 VIP 풀에서 할당 된 Ip를 검색할 수 있습니다 [환경에는 끝점 권한 있는](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-privileged-endpoint)합니다.

```PowerShell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Obtain DNS Servers and Extension Host information from Azure Stack Stamp Information and find the IPs for the Host Extension Endpoints
$StampInformation = Invoke-Command $PEPSession {Get-AzureStackStampInformation} | Select-Object -Property ExternalDNSIPAddress01, ExternalDNSIPAddress02, @{n="TenantHosting";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://*.","testdnsentry"-replace "/"}},  @{n="AdminHosting";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://*.","testdnsentry"-replace "/"}},@{n="TenantHostingDNS";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://",""-replace "/"}},  @{n="AdminHostingDNS";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://",""-replace "/"}}
If (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting -ErrorAction SilentlyContinue) {
    Write-Host "Can access AZS DNS" -ForegroundColor Green
    $AdminIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress02 -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Else {
    Write-Host "Cannot access AZS DNS" -ForegroundColor Yellow
    $AdminIP = (Resolve-DnsName -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Remove-PSSession -Session $PEPSession
```

#### <a name="sample-output"></a>샘플 출력

```PowerShell
Can access AZS DNS
The IP for the Admin Extension Host is: *.adminhosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.adminhosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
The IP address for the Tenant Extension Host is *.hosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.hosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
```

> [!Note]  
> 확장 호스트를 사용 하도록 설정 하기 전에이 변경을 확인 합니다. 이렇게 하면 Azure Stack 포털에 계속 액세스할 수 있습니다.

| 끝점 (VIP) | 프로토콜 | 포트 |
|----------------|----------|-------|
| AdminHosting | HTTPS | 443 |
| Hosting | HTTPS | 443 |

### <a name="update-existing-publishing-rules-post-enablement-of-extension-host"></a>기존 게시 규칙 (확장 호스트의 Post 사용)를 업데이트 합니다.

> [!Note]  
> 1808 Azure Stack 업데이트 패키지는 **되지** 확장 호스트를 아직 사용 하도록 설정 합니다. 필요한 인증서를 가져와서 확장 호스트에 대 한 준비를 수 있습니다. 1808 업데이트 후 Azure Stack 업데이트 패키지를 통해 확장 호스트를 자동으로 사용 하기 전에 모든 포트를 닫지 마세요.

기존 방화벽 규칙에 다음 기존 끝점 포트를 닫아야 합니다.

> [!Note]  
> 유효성 검사를 성공한 후 해당 포트를 닫아야 하는 것이 좋습니다.

| 끝점 (VIP) | 프로토콜 | 포트 |
|----------------------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------|
| 포털 (관리자) | HTTPS | 12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13020<br>13021<br>13026<br>30015 |
| 포털 (사용자) | HTTPS | 12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003 |
| Azure 리소스 관리자 (관리자) | HTTPS | 30024 |
| Azure Resource Manager (사용자) | HTTPS | 30024 |

## <a name="next-steps"></a>다음 단계

- 에 대 한 자세한 [통합 방화벽](azure-stack-firewall.md)합니다.
- 에 대 한 자세한 [Azure Stack 인증서 서명 요청 만들기](azure-stack-get-pki-certs.md)

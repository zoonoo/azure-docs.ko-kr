---
title: SQL 데이터베이스를 사용 하 여 Azure 스택에 | Microsoft Docs
description: SQL 데이터베이스에서 SQL Server 리소스 공급자 어댑터를 배포 하는 Azure 스택 및 빠른 단계 서비스로 배포 하는 방법을 알아봅니다.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: jeffgo
ms.openlocfilehash: d0b287eb61087e90c898aad5273ab5be8c1f98b2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>SQL 데이터베이스를 사용 하 여 Microsoft Azure 스택

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

SQL Server 리소스 공급자 어댑터를 사용 하 여 SQL 데이터베이스의 서비스로 노출할 [Azure 스택](azure-stack-poc.md)합니다. 리소스 공급자를 설치 하 고 하나 이상의 SQL Server 인스턴스에 연결 하 고 사용자가 만들 수 있습니다.:
- 클라우드-네이티브 앱에 대 한 데이터베이스입니다.
- SQL에 기반 하는 웹 사이트입니다.
- SQL에 기반 하는 작업입니다.
가상 컴퓨터를 프로비저닝 (VM) SQL Server를 호스팅하는 각 시간 필요가 없습니다.

리소스 공급자의 모든 데이터베이스 관리 기능을 지원 하지 않습니다 [Azure SQL 데이터베이스](https://azure.microsoft.com/services/sql-database/)합니다. 예를 들어, 탄력적 데이터베이스 풀과 하는 기능이 데이터베이스 성능 아래로 자동으로 사용할 수 없습니다. 그러나 유사한 리소스 공급자가 지원 만들기, 읽기, 업데이트 및 삭제 (CRUD) 작업. API는 SQL 데이터베이스와 호환 되지 않습니다.

## <a name="sql-resource-provider-adapter-architecture"></a>SQL 리소스 공급자 어댑터 아키텍처
리소스 공급자 세 가지 구성 요소로 이루어져 있습니다.

- **SQL 리소스 공급자 어댑터 VM**, 공급자 서비스를 실행 하는 Windows 가상 컴퓨터는입니다.
- **리소스 공급자 자체**준비 요청을 처리 하는, 및 데이터베이스 리소스를 노출 합니다.
- **SQL Server를 호스트 하는 서버**, 데이터베이스에 대 한 용량을 제공 하는 호스팅 서버를 호출 합니다.

하나 이상의 SQL Server 인스턴스를 만들 또는 외부 SQL Server 인스턴스에 대 한 액세스를 제공 해야 합니다.

> [!NOTE]
> Azure 스택에 설치 되어 있는 서버를 호스팅 통합형된 시스템 테 넌 트 구독에서 생성 되어야 합니다. 기본 공급자 구독에서 만들 수 없습니다. 적절 한 로그인을 사용 하 여 PowerShell 세션을 또는 테 넌 트 포털에서 만들 수 있어야 합니다. 모든 호스팅 서버가 유료 대의 vm 및 적절 한 라이선스가 있어야 합니다. 서비스 관리자의 테 넌 트 구독 소유자가 될 수 있습니다.

## <a name="deploy-the-resource-provider"></a>리소스 공급자를 배포

1. 아직 수행 경우 개발 키트를 등록 하 고 Marketplace 관리를 통해 다운로드할 수 있는 Windows Server 2016 데이터 센터 Core 이미지를 다운로드 합니다. Windows Server 2016 Core 이미지를 사용 해야 합니다. 만드는 스크립트를 사용할 수도 있습니다는 [Windows Server 2016 이미지](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)합니다. (해야 코어 옵션을 선택 합니다.)

2. VM 권한 있는 끝점에 액세스할 수 있는 호스트에 로그인 합니다.

    - Azure 스택 개발 키트 설치에서 실제 호스트에 로그인 합니다.

    - 다중 노드 시스템에 호스트 권한 있는 끝점에 액세스할 수 있는 시스템 이어야 합니다.
    
    >[!NOTE]
    > 스크립트가 실행 되 고 시스템 *해야* 최신 버전의.NET 런타임 설치 된 Windows 10 또는 Windows Server 2016 시스템 이어야 합니다. 그렇지 않으면 설치에 실패 합니다. Azure 스택 SDK 호스트는이 조건을 충족합니다.


3. 이진 SQL 리소스 공급자를 다운로드 합니다. 그런 다음 실행을 임시 디렉터리에 콘텐츠를 추출 자동 압축 풀기.

    >[!NOTE] 
    > 리소스 공급자에는 빌드는 최소 해당 Azure 스택 합니다. 실행 하는 Azure 스택 버전에 대 한 올바른 이진을 다운로드 해야 합니다.

    | Azure 스택 빌드 | SQL 리소스 공급자 설치 관리자 |
    | --- | --- |
    | 1802: 1.0.180302.1 | [SQL RP 1.1.18.0 버전](https://aka.ms/azurestacksqlrp1802) |
    | 1712: 1.0.180102.3, 1.0.180103.2 또는 1.0.180106.1 (다중 노드) | [SQL RP 1.1.14.0 버전](https://aka.ms/azurestacksqlrp1712) |
    | 1711: 1.0.171122.1 | [SQL RP 1.1.12.0 버전](https://aka.ms/azurestacksqlrp1711) |
    | 1710: 1.0.171028.1 | [SQL RP 1.1.8.0 버전](https://aka.ms/azurestacksqlrp1710) |
  

4. Azure 스택 SDK에 대 한 자체 서명 된 인증서는이 프로세스의 일부로 생성 됩니다. 통합된 시스템에 대 한 적절 한 인증서를 제공 해야 합니다.

   고유한 인증서를 제공 하려면에서.pfx 파일을 배치는 **DependencyFilesLocalPath** 다음과 같습니다.

    - 에 대 한 와일드 카드 인증서 \*.dbadapter.\< 지역\>.\< 외부 fqdn\> 또는 일반 sqladapter.dbadapter 이름의 단일 사이트 인증서.\< 지역\>.\< 외부 fqdn\>합니다.

    - 이 인증서는 신뢰할 수 있어야 합니다. 즉, 중간 인증서를 요구 하지 않고 신뢰 체인 존재 해야 합니다.

    - 단일 인증서 파일만 DependencyFilesLocalPath 매개 변수가 가리키는 디렉터리에 존재할 수 있습니다.

    - 파일 이름에 특수 문자가 없어야 합니다.


5. 열기는 **새** 관리자 권한 (관리자) PowerShell 콘솔 및 파일의 압축을 푼 디렉터리로 변경 합니다. 시스템에 이미 로드 되어 있는 잘못 된 PowerShell 모듈에서 발생할 수 있는 문제를 방지 하기 위해 새 창을 사용 합니다.

6. [Azure PowerShell 버전 1.2.11 설치](azure-stack-powershell-install.md)합니다.

7. 다음이 단계를 수행 하는 DeploySqlProvider.ps1 스크립트를 실행 합니다.

    - Azure 스택의 저장소 계정에는 인증서와 다른 아티팩트를 업로드합니다.
    - 갤러리를 통해 SQL 데이터베이스를 배포할 수 있도록 갤러리 패키지를 게시 합니다.
    - 호스팅 서버를 배포에 대 한 갤러리 패키지를 게시 합니다.
    - 1 단계에서 만든 하 고 다음 리소스 공급자를 설치 하는 Windows Server 2016 이미지를 사용 하 여 VM을 배포 합니다.
    - VM 리소스 공급자에 매핑하는 로컬 DNS 레코드를 등록 합니다.
    - 리소스 공급자는 로컬 Azure 리소스 관리자와 (사용자 / 관리자)을 등록합니다.
    - 필요에 따라 RP 설치 하는 동안 단일 Windows 업데이트 설치

8. 마켓플레이스 관리에서 최신 Windows Server 2016 Core 이미지를 다운로드 하는 것이 좋습니다. 업데이트를 설치 해야 할 경우에 단일을 배치할 수 있습니다. 로컬 종속성 경로에 MSU 패키지입니다. 둘 이상 있습니다. MSU 파일을 찾을 수, 스크립트가 실패 합니다.


다음은 예제 PowerShell 프롬프트에서 실행할 수 있습니다. (해야 계정 정보 및 필요에 따라 암호를 변경 합니다.)

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 parameters
명령줄에서 이러한 매개 변수를 지정할 수 있습니다. 그렇지 않고 또는 모든 매개 변수 유효성 검사에 실패 하는 경우 필수 매개 변수를 제공 하 라는 메시지가 표시 됩니다.

| 매개 변수 이름 | 설명 | 주석 또는 default 값 |
| --- | --- | --- |
| **CloudAdminCredential** | 권한 있는 끝점에 액세스 하는 데 필요한 클라우드 관리자에 대 한 자격 증명입니다. | _필수_ |
| **AzCredential** | Azure 스택 서비스 관리자 계정에 대 한 자격 증명입니다. Azure 스택 배포에 사용한 것과 동일한 자격 증명을 사용 합니다. | _필수_ |
| **VMLocalCredential** | VM의 SQL 리소스 공급자의 로컬 관리자 계정에 대 한 자격 증명입니다. | _필수_ |
| **PrivilegedEndpoint** | IP 주소 또는 권한 있는 끝점의 DNS 이름입니다. |  _필수_ |
| **DependencyFilesLocalPath** | 인증서.pfx 파일에이 디렉터리에 배치 되어야 합니다. | _선택적_ (_필수_ 다중 노드) |
| **DefaultSSLCertificatePassword** | .Pfx 인증서에 대 한 암호입니다. | _필수_ |
| **MaxRetryCount** | 오류가 발생 하는 경우 각 작업을 다시 시도 하는 횟수입니다.| 2 |
| **RetryDuration** | 초 후에 다시 시도 사이의 시간 제한 간격입니다. | 120 |
| **제거** | 리소스 공급자와 관련 된 모든 리소스 (아래 참고 참조)를 제거 합니다. | 아니요 |
| **DebugMode** | 실패 한 경우 자동 정리를 방지합니다. | 아니요 |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Azure 스택 포털을 사용 하 여 배포 확인

> [!NOTE]
>  설치 스크립트 실행을 완료 한 후 관리 블레이드를 확인 하기 위해 포털을 새로 고쳐야 할 수 있습니다.


1. 서비스 관리자는 관리 포털에 로그인 합니다.

2. 배포에 성공 했는지 확인 합니다. 로 이동 **리소스 그룹**합니다. 다음을 선택 된 **시스템.\< 위치\>.sqladapter** 리소스 그룹입니다. 모든 4 개의 배포에 성공 했는지 확인 합니다.

      ![SQL 리소스 공급자의 배포를 확인 합니다.](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="update-the-sql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>SQL 리소스 공급자 어댑터 (다중 노드, 빌드만 1710 이상)를 업데이트 합니다.
Azure 스택 빌드를 업데이트할 때 새 SQL 리소스 공급자 어댑터를 해제 될 수 있습니다. 기존 어댑터를 작동 하는 동안에 최대한 빨리 최신 빌드를 업데이트 하는 것이 좋습니다. 순서로 업데이트를 설치 해야: 버전을 건너뛸 수 없습니다 (참조 테이블의 3 단계에서 [리소스 공급자를 배포](#deploy-the-resource-provider)).

사용 하는 리소스 공급자의 업데이트는 *UpdateSQLProvider.ps1* 스크립트입니다. 프로세스에 설명 된 대로 리소스 공급자를 설치 하는 데 사용 하는 프로세스와 비슷합니다는 [리소스 공급자를 배포](#deploy-the-resource-provider) 이 문서의 섹션. 이 스크립트는 리소스 공급자의 다운로드에 포함 합니다.

*UpdateSQLProvider.ps1* 스크립트 최신 리소스 공급자 코드를 사용 하 여 새 VM를 만들고는 이전 VM 설정을 새 VM을 마이그레이션합니다. 마이그레이션되는 설정에는 데이터베이스 및 호스팅 서버 정보를 포함 하 고 필요한 DNS 레코드입니다.

DeploySqlProvider.ps1 스크립트에 대 한 설명 하는 동일한 인수를 사용을 해야 하는 스크립트입니다. 여기에 인증서도 제공 합니다. 

마켓플레이스 관리에서 최신 Windows Server 2016 Core 이미지를 다운로드 하는 것이 좋습니다. 업데이트를 설치 해야 할 경우에 단일을 배치할 수 있습니다. 로컬 종속성 경로에 MSU 패키지입니다. 둘 이상 있습니다. MSU 파일을 찾을 수, 스크립트가 실패 합니다.

다음은의 예는 *UpdateSQLProvider.ps1* PowerShell 프롬프트에서 실행할 수 있는 스크립트입니다. 계정 정보 및 필요에 따라 암호를 변경 해야 합니다. 

> [!NOTE]
> 업데이트 프로세스는 통합 된 시스템에만 적용 됩니다.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="updatesqlproviderps1-parameters"></a>UpdateSQLProvider.ps1 parameters
명령줄에서 이러한 매개 변수를 지정할 수 있습니다. 그렇지 않고 또는 모든 매개 변수 유효성 검사에 실패 하는 경우 필수 매개 변수를 제공 하 라는 메시지가 표시 됩니다.

| 매개 변수 이름 | 설명 | 주석 또는 default 값 |
| --- | --- | --- |
| **CloudAdminCredential** | 권한 있는 끝점에 액세스 하는 데 필요한 클라우드 관리자에 대 한 자격 증명입니다. | _필수_ |
| **AzCredential** | Azure 스택 서비스 관리자 계정에 대 한 자격 증명입니다. Azure 스택을 배포 하는 데 사용한 것과 동일한 자격 증명을 사용 합니다. | _필수_ |
| **VMLocalCredential** | VM의 SQL 리소스 공급자의 로컬 관리자 계정에 대 한 자격 증명입니다. | _필수_ |
| **PrivilegedEndpoint** | IP 주소 또는 권한 있는 끝점의 DNS 이름입니다. |  _필수_ |
| **DependencyFilesLocalPath** | 인증서.pfx 파일에이 디렉터리에 배치 되어야 합니다. | _선택적_ (_필수_ 다중 노드) |
| **DefaultSSLCertificatePassword** | .Pfx 인증서에 대 한 암호입니다. | _필수_ |
| **MaxRetryCount** | 오류가 발생 하는 경우 각 작업을 다시 시도 하는 횟수입니다.| 2 |
| **RetryDuration** |초 후에 다시 시도 사이의 시간 제한 간격입니다. | 120 |
| **제거** | 리소스 공급자와 관련 된 모든 리소스 (아래 참고 참조)를 제거 합니다. | 아니요 |
| **DebugMode** | 실패 한 경우 자동 정리를 방지합니다. | 아니요 |


## <a name="collect-diagnostic-logs"></a>진단 로그를 수집 합니다.
SQL 리소스 공급자를 통해 가상 컴퓨터는 잠금 되어 있습니다. PowerShell 관리 JEA (Just Enough) 끝점은 가상 컴퓨터에서 로그를 수집 해야 하는 경우 _DBAdapterDiagnostics_ 목적으로 제공 됩니다. 이 끝점을 통해 사용할 수 있는 두 개의 명령입니다.

* Get AzsDBAdapterLog-RP 진단 로그를 포함 하는 zip 패키지를 준비 하 고 세션 사용자 드라이브에 저장 합니다. 명령 매개 변수 없이 호출 될 수 있으며 로그의 마지막 4 시간을 수집 합니다.
* 리소스 공급자 VM에서 기존 로그 패키지를 제거 AzsDBAdapterLog-정리

사용자 계정 이라는 _dbadapterdiag_ RP 배포 또는 RP 로그를 추출 하기 위한 진단 끝점에 연결 하기 위한 업데이트 중에 만들어집니다. 이 계정은 암호가 배포/업데이트 중에 로컬 관리자 계정에 대해 제공 된 암호와 동일 합니다.

이러한 명령을 사용 하 여 리소스 공급자 가상 컴퓨터에 원격 PowerShell 세션을 만들고 명령을 호출 해야 합니다. 필요에 따라 FromDate 및 ToDate 매개 변수를 제공할 수 있습니다. 하나 또는 둘 다 지정 하지 않으면의 FromDate을 현재 시간 전 4 시간 되며는 ToDate 현재 시간이 됩니다.

이 샘플 스크립트 이러한 명령의 사용을 보여 줍니다.

```
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<see above>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, $diagnosticsUserPassword)
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample captures logs from the previous one hour
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup logs
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session
$session | Remove-PSSession
```

## <a name="maintenance-operations-integrated-systems"></a>유지 관리 작업 (통합된 시스템)
SQL 리소스 공급자를 통해 가상 컴퓨터는 잠금 되어 있습니다. PowerShell 관리 JEA (Just Enough) 끝점을 통해 리소스 공급자 가상 컴퓨터의 보안 업데이트를 수행할 수 있습니다 _DBAdapterMaintenance_합니다.

스크립트는 이러한 작업을 용이 하 게 하려면 RP의 설치 패키지와 함께 제공 됩니다.

### <a name="update-the-virtual-machine-operating-system"></a>가상 컴퓨터 운영 체제를 업데이트 합니다.
Windows Server VM을 업데이트 하는 방법은 여러 가지가 있습니다.
* 현재 패치가 적용 된 Windows Server 2016 Core 이미지를 사용 하 여 최신 리소스 공급자 패키지 설치
* 설치 또는 RP 업데이트 하는 동안 Windows Update 패키지를 설치 합니다.


### <a name="update-the-virtual-machine-windows-defender-definitions"></a>가상 컴퓨터의 Windows Defender 정의 업데이트

Defender 정의 업데이트 하려면 다음이 단계를 수행 합니다.

1. Windows Defender 정의 업데이트에서 다운로드 [Windows Defender 정의](https://www.microsoft.com/en-us/wdsi/definitions)

    이 페이지에서 다운로드에 "수동으로 다운로드 및 설치 정의"에서 "Windows 10 및 Windows 8.1" 64 비트 파일에 대 한 Windows Defender 바이러스 백신 합니다. 
    
    직접 링크: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64

2. SQL RP 어댑터 가상 컴퓨터의 유지 관리 끝점에 대 한 PowerShell 세션 만들기
3. 유지 관리 끝점 세션을 사용 하 여 DB 어댑터 컴퓨터로 정의 업데이트 파일을 복사 합니다.
4. 세션에서 유지 관리 PowerShell 호출의 _업데이트 DBAdapterWindowsDefenderDefinitions_ 명령
5. 설치 후 사용 되는 정의 업데이트 파일을 제거 하는 것이 좋습니다. 사용 하 여 유지 관리 세션에서 제거할 수 있습니다는 _제거 ItemOnUserDrive)_ 명령입니다.


(주소 또는 실제 값으로 가상 컴퓨터의 이름으로 대체) Defender 정의 업데이트 하는 샘플 스크립트는 다음과 같습니다.

```
# Set credentials for the diagnostic user
$diagPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$diagCreds = New-Object System.Management.Automation.PSCredential `
    ("dbadapterdiag", $vmLocalAdminPass)$diagCreds = Get-Credential

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "XX.XX.XX.XX"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"
 
# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64 `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $diagCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\mpam-fe.exe"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinitions -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```

## <a name="remove-the-sql-resource-provider-adapter"></a>SQL 리소스 공급자 어댑터 제거

리소스 공급자를 제거 하려면 먼저 모든 종속성을 제거에 필수적입니다.

1. 이 버전의 SQL 리소스 공급자 어댑터에 대 한 다운로드 하는 원래 배포 패키지가 있는지 확인 합니다.

2. 리소스 공급자에서 모든 사용자 데이터베이스를 삭제 해야 합니다. (사용자 데이터베이스를 삭제 하지 않는 삭제 데이터입니다.) 이 태스크는 사용자가 직접 수행 되어야 합니다.

3. 관리자는 SQL 리소스 공급자 어댑터에서 호스팅 서버를 삭제 해야 합니다.

4. 관리자는 SQL 리소스 공급자 어댑터 참조 하는 모든 계획을 삭제 해야 합니다.

5. 관리자는 모든 Sku 및 SQL 리소스 공급자 어댑터와 관련 된 할당량 삭제 해야 합니다.

6. 다음 요소를 사용 하 여 배포 스크립트를 다시 실행 하십시오.
    - -매개 변수를 제거 합니다.
    - Azure 리소스 관리자 끝점
    - DirectoryTenantID
    - 서비스 관리자 계정에 대 한 자격 증명


## <a name="next-steps"></a>다음 단계

[호스팅 서버 추가](azure-stack-sql-resource-provider-hosting-servers.md) 및 [데이터베이스를 만들](azure-stack-sql-resource-provider-databases.md)합니다.

다른 시도 [PaaS 서비스](azure-stack-tools-paas-services.md) 같은 [MySQL Server 리소스 공급자](azure-stack-mysql-resource-provider-deploy.md) 및 [응용 프로그램 서비스 리소스 공급자](azure-stack-app-service-overview.md)합니다.

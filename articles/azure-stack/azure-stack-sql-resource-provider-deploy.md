---
title: "SQL 데이터베이스를 사용 하 여 Azure 스택에 | Microsoft Docs"
description: "SQL 데이터베이스에서 SQL Server 리소스 공급자 어댑터를 배포 하는 Azure 스택 및 빠른 단계 서비스로 배포 하는 방법을 알아봅니다"
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: JeffGo
ms.openlocfilehash: 4d6683a1a80dfdccdc5d46e9bac095a0d9f4d3e1
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/05/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>SQL 데이터베이스를 사용 하 여 Microsoft Azure 스택

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

SQL Server 리소스 공급자 어댑터를 사용 하 여 SQL 데이터베이스의 서비스로 노출할 [Azure 스택](azure-stack-poc.md)합니다. 리소스 공급자를 설치 하 고 하나 이상의 SQL Server 인스턴스에 연결 하 고 사용자가 만들 수 있습니다.:
- 클라우드-네이티브 앱에 대 한 데이터베이스
- SQL에 기반 하는 웹 사이트
- SQL 있습니다 기반으로 하는 작업 부하를 가상 컴퓨터를 프로비저닝 (VM) SQL Server를 호스팅하는 각 시간 필요가 없습니다.

리소스 공급자의 모든 데이터베이스 관리 기능을 지원 하지 않습니다 [Azure SQL 데이터베이스](https://azure.microsoft.com/services/sql-database/)합니다. 예를 들어, 탄력적 데이터베이스 풀과 자동으로 데이터베이스 성능 및 축소 하는 기능이 사용할 수 없습니다. 그러나 유사한 리소스 공급자가 지원 만들기, 읽기, 업데이트 및 삭제 (CRUD) 작업. API는 SQL DB와 호환 되지 않습니다.

## <a name="sql-resource-provider-adapter-architecture"></a>SQL 리소스 공급자 어댑터 아키텍처
리소스 공급자는 세 가지 구성 요소가 구성 됩니다.

- **SQL 리소스 공급자 어댑터 VM**되는 공급자 서비스를 실행 하는 Windows 가상 컴퓨터.
- **리소스 공급자 자체**준비 요청을 처리 하는, 및 데이터베이스 리소스를 노출 합니다.
- **SQL Server를 호스트 하는 서버**, 데이터베이스에 대 한 용량을 제공 하는 호스팅 서버를 호출 합니다.

하나 이상의 SQL server를 만들고 외부 SQL 인스턴스에 대 한 액세스를 제공 해야 합니다.

## <a name="deploy-the-resource-provider"></a>리소스 공급자를 배포

1. 아직 수행 경우 개발 키트를 등록 하 고 Marketplace 관리를 통해 다운로드할 수 있는 Windows Server 2016 데이터 센터 Core 이미지를 다운로드 합니다. Windows Server 2016 Core 이미지를 사용 해야 합니다. 만드는 스크립트를 사용할 수도 있습니다는 [Windows Server 2016 이미지](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image) -core 옵션을 선택 해야 합니다. .NET 3.5 런타임에서 더 이상 필요 합니다.

2. 권한 있는 끝점 VM에 액세스할 수 있는 호스트에 로그인 합니다.

    a. Azure 스택 개발 키트 (ASDK) 설치에서 실제 호스트에 로그인 합니다.

    나. 다중 노드 시스템에 호스트 권한 있는 끝점에 액세스할 수 있는 시스템 이어야 합니다. 
    
    >[!NOTE]
    > 스크립트가 실행 되 고 시스템 *해야* 최신 버전의.NET 런타임 설치 된 Windows 10 또는 Windows Server 2016 시스템 이어야 합니다. 그렇지 않은 경우에 설치가 실패 합니다. ASDK 호스트는이 조건을 충족 합니다.


3. 이진 SQL 리소스 공급자를 다운로드 하 고 임시 디렉터리에 콘텐츠를 추출 자동 압축 풀기 프로그램을 실행 합니다.

    >[!NOTE] 
    > 리소스 공급자 빌드 Azure 스택 빌드에 해당합니다. Azure 스택 실행 되는 버전에 대 한 올바른 이진을 다운로드 해야 합니다.

    | Azure 스택 빌드 | SQL RP 설치 관리자 |
    | --- | --- |
    | 1.0.180102.3 | **추가 정보에 대 한 잠시 기다려 주십시오, 현재 빌드를 설치 하지 것입니다 되지만 multi-node에서 Azure 스택 업그레이드 한 후 실행 계속 됩니다.** |
    | 1.0.171122.1 | [SQL RP 1.1.12.0 버전](https://aka.ms/azurestacksqlrp) |
    | 1.0.171028.1 | [SQL RP 1.1.8.0 버전](https://aka.ms/azurestacksqlrp1710) |
    | 1.0.170928.3 | [SQL RP 1.1.3.0 버전](https://aka.ms/azurestacksqlrp1709) |
   

4. Azure 스택 루트 인증서는 권한 있는 끝점에서 검색 됩니다. ASDK에 대 한 자체 서명 된 인증서는이 프로세스의 일부로 생성 됩니다. 다중 노드에 대 한 적절 한 인증서를 제공 해야 합니다.

    PFX 파일에 배치 해야는 고유의 인증서를 제공 해야 하는 경우는 **DependencyFilesLocalPath** (아래 참조) 다음과 같습니다.

    - 에 대 한 와일드 카드 인증서 \*.dbadapter.\< 지역\>.\< 외부 fqdn\> 또는 일반 sqladapter.dbadapter 이름의 단일 사이트 인증서.\< 지역\>.\< 외부 fqdn\>
    - 이 인증서는 신뢰할 수 있어야 합니다, 그리고와 같은 인증 기관에서 발급 될 것입니다. 즉, 중간 인증서를 요구 하지 않고 신뢰 체인 존재 해야 합니다.
    - 단일 인증서 파일이는 DependencyFilesLocalPath에 있습니다.
    - 파일 이름에 특수 문자가 없어야 합니다.


5. 열기는 **새** 관리자 권한 (관리자) PowerShell 콘솔 및 파일의 압축을 푼 디렉터리로 변경 합니다. 새 창을 사용 하 여 시스템에 이미 로드 되어 잘못 된 PowerShell 모듈에서 발생할 수 있는 문제를 방지 하려면.

6. [Azure PowerShell 버전 1.2.11 설치](azure-stack-powershell-install.md)합니다.

7. 아래에 나열 된 매개 변수를 가진 DeploySqlProvider.ps1 스크립트를 실행 합니다.

스크립트는 다음이 단계를 수행합니다.

- Azure 스택의 저장소 계정에는 인증서와 다른 아티팩트를 업로드 합니다.
- 갤러리를 통해 SQL 데이터베이스를 배포할 수 있도록 갤러리 패키지를 게시 합니다.
- 호스팅 서버를 배포에 대 한 갤러리 패키지를 게시 합니다.
- 1 단계에서 만든 Windows Server 2016 이미지를 사용 하는 VM을 배포 하 고 리소스 공급자를 설치 합니다.
- VM 리소스 공급자에 매핑되는 로컬 DNS 레코드를 등록 합니다.
- 리소스 공급자는 로컬 Azure 리소스 관리자와 (사용자 / 관리자)을 등록 합니다.

> [!NOTE]
> 설치 들어 이상 90 분이 걸리는 경우 실패할 수 있습니다 및 실패 메시지를 화면의 및 로그 파일에 올바르게 표시 되는데 배포 실패 한 단계에서 다시 시도 됩니다. 시스템 메모리와 vCPU 권장된 사양을 충족 하지 않는 SQL RP 배포할 수 수 있습니다.
>

다음은 PowerShell에서 실행할 수는 예제 메시지를 표시 합니다 (그러나 필요에 따라 계정 정보 및 암호 변경):

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On ASDK, the default is AzureStack and the default prefix is AzS
# For integrated systems, the domain and the prefix will be the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the RP installation files were extracted
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privileged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files
# and adjust the endpoints
. $tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 매개 변수
명령줄에서 이러한 매개 변수를 지정할 수 있습니다. 그렇지 않고 모든 매개 변수 유효성 검사에 실패할 경우 필요한 것을 제공 하 라는 메시지가 표시 됩니다.

| 매개 변수 이름 | 설명 | 주석이 나 기본값 |
| --- | --- | --- |
| **CloudAdminCredential** | 권한 있는 끝점에 액세스 하는 데 필요한 클라우드 관리자에 대 한 자격 증명입니다. | _필수_ |
| **AzCredential** | Azure 스택 서비스 관리자 계정에 대 한 자격 증명을 제공 합니다. 사용 하 여 동일한 자격 증명 Azure 스택을 배포 하는 데 사용). | _필수_ |
| **VMLocalCredential** | VM의 SQL 리소스 공급자의 로컬 관리자 계정의 자격 증명을 정의 합니다. | _필수_ |
| **PrivilegedEndpoint** | Privleged 끝점의 DNS 이름 또는 IP 주소를 입력 합니다. |  _필수_ |
| **DependencyFilesLocalPath** | 인증서 PFX 파일에이 디렉터리에 배치 되어야 합니다. | _선택적_ (_필수_ 다중 노드) |
| **DefaultSSLCertificatePassword** | .Pfx 인증서에 대 한 암호 | _필수_ |
| **MaxRetryCount** | 오류가 없는 경우 각 작업을 다시 시도 하려면 실패 한 횟수를 정의 합니다.| 2 |
| **RetryDuration** | 초 후에 다시 시도 대기 중 제한 시간을 정의 합니다. | 120 |
| **제거** | 리소스 공급자와 관련 된 모든 리소스 (아래 참고 내용 참조)를 제거 합니다. | 아니요 |
| **DebugMode** | 실패 한 경우 자동 정리를 방지합니다. | 아니요 |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Azure 스택 포털을 사용 하 여 배포 확인

> [!NOTE]
>  설치 스크립트가 완료 된 후에 관리 블레이드를 확인 하기 위해 포털 새로 고침 해야 합니다.


1. 서비스 관리자는 관리 포털에 로그인 합니다.

2. 배포에 성공 했는지 확인 합니다. 에 대 한 찾아보기 **리소스 그룹** &gt;, 클릭는 **시스템.\< 위치\>.sqladapter** 리소스 그룹화 하 고 모든 4 개의 배포에 성공 했는지 확인 합니다.

      ![SQL RP의 배포를 확인 합니다.](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="update-the-sql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>SQL 리소스 공급자 어댑터 (다중 노드, 빌드만 1710 이상)를 업데이트 합니다.
Azure 스택 빌드 업데이트 될 때마다 새 SQL 리소스 공급자 어댑터 해제 됩니다. 기존 어댑터 계속 작동할 수, 하는 동안 Azure 스택 업데이트 된 후 최대한 빨리 최신 빌드를 업데이트 하는 것이 좋습니다. 업데이트 프로세스는 위에서 설명한 설치 프로세스와 매우 비슷합니다. 새 VM 최신 RP 코드로 만들고 서버 정보 뿐만 아니라 필요한 DNS 레코드를 호스팅 및 데이터베이스를 포함 하 여이 새 인스턴스를 설정이 마이그레이션됩니다.

UpdateSQLProvider.ps1 스크립트를 사용 하 여 위와 같은 동일한 인수를 사용 합니다. 여기에 인증서도 제공 해야 합니다.

> [!NOTE]
> 업데이트는 다중 노드 시스템에만 지원 됩니다.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On ASDK, the default is AzureStack and the default prefix is AzS
# For integrated systems, the domain and the prefix will be the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the RP installation files were extracted
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privileged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files
# and adjust the endpoints
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="updatesqlproviderps1-parameters"></a>UpdateSQLProvider.ps1 매개 변수
명령줄에서 이러한 매개 변수를 지정할 수 있습니다. 그렇지 않고 모든 매개 변수 유효성 검사에 실패할 경우 필요한 것을 제공 하 라는 메시지가 표시 됩니다.

| 매개 변수 이름 | 설명 | 주석이 나 기본값 |
| --- | --- | --- |
| **CloudAdminCredential** | 권한 있는 끝점에 액세스 하는 데 필요한 클라우드 관리자에 대 한 자격 증명입니다. | _필수_ |
| **AzCredential** | Azure 스택 서비스 관리자 계정에 대 한 자격 증명을 제공 합니다. 사용 하 여 동일한 자격 증명 Azure 스택을 배포 하는 데 사용). | _필수_ |
| **VMLocalCredential** | VM의 SQL 리소스 공급자의 로컬 관리자 계정의 자격 증명을 정의 합니다. | _필수_ |
| **PrivilegedEndpoint** | Privleged 끝점의 DNS 이름 또는 IP 주소를 입력 합니다. |  _필수_ |
| **DependencyFilesLocalPath** | 인증서 PFX 파일에이 디렉터리에 배치 되어야 합니다. | _선택적_ (_필수_ 다중 노드) |
| **DefaultSSLCertificatePassword** | .Pfx 인증서에 대 한 암호 | _필수_ |
| **MaxRetryCount** | 오류가 없는 경우 각 작업을 다시 시도 하려면 실패 한 횟수를 정의 합니다.| 2 |
| **RetryDuration** | 초 후에 다시 시도 대기 중 제한 시간을 정의 합니다. | 120 |
| **제거** | 리소스 공급자와 관련 된 모든 리소스 (아래 참고 내용 참조)를 제거 합니다. | 아니요 |
| **DebugMode** | 실패 한 경우 자동 정리를 방지합니다. | 아니요 |



## <a name="remove-the-sql-resource-provider-adapter"></a>SQL 리소스 공급자 어댑터 제거

리소스 공급자를 제거 하려면 먼저 모든 종속성을 제거에 필수적입니다.

1. 이 버전의 SQL 리소스 공급자 어댑터에 대 한 다운로드 하는 원래 배포 패키지를 확인 합니다.

2. 리소스 공급자 (데이터 삭제 하지 않습니다)에서 모든 사용자 데이터베이스를 삭제 해야 합니다. 이 사용자가 직접 수행 해야 합니다.

3. 관리자가 SQL 리소스 공급자 어댑터에서 호스팅 서버를 삭제 해야

4. 관리자는 SQL 리소스 공급자 어댑터 참조 하는 모든 계획을 삭제 해야 합니다.

5. 관리자는 Sku 및 SQL 리소스 공급자 어댑터에 연결 된 할당량 삭제 해야 합니다.

6. 배포 스크립트를 다시 실행-매개 변수, Azure 리소스 관리자 끝점, DirectoryTenantID, 및 서비스 관리자 계정에 대 한 자격 증명을 제거 합니다.


## <a name="next-steps"></a>다음 단계

[호스팅 서버 추가](azure-stack-sql-resource-provider-hosting-servers.md) 및 [데이터베이스를 만들](azure-stack-sql-resource-provider-databases.md)합니다.

다른 시도 [PaaS 서비스](azure-stack-tools-paas-services.md) 같은 [MySQL Server 리소스 공급자](azure-stack-mysql-resource-provider-deploy.md) 및 [응용 프로그램 서비스 리소스 공급자](azure-stack-app-service-overview.md)합니다.

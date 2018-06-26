---
title: MySQL 데이터베이스를 사용 하 여 Azure 스택에 | Microsoft Docs
description: MySQL Server 리소스 공급자 어댑터를 배포 하는 Azure 스택 및 빠른 단계에서 서비스로 MySQL 데이터베이스를 배포 하는 방법을 알아봅니다.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: e4c3eb1d7dfd4894576d5fbed52cf4de5fed9e44
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938116"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack"></a>Azure 스택 MySQL 리소스 공급자를 배포 합니다.

MySQL Server 리소스 공급자를 사용 하 여 MySQL 데이터베이스는 Azure 스택 서비스로 노출 합니다. MySQL 리소스 공급자는 Windows Server 2016 Server Core 가상 컴퓨터 (VM)에서 서비스를 실행 합니다.

## <a name="prerequisites"></a>필수 조건

Azure 스택 MySQL 리소스 공급자를 배포 하기 전에 준비 되어야 하는 몇 가지 필수 구성 요소가 있습니다. 이러한 요구를 충족 하기 위해 권한 있는 VM 끝점에 액세스할 수 있는 컴퓨터에서이 문서의 단계를 완료 합니다.

* 아직 수행 하지 않은 경우 [레지스터 Azure 스택](.\azure-stack-registration.md) azure Azure 마켓플레이스 항목을 다운로드할 수 있도록 합니다.
* 다운로드 하 여 Azure 스택 marketplace에 필요한 Windows Server 코어 VM 추가 **Windows Server 2016 Datacenter Server Core** 이미지입니다. 만드는 스크립트를 사용할 수도 있습니다는 [Windows Server 2016 이미지](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)합니다. 스크립트를 실행할 때 핵심 옵션을 선택 하 고 있는지 확인 합니다.

  >[!NOTE]
  >업데이트를 설치 해야 할 경우에 단일을 배치할 수 있습니다. 로컬 종속성 경로에 MSU 패키지입니다. 둘 이상 있습니다. MSU 파일이, MySQL 리소스 공급자 설치에 실패 합니다.

* 이진 MySQL 리소스 공급자를 다운로드 하 고 임시 디렉터리에 콘텐츠를 추출 자동 압축 풀기 프로그램을 실행 하십시오.

  >[!NOTE]
  >없는 경우 인터넷에 액세스 하는 시스템에서 MySQL 공급자를 배포 하려면 복사는 [mysql 커넥터-net 6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi) 파일을 로컬 공유 합니다. 에 대 한 메시지가 나타나면 공유 이름을 제공 합니다. Azure 및 Azure 스택 PowerShell 모듈을 설치 해야 합니다.

* 리소스 공급자에는 빌드는 최소 해당 Azure 스택 합니다. Azure 스택 실행 중인 버전에 대 한 올바른 이진을 다운로드 하 고 있는지 확인 합니다.

    | Azure 스택 버전 | MySQL RP 버전|
    | --- | --- |
    | 버전 1804 (1.0.180513.1)|[MySQL RP 1.1.24.0 버전](https://aka.ms/azurestackmysqlrp1804) |
    | 버전 1802 (1.0.180302.1) | [MySQL RP 1.1.18.0 버전](https://aka.ms/azurestackmysqlrp1802) |
    | 버전 1712 (1.0.180102.3 또는 1.0.180106.1 (통합된 시스템)) | [MySQL RP 1.1.14.0 버전](https://aka.ms/azurestackmysqlrp1712) |

### <a name="certificates"></a>인증서

ASDK에 대 한 자체 서명 된 인증서는이 설치 프로세스의 일부로 생성 됩니다. Azure 스택 통합 하는 시스템에 대 한 적절 한 인증서를 제공 해야 합니다. 고유한 인증서를 제공 해야 하는 경우에.pfx 파일을 배치는 **DependencyFilesLocalPath** 다음 조건을 만족 하 합니다.

* 에 대 한 와일드 카드 인증서 \*.dbadapter.\< 지역\>.\< 외부 fqdn\> 또는 일반 mysqladapter.dbadapter 이름의 단일 사이트 인증서.\< 지역\>.\< 외부 fqdn\>합니다.
* 인증서는 신뢰할 수 있어야 합니다. 중간 인증서를 요구 하지 않고 신뢰 체인 존재 해야 합니다.
* 단일 인증서 파일이는 DependencyFilesLocalPath에 있습니다.
* 파일 이름은 특수 문자 또는 공백을 사용할 수 없습니다.

## <a name="deploy-the-resource-provider"></a>리소스 공급자를 배포

모든 필수 구성 요소를 설치 되어 있으므로 후 실행 하는 **DeployMySqlProvider.ps1** MYSQL 리소스 공급자를 배포 하는 스크립트입니다. Azure 스택 버전에 대 한 다운로드 한 MySQL 리소스 공급자 이진 파일의 일부로 DeployMySqlProvider.ps1 스크립트 추출 됩니다.

> [!IMPORTANT]
> 스크립트를 실행 하는 시스템에 설치 된.NET 런타임의 최신 버전으로 Windows 10 또는 Windows Server 2016 시스템 이어야 합니다.

MySQL 리소스 공급자를 배포 하려면 관리자 권한으로 새 PowerShell 콘솔 창을 열고 MySQL 리소스 공급자 이진 파일의 압축을 푼 디렉터리로 변경 합니다. 이미 로드 되어 있는 PowerShell 모듈에 의해 인 한 잠재적 문제를 방지 하기 위해 새 PowerShell 창을 사용 하는 것이 좋습니다.

실행 된 **DeployMySqlProvider.ps1** 다음 작업을 완료 하는 스크립트:

* Azure 스택의 저장소 계정에는 인증서와 다른 아티팩트를 업로드합니다.
* 갤러리를 사용 하 여 MySQL 데이터베이스를 배포할 수 있도록 갤러리 패키지를 게시 합니다.
* 호스팅 서버를 배포에 대 한 갤러리 패키지를 게시 합니다.
* 다운로드 한 다음 MySQL 리소스 공급자를 설치 Windows Server 2016 core 이미지를 사용 하는 VM을 배포 합니다.
* VM 리소스 공급자에 매핑하는 로컬 DNS 레코드를 등록 합니다.
* 연산자 및 사용자 계정에 대 한 로컬 Azure 리소스 관리자와 사용자의 리소스 공급자를 등록합니다.
* 필요에 따라 리소스 공급자 설치 하는 동안 단일 Windows Server 업데이트를 설치합니다.

> [!NOTE]
> MySQL 리소스 공급자 배포를 시작할 때, **system.local.mysqladapter** 리소스 그룹이 만들어집니다. 이 리소스 그룹에 필요한 4 개의 배포를 완료 후 75 분까지 걸릴 수 있습니다.

### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1 parameters

명령줄에서 이러한 매개 변수를 지정할 수 있습니다. 이렇게 하지 않으면 또는 모든 매개 변수 유효성 검사에 실패 하는 경우 필수 매개 변수를 제공 하 라는 메시지가 표시 합니다.

| 매개 변수 이름 | 설명 | 주석 또는 default 값 |
| --- | --- | --- |
| **CloudAdminCredential** | 권한 있는 끝점에 액세스 하는 데 필요한 클라우드 관리자에 대 한 자격 증명입니다. | _필수_ |
| **AzCredential** | Azure 스택 서비스 관리자 계정에 대 한 자격 증명입니다. Azure 스택을 배포 하는 데 사용한 것과 동일한 자격 증명을 사용 합니다. | _필수_ |
| **VMLocalCredential** | VM의 MySQL 리소스 공급자의 로컬 관리자 계정에 대 한 자격 증명입니다. | _필수_ |
| **PrivilegedEndpoint** | IP 주소 또는 권한 있는 끝점의 DNS 이름입니다. |  _필수_ |
| **DependencyFilesLocalPath** | 경로를 포함 하는 로컬 공유 [mysql 커넥터-net 6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi)합니다. 이러한 경로 중 하나를 제공 하는 경우 인증서 파일을이 디렉터리에 배치 되어야 합니다. | _선택적_ 단일 노드에 대 한 _필수_ 다중 노드에 대 한 합니다. |
| **DefaultSSLCertificatePassword** | .Pfx 인증서에 대 한 암호입니다. | _필수_ |
| **MaxRetryCount** | 오류가 발생 하는 경우 각 작업을 다시 시도 하는 횟수입니다.| 2 |
| **RetryDuration** | 초 후에 다시 시도 사이의 시간 제한 간격입니다. | 120 |
| **제거** | 리소스 공급자와 관련 된 모든 리소스 (아래 참고 참조)를 제거 합니다. | 아니요 |
| **DebugMode** | 실패 한 경우 자동 정리를 방지합니다. | 아니요 |
| **AcceptLicense** | GPL 라이선스를 수락 하 라는 메시지가 건너뜁니다.  <http://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

> [!NOTE]
> Sku 포털에 표시 되도록 최대 한 시간이 걸릴 수 있습니다. SKU에 배포 하 고 실행 될 때까지 데이터베이스를 만들 수 없습니다.

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>사용자 지정 스크립트를 사용 하 여 MySQL 리소스 공급자를 배포 합니다.

수동 구성 리소스 공급자를 배포 하는 경우를 제거 하려면 다음 스크립트를 사용자 지정할 수 있습니다. Azure 스택 배포에 필요한 기본 계정 정보 및 암호를 변경 합니다.

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"  

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Run the installation script from the folder where you extracted the installation files.
# Find the ERCS01 IP address first, and make sure the certificate file is in the specified directory.
. $tempDir\DeployMySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert `
    -AcceptLicense

```

리소스 공급자 설치 스크립트가 완료 되 면 최신 업데이트를 볼 수 있도록 하려면 브라우저를 새로 고칩니다.

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Azure 스택 포털을 사용 하 여 배포를 확인 합니다.

1. 서비스 관리자는 관리 포털에 로그인 합니다.
2. 선택 **리소스 그룹**
3. 선택 된 **시스템.\< 위치\>.mysqladapter** 리소스 그룹입니다.
4. 리소스에 대 한 요약 페이지에서 그룹 개요, 아래 메시지 **배포** 해야 **3 Succeeded**합니다.
5. 리소스 공급자 배포에 대 한 더 자세한 정보를 얻을 수 **설정을**합니다. 선택 **배포** 같은 정보를 가져오는 데: 상태, 타임 스탬프 및 각 배포에 대 한 기간입니다.

## <a name="next-steps"></a>다음 단계

[호스팅 서버 추가](azure-stack-mysql-resource-provider-hosting-servers.md)

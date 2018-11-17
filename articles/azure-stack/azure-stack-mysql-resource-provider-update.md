---
title: Azure Stack MySQL 리소스 공급자를 업데이트 하는 중 | Microsoft Docs
description: Azure Stack MySQL 리소스 공급자를 업데이트 하는 방법을 알아봅니다.
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
ms.date: 11/15/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: 36d37adff6d45b7d154511c11888aad090c54638
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853679"
---
# <a name="update-the-mysql-resource-provider"></a>MySQL 리소스 공급자 업데이트 

*적용 대상: Azure Stack 통합 시스템입니다.*

Azure Stack 빌드를 업데이트할 때 새 SQL 리소스 공급자 어댑터를 해제 될 수 있습니다. 기존 어댑터를 작동 하는 동안에 가능한 한 빨리 최신 빌드를 업데이트 하는 것이 좋습니다. 

> [!IMPORTANT]
> 릴리스되는 순서 대로 업데이트를 설치 해야 합니다. 버전을 건너뛸 수 없습니다. 버전 목록 가리킵니다 [리소스 공급자 필수 구성 요소 배포](.\azure-stack-mysql-resource-provider-deploy.md#prerequisites)합니다.

## <a name="update-the-mysql-resource-provider-adapter-integrated-systems-only"></a>MySQL 리소스 공급자 어댑터 (통합된 시스템에만 해당) 업데이트

Azure Stack 빌드를 업데이트할 때 새 SQL 리소스 공급자 어댑터를 해제 될 수 있습니다. 기존 어댑터를 작동 하는 동안에 가능한 한 빨리 최신 빌드를 업데이트 하는 것이 좋습니다.  
 
사용 하는 리소스 공급자 업데이트를 **UpdateMySQLProvider.ps1** 스크립트입니다. 프로세스는에 설명 된 대로 리소스 공급자를 설치 하는 데 사용 하는 프로세스와 유사 합니다 [리소스 공급자 배포](#deploy-the-resource-provider) 이 문서의 섹션입니다. 스크립트는 리소스 공급자의 다운로드에 포함 되어 있습니다. 

합니다 **UpdateMySQLProvider.ps1** 스크립트 최신 리소스 공급자 코드를 사용 하 여 새 VM을 만들고 설정을 기존 VM에서 새 VM을 마이그레이션합니다. 필요한 DNS 레코드 및 설정을 마이그레이션하는 데이터베이스 및 호스팅 서버 정보를 포함 됩니다. 

>[!NOTE]
>Marketplace 관리에서 최신 Windows Server 2016 Core 이미지를 다운로드 하는 것이 좋습니다. 업데이트를 설치 해야 하는 경우 배치할 수 있습니다는 **단일** 로컬 종속성 경로에서 MSU 패키지 있습니다. 스크립트는이 위치에 MSU 파일이 둘 이상 있으면 실패 합니다.

DeployMySqlProvider.ps1 스크립트에 대 한 설명 된 동일한 인수를 사용을 해야 하는 스크립트입니다. 여기에 인증서도 제공 합니다.  

다음은의 예는 *UpdateMySQLProvider.ps1* PowerShell 프롬프트에서 실행할 수 있는 스크립트입니다. 계정 정보 및 필요에 따라 암호를 변경 해야 합니다.  

> [!NOTE] 
> 업데이트 프로세스를 통합된 시스템에만 적용 됩니다. 

```powershell 
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.5.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time. 
$domain = "AzureStack" 

# For integrated systems, use the IP address of one of the ERCS virtual machines 
$privilegedEndpoint = "AzS-ERCS01" 

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted. 
$tempDir = 'C:\TEMP\MYSQLRP' 

# The service admin account (can be Azure Active Directory or Active Directory Federation Services). 
$serviceAdmin = "admin@mydomain.onmicrosoft.com" 
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 
 
# Set credentials for the new resource provider VM. 
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass) 
 
# And the cloudadmin credential required for privileged endpoint access. 
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass) 

# Change the following as appropriate. 
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
 
# Change directory to the folder where you extracted the installation files. 
# Then adjust the endpoints. 
$tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds ` 
-VMLocalCredential $vmLocalAdminCreds ` 
-CloudAdminCredential $cloudAdminCreds ` 
-PrivilegedEndpoint $privilegedEndpoint ` 
-AzureEnvironment $AzureEnvironment `
-DefaultSSLCertificatePassword $PfxPass ` 
-DependencyFilesLocalPath $tempDir\cert ` 
-AcceptLicense 
``` 
 
### <a name="updatemysqlproviderps1-parameters"></a>UpdateMySQLProvider.ps1 매개 변수 
명령줄에서 이러한 매개 변수를 지정할 수 있습니다. 그렇지 않으면, 모든 매개 변수 유효성 검사에 실패 하는 경우 필수 매개 변수를 제공 하 라는 메시지가 표시 됩니다. 

| 매개 변수 이름 | 설명 | 주석 또는 기본 값 | 
| --- | --- | --- | 
| **CloudAdminCredential** | 권한 있는 끝점에 액세스 하는 데 필요한 클라우드 관리자에 대 한 자격 증명입니다. | _필수_ | 
| **AzCredential** | Azure Stack에 대 한 자격 증명을 서비스 관리자 계정입니다. Azure Stack 배포에 사용한 것과 동일한 자격 증명을 사용 합니다. | _필수_ | 
| **VMLocalCredential** |SQL 리소스 공급자 VM의 로컬 관리자 계정의 자격 증명입니다. | _필수_ | 
| **PrivilegedEndpoint** | IP 주소 또는 권한 있는 끝점의 DNS 이름입니다. |  _필수_ | 
| **AzureEnvironment** | Azure Stack을 배포 하기 위한 사용 하는 서비스 관리자 계정의 Azure 환경입니다. Azure AD 배포에만 필요합니다. 지원 되는 환경 이름은 **AzureCloud**를 **AzureUSGovernment**에 중국의 Azure AD를 사용 하는 경우 또는 **AzureChinaCloud**합니다. | AzureCloud |
| **DependencyFilesLocalPath** | 인증서.pfx 파일에서이 디렉터리에 배치 되어야 합니다. | _선택적_ (_필수_ 다중 노드) | 
| **DefaultSSLCertificatePassword** | .Pfx 인증서에 대 한 암호입니다. | _필수_ | 
| **MaxRetryCount** | 오류가 발생 하는 경우 각 작업을 다시 시도 하려는 횟수입니다.| 2 | 
| **RetryDuration** | 시간 (초)에서 재시도 사이의 시간 제한 간격입니다. | 120 | 
| **제거** | 리소스 공급자와 연결 된 모든 리소스 (아래 참고 참조)를 제거 합니다. | 아닙니다. | 
| **DebugMode** | 실패 한 경우 자동 정리를 방지합니다. | 아닙니다. | 
| **AcceptLicense** | GPL 라이선스에 동의 하는 메시지를 건너뜁니다.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 
 

## <a name="next-steps"></a>다음 단계
[MySQL 리소스 공급자를 유지 관리](azure-stack-mysql-resource-provider-maintain.md)

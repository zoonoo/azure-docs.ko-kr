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
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 061d9431890622dafafa8c180027b8df735c100e
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295757"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>MySQL 데이터베이스를 사용 하 여 Microsoft Azure 스택
Azure 스택 MySQL Server 리소스 공급자를 사용 하 여 MySQL 데이터베이스를 Azure 스택의 서비스로 노출 합니다. MySQL 리소스 공급자 서비스는 MySQL 리소스 공급자는 Windows Server 코어 가상 컴퓨터는 VM에서 실행 됩니다.

## <a name="deploy-the-resource-provider"></a>리소스 공급자를 배포 
1. 아직 수행 경우 개발 키트를 등록 하 고 Marketplace 관리를 통해 다운로드할 수 있는 Windows Server 2016 데이터 센터 Core 이미지를 다운로드 합니다. Windows Server 2016 Core 이미지를 사용 해야 합니다. 만드는 스크립트를 사용할 수도 있습니다는 [Windows Server 2016 이미지](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)합니다. (해야 코어 옵션을 선택 합니다.) 

2. VM 권한 있는 끝점에 액세스할 수 있는 호스트에 로그인 합니다.
    - Azure SDK 설치에서 실제 호스트에 로그인 합니다.  
    - 통합된 시스템에 호스트 권한 있는 끝점에 액세스할 수 있는 시스템 이어야 합니다. 
    
    >[!NOTE] 
    > 스크립트가 실행 되 고 시스템 *해야* 최신 버전의.NET 런타임 설치 된 Windows 10 또는 Windows Server 2016 시스템 이어야 합니다. 그렇지 않으면 설치에 실패 합니다. Azure 스택 ASDK 호스트는이 조건을 충족합니다. 
    
3. 이진 MySQL 리소스 공급자를 다운로드 합니다. 그런 다음 실행을 임시 디렉터리에 콘텐츠를 추출 자동 압축 풀기. 
    >[!NOTE]  
    > 리소스 공급자에는 빌드는 최소 해당 Azure 스택 합니다. 실행 하는 Azure 스택 버전에 대 한 올바른 이진을 다운로드 해야 합니다.

    | Azure 스택 버전 | MySQL RP 버전| 
    | --- | --- | 
    | 버전 1804 (1.0.180513.1)|[MySQL RP 1.1.24.0 버전](https://aka.ms/azurestackmysqlrp1804) | 
    | 버전 1802 (1.0.180302.1) | [MySQL RP 1.1.18.0 버전](https://aka.ms/azurestackmysqlrp1802) | 
    | 버전 1712 (1.0.180102.3 또는 1.0.180106.1 (통합된 시스템)) | [MySQL RP 1.1.14.0 버전](https://aka.ms/azurestackmysqlrp1712) | 

4.  ASDK에 대 한 자체 서명 된 인증서는이 프로세스의 일부로 생성 됩니다. 통합된 시스템에 대 한 적절 한 인증서를 제공 해야 합니다. 고유한 인증서를 제공 해야 하는 경우에.pfx 파일을 배치는 **DependencyFilesLocalPath** 다음 조건을 만족 하 합니다. 
    - 에 대 한 와일드 카드 인증서 \*.dbadapter.\< 지역\>.\< 외부 fqdn\> 또는 일반 mysqladapter.dbadapter 이름의 단일 사이트 인증서.\< 지역\>.\< 외부 fqdn\>합니다. 
    - 이 인증서는 신뢰할 수 있어야 합니다. 즉, 중간 인증서를 요구 하지 않고 신뢰 체인 존재 해야 합니다. 
    - 단일 인증서 파일이는 DependencyFilesLocalPath에 있습니다. 
    - 파일 이름은 특수 문자 또는 공백을 사용할 수 없습니다. 

5. 열기는 **새** 관리자 권한 (관리자) PowerShell 콘솔. 다음 파일의 압축을 푼 디렉터리로 변경 합니다. 시스템에 이미 로드 되어 있는 잘못 된 PowerShell 모듈에서 발생할 수 있는 문제를 방지 하기 위해 새 창을 사용 합니다. 

6. 실행 된 **DeployMySqlProvider.ps1** 스크립트입니다. 스크립트는 다음이 단계를 수행합니다. 
    - (이 제공 될 수 있습니다 오프 라인)는 MySQL 커넥터 이진을 다운로드 합니다. 
    - Azure 스택의 저장소 계정에는 인증서와 다른 아티팩트를 업로드합니다. 
    - 갤러리를 통해 SQL 데이터베이스를 배포할 수 있도록 갤러리 패키지를 게시 합니다. 
    - 호스팅 서버를 배포에 대 한 갤러리 패키지를 게시 합니다. 
    - Windows Server 2016 Azure 스택 마켓플레이스 이미지를 사용 하 여 VM을 배포 하 고 리소스 공급자를 설치 합니다. 
    - VM 리소스 공급자에 매핑하는 로컬 DNS 레코드를 등록 합니다. 
    - 로컬 Azure 리소스 관리자 (테 넌 트 및 관리자) 리소스 공급자를 등록합니다. 

    명령줄에 필요한 매개 변수 지정 일 수 있습니다, 매개 변수 없이 스크립트를 실행 하 고 메시지가 표시 되 면 해당를 입력 합니다. 

    다음은 예제 PowerShell 프롬프트에서 실행할 수 있습니다. 계정 정보 및 필요에 따라 암호를 변경 해야 합니다. 

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
    # Find the ERCS01 IP address first, and make sure the certificate 
    # file is in the specified directory. 
    $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds ` 
        -VMLocalCredential $vmLocalAdminCreds ` 
        -CloudAdminCredential $cloudAdminCreds ` 
        -PrivilegedEndpoint $privilegedEndpoint ` 
        -DefaultSSLCertificatePassword $PfxPass ` 
        -DependencyFilesLocalPath $tempDir\cert ` 
        -AcceptLicense 
    ``` 

### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1 parameters 
명령줄에서 이러한 매개 변수를 지정할 수 있습니다. 그렇지 않고 또는 모든 매개 변수 유효성 검사에 실패 하는 경우 필수 매개 변수를 제공 하 라는 메시지가 표시 됩니다. 

| 매개 변수 이름 | 설명 | 주석 또는 default 값 | 
| --- | --- | --- | 
| **CloudAdminCredential** | 권한 있는 끝점에 액세스 하는 데 필요한 클라우드 관리자에 대 한 자격 증명입니다. | _필수_ | 
| **AzCredential** | Azure 스택 서비스 관리자 계정에 대 한 자격 증명입니다. Azure 스택을 배포 하는 데 사용한 것과 동일한 자격 증명을 사용 합니다. | _필수_ | 
| **VMLocalCredential** | VM의 MySQL 리소스 공급자의 로컬 관리자 계정에 대 한 자격 증명입니다. | _필수_ | 
| **PrivilegedEndpoint** | IP 주소 또는 권한 있는 끝점의 DNS 이름입니다. |  _필수_ | 
| **DependencyFilesLocalPath** | 경로를 포함 하는 로컬 공유 [mysql 커넥터-net 6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi)합니다. 이러한 경로 중 하나를 제공 하는 경우 인증서 파일을이 디렉터리에 배치 되어야 합니다. | _선택적_ (_필수_ 다중 노드) | 
| **DefaultSSLCertificatePassword** | .Pfx 인증서에 대 한 암호입니다. | _필수_ | 
| **MaxRetryCount** | 오류가 발생 하는 경우 각 작업을 다시 시도 하는 횟수입니다.| 2 | 
| **RetryDuration** | 초 후에 다시 시도 사이의 시간 제한 간격입니다. | 120 | 
| **제거** | 리소스 공급자와 관련 된 모든 리소스 (아래 참고 참조)를 제거 합니다. | 아니요 | 
| **DebugMode** | 실패 한 경우 자동 정리를 방지합니다. | 아니요 | 
| **AcceptLicense** | GPL 라이선스를 수락 하 라는 메시지가 건너뜁니다.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

> [!NOTE] 
> Sku 포털에 표시 되도록 최대 한 시간이 걸릴 수 있습니다. SKU 만들어질 때까지 데이터베이스를 만들 수 없습니다. 

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Azure 스택 포털을 사용 하 여 배포를 확인 합니다. 
> [!NOTE] 
>  설치 스크립트 실행을 완료 한 후 관리 블레이드를 확인 하기 위해 포털을 새로 고쳐야 할 수 있습니다. 

1. 서비스 관리자는 관리 포털에 로그인 합니다. 
2. 배포에 성공 했는지 확인 합니다. 로 이동 **리소스 그룹**를 선택한 후는 **시스템.\< 위치\>.mysqladapter** 리소스 그룹입니다. 모든 4 개의 배포에 성공 했는지 확인 합니다.

      ![MySQL RP의 배포를 확인 합니다.](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png) 


## <a name="next-steps"></a>다음 단계
[호스팅 서버 추가](azure-stack-mysql-resource-provider-hosting-servers.md)

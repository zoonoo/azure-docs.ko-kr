---
title: Azure Stack에서 App Service를 배포 하기 전에 | Microsoft Docs
description: Azure Stack에서 App Service를 배포 하기 전에 완료 하는 단계
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 02/22/2019
ms.openlocfilehash: 3b057e9c4a0fccb3f85ec237433e4020fd4a84da
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737487"
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Azure Stack에서 App Service를 사용 하 여 시작 하기 전에

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure App Service on Azure Stack을 배포 하기 전에이 문서의 필수 구성 요소 단계를 완료 해야 합니다.

> [!IMPORTANT]
> Azure Stack 통합 시스템 1809 업데이트를 적용 하거나 Azure 앱 서비스 1.4를 배포 하기 전에 최신 Azure Stack 개발 키트 ASDK ()를 배포 합니다.

## <a name="download-the-installer-and-helper-scripts"></a>설치 관리자 및 도우미 스크립트 다운로드

1. 다운로드 합니다 [App Service에서 Azure Stack 배포 도우미 스크립트](https://aka.ms/appsvconmashelpers)합니다.
2. 다운로드 합니다 [설치 관리자를 Azure Stack에서 App Service](https://aka.ms/appsvconmasinstaller)합니다.
3. 도우미 스크립트.zip 파일에서 파일을 추출 합니다. 다음 파일과 폴더가 추출 됩니다.

   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Remove-AppService.ps1
   - 모듈 폴더
     - GraphAPI.psm1

## <a name="syndicate-the-custom-script-extension-from-the-marketplace"></a>Marketplace에서 사용자 지정 스크립트 확장을 구성 합니다.

Azure App Service on Azure Stack 사용자 지정 스크립트 확장 v1.9.0이 필요합니다.  확장명은 이어야 [Marketplace에서 신디케이티드](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item) Azure Stack에 배포 또는 Azure App Service의 업그레이드를 시작 하기 전에

## <a name="high-availability"></a>고가용성

Azure Stack 1802 업데이트에는 장애 도메인에 대 한 지원이 추가 되었습니다. Azure Stack에서 Azure App Service의 새로운 배포 장애 도메인 분산 및 내결함성을 제공 합니다.

1802 업데이트 전에 배포 된 Azure Stack에서 Azure App Service의 기존 배포에 대 한 참조를 [App Service 리소스 공급자는 장애 도메인 간에 균형 다시 맞추기](azure-stack-app-service-fault-domain-update.md) 문서.

또한 필요한 파일 서버 및 고가용성 구성의 SQL Server 인스턴스를 배포 합니다.

## <a name="get-certificates"></a>인증서 가져오기

### <a name="azure-resource-manager-root-certificate-for-azure-stack"></a>Azure Stack에 대 한 azure 리소스 관리자 루트 인증서

Azure Stack 통합 시스템 또는 Azure Stack 개발 키트 호스트에서 권한 있는 끝점에 연결할 수 있는 컴퓨터에서 관리자 권한 PowerShell 세션을 엽니다.

실행 합니다 *Get AzureStackRootCert.ps1* 도우미 스크립트의 압축을 푼 폴더에서 스크립트입니다. 스크립트는 App Service 인증서를 만드는 스크립트와 같은 폴더에 루트 인증서를 만듭니다.

다음 PowerShell 명령을 실행 하는 경우는 AzureStack\CloudAdmin에 대 한 권한 있는 끝점 및 자격 증명을 제공 해야 합니다.

```PowerShell
    Get-AzureStackRootCert.ps1
```

#### <a name="get-azurestackrootcertps1-script-parameters"></a>Get-AzureStackRootCert.ps1 script parameters

| 매개 변수 | 필수 또는 선택 | 기본값 | 설명 |
| --- | --- | --- | --- |
| PrivilegedEndpoint | 필수 | AzS-ERCS01 | 권한 있는 끝점 |
| CloudAdminCredential | 필수 | AzureStack\CloudAdmin | Azure Stack 클라우드 관리자에 대 한 도메인 계정 자격 증명 |

### <a name="certificates-required-for-asdk-deployment-of-azure-app-service"></a>Azure App Service의 ASDK 배포에 필요한 인증서

합니다 *AppServiceCerts.ps1 만들기* 스크립트 App Service는 4 개의 인증서를 만드는 Azure Stack 기관 작동 합니다.

| 파일 이름 | 사용 |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | App Service 기본 SSL 인증서 |
| api.appservice.local.azurestack.external.pfx | App Service API SSL 인증서 |
| ftp.appservice.local.azurestack.external.pfx | App Service 게시자 SSL 인증서 |
| sso.appservice.local.azurestack.external.pfx | 앱 서비스 id 응용 프로그램 인증서 |

인증서를 만들려면 다음이 단계를 수행 합니다.

1. AzureStack\AzureStackAdmin 계정을 사용 하 여 Azure Stack 개발 키트 호스트에 로그인 합니다.
2. 관리자 권한 PowerShell 세션을 엽니다.
3. 실행 합니다 *AppServiceCerts.ps1 만들기* 도우미 스크립트의 압축을 푼 폴더에서 스크립트입니다. 이 스크립트는 App Service 인증서를 만드는 스크립트와 같은 폴더에 4 개의 인증서를 만듭니다.
4. .Pfx 파일을 보호 하는 데 암호를 입력 하 고 수 있도록를 기록해 둡니다. 설치 관리자를 Azure Stack에서 App Service에 입력 해야 합니다.

#### <a name="create-appservicecertsps1-script-parameters"></a>만들 AppServiceCerts.ps1 스크립트 매개 변수

| 매개 변수 | 필수 또는 선택 | 기본값 | 설명 |
| --- | --- | --- | --- |
| pfxPassword | 필수 | Null | 보호할 수 있는 인증서 개인 키 암호 |
| DomainName | 필수 | local.azurestack.external | Azure Stack 지역 및 도메인 접미사 |

### <a name="certificates-required-for-azure-stack-production-deployment-of-azure-app-service"></a>Azure App Service의 Azure Stack 프로덕션 배포에 필요한 인증서

프로덕션 환경에서 리소스 공급자를 실행 하려면 다음 인증서를 제공 해야 합니다.

- 기본 도메인 인증서
- API 인증서
- 게시 인증서
- Id 인증서

#### <a name="default-domain-certificate"></a>기본 도메인 인증서

기본 도메인 인증서는 프런트 엔드 역할에 배치 됩니다. Azure App Service에 와일드 카드 또는 기본 도메인 요청에 대 한 사용자 응용 프로그램은이 인증서를 사용 합니다. 소스 제어 작업 (Kudu)에 대 한 인증서도 사용 됩니다.

인증서는.pfx 형식 이어야 합니다 하 고 세 주체 와일드 카드 인증서 여야 합니다. 이 요구 기본 도메인 및 소스 제어 작업에 대 한 SCM 끝점을 처리 하기 위해 하나의 인증서를 허용 합니다.

| 형식 | 예 |
| --- | --- |
| \*.appservice.\<region\>.\<DomainName\>.\<extension\> | \*.appservice.redmond.azurestack.external |
| \*.scm.appservice.<region>.<DomainName>.<extension> | \*.scm.appservice.redmond.azurestack.external |
| \*.sso.appservice.<region>.<DomainName>.<extension> | \*.sso.appservice.redmond.azurestack.external |

#### <a name="api-certificate"></a>API 인증서

API 인증서 관리 역할에 배치 됩니다. 리소스 공급자 API 호출 보호 하는 데 사용 됩니다. 게시용 인증서 API DNS 항목과 일치 하는 주체를 포함 해야 합니다.

| 형식 | 예 |
| --- | --- |
| api.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>게시 인증서

게시자 역할에 대 한 인증서 콘텐츠를 업로드 하는 경우 응용 프로그램 소유자에 대 한 FTPS 트래픽도 보호 합니다. 게시용 인증서는 FTPS DNS 항목과 일치 하는 주체를 포함 해야 합니다.

| 형식 | 예 |
| --- | --- |
| ftp.appservice.\<region\>.\<DomainName\>.\<extension\> | ftp.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>Id 인증서

Id 응용 프로그램에 대 한 인증서를 사용 하도록 설정 합니다.

- Azure Active Directory (Azure AD) 또는 Active Directory Federation Services (AD FS) 디렉터리, Azure Stack 및 App Service compute 리소스 공급자를 사용 하 여 통합을 지 원하는 통합 합니다.
- Single sign-on 시나리오 Azure Stack에서 Azure App Service 내에서 고급 개발자 도구에 대 한 합니다.

Id에 대 한 인증서는 다음 형식과 일치 하는 주체를 포함 해야 합니다.

| 형식 | 예 |
| --- | --- |
| sso.appservice.\<region\>.\<DomainName\>.\<extension\> | sso.appservice.redmond.azurestack.external |


### <a name="validate-certificates"></a>인증서의 유효성 검사
App service 리소스 공급자를 배포 하기 전에 수행 해야 합니다 [사용할 인증서의 유효성 검사](azure-stack-validate-pki-certs.md#perform-platform-as-a-service-certificate-validation) 에서 사용할 수 있는 Azure Stack 준비 상태 검사기 도구를 사용 하는 [PowerShell 갤러리](https://aka.ms/AzsReadinessChecker)합니다. Azure Stack 준비 검사 도구는 생성 된 PKI 인증서를 사용 하 여 app services 배포에 적합 한 인지를 확인 합니다. 

모범 사례로, 필요한 중 하나를 사용 하 여 작업 하는 경우 [Azure Stack PKI 인증서](azure-stack-pki-certs.md), 시간을 충분히 테스트 하 고 필요한 경우 인증서를 다시 발급 해야 합니다. 

## <a name="virtual-network"></a>가상 네트워크

> [!NOTE]
> Azure App Service on Azure Stack 필요한 가상 네트워크를 만들 수 있지만 SQL 및 공용 IP 주소를 통해 파일 서버와 통신 해야 다음 사용자 지정 가상 네트워크의 사전 생성 선택 사항입니다.

Azure Stack의 azure App Service 리소스 공급자에 기존 가상 네트워크에 배포할 수 있습니다 또는 배포의 일부분으로 가상 네트워크를 만들 수 있습니다. 기존 가상 네트워크를 사용 하 여 파일 서버 및 Azure Stack에서 Azure App Service에 필요한 SQL server에 연결할 내부 Ip 사용 하도록 설정 합니다. Azure Stack에서 Azure App Service를 설치 하기 전에 다음 주소 범위 및 서브넷을 사용 하 여 가상 네트워크를 구성 해야 합니다.

Virtual Network - /16

서브넷

- ControllersSubnet /24
- ManagementServersSubnet /24
- FrontEndsSubnet /24
- PublishersSubnet /24
- WorkersSubnet /21

## <a name="prepare-the-file-server"></a>파일 서버를 준비 합니다.

Azure App Service는 파일 서버를 사용을 해야 합니다. 프로덕션 배포의 경우 항상 사용 가능 하 고 오류를 처리할 수 있는 파일 서버를 구성 합니다.

### <a name="quickstart-template-for-file-server-for-deployments-of-azure-app-service-on-asdk"></a>ASDK의 Azure App Service의 배포에 대 한 파일 서버에 대 한 빠른 시작 템플릿.

Azure Stack 개발 키트 배포에 사용할 수 있습니다 합니다 [Azure Resource Manager 배포 템플릿의 예](https://aka.ms/appsvconmasdkfstemplate) 구성 된 단일 노드 파일 서버를 배포 합니다. 단일 노드 파일 서버를 작업 그룹에 있게 됩니다.

### <a name="quickstart-template-for-highly-available-file-server-and-sql-server"></a>항상 사용 가능한 파일 서버 및 SQL Server에 대 한 빠른 시작 템플릿

A [참조 아키텍처 빠른 시작 템플릿](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/appservice-fileserver-sqlserver-ha) 수 이제 파일 서버, SQL Server 배포 하는, 가상 네트워크에 인프라가 Active Directory를 지 원하는 항상 사용 가능한 배포를 지원 하도록 구성 Azure Stack의 azure App Service입니다.  

### <a name="steps-to-deploy-a-custom-file-server"></a>사용자 지정 파일 서버를 배포 하는 단계

>[!IMPORTANT]
> 기존 가상 네트워크에 App Service를 배포 하려는 경우 파일 서버는 App Service에서 별도 서브넷에 배포 되어야 합니다.

#### <a name="provision-groups-and-accounts-in-active-directory"></a>Active Directory에서 그룹 및 계정 프로 비전

1. 다음 Active Directory 글로벌 보안 그룹을 만듭니다.

   - FileShareOwners
   - FileShareUsers

2. 서비스 계정으로 다음과 같은 Active Directory 계정을 만듭니다.

   - FileShareOwner
   - FileShareUser

   보안 모범 사례, 사용자가 이러한 계정에 대 한 (및 모든 웹 역할)는 고유 하 고 강력한 사용자 이름 및 암호 해야 합니다. 다음 조건에 따라 암호를 설정 합니다.

   - 사용 하도록 설정 **암호 사용 기간 제한 없음**합니다.
   - 사용 하도록 설정 **사용자 암호를 변경할 수 없습니다**합니다.
   - 사용 안 함 **사용자는 다음 로그온 할 때 암호를 변경 해야**합니다.

3. 같이 그룹 멤버 자격에 계정을 추가 합니다.

   - 추가 **FileShareOwner** 에 **FileShareOwners** 그룹입니다.
   - 추가 **FileShareUser** 에 **FileShareUsers** 그룹입니다.

#### <a name="provision-groups-and-accounts-in-a-workgroup"></a>작업 그룹의 그룹 및 계정 프로 비전

>[!NOTE]
> 모든 다음 명령을 실행 하는 파일 서버를 구성 하는 경우는 **관리자 권한 명령 프롬프트**합니다. <br>***PowerShell을 사용 하지 마세요.***

Azure Resource Manager 템플릿을 사용 하는 사용자가 이미 생성 됩니다.

1. FileShareOwner 및 FileShareUser 계정을 만들려면 다음 명령을 실행 합니다. `<password>`을 고유한 값으로 바꿉니다.

   ``` DOS
   net user FileShareOwner <password> /add /expires:never /passwordchg:no
   net user FileShareUser <password> /add /expires:never /passwordchg:no
   ```

2. 다음 WMIC 명령을 실행 하 여 만료 되지 않도록 계정에 대 한 암호를 설정 합니다.

   ``` DOS
   WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
   WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
   ```

3. FileShareUsers 및 FileShareOwners, 로컬 그룹을 만들고에 첫 번째 단계의 계정을 추가 합니다.

   ``` DOS
   net localgroup FileShareUsers /add
   net localgroup FileShareUsers FileShareUser /add
   net localgroup FileShareOwners /add
   net localgroup FileShareOwners FileShareOwner /add
   ```

#### <a name="provision-the-content-share"></a>콘텐츠 공유를 프로 비전

콘텐츠 공유에 테 넌 트 웹 사이트 콘텐츠가 포함 됩니다. 단일 파일 서버에서 콘텐츠 공유를 프로 비전 하는 절차는 Active Directory와 작업 그룹 환경에 대 한 동일 합니다. 하지만 Active Directory의 장애 조치 클러스터에 대 한 것이 다릅니다.

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>단일 파일 서버 (Active Directory 또는 작업 그룹)에서 콘텐츠 공유를 프로 비전

단일 파일 서버에서 관리자 권한 명령 프롬프트에서 다음 명령을 실행 합니다. 에 대 한 값을 바꿉니다 `C:\WebSites` 환경의 해당 경로로 합니다.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="configure-access-control-to-the-shares"></a>공유에 대 한 액세스 제어 구성

파일 서버 또는 현재 클러스터 리소스 소유자는 장애 조치 클러스터 노드에서 관리자 권한 명령 프롬프트에서 다음 명령을 실행 합니다. 기울임꼴로 표시 값을 환경과 관련 된 값으로 바꿉니다.

#### <a name="active-directory"></a>Active Directory

```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>작업 그룹

```DOS
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

## <a name="prepare-the-sql-server-instance"></a>SQL Server 인스턴스 준비

Azure Stack 호스팅 및 측정 데이터베이스를 Azure App Service에 대 한 App Service 데이터베이스를 저장할 SQL Server 인스턴스를 준비 해야 합니다.

Azure Stack 개발 키트 배포에 대 한 SQL Server 2014 Express SP2를 사용할 수 있습니다 이상.

프로덕션 및 높은 가용성을 위해 있습니다를 사용 하 여 SQL Server 2014 SP2의 전체 버전 또는 나중에 혼합 모드 인증을 사용 하도록 설정에서 배포를 [항상 사용 가능한 구성](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server)합니다.

Azure App Service on Azure Stack에 대 한 SQL Server 인스턴스를 모든 App Service 역할에서 액세스할 수 있어야 합니다. Azure Stack의 기본 공급자 구독 내에서 SQL Server를 배포할 수 있습니다. 만들 수 있습니다 (으로 Azure Stack에 연결 되어 있는지) 조직 내에서 기존 인프라를 활용 합니다. Azure Marketplace 이미지를 사용 하는 경우 방화벽을 적절 하 게 구성 해야 합니다.

>[!NOTE]
> SQL IaaS 가상 머신 이미지 수가 Marketplace 관리 기능을 통해 사용할 수 있습니다. 해야 마켓플레이스 항목을 사용 하는 VM을 배포 하기 전에 항상 최신 버전의 SQL IaaS 확장을 다운로드 합니다. SQL 이미지를 Azure에서 사용할 수 있는 SQL Vm과 동일 합니다. 이러한 이미지를 IaaS 확장에서 만들고 포털의 향상 된 기능에 해당 하는 SQL Vm에 대 한 자동 패치, 백업 기능과 같은 기능을 제공 합니다.
>
모든 SQL Server 역할에 대해 기본 인스턴스나 명명 된 인스턴스를 사용할 수 있습니다. 명명된 된 인스턴스를 사용 하는 경우 해야 수동으로 SQL Server Browser 서비스를 시작 하 고 포트 1434를 열어야 합니다.

App Service 설치 관리자는 SQL Server에 사용 하도록 설정 하는 데이터베이스 포함 하는지 확인 합니다. App Service 데이터베이스를 호스팅하는 SQL Server에서 데이터베이스 포함을 사용 하도록 설정 하려면 다음 SQL 명령을 실행 합니다.

```sql
sp_configure 'contained database authentication', 1;  
GO  
RECONFIGURE;  
GO
```


>[!IMPORTANT]
> 기존 가상 네트워크에 App Service를 배포 하려는 경우 SQL Server는 App Service 및 파일 서버에서 별도 서브넷에 배포 되어야 합니다.
>

## <a name="create-an-azure-active-directory-application"></a>Azure Active Directory 애플리케이션 만들기

다음 작업을 지 원하는 Azure AD 서비스 주체를 구성 합니다.

- 가상 머신 확장 작업자 계층에서 통합을 설정 합니다.
- Azure Functions 포털 및 고급 개발자 도구에 대 한 SSO를 제공 합니다.

이러한 단계만에 Azure AD로 보호 된 Azure Stack 환경에 적용 됩니다.

관리자는 SSO를 구성 해야 합니다.

- App Service (Kudu) 내에서 고급 개발자 도구를 사용 하도록 설정 합니다.
- Azure Functions 포털 환경을 사용 합니다.

다음 단계를 수행하세요.

1. Azurestack\AzureStackAdmin로 PowerShell 인스턴스를 엽니다.
2. 스크립트를 다운로드 하 고에서 추출한 위치로 이동 합니다 [필수 조건 단계](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started)합니다.
3. [Azure Stack 용 PowerShell 설치](azure-stack-powershell-install.md)합니다.
4. 실행 합니다 **AADIdentityApp.ps1 만들기** 스크립트입니다. 메시지가 나타나면 Azure Stack 배포에 사용 하는 Azure AD 테 넌 트 ID를 입력 합니다. 예를 들어 입력 **myazurestack.onmicrosoft.com**합니다.
5. 에 **자격 증명** 창에서 Azure AD 서비스 관리자 계정 및 암호를 입력 합니다. **확인**을 선택합니다.
6. 인증서 파일 경로 및 인증서 암호를 입력 합니다 [앞에서 만든 인증서](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started)합니다. 기본적으로이 단계에 만든 인증서 **sso.appservice.local.azurestack.external.pfx**합니다.
7. 스크립트는 테 넌 트 Azure AD 인스턴스에 새 응용 프로그램을 만듭니다. PowerShell 출력에 반환 되는 응용 프로그램 ID를 기록해 둡니다. 설치 하는 동안이 정보가 필요합니다.
8. 새 브라우저 창을 열고 로그인 합니다 [Azure portal](https://portal.azure.com) 에 Azure Active Directory 서비스 관리자로
9. Azure AD 리소스 공급자를 엽니다.
10. 선택 **앱 등록**합니다.
11. 7 단계의 일부로 반환 된 응용 프로그램 ID 검색 합니다. App Service 응용 프로그램 나열 됩니다.
12. 선택 **응용 프로그램** 목록에서입니다.
13. **설정**을 선택합니다.
14. 선택 **필요한 권한** > **권한 부여** > **예**합니다.

```PowerShell
    Create-AADIdentityApp.ps1
```

| 매개 변수 | 필수 또는 선택 | 기본값 | 설명 |
| --- | --- | --- | --- |
| DirectoryTenantName | 필수 | Null | Microsoft Azure Active Directory 테넌트 ID. 문자열 또는 GUID를 제공 합니다. 예 myazureaaddirectory.onmicrosoft.com입니다. |
| AdminArmEndpoint | 필수 | Null | 관리자 Azure Resource Manager 끝점입니다. 예 adminmanagement.local.azurestack.external입니다. |
| TenantARMEndpoint | 필수 | Null | 테 넌 트 Azure Resource Manager 끝점입니다. 예 management.local.azurestack.external입니다. |
| AzureStackAdminCredential | 필수 | Null | Azure AD 서비스 관리자 자격 증명입니다. |
| CertificateFilePath | 필수 | Null | **전체 경로** identity 응용 프로그램 인증서 파일 이전에 생성 합니다. |
| CertificatePassword | 필수 | Null | 인증서 개인 키를 보호 하는 암호입니다. |
| Environment | 옵션 | AzureCloud | 대상 Azure Active Directory 그래프 서비스를 사용할 수 있는 지원 되는 클라우드 환경의 이름입니다.  허용되는 값은 다음과 같습니다. 'AzureCloud', 'AzureChinaCloud', 'AzureUSGovernment', 'AzureGermanCloud'.|

## <a name="create-an-active-directory-federation-services-application"></a>Active Directory Federation Services 응용 프로그램 만들기

AD FS에서 보안이 유지 되는 Azure Stack 환경에서는 다음 작업을 지원 하도록 AD FS 서비스 주체를 구성 해야 합니다.

- 가상 머신 확장 작업자 계층에서 통합을 설정 합니다.
- Azure Functions 포털 및 고급 개발자 도구에 대 한 SSO를 제공 합니다.

관리자는 SSO를 구성 해야 합니다.

- 작업자 계층에 가상 머신 확장 집합 통합에 대 한 서비스 주체를 구성 합니다.
- App Service (Kudu) 내에서 고급 개발자 도구를 사용 하도록 설정 합니다.
- Azure Functions 포털 환경을 사용 합니다.

다음 단계를 수행하세요.

1. Azurestack\AzureStackAdmin로 PowerShell 인스턴스를 엽니다.
2. 스크립트를 다운로드 하 고에서 추출한 위치로 이동 합니다 [필수 조건 단계](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started)합니다.
3. [Azure Stack 용 PowerShell 설치](azure-stack-powershell-install.md)합니다.
4. 실행 합니다 **ADFSIdentityApp.ps1 만들기** 스크립트입니다.
5. 에 **자격 증명** 창 AD FS 클라우드 관리자 계정 및 암호를 입력 합니다. **확인**을 선택합니다.
6. 인증서 암호를 확인 하 고 인증서 파일 경로 제공 합니다 [앞에서 만든 인증서](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started)합니다. 기본적으로이 단계에 만든 인증서 **sso.appservice.local.azurestack.external.pfx**합니다.

```PowerShell
    Create-ADFSIdentityApp.ps1
```

| 매개 변수 | 필수 또는 선택 | 기본값 | 설명 |
| --- | --- | --- | --- |
| AdminArmEndpoint | 필수 | Null | 관리자 Azure Resource Manager 끝점입니다. 예 adminmanagement.local.azurestack.external입니다. |
| PrivilegedEndpoint | 필수 | Null | 권한 있는 끝점입니다. 예는 AzS ERCS01입니다. |
| CloudAdminCredential | 필수 | Null | Azure Stack 클라우드 관리자에 대 한 도메인 계정 자격 증명입니다. 예 Azurestack\CloudAdmin입니다. |
| CertificateFilePath | 필수 | Null | **전체 경로** id 응용 프로그램의 인증서 PFX 파일에 있습니다. |
| CertificatePassword | 필수 | Null | 인증서 개인 키를 보호 하는 암호입니다. |

## <a name="next-steps"></a>다음 단계

[App Service 리소스 공급자를 설치 합니다.](azure-stack-app-service-deploy.md)

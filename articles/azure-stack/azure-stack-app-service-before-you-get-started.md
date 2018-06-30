---
title: Azure 스택 앱 서비스를 배포 하기 전에 | Microsoft Docs
description: Azure 스택 앱 서비스를 배포 하기 전에 완료 하기 위한 단계
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2018
ms.author: anwestg
ms.openlocfilehash: 37d6ee2f047768f08ea7a113b7d97911d58a46e2
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110578"
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Azure 스택 앱 서비스를 시작 하기 전에

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택 Azure 앱 서비스를 배포 하기 전에이 문서의 필수 구성 요소 단계를 완료 해야 합니다.

> [!IMPORTANT]
> Azure 스택 통합 시스템 1804 업데이트를 적용 하거나 Azure 앱 서비스 1.2를 배포 하기 전에 최신 Azure 스택 개발 키트 (ASDK)를 배포 합니다.

## <a name="download-the-installer-and-helper-scripts"></a>설치 관리자 및 도우미 스크립트를 다운로드 합니다.

1. 다운로드는 [Azure 스택 배포 도우미 스크립트에 대 한 앱 서비스](https://aka.ms/appsvconmashelpers)합니다.
2. 다운로드는 [Azure 스택 설치 관리자에서 응용 프로그램 서비스](https://aka.ms/appsvconmasinstaller)합니다.
3. 도우미 스크립트.zip 파일에서 파일을 추출 합니다. 다음 파일과 폴더가 추출 됩니다.

   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Remove-AppService.ps1
   - 모듈 폴더
     - GraphAPI.psm1

## <a name="high-availability"></a>고가용성

Azure 스택 1802 업데이트가 오류 도메인에 대 한 지원을 추가 합니다. Azure 스택 앱 서비스를 Azure의 새 배포 오류 도메인에 배포 되 고 내결함성을 제공 합니다.

Azure 스택, Azure 앱 서비스의 1802 업데이트 전에 배포 된 기존 배포에 대 한 참조는 [앱 서비스 리소스 공급자 오류 도메인 간에 균형을 다시 조정](azure-stack-app-service-fault-domain-update.md) 문서.

또한 필요한 파일 서버 및 항상 사용 가능한 구성의 SQL Server 인스턴스를 배포 합니다.

## <a name="get-certificates"></a>인증서 가져오기

### <a name="azure-resource-manager-root-certificate-for-azure-stack"></a>Azure 리소스 관리자 루트 인증서에 대 한 Azure 스택

Azure 스택 통합 시스템 또는 Azure 스택 개발 키트 호스트에 권한 있는 끝점에 도달할 수 있는 컴퓨터에서 관리자 권한 PowerShell 세션을 엽니다.

실행 된 *Get AzureStackRootCert.ps1* 도우미 스크립트의 압축을 푼 폴더에서 스크립트입니다. 스크립트 응용 프로그램 서비스 인증서를 만드는 스크립트와 같은 폴더에 루트 인증서를 만듭니다.

다음 PowerShell 명령을 실행 하면는 AzureStack\CloudAdmin에 대 한 권한 있는 끝점 및 자격 증명을 제공 해야 합니다.

```PowerShell
    Get-AzureStackRootCert.ps1
```

#### <a name="get-azurestackrootcertps1-script-parameters"></a>Get AzureStackRootCert.ps1 스크립트 매개 변수

| 매개 변수 | 필수 또는 선택 | 기본값 | 설명 |
| --- | --- | --- | --- |
| PrivilegedEndpoint | 필요 | AzS-ERCS01 | 권한 있는 끝점 |
| CloudAdminCredential | 필요 | AzureStack\CloudAdmin | Azure 스택 클라우드 관리자에 대 한 도메인 계정 자격 증명 |

### <a name="certificates-required-for-asdk-deployment-of-azure-app-service"></a>Azure 앱 서비스의 ASDK 배포에 필요한 인증서

*만들기 AppServiceCerts.ps1* 스크립트 응용 프로그램 서비스는 4 개의 인증서를 만드는 Azure 스택 인증 기관으로 작동 합니다.

| 파일 이름 | 사용 |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | App Service 기본 SSL 인증서 |
| api.appservice.local.azurestack.external.pfx | 앱 서비스 API SSL 인증서 |
| ftp.appservice.local.azurestack.external.pfx | 응용 프로그램 서비스 게시자 SSL 인증서 |
| sso.appservice.local.azurestack.external.pfx | 앱 서비스 id 응용 프로그램 인증서 |

인증서를 만들려면 다음이 단계를 수행 합니다.

1. Azure 스택 개발 키트 호스트 AzureStack\AzureStackAdmin 계정을 사용 하 여에 로그인 합니다.
2. 관리자 권한 PowerShell 세션을 엽니다.
3. 실행 된 *만들기 AppServiceCerts.ps1* 도우미 스크립트의 압축을 푼 폴더에서 스크립트입니다. 이 스크립트는 앱 서비스 인증서를 만드는 스크립트와 같은 폴더에 4 개의 인증서를 만듭니다.
4. .Pfx 파일을 보호 하려면 암호를 입력 하 고는 기록 합니다. Azure 스택 설치 관리자에서 응용 프로그램 서비스에 입력 해야 합니다.

#### <a name="create-appservicecertsps1-script-parameters"></a>만들 AppServiceCerts.ps1 스크립트 매개 변수

| 매개 변수 | 필수 또는 선택 | 기본값 | 설명 |
| --- | --- | --- | --- |
| pfxPassword | 필요 | Null | 보호할 수 있는 인증서 개인 키 암호 |
| DomainName | 필요 | local.azurestack.external | Azure 스택 지역 및 도메인 접미사 |

### <a name="certificates-required-for-azure-stack-production-deployment-of-azure-app-service"></a>Azure 앱 서비스의 Azure 스택 프로덕션 배포에 필요한 인증서

프로덕션 환경에서 리소스 공급자를 실행 하려면 다음과 같은 인증서를 제공 해야 합니다.

- 기본 도메인 인증서
- API 인증서
- 게시 인증서
- 인증서 확인

#### <a name="default-domain-certificate"></a>기본 도메인 인증서

기본 도메인 인증서는 프런트 엔드 역할에 배치 됩니다. Azure 앱 서비스에 와일드 카드 또는 기본 도메인 요청에 대 한 사용자 응용 프로그램은이 인증서를 사용 합니다. 인증서 (Kudu) 소스 제어 작업에도 사용 됩니다.

인증서는.pfx 형식에서 이어야 하며 3 주체 와일드 카드 인증서 여야 합니다. 이 요구 소스 제어 작업에 대 한 SCM 끝점과 기본 도메인을 모두 포함 하는 하나의 인증서를 허용 합니다.

| 형식 | 예 |
| --- | --- |
| \*.appservice.\<region\>.\<DomainName\>.\<extension\> | \*.appservice.redmond.azurestack.external |
| \*.scm.appservice.<region>.<DomainName>.<extension> | \*.scm.appservice.redmond.azurestack.external |
| \*.sso.appservice.<region>.<DomainName>.<extension> | \*.sso.appservice.redmond.azurestack.external |

#### <a name="api-certificate"></a>API 인증서

API 인증서는 관리 역할에 배치 됩니다. 리소스 공급자 API 호출 보안을 위해 사용 됩니다. 게시용 인증서에 주체 API DNS 항목과 일치 하는 주체를 포함 해야 합니다.

| 형식 | 예 |
| --- | --- |
| api.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>게시 인증서

콘텐츠를 업로드 하는 경우 게시자 역할에 대 한 인증서는 응용 프로그램 소유자에 대 한 FTPS 트래픽의 보호 합니다. 게시용 인증서는 FTPS DNS 항목과 일치 하는 주체를 포함 해야 합니다.

| 형식 | 예 |
| --- | --- |
| ftp.appservice.\<region\>.\<DomainName\>.\<extension\> | ftp.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>인증서 확인

Identity 응용 프로그램에 대 한 인증서를 사용 하면:

- 와 Azure Active Directory (Azure AD) 또는 Active Directory Federation Services (AD FS) 디렉터리, Azure 스택 앱 서비스 계산 리소스 공급자와의 통합을 지원 하기 위해 통합 합니다.
- Single sign-on 시나리오 내 Azure 스택 앱 서비스를 Azure에서 고급 개발자 도구에 대 한 합니다.

Id에 대 한 인증서는 다음 형식과 일치 하는 주체를 포함 해야 합니다.

| 형식 | 예 |
| --- | --- |
| sso.appservice.\<region\>.\<DomainName\>.\<extension\> | sso.appservice.redmond.azurestack.external |

## <a name="virtual-network"></a>가상 네트워크

Azure 스택 앱 서비스를 azure 리소스 공급자에 기존 가상 네트워크에 배포할 수 있습니다 또는 배포의 일부분으로 가상 네트워크를 만들 수 있습니다. 기존 가상 네트워크를 사용 하 여 내부 ip 파일 서버와 Azure 스택에 Azure 앱 서비스에서 요구 하는 SQL server에 연결 하는 데 사용할 수 있습니다. 가상 네트워크 스택 Azure에 Azure 앱 서비스를 설치 하기 전에 다음 주소 범위 및 서브넷으로 구성 해야 합니다.

가상 네트워크-/ 16

서브넷

- ControllersSubnet/24
- ManagementServersSubnet/24
- FrontEndsSubnet/24
- PublishersSubnet/24
- WorkersSubnet /21

## <a name="prepare-the-file-server"></a>파일 서버 준비

Azure 앱 서비스는 파일 서버를 사용을 해야 합니다. 프로덕션 배포에 항상 사용 가능 하 고 오류를 처리할 수 있는 파일 서버를 구성 합니다.

Azure 스택 개발 키트 배포에 사용할 수 있습니다는 [예제에서는 Azure Resource Manager 배포 템플릿](https://aka.ms/appsvconmasdkfstemplate) 구성 된 단일 노드 파일 서버를 배포 합니다. 작업 그룹에서 단일 노드 파일 서버가 됩니다.

>[!IMPORTANT]
> 기존 가상 네트워크에 응용 프로그램 서비스를 배포 하도록 선택한 경우 파일 서버 응용 프로그램 서비스에서 별도 서브넷에 배포 되어야 합니다.

### <a name="provision-groups-and-accounts-in-active-directory"></a>Active Directory에서 그룹 및 계정 프로 비전

1. 다음 Active Directory 글로벌 보안 그룹을 만듭니다.

   - FileShareOwners
   - FileShareUsers

2. 서비스 계정으로 다음과 같은 Active Directory 계정을 만듭니다.

   - FileShareOwner
   - FileShareUser

   보안 모범 사례로, 이러한 계정에 대 한 (및 모든 웹 역할에 대 한) 사용자 고유와 강력한 사용자 이름 및 암호가 있어야 합니다. 다음 조건에 따라 암호를 설정 합니다.

   - 사용 하도록 설정 **암호 사용 기간 제한 없음**합니다.
   - 사용 하도록 설정 **사용자 암호를 변경할 수 없음**합니다.
   - 사용 안 함 **사용자 다음 로그온 할 때 암호를 변경 해야**합니다.

3. 다음과 같이 그룹 멤버 자격에 계정을 추가:

   - 추가 **FileShareOwner** 에 **FileShareOwners** 그룹입니다.
   - 추가 **FileShareUser** 에 **Fileshareuser** 그룹입니다.

### <a name="provision-groups-and-accounts-in-a-workgroup"></a>작업 그룹에서 그룹 및 계정 프로 비전

>[!NOTE]
> 모든 다음 명령을 실행 하는 파일 서버를 구성 하는 경우는 **관리자 명령 프롬프트**합니다. <br>***PowerShell을 사용 하지 마십시오.***

Azure 리소스 관리자 템플릿을 사용 하면 사용자가 이미 생성 되어 있습니다.

1. FileShareOwner 및 FileShareUser 계정을 만들려면 다음 명령을 실행 합니다. 대체 `<password>` 를 원하는 값으로.

    ``` DOS
    net user FileShareOwner <password> /add /expires:never /passwordchg:no
    net user FileShareUser <password> /add /expires:never /passwordchg:no
    ```
2. 다음 WMIC 명령을 실행 하 여 만료 되지 않도록 하는 계정의 암호를 설정 합니다.

    ``` DOS
    WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
    WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
    ```
3. 로컬 그룹, Fileshareowner 및 Fileshareuser 만들고 첫 번째 단계에 계정을 추가 합니다.

    ``` DOS
    net localgroup FileShareUsers /add
    net localgroup FileShareUsers FileShareUser /add
    net localgroup FileShareOwners /add
    net localgroup FileShareOwners FileShareOwner /add
    ```

### <a name="provision-the-content-share"></a>콘텐츠 공유를 프로 비전

콘텐츠 공유에 테 넌 트 웹 사이트 콘텐츠가 포함 됩니다. 단일 파일 서버에 콘텐츠 공유를 프로 비전 하는 절차는 Active Directory 및 작업 그룹 환경에 대해 같습니다. 그러나 Active Directory의 장애 조치 클러스터에 대 한 다릅니다.

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>단일 파일 서버 (Active Directory 또는 작업 그룹)에 콘텐츠 공유를 프로 비전

단일 파일 서버에서 관리자 권한 명령 프롬프트에서 다음 명령을 실행 합니다. 에 대 한 값을 대체 `C:\WebSites` 사용자 환경의 해당 경로로 합니다.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="add-the-fileshareowners-group-to-the-local-administrators-group"></a>로컬 관리자 그룹에 FileShareOwners 그룹을 추가 합니다.

제대로 작동 하려면 Windows 원격 관리에 대 한 로컬 관리자 그룹에 FileShareOwners 그룹을 추가 해야 합니다.

#### <a name="active-directory"></a>Active Directory

장애 조치 클러스터 노드 역할을 하는 모든 파일 서버 또는 파일 서버에서 관리자 권한 명령 프롬프트에서 다음 명령을 실행 합니다. 에 대 한 값을 대체 `<DOMAIN>` 사용 하려는 도메인 이름을 사용 합니다.

```DOS
set DOMAIN=<DOMAIN>
net localgroup Administrators %DOMAIN%\FileShareOwners /add
```

#### <a name="workgroup"></a>작업 그룹

파일 서버에서 관리자 권한 명령 프롬프트에서 다음 명령을 실행 합니다.

```DOS
net localgroup Administrators FileShareOwners /add
```

### <a name="configure-access-control-to-the-shares"></a>공유에 대 한 액세스 제어 구성

파일 서버 또는 현재 클러스터 리소스 소유자 인 장애 조치 클러스터 노드에서 관리자 권한 명령 프롬프트에서 다음 명령을 실행 합니다. 기울임꼴로 값을 환경에 관련 된 값으로 바꿉니다.

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

Azure 스택 호스팅 및 계량 데이터베이스에서 Azure 응용 프로그램 서비스에 대 한 응용 프로그램 서비스 데이터베이스를 보관할 SQL Server 인스턴스를 준비 해야 합니다.

Azure 스택 개발 키트 배포에 대 한 SQL Server 2014 SP2 Express를 사용할 수 이상입니다.

프로덕션 환경과 높은 가용성을 위해 사용는 정식 버전의 SQL Server 2014 SP2 또는 이상 버전에서는 혼합 모드 인증을 사용 하도록 설정 하 고 해야 배포는 [항상 사용 가능한 구성](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server)합니다.

Azure 스택 앱 서비스를 Azure에 대 한 SQL Server 인스턴스는 모든 앱 서비스 역할에서 액세스할 수 있어야 합니다. Azure 스택에 기본 공급자 구독 내에서 SQL Server를 배포할 수 있습니다. 만들 수 있습니다 (으로 Azure 스택에 연결이) 조직 내에서 기존 인프라를 사용 합니다. Azure 마켓플레이스 이미지를 사용 하는 경우 그에 따라 방화벽을 구성 해야 합니다.

>[!NOTE]
> SQL IaaS 가상 컴퓨터 이미지의 많은 마켓플레이스 관리 기능을 통해 사용할 수 있습니다. 사용 하면 항상 마켓플레이스 항목을 사용 하는 VM을 배포 하기 전에 SQL IaaS 확장의 최신 버전을 다운로드 합니다. SQL 이미지는 Azure에서 사용할 수 있는 SQL Vm와 동일 합니다. 이러한 이미지를 IaaS 확장에서 만들어지고 포털의 향상 된 기능에 해당 vm의 SQL 자동 패치 및 백업 기능 등의 기능을 제공 합니다.
>
모든 SQL Server 역할에 대해 기본 인스턴스나 명명된 된 인스턴스를 사용할 수 있습니다. 명명된 된 인스턴스를 사용 하는 경우 수동으로 SQL Server Browser 서비스를 시작 하 고 포트 1434를 열어야를 해야 합니다.

>[!IMPORTANT]
> 기존 가상 네트워크에 응용 프로그램 서비스를 배포 하도록 선택한 경우 SQL Server 응용 프로그램 서비스 및 파일 서버에서 별도 서브넷에 배포 되어야 합니다.
>

## <a name="create-an-azure-active-directory-application"></a>Azure Active Directory 응용 프로그램 만들기

Azure AD 서비스 사용자를 사용 하 여 다음 작업을 지 원하는를 구성 합니다.

- 가상 컴퓨터 크기 작업자 계층에서 통합을 설정합니다.
- Azure 함수 포털 및 고급 개발자 도구에 대 한 SSO를 제공 합니다.

이러한 단계만에 Azure AD 보안 Azure 스택 환경에 적용 됩니다.

관리자는 SSO를 구성 해야 합니다.

- 응용 프로그램 서비스 (Kudu) 내에서 고급 개발자 도구를 사용 하도록 설정 합니다.
- Azure 함수 포털 환경에서 사용 하도록 설정 합니다.

다음 단계를 수행하십시오.

1. Azurestack\AzureStackAdmin로 PowerShell 인스턴스를 엽니다.
2. 스크립트를 다운로드 하 고에서 추출의 위치로 이동 된 [필수 구성 요소 단계](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts)합니다.
3. [Azure 스택에 대 한 PowerShell을 설치](azure-stack-powershell-install.md)합니다.
4. 실행 된 **만들기 AADIdentityApp.ps1** 스크립트입니다. 메시지가 나타나면 Azure 스택 배포에 사용 하는 Azure AD 테 넌 트 ID를 입력 합니다. 예를 들어 입력 **myazurestack.onmicrosoft.com**합니다.
5. 에 **자격 증명** 창에서 Azure AD 서비스 관리자 계정 및 암호를 입력 합니다. **확인**을 선택합니다.
6. 인증서 파일 경로 및 인증서 암호를 입력의 [앞에서 만든 인증서](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack)합니다. 기본적으로이 단계에 대해 만들어진 인증서 **sso.appservice.local.azurestack.external.pfx**합니다.
7. 스크립트는 테 넌 트 Azure AD 인스턴스에 새 응용 프로그램을 만듭니다. PowerShell 출력에 반환 되는 응용 프로그램 ID를 기록해 둡니다. 설치 하는 동안이 정보가 필요 합니다.
8. 새 브라우저 창을 열고에 로그인 하 고 [Azure 포털](https://portal.azure.com) Azure Active Directory 서비스 관리자로
9. Azure AD 리소스 공급자를 엽니다.
10. 선택 **앱 등록**합니다.
11. 7 단계의 일부로 반환 된 응용 프로그램 ID 검색 합니다. 앱 서비스 응용 프로그램 나열 됩니다.
12. 선택 **응용 프로그램** 목록에 있습니다.
13. **설정**을 선택합니다.
14. 선택 **필요한 권한** > **권한을 부여** > **예**합니다.

```PowerShell
    Create-AADIdentityApp.ps1
```

| 매개 변수 | 필수 또는 선택 | 기본값 | 설명 |
| --- | --- | --- | --- |
| DirectoryTenantName | 필요 | Null | Microsoft Azure Active Directory 테넌트 ID. GUID 또는 문자열을 제공 합니다. 예 myazureaaddirectory.onmicrosoft.com입니다. |
| AdminArmEndpoint | 필요 | Null | Azure 리소스 관리자 관리 끝점입니다. 예 adminmanagement.local.azurestack.external입니다. |
| TenantARMEndpoint | 필요 | Null | 테 넌 트 Azure 리소스 관리자 끝점입니다. 예 management.local.azurestack.external입니다. |
| AzureStackAdminCredential | 필요 | Null | Azure AD 서비스 관리자 자격 증명입니다. |
| CertificateFilePath | 필요 | Null | **전체 경로** identity 응용 프로그램 인증서 파일을 이전에 생성 합니다. |
| CertificatePassword | 필요 | Null | 보호할 수 있는 인증서 개인 키 암호입니다. |

## <a name="create-an-active-directory-federation-services-application"></a>Active Directory Federation Services 응용 프로그램 만들기

AD FS로 보호 되는 Azure 스택 환경에서는 다음 작업을 지원 하도록 AD FS 서비스 사용자는 구성 해야 합니다.

- 가상 컴퓨터 크기 작업자 계층에서 통합을 설정합니다.
- Azure 함수 포털 및 고급 개발자 도구에 대 한 SSO를 제공 합니다.

관리자는 SSO를 구성 해야 합니다.

- 작업자 계층에 가상 컴퓨터 크기 조정 설정 통합을 위한 서비스 사용자를 구성 합니다.
- 응용 프로그램 서비스 (Kudu) 내에서 고급 개발자 도구를 사용 하도록 설정 합니다.
- Azure 함수 포털 환경에서 사용 하도록 설정 합니다.

다음 단계를 수행하십시오.

1. Azurestack\AzureStackAdmin로 PowerShell 인스턴스를 엽니다.
2. 스크립트를 다운로드 하 고에서 추출의 위치로 이동 된 [필수 구성 요소 단계](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts)합니다.
3. [Azure 스택에 대 한 PowerShell을 설치](azure-stack-powershell-install.md)합니다.
4. 실행 된 **만들기 ADFSIdentityApp.ps1** 스크립트입니다.
5. 에 **자격 증명** 창에서 AD FS 클라우드 관리자 계정 및 암호를 입력 합니다. **확인**을 선택합니다.
6. 인증서 파일 경로 및에 대 한 인증서 암호를 제공는 [앞에서 만든 인증서](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack)합니다. 기본적으로이 단계에 대해 만들어진 인증서 **sso.appservice.local.azurestack.external.pfx**합니다.

```PowerShell
    Create-ADFSIdentityApp.ps1
```

| 매개 변수 | 필수 또는 선택 | 기본값 | 설명 |
| --- | --- | --- | --- |
| AdminArmEndpoint | 필요 | Null | Azure 리소스 관리자 관리 끝점입니다. 예 adminmanagement.local.azurestack.external입니다. |
| PrivilegedEndpoint | 필요 | Null | 권한 있는 끝점입니다. 예는 AzS ERCS01입니다. |
| CloudAdminCredential | 필요 | Null | Azure 스택 클라우드 관리자에 대 한 도메인 계정 자격 증명입니다. 예 Azurestack\CloudAdmin입니다. |
| CertificateFilePath | 필요 | Null | **전체 경로** identity 응용 프로그램의 인증서 PFX 파일에 있습니다. |
| CertificatePassword | 필요 | Null | 보호할 수 있는 인증서 개인 키 암호입니다. |

## <a name="next-steps"></a>다음 단계

[앱 서비스 리소스 공급자를 설치 합니다.](azure-stack-app-service-deploy.md)

---
title: "Azure 스택 앱 서비스를 배포 하기 전에 | Microsoft Docs"
description: "Azure 스택 앱 서비스를 배포 하기 전에 완료 하기 위한 단계"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: anwestg
ms.openlocfilehash: 17967131853d4334ae2c0ba3c0aa01089b7f3b61
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2017
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Azure 스택 앱 서비스를 시작 하기 전에

Azure 스택 앱 서비스를 azure에 배포 하기 전에 완료 해야 하는 필수 단계 수가 있습니다.

- Azure 스택 도우미 스크립트에 Azure 앱 서비스를 다운로드 합니다.
- 고가용성
- Azure 스택 앱 서비스를 Azure에 필요한 인증서
- 파일 서버 준비
- SQL Server 준비
- Azure Active Directory 응용 프로그램 만들기
- Active Directory Federation Services 응용 프로그램 만들기

## <a name="download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts"></a>Azure 스택 설치 관리자 및 도우미 스크립트에 Azure 앱 서비스를 다운로드 합니다.

1. 다운로드는 [Azure 스택 배포 도우미 스크립트에 대 한 앱 서비스](https://aka.ms/appsvconmashelpers)합니다.
2. 다운로드는 [Azure 스택 설치 관리자에서 응용 프로그램 서비스](https://aka.ms/appsvconmasinstaller)합니다.
3. 도우미 스크립트 zip 파일에서 파일을 추출 합니다. 다음 파일 및 폴더 구조를 표시 됩니다.
  - Common.ps1
  - AADIdentityApp.ps1 만들기
  - ADFSIdentityApp.ps1 만들기
  - AppServiceCerts.ps1 만들기
  - Get AzureStackRootCert.ps1
  - AppService.ps1 제거
  - 모듈
    - GraphAPI.psm1
    
## <a name="high-availability"></a>고가용성

Azure 스택에 azure 앱 서비스에 현재 Azure 스택만 하나의 단일 장애 도메인에 대 한 작업 부하를 배포 하기 때문에 고가용성을 제공할 수 없는 경우

고가용성을 위해 Azure 스택에 Azure 앱 서비스를 준비 하려면 항상 사용 가능한 구성에 필요한 파일 서버와 SQL Server를 배포 해야 합니다. Azure 스택 여러 오류 도메인을 지 원하는에서는 항상 사용 가능한 구성에서 Azure 스택에 Azure 앱 서비스를 사용 하도록 설정 하는 방법에 지침을 제공 합니다.


## <a name="certificates-required-for-azure-app-service-on-azure-stack"></a>Azure 스택 앱 서비스를 Azure에 필요한 인증서

### <a name="certificates-required-for-the-azure-stack-development-kit"></a>Azure 스택 개발 키트에 필요한 인증서

이 첫 번째 스크립트 응용 프로그램 서비스에 필요한 4 개의 인증서를 만드는 Azure 스택 인증 기관이 작동 합니다.

| 파일 이름 | 사용 |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | 앱 서비스 기본 SSL 인증서 |
| Api.appservice.local.azurestack.external.pfx | 앱 서비스 API SSL 인증서 |
| ftp.appservice.local.azurestack.external.pfx | 응용 프로그램 서비스 게시자 SSL 인증서 |
| Sso.appservice.local.azurestack.external.pfx | 앱 서비스 Id 응용 프로그램 인증서 |

Azure 스택 개발 키트 호스트에서 스크립트를 실행 하 고 azurestack\CloudAdmin으로 PowerShell을 실행 중인지 확인 합니다.

1. Azurestack\AzureStackAdmin로 실행 하 여 PowerShell 세션에서 도우미 스크립트의 압축을 푼 폴더에서 AppServiceCerts.ps1 만들기 스크립트를 실행 합니다. 스크립트는 응용 프로그램 서비스 만들기 인증서 스크립트와 같은 폴더에 4 개의 인증서를 만듭니다.
2. .Pfx 파일을 보호 하려면 암호를 입력 하 고는 기록 합니다. Azure 스택 설치 관리자에 앱 서비스에서 입력 해야 합니다.

#### <a name="create-appservicecertsps1-parameters"></a>만들 AppServiceCerts.ps1 매개 변수

| 매개 변수 | 필수/선택 | 기본값 | 설명 |
| --- | --- | --- | --- |
| PfxPassword | 필수 | Null | 인증서 개인 키를 보호 하는 데 사용 되는 암호 |
| DomainName | 필수 | local.azurestack.external | Azure 스택 지역 및 도메인 접미사 |

### <a name="certificates-required-for-a-production-deployment-of-azure-app-service-on-azure-stack"></a>Azure 스택 앱 서비스를 Azure의 프로덕션 배포에 필요한 인증서

프로덕션 환경에서 리소스 공급자를 작동 하려면 다음과 같은 4 개의 인증서를 제공 해야 합니다.

#### <a name="default-domain-certificate"></a>기본 도메인 인증서

기본 도메인 인증서는 프런트 엔드 역할에 배치 됩니다. 와일드 카드 기본 도메인 요청이 나 Azure 앱 서비스에 대 한 사용자 응용 프로그램에서 사용 됩니다. 인증서 (KUDU) 소스 제어 작업에도 사용 됩니다.

인증서는.pfx 형식에서 이어야 하며 주체가 2 개인 와일드 카드 인증서 여야 합니다. 따라서 기본 도메인을 하나의 인증서에 의해 제공 소스 제어 작업에 대 한 scm 끝점과 모두 있습니다.

| 형식 | 예제 |
| --- | --- |
| \*.appservice 합니다. \<지역\>.\< DomainName\>.\< 확장\> | \*. appservice.redmond.azurestack.external |
| \*. scm.appservice 합니다. <region>. <DomainName>.<extension> | \*. appservice.scm.redmond.azurestack.external |

#### <a name="api-certificate"></a>API 인증서

API 인증서는 관리 역할에 배치 하 고 api 호출 보안에 리소스 공급자가 사용 됩니다. 게시용 인증서에 주체 API DNS 항목과 일치 하는 주체를 포함 해야 합니다.

| 형식 | 예제 |
| --- | --- |
| api.appservice 합니다. \<지역\>.\< DomainName\>.\< 확장\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>게시 인증서

콘텐츠를 업로드 하는 경우 게시자 역할에 대 한 인증서는 응용 프로그램 소유자에 대 한 FTPS 트래픽의 보호 합니다.  게시용 인증서는 FTPS DNS 항목과 일치 하는 주체를 포함 해야 합니다.

| 형식 | 예제 |
| --- | --- |
| ftp.appservice 합니다. \<지역\>.\< DomainName\>.\< 확장\> | api.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>인증서 확인

Identity 응용 프로그램에 대 한 인증서를 사용 하면:
- AAD/ADFS 디렉터리, Azure 스택 및 계산 리소스 공급자와의 통합을 지원 하도록 응용 프로그램 서비스 간의 통합
- Single Sign On Azure 스택에 Azure 앱 서비스 내에서 고급 개발자 도구에 대 한 시나리오
Id에 대 한 인증서는 다음 형식과 일치 하는 주체를 포함 해야 합니다.

| 형식 | 예제 |
| --- | --- |
| sso.appservice 합니다. \<지역\>.\< DomainName\>.\< 확장\> | sso.appservice.redmond.azurestack.external |

### <a name="extract-the-azure-stack-azure-resource-manager-root-certificate"></a>Azure 스택 Azure 리소스 관리자 루트 인증서를 추출 합니다.

Azurestack\CloudAdmin로 실행 하 여 PowerShell 세션에서 도우미 스크립트의 압축을 푼 폴더에서 Get AzureStackRootCert.ps1 스크립트를 실행 합니다. 스크립트는 응용 프로그램 서비스 만들기 인증서 스크립트와 같은 폴더에 4 개의 인증서를 만듭니다.

| Get AzureStackRootCert.ps1 매개 변수 | 필수/선택 | 기본값 | 설명 |
| --- | --- | --- | --- |
| PrivelegedEndpoint | 필수 | AzS ERCS01 | 권한 있는 끝점입니다. |
| CloudAdminCredential | 필수 | AzureStack\CloudAdmin | Azure 스택 클라우드 관리 도메인 계정 자격 증명 |


## <a name="prepare-the-file-server"></a>파일 서버 준비

Azure 앱 서비스는 파일 서버를 사용을 해야 합니다. 프로덕션 배포에 대 한 파일 서버에 항상 사용 가능 하도록 구성 하 고 오류를 처리할 수 있어야 합니다.

와 함께 Azure 스택 개발 키트 배포 환경에만 사용 하는 데이 예제에서는 Azure Resource Manager 배포 템플릿 구성 된 단일 노드 파일 서버를 배포: https://aka.ms/appsvconmasdkfstemplate 합니다. 작업 그룹에서 단일 노드 파일 서버가 됩니다.

### <a name="provision-groups-and-accounts-in-active-directory"></a>Active Directory에서 그룹 및 계정 프로 비전


1. 다음 Active Directory 글로벌 보안 그룹을 만듭니다.
    - FileShareOwners
    - FileShareUsers
2. 서비스 계정으로 다음과 같은 Active Directory 계정을 만듭니다.
    - FileShareOwner
    - FileShareUser

    보안 모범 사례로, 이러한 계정에 대 한 (및 모든 웹 역할에 대 한) 사용자는 서로 다를 수 하 고 강력한 사용자 이름 및 암호가 있어야 해야 합니다.
    다음 조건에 따라 암호를 설정 합니다.
     - 사용 하도록 설정 **암호 사용 기간 제한 없음**합니다.
     - 사용 하도록 설정 **사용자 암호를 변경할 수 없음**합니다.
     - 사용 안 함 **사용자 다음 로그온 할 때 암호를 변경 해야**합니다.
3. 다음과 같이 그룹 멤버 자격에 계정을 추가:
    - 추가 **FileShareOwner** 에 **FileShareOwners** 그룹입니다.
    - 추가 **FileShareUser** 에 **Fileshareuser** 그룹입니다.

### <a name="provision-groups-and-accounts-in-a-workgroup"></a>작업 그룹에서 그룹 및 계정 프로 비전

>[!NOTE]
> 관리자 명령 프롬프트 세션에서 파일 서버를 구성할 때 다음과 같은 모든 명령을 실행 합니다.  **PowerShell을 사용 하지 마십시오.**

위의 Azure 리소스 관리자 템플릿을 사용 하는 사용자가 이미 생성 되어 있습니다.

1. FileShareOwner 및 FileShareUser 계정을 만들려면 다음 명령을 실행 합니다. 대체 <password> 를 원하는 값으로.
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

콘텐츠 공유에 테 넌 트 웹 사이트 콘텐츠가 포함 됩니다. 단일 파일 서버에 콘텐츠 공유를 프로 비전 하는 절차는 Active Directory의 장애 조치 클러스터에 대 한 다르지만 Active Directory 및 작업 그룹 환경에서 동일 합니다.

#### <a name="provision-the-content-share-on-a-single-file-server-ad-or-workgroup"></a>단일 파일 서버에 콘텐츠 공유를 프로 비전 (AD 또는 작업 그룹)

단일 파일 서버에서 관리자 권한 명령 프롬프트에서 다음 명령을 실행 합니다. 'C:\WebSites'에 대 한 값을 사용자 환경의 해당 경로로 바꿉니다.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="to-enable-winrm-add-the-fileshareowners-group-to-the-local-administrators-group"></a>WinRM을 사용 하려면 로컬 관리자 그룹에 FileShareOwners 그룹 추가

Windows 원격 관리가 제대로 작동 하려면을 위해 로컬 관리자 그룹에 FileShareOwners 그룹을 추가 해야 합니다.

#### <a name="active-directory"></a>Active Directory

파일 서버 또는 모든 파일 서버 장애 조치 클러스터 노드에서 관리자 권한 명령 프롬프트에서 다음 명령을 실행 합니다. 에 대 한 값을 대체 <DOMAIN> 사용 하려는 도메인 이름입니다.

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

파일 서버 또는 현재 클러스터 리소스 소유자 인 파일 서버 장애 조치 클러스터 노드에서 관리자 권한 명령 프롬프트에서 다음 명령을 실행 합니다. 기울임꼴로 표시 값을 사용자 환경의 특정 값으로 대체 합니다.

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

## <a name="prepare-the-sql-server"></a>SQL Server 준비

Azure 스택 호스팅 및 계량 데이터베이스에서 Azure 응용 프로그램 서비스를 Azure 응용 프로그램 서비스 데이터베이스를 보관할 SQL Server를 준비 해야 합니다.

Azure 스택 개발 키트에 사용 하도록 SQL Express 2014 s p 2를 사용할 수 이상입니다.

프로덕션 및 고가용성 목적으로 사용 SQL 2014 s p 2의 전체 버전 또는 이상 버전에서는 혼합 모드 인증을 사용 하도록 설정 하 고 해야에 배포 된 [항상 사용 가능한 구성](https://docs.microsoft.com/en-us/sql/sql-server/failover-clusters/high-availability-solutions-sql-server)합니다.

Azure 스택 SQL Server에서 Azure 앱 서비스는 모든 앱 서비스 역할에서 액세스할 수 있어야 합니다. Azure 스택에 기본 공급자 구독 내에서 SQL Server는 배포할 수 있습니다. 만들 수 있습니다 (으로 Azure 스택에 연결이) 조직 내에서 기존 인프라를 사용 합니다. Azure 마켓플레이스 이미지를 사용 하는 경우 그에 따라 방화벽을 구성 해야 합니다. 

모든 SQL Server 역할에 대해 기본 인스턴스나 명명된 된 인스턴스를 사용할 수 있습니다. 그러나 명명된 된 인스턴스를 사용 하는 경우 반드시 수동으로 SQL Browser 서비스를 시작 해 서 포트 1434를 열어야 합니다.

## <a name="create-an-azure-active-directory-application"></a>Azure Active Directory 응용 프로그램 만들기

다음을 지원 하려면 Azure AD 서비스 사용자를 구성 합니다.
- 가상 컴퓨터 크기 작업자 계층에서 통합을 설정합니다.
- Azure 함수 포털 및 고급 개발자 도구에 대 한 SSO를 제공 합니다.

이 단계는 Azure 스택 환경에만 보호 하는 Azure AD에 적용 합니다.

관리자는 SSO를 구성 해야 합니다.
- 응용 프로그램 서비스 (Kudu) 내에서 고급 개발자 도구를 사용 하도록 설정 합니다.
- Azure 함수 포털 환경에서 사용 하도록 설정 합니다.

다음 단계를 수행하세요.

1. Azurestack\AzureStackAdmin로 PowerShell 인스턴스를 엽니다.
2. 스크립트를 다운로드 하 고에서 추출한 위치로 이동 된 [필수 구성 요소 단계](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts)합니다.
3. [Azure 스택 PowerShell 설치](azure-stack-powershell-install.md)합니다.
4. 실행 된 **만들기 AADIdentityApp.ps1** 스크립트입니다. Azure AD 테 넌 트 ID에 대 한 메시지가 면 myazurestack.onmicrosoft.com 예를 들어, Azure 스택 배포에 사용할 Azure AD 테 넌 트 ID를 입력 합니다.
5. 에 **자격 증명** 창에서 Azure AD 서비스 관리자 계정 및 암호를 입력 합니다. **확인**을 클릭합니다.
6. 인증서 파일 경로 및 인증서 암호를 입력의 [앞에서 만든 인증서](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack)합니다. 기본적으로이 단계에 대해 만들어진 인증서 **sso.appservice.local.azurestack.external.pfx**합니다.
7. 스크립트는 Azure AD 테 넌 트에 새 응용 프로그램을 만듭니다. PowerShell 출력에 반환 되는 응용 프로그램 ID를 기록해 둡니다. 설치 하는 동안이 정보가 필요 합니다.
8. 새 브라우저 창을 열고로 (portal.azure.com) Azure 포털에 로그인 된 **Azure Active Directory 서비스 관리자**합니다.
9. Azure AD 리소스 공급자를 엽니다.
10. 클릭 **앱 등록**합니다.
11. 검색할는 **응용 프로그램 ID** 7 단계의 일환으로 반환 합니다. 앱 서비스 응용 프로그램 나열 됩니다.
12. 클릭 **응용 프로그램** 목록에서
13. 클릭 **필요한 권한** > **권한을 부여** > **예**합니다.

| 만들 AADIdentityApp.ps1 매개 변수 | 필수/선택 | 기본값 | 설명 |
| --- | --- | --- | --- |
| DirectoryTenantName | 필수 | Null | Microsoft Azure Active Directory 테넌트 ID. GUID 또는 문자열을 예를 들어 myazureaaddirectory.onmicrosoft.com를 제공 합니다. |
| AdminArmEndpoint | 필수 | Null | 예를 들어 adminmanagement.local.azurestack.external 관리 Azure 리소스 관리자 끝점 |
| TenantARMEndpoint | 필수 | Null | 예를 들어 Azure 리소스 관리자 끝점, 테 넌 트: management.local.azurestack.external |
| AzureStackAdminCredential | 필수 | Null | Azure AD 서비스 관리자 자격 증명 |
| CertificateFilePath | 필수 | Null | 이전에 생성 identity 응용 프로그램 인증서 파일 경로입니다. |
| CertificatePassword | 필수 | Null | 인증서 개인 키를 보호 하는 데 사용 하는 암호입니다. |

## <a name="create-an-active-directory-federation-services-application"></a>Active Directory Federation Services 응용 프로그램 만들기

AD FS로 보호 되는 Azure 스택 환경에서는 다음을 지원 하려면 AD FS 서비스 사용자는 구성 해야 합니다.
- 가상 컴퓨터 크기 작업자 계층에서 통합을 설정합니다.
- Azure 함수 포털 및 고급 개발자 도구에 대 한 SSO를 제공 합니다.

관리자는 SSO를 구성 해야 합니다.
- 작업자 계층에 가상 컴퓨터 크기 조정 설정 통합을 위한 서비스 사용자를 구성 합니다.
- 응용 프로그램 서비스 (Kudu) 내에서 고급 개발자 도구를 사용 하도록 설정 합니다.
- Azure 함수 포털 환경에서 사용 하도록 설정 합니다.

다음 단계를 수행하세요.

1. Azurestack\azurestackadmin로 PowerShell 인스턴스를 엽니다.
2. 스크립트를 다운로드 하 고에서 추출한 위치로 이동 된 [필수 구성 요소 단계](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts)합니다.
3. [Azure 스택 PowerShell 설치](azure-stack-powershell-install.md)합니다.
4.  실행 된 **만들기 ADFSIdentityApp.ps1** 스크립트입니다.
5.  에 **자격 증명** 창에서 AD FS 클라우드 관리자 계정 및 암호를 입력 합니다. **확인**을 클릭합니다.
6.  인증서 파일 경로 및에 대 한 인증서 암호를 제공는 [앞에서 만든 인증서](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack)합니다. 기본적으로이 단계에 대해 만든 인증서 sso.appservice.local.azurestack.external.pfx 됩니다.

| 만들 ADFSIdentityApp.ps1 매개 변수 | 필수/선택 | 기본값 | 설명 |
| --- | --- | --- | --- |
| AdminArmEndpoint | 필수 | Null | 관리자는 Azure 리소스 관리자 끝점입니다. 예를 들어 adminmanagement.local.azurestack.external 합니다. |
| PrivilegedEndpoint | 필수 | Null | 권한 있는 끝점입니다. 예를 들어 AzS ERCS01 합니다. |
| CloudAdminCredential | 필수 | Null | Azure 스택 cloudadmin 도메인 계정 자격 증명입니다. 예를 들어 Azurestack\CloudAdmin 합니다. |
| CertificateFilePath | 필수 | Null | Identity 응용 프로그램의 인증서 PFX 파일 경로입니다. |
| CertificatePassword | 필수 | Null | 인증서 개인 키를 보호 하는 데 사용 하는 암호입니다. |


## <a name="next-steps"></a>다음 단계

[앱 서비스 리소스 공급자를 설치](azure-stack-app-service-deploy.md)합니다.

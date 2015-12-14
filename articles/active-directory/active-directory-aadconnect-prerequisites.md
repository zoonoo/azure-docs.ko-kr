<properties
   pageTitle="Azure AD Connect: 필수 조건 및 하드웨어 | Microsoft Azure"
   description="대부분의 검색 결과에 표시할 문서 설명 검색"
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor="curtand"/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="11/16/2015"
   ms.author="andkjell;billmath"/>

# Azure AD Connect에 대한 필수 조건
이 항목에서는 Azure AD Connect에 대한 필수 조건 및 하드웨어 요구 사항을 설명합니다.

## Azure AD Connect를 설치하기 전에
Azure AD Connect를 설치하기 전에 필요한 몇 가지 사항이 있습니다.

**Azure AD**

- Azure 구독 또는 [Azure 평가판 구독](http://azure.microsoft.com/pricing/free-trial/) 이는 Azure AD Connect를 사용하지 않고 Azure 포털에 액세스하기 위해서만 필요합니다. PowerShell 또는 Office 365를 사용하는 경우, Azure AD Connect를 사용하여 Azure 구독을 설치할 필요가 없습니다. 또한 Office 365 라이선스가 있는 경우 Office 365 포털을 사용할 수 있습니다. 또한 유료 Office 365 라이선스를 가지고 Office 365 포털에서 Azure 포털로 가져올 수 있습니다.
- Azure AD에서 사용하려는 [도메인을 추가하고 확인합니다](active-directory-add-domain.md). 예를 들어 사용자가 contoso.com을 사용 하려는 경우 해당 도메인을 확인하고 contoso.onmicrosoft.com 기본 도메인을 사용하지 않도록 합니다.
- Azure AD 디렉터리는 기본적으로 5만 개의 개체를 허용합니다. 도메인을 확인하는 경우 제한은 30만 개의 개체로 늘어납니다. Azure AD에서 더 많은 개체가 필요한 경우 제한을 더 증가시키려면 지원 사례를 열어야 합니다. 50만 개가 넘게 필요한 경우 Office 365, Azure AD Basic, Azure AD Premium 또는 Enterprise Mobility Suite와 같은 라이선스가 필요합니다.

**온-프레미스 서버 및 환경**

- AD 스키마의 버전 및 포리스트 기능 수준은 Windows Server 2003 이상이어야 합니다. 도메인 컨트롤러는 스키마와 포레스트의 수준 요구 사항이 맞으면 어떤 버전도 실행할 수 있습니다.
- **비밀번호 쓰기 저장** 기능을 사용하려는 경우 도메인 컨트롤러가 Windows Server 2008(최신 SP 포함) 이상에 있어야 합니다.
- Azure AD Connect는 Small Business Server 또는 Windows Server Essentials에 설치할 수 없습니다. 서버는 Windows Server Standard 이상을 사용해야 합니다.
- Azure AD Connect는 반드시 Windows Server 2008 이상의 버전에 설치되어야 합니다. Express 설정을 사용하는 경우 이 서버는 도메인 컨트롤러 또는 멤버 서버일 수 있습니다. 사용자 지정 설정을 사용하는 경우 서버는 독립 실행형일 수 있고 도메인에 가입할 필요는 없습니다.
- **암호 동기화** 기능을 사용하려는 경우 Azure AD Connect 서버가 Windows Server 2008 R2 SP1 이상에 있어야 합니다.
- Azure AD Connect 서버에는 [.NET 4.5.1](#component-prerequisites) 이상 및 [PowerShell 3.0](#component-prerequisites) 이상이 설치되어 있어야 합니다.
- Active Directory Federation Services를 배포하는 경우 AD FS 또는 웹 응용 프로그램 프록시가 설치될 서버는 Windows Server 2012 R2 이상이어야 합니다. 원격 설치를 위해 이러한 서버에서 [Windows 원격 관리](#windows-remote-management)를 사용할 수 있어야 합니다.
- Active Directory Federation Services를 배포하고 있는 경우 [SSL 인증서](#ssl-certificate-requirements)가 필요합니다.
- Azure AD Connect는 ID 데이터를 저장하기 위한 SQL Server 데이터베이스가 필요합니다. 기본적으로 SQL Server 2012 Express LocalDB(SQL Server Express의 라이트 버전)가 설치되고 서비스에 대한 서비스 계정을 로컬 컴퓨터에 생성합니다. SQL Server Express는 약 100,000 개체를 관리할 수 있는 10GB의 용량을 제공합니다. 더 큰 볼륨의 디렉터리 개체 관리가 필요한 경우 설치 프로세스가 SQL Server의 다른 버전을 가리키도록 해야 합니다. Azure AD Connect는 SQL Server 2008(SP4)에서 SQL Server 2014까지 Microsoft SQL Server의 모든 버전을 지원합니다.

**계정**

- 통합하려는 Azure AD 디렉터리에 대한 Azure AD 전역 관리자 계정
- Express 설정을 사용하거나 DirSync에서 업그레이드하는 경우 로컬 Active Directory에 대한 엔터프라이즈 관리자 계정입니다.
- 사용자 지정 설정 설치 경로를 사용하는 경우 [계정은 Active Directory입니다](active-directory-aadconnect-accounts-permissions.md).

**연결**

- 인터넷에 연결하는 데 아웃바운드 프록시를 사용하는 경우 설치 마법사 및 Azure AD Sync에서 인터넷 및 Azure AD에 연결할 수 있으려면 **C:\\Windows\\Microsoft.NET\\Framework64\\v4.0.30319\\Config\\machine.config** 파일에 다음 설정을 추가해야 합니다.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

이 텍스트는 파일의 맨 아래에 입력해야 합니다. 이 코드에서 &lt;PROXYADRESS&gt;는 실제 프록시 IP 주소 또는 호스트 이름을 나타냅니다. 프록시가 액세스할 수 있는 URL을 제한하는 경우 [Office 365 URL 및 IP 주소 범위](https://support.office.com/ko-KR/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)에서 설명한 URL이 프록시에서 열려야 합니다.

**기타**

- 선택 사항: 동기화를 확인할 테스트 사용자 계정

## 구성 요소 필수 조건

Azure AD Connect는 PowerShell 및.Net 4.5.1에 따라 달라집니다. Windows Server 버전에 따라 다음을 수행합니다.

- Windows Server 2012R2
  - PowerShell은 기본적으로 설치되므로 조치가 필요하지 않습니다.
  - .NET 4.5.1 이후 릴리스는 Windows 업데이트를 통해 제공됩니다. 제어판에서 Windows Server에 최신 업데이트를 설치했는지 확인합니다.
- Windows Server 2008R2 및 Windows Server 2012
  - 최신 버전의 PowerShell은 **Windows Management Framework 4.0**에서 사용할 수 있으며 이는 [Microsoft 다운로드 센터](http://www.microsoft.com/downloads)에서 찾을 수 있습니다.
  - .NET 4.5.1 이후 릴리스는 [Microsoft 다운로드 센터](http://www.microsoft.com/downloads)에서 찾을 수 있습니다.
- Windows Server 2008
  - 지원되는 최신 버전의 PowerShell은 **Windows Management Framework 3.0**에서 사용할 수 있으며 이는 [Microsoft 다운로드 센터](http://www.microsoft.com/downloads)에서 찾을 수 있습니다.
 - .NET 4.5.1 이후 릴리스는 [Microsoft 다운로드 센터](http://www.microsoft.com/downloads)에서 찾을 수 있습니다.

## Windows 원격 관리

 Azure AD Connect를 사용하여 웹 응용 프로그램 프록시 또는 Active Directory Federation Services를 배포하고 아래 요구 사항을 확인하여 연결 및 구성이 성공하도록 합니다.

 - 대상 서버가 도메인에 가입된 경우 Windows 원격 관리가 사용되는지 확인합니다.
    - 관리자 권한 PSH 명령 창에서 `Enable-PSRemoting –force` 명령을 사용합니다.
 - 대상 서버가 도메인에 연결된 WAP 컴퓨터가 아닌 경우, 몇가지 추가 요구 사항이 있습니다.
 	- 대상 컴퓨터(WAP 컴퓨터):
         - Winrm(Windows Remote Management / WS-Management) 서비스가 서비스 스냅인을 통해 실행되는지 확인합니다.
         - 관리자 권한 PSH 명령 창에서 `Enable-PSRemoting –force` 명령을 사용합니다.
    - 마법사를 실행 중인 컴퓨터(대상 컴퓨터가 도메인에 가입되지 않거나 신뢰할 수 없는 도메인인 경우):
        - 관리자 권한 PSH 명령 창에서 `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate` 명령을 사용합니다.
 	    - 서버 관리자:
 		     - DMZ WAP 호스트를 컴퓨터 풀에 추가(서버 관리자 -> 관리 -> 서버 추가...DNS 탭 사용)
 		     - 서버 관리자 모든 서버 탭: WAP 서버를 마우스 오른쪽 단추로 클릭하고 다음으로 관리...를 선택하고, WAP 컴퓨터에 대한 로컬(도메인이 아닌) 자격 증명을 입력합니다.
 		     - 서버 관리자 모든 서버 탭에서 원격 PSH 연결 유효성을 검사하려면, WAP 서버를 마우스 오른쪽 단추로 클릭하고 Windows PowerShell을 선택합니다. 원격 PowerShell 세션을 설정할 수 있도록 원격 PSH 세션을 열어야 합니다.

## SSL 인증서 요구 사항

**중요:** 모든 웹 응용 프로그램 프록시 서버뿐 아니라 AD FS 팜의 모든 노드에서 동일한 SSL 인증서를 사용하는 것이 가장 좋습니다.

- 인증서는 X509 인증서여야 합니다.
- 테스트 랩 환경의 페더레이션 서버에서 자체 서명된 인증서를 사용할 수 있습니다. 그러나 프로덕션 환경에서는 공용 CA에서 인증서를 가져오는 것이 좋습니다.
    - 공개적으로 신뢰할 수 없는 인증서를 사용하면 각 웹 응용 프로그램 프록시 서버에 설치된 인증서가 모든 페더레이션 서버 및 로컬 서버에서 신뢰할 수 있는 지 확인합니다.
- 인증서의 ID는 페더레이션 서비스 이름 (예: fs.contoso.com)과 일치해야 합니다.
    - ID는 dNSName 유형의 주체 대체 이름(SAN)의 확장 유형이거나, SAN 항목이 없는 경우 공통 이름으로 지정된 주제 이름입니다.  
    - 그 중 하나가 페더레이션 서비스 이름과 일치하는 경우 여러 SAN 항목이 인증서에 있을 수 있습니다.
    - 작업 공간 연결을 사용하려는 경우 추가 SAN은 값 **enterpriseregistration.** 다음에 조직의 UPN(사용자 계정 이름) 접미사(예: **enterpriseregistration.contoso.com**)가 필요합니다.
- CryptoAPI 다음 세대(CNG) 및 키 저장소 공급자를 기반으로 하는 인증서는 지원되지 않습니다. 즉, KSP(키 저장소 공급자)가 아닌 CSP(암호화 서비스 공급자)를 기반으로 인증서를 사용해야 합니다.
- 와일드 카드 인증서가 지원됩니다.

## Azure AD Connect 지원 구성 요소

다음은 Azure AD Connect를 설치한 서버에 Azure AD Connect가 설치되는 데 필요한 구성 요소의 목록입니다. 이 목록은 기본 Express 설치에 해당합니다. 동기화 서비스 설치 페이지에서 다른 SQL Server를 사용하도록 선택하는 경우 SQL Express LocalDB는 로컬에 설치되지 않습니다.

- Microsoft SQL Server 2012 명령줄 유틸리티
- Microsoft SQL Server 2012 Native Client
- Microsoft SQL Server 2012 Express LocalDB
- Windows PowerShell용 Azure Active Directory 모듈
- IT 전문가를 위한 Microsoft Online Services 로그인 도우미
- Microsoft Visual C++ 2013 재배포 패키지


## Azure AD Connect의 하드웨어 요구 사항
아래 테이블은 Azure AD Connect 동기화 컴퓨터의 최소 요구 사항을 보여 줍니다.

| Active Directory의 개체 수 | CPU | 메모리 | 하드 드라이브 크기 |
| ------------------------------------- | --- | ------ | --------------- |
| 10,000개 미만 | 1\.6GHz | 4GB | 70GB |
| 10,000–50,000개 | 1\.6GHz | 4GB | 70GB |
| 50,000–100,000개 | 1\.6GHz | 16GB | 100GB |
| 처음 사용자용 SQL Server가 필요한 100,000개 이상의 개체| | | |
| 100,000–300,000개 | 1\.6GHz | 32GB | 300GB |
| 300,000–600,000개 | 1\.6GHz | 32GB | 450GB |
| 600,000개 초과 | 1\.6GHz | 32GB | 500GB |

AD FS 또는 웹 응용 프로그램 서버를 실행하는 컴퓨터에 대한 최소 요구 사항은 다음과 같습니다.

- CPU: 듀얼 코어 1.6GHz 이상
- 메모리: 2GB 이상
- Azure VM: A2 구성 이상


## 다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_1203_2015-->
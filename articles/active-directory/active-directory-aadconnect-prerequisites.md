<properties
   pageTitle="Azure Active Directory Connect에 대한 필수 조건 | Microsoft Azure"
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
   ms.date="10/13/2015"
   ms.author="andkjell;billmath"/>

# Azure Active Directory Connect(Azure AD Connect)에 대한 필수 조건
이 항목에서는 Azure AD Connect에 대한 필수 조건 및 하드웨어 요구 사항을 설명합니다.

## Azure AD Connect를 설치하기 전에
Azure AD Connect를 설치하기 전에 필요한 몇 가지 사항이 있습니다.

**Azure AD**

- Azure 구독 또는 [Azure 평가판 구독](http://azure.microsoft.com/pricing/free-trial/) -Azure AD Connect를 사용하지 않고 Azure 포털에 액세스하기 위해서만 필요합니다. PowerShell 또는 Office 365를 사용하는 경우, Azure AD Connect를 사용하여 Azure 구독을 설치할 필요가 없습니다. 또한 Office 365 라이선스가 있는 경우 Office 365 포털을 사용할 수 있습니다. 또한 유료 Office 365 라이선스를 가지고 Office 365 포털에서 Azure 포털로 가져올 수 있습니다.
- Azure AD에서 사용하려는 도메인을 확인합니다. 예를 들어 사용자가 contoso.com을 사용 하려는 경우 해당 도메인을 확인하고 contoso.onmicrosoft.com 기본 도메인을 사용하지 않도록 합니다.
- Azure AD 디렉터리는 기본적으로 5만 개의 개체를 허용합니다. 도메인을 확인하는 경우 제한은 30만 개의 개체로 늘어납니다. Azure AD에서 더 많은 개체가 필요한 경우 제한을 더 증가시키려면 지원 사례를 열어야 합니다. 50만 개가 넘게 필요한 경우 Office 365, Azure AD Basic, Azure AD Premium 또는 Enterprise Mobility Suite와 같은 라이선스가 필요합니다.

**온-프레미스 서버 및 환경**

- AD 스키마의 버전 및 포리스트 기능 수준은 Windows Server 2003 이상이어야 합니다. 도메인 컨트롤러는 스키마와 포레스트의 수준 요구 사항이 맞으면 어떤 버전도 실행할 수 있습니다.
- Azure AD Connect는 반드시 Windows Server 2008 이상의 버전에 설치되어야 합니다. Express 설정을 사용하는 경우 이 서버는 도메인 컨트롤러 또는 멤버 서버일 수 있습니다. 사용자 지정 설정을 사용하는 경우 서버는 독립 실행형일 수 있고 도메인에 가입할 필요는 없습니다.
- 기능 암호 동기화를 사용하려는 경우 서버는 Windows Server 2008 R2 SP1 이후여야 합니다.
- Active Directory Federation Services를 배포하는 경우 AD FS 또는 웹 응용 프로그램 프록시가 설치될 서버는 Windows Server 2012 R2 이상이어야 합니다. 원격 설치를 위해 이러한 서버에서 Windows 원격 관리를 사용할 수 있어야 합니다.
- Azure AD Connect는 ID 데이터를 저장하기 위한 SQL Server 데이터베이스가 필요합니다. 기본적으로 SQL Server 2012 Express LocalDB(SQL Server Express의 라이트 버전)가 설치되고 서비스에 대한 서비스 계정을 로컬 컴퓨터에 생성합니다. SQL Server Express는 약 100,000 개체를 관리할 수 있는 10GB의 용량을 제공합니다. 더 큰 볼륨의 디렉터리 개체 관리가 필요한 경우 설치 프로세스가 SQL Server의 다른 버전을 가리키도록 해야 합니다. Azure AD Connect는 SQL Server 2008(SP4)에서 SQL Server 2014까지 Microsoft SQL Server의 모든 버전을 지원합니다.

**계정**

- 통합하려는 Azure AD 디렉터리에 대한 Azure AD 전역 관리자 계정
- Express 설정을 사용하거나 DirSync에서 업그레이드하는 경우 로컬 Active Directory에 대한 엔터프라이즈 관리자 계정입니다.
- 사용자 지정 설정 설치 경로를 사용하는 경우 [계정은 Active Directory입니다](active-directory-aadconnect-accounts-permissions.md).

**연결**

- 인터넷에 연결하는 데 아웃 바운드 프록시를 사용하는 경우 **C:\\Windows\\Microsoft.NET\\Framework64\\v4.0.30319\\Config\\machine.config** 파일의 다음 설정은 설치 마법사 및 Azure AD 동기화에 추가되어 인터넷 및 Azure AD에 연결할 수 있어야 합니다.

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

이 텍스트는 파일의 맨 아래에 입력해야 합니다. 이 코드에서 &lt;PROXYADRESS&gt;는 실제 프록시 IP 주소 또는 호스트 이름을 나타냅니다. - 프록시가 액세스할 수 있는 URL을 제한하는 경우 [Office 365 URL 및 IP 주소 범위](https://support.office.com/ko-KR/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)에서 설명한 URL은 프록시에서 열려야 합니다.

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
  - 최신 지원 버전의 PowerShell은 **Windows Management Framework 3.0**에서 사용할 수 있으며 이는 [Microsoft 다운로드 센터](http://www.microsoft.com/downloads)에서 찾을 수 있습니다.
 - .NET 4.5.1 이후 릴리스는 [Microsoft 다운로드 센터](http://www.microsoft.com/downloads)에서 찾을 수 있습니다.

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

<!---HONumber=Nov15_HO1-->
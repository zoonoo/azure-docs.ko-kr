---
title: 'Azure AD Connect: 필수 조건 및 하드웨어 | Microsoft Docs'
description: 이 항목에서는 Azure AD Connect에 대한 필수 조건 및 하드웨어 요구 사항에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f250d4593c8dac8007590245e1b774b95d8fa786
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75767945"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Azure AD Connect에 대한 필수 조건
이 항목에서는 Azure AD Connect에 대한 필수 조건 및 하드웨어 요구 사항을 설명합니다.

## <a name="before-you-install-azure-ad-connect"></a>Azure AD Connect를 설치하기 전에
Azure AD Connect를 설치하기 전에 필요한 몇 가지 사항이 있습니다.

### <a name="azure-ad"></a>Azure AD
* Azure AD 테넌트. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)에서 사용할 수 있습니다. 다음 포털 중 하나를 사용하여 Azure AD Connect를 관리할 수 있습니다.
  * [Azure Portal](https://portal.azure.com)
  * [Office 포털](https://portal.office.com)  
* [도메인을 추가하고 확인합니다](../active-directory-domains-add-azure-portal.md) . 예를 들어, 사용자가 contoso.com을 사용하려는 경우 해당 도메인을 확인하고 contoso.onmicrosoft.com 기본 도메인을 사용하지 않도록 합니다.
* Azure AD 테넌트는 기본적으로 5만 개의 개체를 허용합니다. 도메인을 확인하는 경우, 제한은 30만 개의 개체로 늘어납니다. Azure AD에서 더 많은 개체가 필요한 경우 제한을 더 증가시키려면 지원 사례를 열어야 합니다. 개체가 50만 개 이상 필요한 경우 Office 365, Azure AD Basic, Azure AD Premium 또는 Enterprise Mobility 및 Security와 같은 라이선스가 필요합니다.

### <a name="prepare-your-on-premises-data"></a>온-프레미스 데이터 준비
* Azure AD 및 Office 365로 동기화하기 전에 [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac)를 사용하여 디렉터리의 중복 및 서식 문제 등의 오류가 있는지 식별합니다.
* [Azure AD에서 사용하도록 설정할 수 있는 선택적 동기화 기능](how-to-connect-syncservice-features.md) 을 검토하여 사용하도록 설정해야 할 기능을 평가합니다.

### <a name="on-premises-active-directory"></a>온-프레미스 Active Directory
* AD 스키마의 버전 및 포리스트 기능 수준은 Windows Server 2003 이상이어야 합니다. 도메인 컨트롤러는 스키마와 포레스트의 수준 요구 사항이 맞으면 어떤 버전도 실행할 수 있습니다.
* **비밀번호 쓰기 저장** 기능을 사용하려는 경우 도메인 컨트롤러가 Windows Server 2008 R2 이상에 있어야 합니다.
* Azure AD에서 사용되는 도메인 컨트롤러는 쓰기 가능해야 합니다. RODC(읽기 전용 도메인 컨트롤러)를 사용하는 것은 **지원되지 않으며** Azure AD Connect는 쓰기 리디렉션을 따르지 않습니다.
* "점으로 구분된"(이름에 마침표 "." 포함) NetBios 이름을 사용하는 온-프레미스 포리스트/도메인은 **지원되지 않습니다**.
* [Active Directory 휴지통을 사용하도록 설정](how-to-connect-sync-recycle-bin.md)하는 것이 좋습니다.

### <a name="azure-ad-connect-server"></a>Azure AD Connect 서버
>[!IMPORTANT]
>Azure AD Connect 서버에는 중요 한 id 데이터가 포함 되어 있으며 [Active Directory 관리 계층 모델](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) 에 설명 된 대로 계층 0 구성 요소로 처리 되어야 합니다.

* Azure AD Connect는 Small Business Server 또는 2019 이전 Windows Server Essentials에 설치할 수 없습니다(Windows Server Essentials 2019는 지원됨). 서버는 Windows Server Standard 이상을 사용해야 합니다.
* 도메인 컨트롤러에 Azure AD Connect를 설치 하는 것은 Azure AD Connect을 제대로 설치할 수 없게 하는 보안 방법 및 더 제한적인 설정으로 인해 권장 되지 않습니다.
* Azure AD Connect 서버에는 전체 GUI가 설치되어 있어야 합니다. Server Core에 설치하는 것은 **지원되지 않습니다**.
>[!IMPORTANT]
>Small business server, server essentials 또는 server core에 Azure AD Connect를 설치 하는 것은 지원 되지 않습니다.

* Azure AD Connect는 Windows Server 2012 이상에 설치 해야 합니다. 이 서버는 도메인에 가입 되어 있어야 하며 도메인 컨트롤러 또는 구성원 서버가 될 수 있습니다.
* Azure AD Connect 마법사를 사용 하 여 ADFS 구성을 관리 하는 경우에는 Azure AD Connect 서버에서 PowerShell 기록을 사용 하도록 설정 그룹 정책 하지 않아야 합니다. Azure AD Connect 마법사를 사용하여 동기화 구성을 관리하는 경우 PowerShell 기록을 사용하도록 설정할 수 있지만,
* Active Directory Federation Services를 배포하는 경우 AD FS 또는 웹 애플리케이션 프록시가 설치될 서버는 Windows Server 2012 R2 이상이어야 합니다. [Windows 원격 관리](#windows-remote-management) 를 사용할 수 있어야 합니다.
* Active Directory Federation Services를 배포하고 있는 경우 [SSL 인증서](#ssl-certificate-requirements)가 필요합니다.
* Active Directory Federation Services를 배포하고 있는 경우 [이름 확인](#name-resolution-for-federation-servers)을 구성해야 합니다.
* 전역 관리자가 MFA를 사용하도록 설정한 경우 URL **https://secure.aadcdn.microsoftonline-p.com** 이 신뢰할 수 있는 사이트 목록에 있어야 합니다. MFA 챌린지를 묻는 메시지가 표시되기 전에 이 사이트를 추가하지 않은 경우 신뢰할 수 있는 사이트 목록에 추가하라는 메시지가 표시됩니다. Internet Explorer를 사용하여 신뢰할 수 있는 사이트에 추가할 수 있습니다.
* IT 환경의이 중요 한 구성 요소에 대 한 보안 공격 노출 영역을 줄이려면 Azure AD Connect 서버를 강화 하는 것이 좋습니다.  아래의 권장 사항에 따라 조직의 보안 위험을 줄일 수 있습니다.

* 도메인에 가입 된 서버에 Azure AD Connect를 배포 하 고 도메인 관리자 또는 엄격 하 게 제어 되는 다른 보안 그룹에 대 한 관리 액세스를 제한 합니다

자세한 내용은 다음을 참조하세요. 

* [관리자 그룹 보안](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [기본 제공 관리자 계정 보안](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [공격 노출 영역을 줄임으로써 보안 향상 및 sustainment](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Active Directory 공격 노출 영역 축소](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

### <a name="sql-server-used-by-azure-ad-connect"></a>Azure AD Connect에서 사용하는 SQL Server
* Azure AD Connect는 ID 데이터를 저장하기 위한 SQL Server 데이터베이스가 필요합니다. 기본적으로 SQL Server 2012 Express LocalDB(SQL Server Express의 라이트 버전)가 설치됩니다. SQL Server Express는 약 100,000개의 개체를 관리할 수 있는 10GB의 용량을 제공합니다. 더 큰 볼륨의 디렉터리 개체 관리가 필요한 경우 설치 마법사가 SQL Server의 다른 설치를 가리키도록 해야 합니다. SQL Server 설치의 유형은 [Azure AD Connect의 성능](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors)에 영향을 줄 수 있습니다.
* 다른 SQL Server 설치를 사용 하는 경우 다음 요구 사항이 적용 됩니다.
  * Azure AD Connect는 2012 (최신 서비스 팩 포함)의 모든 버전 Microsoft SQL Server를 SQL Server 2019로 지원 합니다. Microsoft Azure SQL Database는 데이터베이스로 **지원되지 않습니다** .
  * 대/소문자를 구분하지 않는 SQL 데이터 정렬을 사용해야 합니다. 이러한 데이터 정렬은 이름에 \_CI_를 사용하여 식별됩니다. 이름에 \_CS_를 사용하여 식별되는 대/소문자 구분 데이터 정렬을 사용하는 것은 **지원되지 않습니다**.
  * SQL 인스턴스당 동기화 엔진을 한 개만 사용할 수 있습니다. SQL 인스턴스를 FIM/MIM 동기화, DirSync 또는 Azure AD Sync와 공유하는 것은 **지원되지 않습니다**.

### <a name="accounts"></a>계정
* 통합하려는 Azure AD 테넌트에 대한 Azure AD 전역 관리자 계정. 이 계정은 **학교 또는 조직 계정**이어야 하며 **Microsoft 계정**이 될 수 없습니다.
* Express 설정을 사용하거나 DirSync에서 업그레이드하는 경우 온-프레미스 Active Directory에 대한 엔터프라이즈 관리자 계정이 있어야 합니다.
* [Active Directory의 계정](reference-connect-accounts-permissions.md) - 사용자 지정 설정 설치 경로 또는 온-프레미스 Active Directory의 엔터프라이즈 관리자 계정을 사용하는 경우

### <a name="connectivity"></a>연결
* Azure AD Connect 서버는 인트라넷 및 인터넷에 대해 DNS 확인을 해야 합니다. DNS 서버는 온-프레미스 Active Directory와 Azure AD 엔드포인트 모두에 대해 이름을 확인할 수 있어야 합니다.
* 인트라넷에 방화벽이 있고 Azure AD Connect 서버와 도메인 컨트롤러 사이에서 포트를 열어야 하는 경우 자세한 내용은 [Azure AD Connect 포트](reference-connect-ports.md) 를 참조하세요.
* 프록시 또는 방화벽에 액세스할 수 있는 URL을 제한하는 경우 [Office 365 URL 및 IP 주소 범위 ](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) 에서 설명한 URL이 열려야 합니다.
  * 독일의 Microsoft Cloud 또는 Microsoft Azure Government 클라우드를 사용하는 경우 URL은 [Azure AD Connect 동기화 서비스 인스턴스가 고려 사항](reference-connect-instances.md) 을 참조하세요.
* Azure AD Connect(1.1.614.0 버전 이상)는 기본적으로 TLS 1.2를 사용하여 동기화 엔진과 Azure AD 간의 통신을 암호화합니다. 기본 운영 체제에서 TLS 1.2를 사용할 수 없는 경우 Azure AD Connect에 이전 프로토콜(TLS 1.1 및 TLS 1.0)이 점차적으로 다시 적용됩니다.
* 1\.1.614.0 버전 이전의 Azure AD Connect는 기본적으로 TLS 1.0을 사용하여 동기화 엔진과 Azure AD 간의 통신을 암호화합니다. TLS 1.2로 변경하려면 [Azure AD Connect에 TLS 1.2 사용](#enable-tls-12-for-azure-ad-connect)의 단계를 수행합니다.
* 인터넷에 연결하는 데 아웃바운드 프록시를 사용하는 경우 설치 마법사 및 Azure AD Connect 동기화에서 인터넷 및 Azure AD에 연결하려면 **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** 파일에 다음 설정을 추가해야 합니다. 이 텍스트는 파일의 맨 아래에 입력해야 합니다. 이 코드에서 &lt;PROXYADDRESS&gt;는 실제 프록시 IP 주소 또는 호스트 이름을 나타냅니다.

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

* 프록시 서버에 인증이 필요한 경우 [서비스 계정](reference-connect-accounts-permissions.md#adsync-service-account)이 도메인에 있어야 하고 사용자 지정 설정 설치 경로를 사용하여 [사용자 지정 서비스 계정](how-to-connect-install-custom.md#install-required-components)을 지정해야 합니다. Machine.config의 다른 변경도 필요 합니다. Machine.config에서이 변경 내용을 사용 하면 설치 마법사와 동기화 엔진이 프록시 서버의 인증 요청에 응답 합니다. **구성** 페이지를 제외하고 모든 설치 마법사 페이지에서 로그인한 사용자의 자격 증명이 사용됩니다. 설치 마법사의 끝에 나오는 **구성** 페이지에서 컨텍스트가 이전에 만든 [서비스 계정](reference-connect-accounts-permissions.md#adsync-service-account)으로 전환됩니다. machine.config 섹션은 다음과 같이 표시됩니다.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Azure AD Connect에서 디렉터리 동기화의 일부로 웹 요청을 Azure AD로 보내면 Azure AD에서 응답하는 데 최대 5분이 걸릴 수 있습니다. 프록시 서버에서 연결 유휴 시간 제한 구성을 사용하는 것이 일반적입니다. 구성이 6분 이상으로 설정되었는지 확인해 주세요.

자세한 내용은 [기본 프록시 요소](https://msdn.microsoft.com/library/kd3cf2ex.aspx)에 대한 MSDN을 참조하세요.  
연결에 문제가 있는 경우 [연결 문제 해결](tshoot-connect-connectivity.md)을 참조하세요.

### <a name="other"></a>기타
* 선택 사항: 동기화를 확인할 테스트 사용자 계정

## <a name="component-prerequisites"></a>구성 요소 필수 조건
### <a name="powershell-and-net-framework"></a>PowerShell 및 .NET Framework
Azure AD Connect는 Microsoft PowerShell 및 .NET Framework 4.5.1에 따라 다릅니다. 서버에 이 버전 이상을 설치해야 합니다. Windows Server 버전에 따라 다음을 수행합니다.

* Windows Server 2012R2
  * Microsoft PowerShell은 기본적으로 설치되므로 아무런 작업도 필요하지 않습니다.
  * .NET Framework 4.5.1 이후 릴리스는 Windows 업데이트를 통해 제공됩니다. 제어판에서 Windows Server에 최신 업데이트를 설치했는지 확인합니다.
* Windows Server 2012
  * 최신 버전의 Microsoft PowerShell은 **Windows Management Framework 4.0**에서 사용할 수 있으며 이는 [Microsoft 다운로드 센터](https://www.microsoft.com/downloads)에서 찾을 수 있습니다.
  * .NET Framework 4.5.1과 이후 릴리스는 [Microsoft 다운로드 센터](https://www.microsoft.com/downloads)에서 찾을 수 있습니다.


### <a name="enable-tls-12-for-azure-ad-connect"></a>Azure AD Connect에 TLS 1.2 사용
1\.1.614.0 버전 이전의 Azure AD Connect는 기본적으로 TLS 1.0을 사용하여 동기화 엔진 서버와 Azure AD 간의 통신을 암호화합니다. 서버에서 기본적으로 TLS 1.2를 사용 하도록 .NET 응용 프로그램을 구성 하 여이를 변경할 수 있습니다. TLS 1.2에 대한 자세한 내용은 [Microsoft 보안 권고 2960358](https://technet.microsoft.com/security/advisory/2960358)에서 찾을 수 있습니다.

1.  운영 체제에 대해 .NET 4.5.1 핫픽스를 설치 했는지 확인 하세요. [Microsoft 보안 공지 2960358](https://technet.microsoft.com/security/advisory/2960358)을 참조 하세요. 이 핫픽스 또는 이후 릴리스를 서버에 이미 설치했을 수 있습니다.
    ```
2. For all operating systems, set this registry key and restart the server.
    ```
    HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\.NETFramework\v4.0.30319 "SchUseStrongCrypto" = dword: 00000001
    ```
4. If you also want to enable TLS 1.2 between the sync engine server and a remote SQL Server, then make sure you have the required versions installed for [TLS 1.2 support for Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## Prerequisites for federation installation and configuration
### Windows Remote Management
When using Azure AD Connect to deploy Active Directory Federation Services or the Web Application Proxy, check these requirements:

* If the target server is domain joined, then ensure that Windows Remote Managed is enabled
  * In an elevated PSH command window, use command `Enable-PSRemoting –force`
* If the target server is a non-domain joined WAP machine, then there are a couple of additional requirements
  * On the target machine (WAP machine):
    * Ensure the winrm (Windows Remote Management / WS-Management) service is running via the Services snap-in
    * In an elevated PSH command window, use command `Enable-PSRemoting –force`
  * On the machine on which the wizard is running (if the target machine is non-domain joined or untrusted domain):
    * In an elevated PSH command window, use the command `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * In Server Manager:
      * add DMZ WAP host to machine pool (server manager -> Manage -> Add Servers...use DNS tab)
      * Server Manager All Servers tab: right click WAP server and choose Manage As..., enter local (not domain) creds for the WAP machine
      * To validate remote PSH connectivity, in the Server Manager All Servers tab: right click WAP server and choose Windows PowerShell. A remote PSH session should open to ensure remote PowerShell sessions can be established.

### SSL Certificate Requirements
* It’s strongly recommended to use the same SSL certificate across all nodes of your AD FS farm and all Web Application proxy servers.
* The certificate must be an X509 certificate.
* You can use a self-signed certificate on federation servers in a test lab environment. However, for a production environment, we recommend that you obtain the certificate from a public CA.
  * If using a certificate that is not publicly trusted, ensure that the certificate installed on each Web Application Proxy server is trusted on both the local server and on all federation servers
* The identity of the certificate must match the federation service name (for example, sts.contoso.com).
  * The identity is either a subject alternative name (SAN) extension of type dNSName or, if there are no SAN entries, the subject name specified as a common name.  
  * Multiple SAN entries can be present in the certificate, provided one of them matches the federation service name.
  * If you are planning to use Workplace Join, an additional SAN is required with the value **enterpriseregistration.** followed by the User Principal Name (UPN) suffix of your organization, for example, **enterpriseregistration.contoso.com**.
* Certificates based on CryptoAPI next generation (CNG) keys and key storage providers are not supported. This means you must use a certificate based on a CSP (cryptographic service provider) and not a KSP (key storage provider).
* Wild-card certificates are supported.

### Name resolution for federation servers
* Set up DNS records for the AD FS federation service name (for example sts.contoso.com) for both the intranet (your internal DNS server) and the extranet (public DNS through your domain registrar). For the intranet DNS record, ensure that you use A records and not CNAME records. This is required for windows authentication to work correctly from your domain joined machine.
* If you are deploying more than one AD FS server or Web Application Proxy server, then ensure that you have configured your load balancer and that the DNS records for the AD FS federation service name (for example sts.contoso.com) point to the load balancer.
* For windows integrated authentication to work for browser applications using Internet Explorer in your intranet, ensure that the AD FS federation service name (for example sts.contoso.com) is added to the intranet zone in IE. This can be controlled via group policy and deployed to all your domain joined computers.

## Azure AD Connect supporting components
The following is a list of components that Azure AD Connect installs on the server where Azure AD Connect is installed. This list is for a basic Express installation. If you choose to use a different SQL Server on the Install synchronization services page, then SQL Express LocalDB is not installed locally.

* Azure AD Connect Health
* Microsoft SQL Server 2012 Command Line Utilities
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 Redistribution Package

## Hardware requirements for Azure AD Connect
The table below shows the minimum requirements for the Azure AD Connect sync computer.

| Number of objects in Active Directory | CPU | Memory | Hard drive size |
| --- | --- | --- | --- |
| Fewer than 10,000 |1.6 GHz |4 GB |70 GB |
| 10,000–50,000 |1.6 GHz |4 GB |70 GB |
| 50,000–100,000 |1.6 GHz |16 GB |100 GB |
| For 100,000 or more objects the full version of SQL Server is required | | | |
| 100,000–300,000 |1.6 GHz |32 GB |300 GB |
| 300,000–600,000 |1.6 GHz |32 GB |450 GB |
| More than 600,000 |1.6 GHz |32 GB |500 GB |

The minimum requirements for computers running AD FS or Web Application Proxy Servers is the following:

* CPU: Dual core 1.6 GHz or higher
* MEMORY: 2 GB or higher
* Azure VM: A2 configuration or higher

## Next steps
Learn more about [Integrating your on-premises identities with Azure Active Directory](whatis-hybrid-identity.md).

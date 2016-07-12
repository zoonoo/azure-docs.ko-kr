<properties
	pageTitle="Office 365 및 Azure AD 사용자를 위한 인증서 갱신 지침. | Microsoft Azure"
	description="이 문서에서는 Office 365 사용자가 인증서 갱신에 대해 알리는 전자 메일을 받는 경우 문제를 해결하는 방법에 대해 설명합니다."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="billmath"/>


# Office 365 및 Azure AD에 대한 페더레이션 인증서 갱신

##개요

Azure AD와 AD FS 간의 성공적인 페더레이션을 위해 AD FS에서 Azure AD에 대한 보안 토큰을 서명하는 데 사용하는 인증서는 Azure AD에서 구성된 인증서와 일치해야 합니다. 일치하지 않는 경우 AD FS 및 AAD 간의 트러스트가 깨질 수 있습니다. 엑스트라넷에 액세스하기 위해 AD FS 및 웹 응용 프로그램 프록시를 배포하는 경우 Azure AD는 이 정보의 동기화를 유지합니다.

이 문서에서는 다음과 같은 경우 토큰 서명 인증서를 관리하고 Azure AD와 동기화하는 추가 정보를 제공합니다.

* 웹 응용 프로그램 프록시를 배포하지 않았고 따라서 엑스트라넷에서 페더레이션 메타데이터를 사용할 수 없습니다.
* 토큰 서명 인증서에 AD FS의 기본 구성을 사용하지 않는 경우
* 타사 ID 공급자를 사용하는 경우

## 토큰 서명 인증서에 대한 AD FS의 기본 구성

토큰 서명 및 인증서의 암호를 해독하는 토큰은 일반적으로 자체 서명된 인증서이며 1년 동안 사용할 수 있습니다. 토큰 서명 및 인증서의 암호를 해독하는 토큰과 관련하여 AD FS의 기본 구성은 **AutoCertificateRollover**라는 자동 갱신 프로세스를 포함합니다. AD FS 2.0 이상을 사용하는 경우는 인증서가 만료되기 전에 Office 365 및 Azure AD에서 자동으로 인증서를 업데이트합니다.

### 갱신 알림 - O365 포털 및 전자 메일 알림

>[AZURE.NOTE] Office용 인증서를 갱신하도록 요청하는 전자 메일 또는 포털 알림을 받은 경우 아래의 [토큰 서명 인증서에 대한 변경 내용 관리](#managecerts)에 설명된 단계를 수행하여 조치를 취해야 하는지를 확인합니다. 아무 작업도 필요하지 않은 경우에도 Microsoft에서는 인증서 갱신을 위해 사용자에게 알림을 발생시킬 수 있는 가능한 문제에 주의를 기울입니다.

Azure AD는 페더레이션 메타데이터에서 표시한 대로 페더레이션 메타데이터를 모니터링하고 토큰 서명 인증서를 업데이트하려고 합니다. 토큰 서명 인증서가 만료되기 전에 30일 동안 Azure AD는 페더레이션 메타데이터를 폴링하여 새 인증서를 사용할 수 있는지를 확인합니다.

* 페더레이션 메타데이터를 성공적으로 폴링하고 새 인증서를 검색할 수 있는 경우 사용자에게 전자 메일 알림 또는 O365 포털 경고가 주어지지 않습니다.
* 페더레이션 메타데이터가 연결할 수 없거나 자동 인증서 롤오버를 사용할 수 없기 때문에 새 토큰 서명 인증서를 검색할 수 없는 경우 전자 메일 알림이 생성되고 O365 포털에서 경고를 표시합니다.

![O365 포털 알림](./media/active-directory-aadconnect-o365-certs/notification.png)

>[AZURE.IMPORTANT] 비즈니스 연속성을 위해 AD FS를 사용하는 경우 알려진 문제에 대한 인증 실패가 발생하지 않도록 서버에 다음 패치를 설치했는지 확인합니다. 이는 갱신 및 향후 갱신 기간에 대해 알려진 AD FS 프록시 서버 문제를 완화합니다.
>
>서버 2012 R2 - [Windows Server 2014년 5월 롤업](http://support.microsoft.com/kb/2955164)
>
>Server 2008 R2 및 2012 - [Windows Server 2008 또는 Windows 2012 R2 SP1에서 프록시를 통한 인증 실패](http://support.microsoft.com/kb/3094446)

## 인증서를 업데이트해야 하는지를 확인합니다. <a name="managecerts"></a>

### 1단계: AutoCertificateRollover 상태 검사

AD FS 서버에서 Microsoft Powershell을 엽니다. AutoCertRollover 값을 TRUE로 설정했는지 확인합니다.

	Get-Adfsproperties

![AutoCertificateRollover](./media/active-directory-aadconnect-o365-certs/autocertrollover.png)

[AZURE.NOTE] AD FS 2.0을 사용하는 경우 Add-Pssnapin Microsoft.Adfs.Powershell을 먼저 실행해야 합니다.

### 2단계: AD FS와 Azure AD가 동기화되었는지 확인

AD FS 서버에서 Azure AD Powershell 프롬프트를 열고 Azure AD에 연결합니다.

>[AZURE.NOTE] [여기](https://technet.microsoft.com/library/jj151815.aspx)에서 아직 설치되지 않은 경우 Azure AD Powershell을 다운로드할 수 있습니다.

	Connect-MsolService

AD FS에서 구성된 인증서 및 지정된 도메인에 대한 Azure AD 트러스트 속성을 확인합니다.

	Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/active-directory-aadconnect-o365-certs/certsync.png)

두 출력의 지문이 일치하는 경우 인증서가 Azure AD와 동기화되었는지 확인합니다.

### 3단계: 인증서가 만료되려고 하는지 확인

Get-MsolFederationProperty 또는 Get-AdfsCertificate 중 하나의 출력에서 "이후가 아님"에 대비되는 날짜를 확인합니다. 날짜가 30일 이내인 경우 조치를 취해야 합니다.

### 다음 단계

| AutoCertificateRollover | Azure AD와 동기화된 인증서 | 페더레이션 메타데이터는 공개적으로 액세스할 수 있습니다. | 유효성 검사 | 작업 |
|:-----------------------:|:-----------------------:|:-----------------------:|:-----------------------:|:-----------------------:|
| 예 | 예 | 예 | - | 작업 필요 없음. [자동으로 토큰 서명 인증서 갱신](#autorenew)을 참조하세요. |
| 예 | 아니요 | - | 15일 이내 | 즉시 갱신합니다. [수동으로 토큰 서명 인증서 갱신](#manualrenew)을 참조하세요. |
| 아니요 | - | - | 30일 이내 | 즉시 갱신합니다. [수동으로 토큰 서명 인증서 갱신](#manualrenew)을 참조하세요. |

[-] 중요하지 않습니다.

## 토큰 서명 인증서를 자동으로 갱신(권장됨) <a name="autorenew"></a>

엑스트라넷에서 페더레이션 메타데이터에 액세스를 사용할 수 있는 웹 응용 프로그램 프록시를 배포하고 AD FS 기본 구성을 사용하는 경우(즉 AutoCertificateRollover를 사용) **수동 단계를 수행할 필요가 없습니다.** 다음을 확인하여 인증서의 자동 업데이트가 발생할 수 있는지 확인합니다.

**#1 AD FS 속성 AutoCertificateRollover를 True로 설정해야 합니다.**

이는 기존 항목이 만료되기 전에 AD FS가 자동으로 새 토큰 서명 및 토큰 암호 해독 인증서를 생성함을 의미합니다.

**#2 AD FS 페더레이션 메타데이터는 공개적으로 액세스할 수 있습니다.**

공용 인터넷(회사 네트워크 외부)에 있는 컴퓨터에서 다음 URL로 이동하여 공개적으로 페더레이션 메타 데이터에 액세스할 수 있는지 확인합니다.


https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

여기서 `(your_FS_name) `은 fs.contoso.com과 같이 조직에서 사용하는 페더레이션 서비스 호스트 이름으로 대체됩니다. 두 설정을 모두 확인할 수 있는 경우 그 밖에 다른 작업을 수행할 필요는 없습니다.

예제: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml

## 수동으로 토큰 서명 인증서 갱신 <a name="manualrenew"></a>

토큰 서명 인증서를 수동으로 갱신하도록 선택할 수 있습니다. 토큰 서명 인증서를 수동으로 업데이트하려는 일반적인 시나리오는 다음과 같습니다.
* 토큰 서명 인증서는 자체 서명된 인증서가 아닙니다. 가장 일반적인 이유는 조직이 조직 인증 기관에서 등록한 AD FS 인증서를 관리하기 때문입니다. 
* 네트워크 보안은 페더레이션 메타데이터를 공개적으로 사용할 수 없습니다.

토큰 서명 인증서를 업데이트할 때마다 이러한 시나리오에서는 PowerShell 명령인 Update-MsolFederatedDomain을 사용하여 Office 365 도메인을 업데이트해야 합니다.

### 토큰 서명 인증서를 갱신하고 O365 페더레이션 트러스트를 업데이트하는 단계

**1단계: AD FS에 새 토큰 서명 인증서가 있는지 확인**

### 기본이 아닌 구성
**AutoCertificateRollover**가 **False**로 설정된 AD FS의 기본이 아닌 구성을 사용하는 경우 사용자 지정 인증서(자체 서명되지 않음)을 사용할 가능성이 높습니다. AD FS 토큰 서명 인증서를 갱신하는 방법에 대한 포괄적인 지침은 [AD FS 자체 서명된 인증서를 사용하지 않는 고객에 대한 지침](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert)을 참고하세요.

### 페더레이션 메타데이터를 공개적으로 사용할 수 없습니다.
반면에 **AutoCertificateRollover**가 **True**로 설정되었지만 페더레이션 메타데이터가 공개적으로 액세스할 수 없는 경우 먼저 AD FS에서 새 토큰 서명 인증서를 생성했는지를 확인합니다. 다음 단계를 수행하여 새 토큰 서명 인증서를 확인합니다.

1. 기본 AD FS 서버에 로그온했는지 확인합니다.
2. PowerShell 명령 창을 열고 다음 명령을 실행하여 AD FS에서 현재 서명 인증서를 확인합니다.

	PS C:\>Get-ADFSCertificate –CertificateType token-signing

	>[AZURE.NOTE] AD FS 2.0을 사용하는 경우 Add-Pssnapin Microsoft.Adfs.Powershell을 먼저 실행해야 합니다.

3. 나열된 모든 인증서에서 명령 출력을 살펴봅니다. AD FS에서 새 인증서를 생성했으면 두 인증서가 출력에 표시됩니다. 하나는 IsPrimary 값이 True이고 NotAfter 날짜는 5일 이내이며 다른 하나는 IsPrimary가 False이고 NotAfter는 향후 1년 정도입니다.

4. 인증서가 하나만 표시되는 경우 NotAfter 날짜가 5일 이내이면 다음 단계를 실행하여 새 인증서를 생성해야 합니다.

5. 새 인증서를 생성하려면 PowerShell 명령 프롬프트에서 다음 명령을 실행합니다:`PS C:\>Update-ADFSCertificate –CertificateType token-signing`.

6. PS C:\>Get-ADFSCertificate –CertificateType token-signing 명령을 다시실행하여 업데이트를 확인합니다.

이제 두 인증서 나열되어야 하며 둘 중 하나의 NotAfter 날짜가 향후 약 1년 정도이고 IsPrimary 값은 False입니다.

**2단계: O365 트러스트에 대한 새 토큰 서명 인증서 업데이트**

아래에서 지정한 단계를 따라 새 토큰 서명 인증서를 가진 O365를 트러스트에 사용하도록 업데이트합니다.

1.	Windows PowerShell용 Microsoft Azure Active Directory 모듈을 엽니다.
2.	$cred=Get-Credential을 실행합니다. 이 cmdlet에서 자격 증명을 물어보면 클라우드 서비스 관리자 계정 자격 증명을 입력합니다.
3.	Connect-MsolService –Credential $cred를 실행합니다. 이 cmdlet을 실행하면 클라우드 서비스에 연결됩니다. 도구를 통해 설치되는 추가 cmdlet을 실행하려면 먼저 클라우드 서비스에 연결되는 컨텍스트를 만들어야 합니다.
4.	AD FS 기본 페더레이션 서버가 아닌 컴퓨터에서 이러한 명령을 실행하는 경우 Set-MSOLAdfscontext -Computer <AD FS primary server>을 실행합니다. 여기서 <AD FS primary server>는 기본 AD FS 서버의 내부 FQDN 이름입니다. 이 cmdlet은 AD FS에 연결되는 컨텍스트를 만듭니다.
5.	Update-MSOLFederatedDomain –DomainName <domain>을 실행합니다. 이 cmdlet은 AD FS에서 클라우드 서비스로 설정을 업데이트하고 둘 사이의 트러스트 관계를 구성합니다.


>[AZURE.NOTE] contoso.com과 fabrikam.com 등의 여러 최상위 도메인을 지원해야 하는 경우에는 cmdlet과 함께 SupportMultipleDomain 스위치를 사용해야 합니다. 자세한 내용은 [여러 최상위 도메인에 대한 지원](active-directory-aadconnect-multiple-domains.md)을 참조하세요.

## AAD Connect를 사용하여 Azure AD 트러스트 복구 <a name="connectrenew"></a>

설치했다면 Azure AD Connect를 사용하여 AD FS 팜/Azure AD 트러스트를 구성한 다음 Azure AD Connect을 사용하여 모든 작업을 토큰 서명 인증서에 가져가야 하는지를 감지할 수 있습니다. 인증서를 갱신해야 하는 경우 몇 번만 클릭하면 필요한 작업에 Azure AD Connect를 사용할 수 있습니다.

자세한 내용은 [트러스트 복구](./active-directory-aadconnect-federation-management.md#repairing-the-trust)를 참고합니다.

<!---HONumber=AcomDC_0622_2016-->
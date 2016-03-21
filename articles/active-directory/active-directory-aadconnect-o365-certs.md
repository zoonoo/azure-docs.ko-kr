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
	ms.date="03/07/2016"
	ms.author="billmath"/>


# Office 365 및 Azure AD에 대한 페더레이션 인증서 갱신

이 문서는 Office 365에 대한 인증서를 갱신하도록 요청하는 전자 메일 또는 포털 알림 메시지를 받은 경우 문제를 해결하고 문제가 다시 발생하지 않도록 도움을 주기 위한 것입니다. 이 문서에서는 AD FS를 페더레이션 서버로 사용한다고 가정합니다.

>[AZURE.IMPORTANT] 프록시를 통한 인증은 다음 중 하나를 수행한 후 Windows Server 2012 또는 Windows Server 2008 R2에서 실패할 수 있습니다.
>
- 프록시가 AD FS에서 인증서 롤오버 후 해당 보안 토큰 갱신
- AD FS 인증서를 수동으로 대체
>
핫픽스로 이 문제를 해결할 수 있습니다. [Windows Server 2012 또는 Windows 2008 R2 SP1에서 프록시를 통한 인증 실패](http://support.microsoft.com/kb/3094446)를 참조하세요.

## 어떤 작업을 수행해야 하는지 확인합니다

AD FS 2.0 이상을 사용하는 경우는 인증서가 만료되기 전에 Office 365 및 Azure AD에서 자동으로 인증서를 업데이트합니다. 수동 단계를 수행하거나 예약된 작업으로 스크립트를 실행할 필요가 없습니다. 이를 위해서 다음 두 가지 기본 AD FS 구성 설정을 적용해야 합니다.

- AD FS 속성 AutoCertificateRollover를 True로 설정해야 만료되기 전에 AD FS가 자동으로 새 토큰 서명 및 토큰 암호 해독 인증서를 생성합니다.
	- 이 값이 False이면 사용자 지정 인증서 설정을 사용합니다. 포괄적인 지침을 보려면 [여기](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert)로 이동하세요.
- 페더레이션 메타 데이터는 공용 인터넷에 사용할 수 있어야 합니다.

	다음은 확인하는 방법입니다.

	- 기본 페더레이션 서버의 PowerShell 명령 창에서 다음 명령을 실행하여 AD FS 설치에서 자동 인증서 롤오버를 사용하는지 확인합니다.

	`PS C:\> Get-ADFSProperties`

(AD FS 2.0을 사용하는 경우는 Add-Pssnapin Microsoft.Adfs.Powershell을 먼저 실행해야 합니다.)

결과 출력에서 다음 설정을 확인합니다.
	
	AutoCertificateRollover :True

공용 인터넷(회사 네트워크 외부)에 있는 컴퓨터에서 다음 URL로 이동하여 공개적으로 페더레이션 메타 데이터에 액세스할 수 있는지 확인합니다.


https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

여기서 `(your_FS_name) `은 fs.contoso.com과 같이 조직에서 사용하는 페더레이션 서비스 호스트 이름으로 대체됩니다. 두 설정을 모두 확인할 수 있는 경우 그 밖에 다른 작업을 수행할 필요는 없습니다.

예제: https://fs.contospocom/federationmetadata/2007-06/federationmetadata.xml

## 인증서를 수동으로 업데이트하려는 경우
수동으로 AD FS 인증서를 업데이트할 때마다 PowerShell 명령 Update-MsolFederatedDomain을 사용하여 Office 365 도메인을 업데이트해야 합니다. 이 섹션의 Office 365 페더레이션 트러스트 속성을 수동으로 업데이트 아래 단계([여기](#if-your-metadata-is-not-publicly-accessible))를 참조하세요.

## AutoCertificateRollover 속성이 False로 설정된 경우

AutoCertificateRollover 속성이 False로 설정된 경우 기본이 아닌 AD FS 인증서 설정을 사용하는 중입니다. 가장 일반적인 이유는 조직이 조직 인증 기관에서 등록한 AD FS 인증서를 관리하기 때문입니다. 이 경우 조직 자체적으로 인증서를 갱신 및 업데이트해야 합니다. [여기](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert)의 지침을 사용하세요.

## 메타 데이터에 공개적으로 액세스할 수 없는 경우
AutocertificateRollover 설정이 True이지만 페더레이션 메타 데이터를 공개적으로 사용할 수 없는 경우 아래 절차에 따라 인증서가 온-프레미스와 클라우드에서 모두 업데이트되었는지 확인해야 합니다.

### AD FS 시스템에서 새 인증서를 생성했는지 확인합니다.

- 기본 AD FS 서버에 로그온했는지 확인합니다.
- PowerShell 명령 창을 열고 다음 명령을 실행하여 AD FS에서 현재 서명 인증서를 확인합니다.

`PS C:\>Get-ADFSCertificate –CertificateType token-signing.`

(AD FS 2.0을 사용하는 경우는 Add-Pssnapin Microsoft.Adfs.Powershell을 먼저 실행해야 합니다.)


- 나열된 모든 인증서에서 명령 출력을 살펴봅니다. AD FS에서 새 인증서를 생성했으면 두 인증서가 출력에 표시됩니다. 하나는 IsPrimary 값이 True이고 NotAfter 날짜는 5일 이내이며 다른 하나는 IsPrimary가 False이고 NotAfter는 향후 1년 정도입니다.

- 인증서가 하나만 표시되는 경우 NotAfter 날짜가 5일 이내이면 다음 단계를 실행하여 새 인증서를 생성해야 합니다.

- 새 인증서를 생성하려면 PowerShell 명령 프롬프트에서 다음 명령을 실행합니다:`PS C:\>Update-ADFSCertificate –CertificateType token-signing`.

- PS C:\>Get-ADFSCertificate –CertificateType token-signing 명령을 다시실행하여 업데이트를 확인합니다.
	- 이제 두 인증서 나열되어야 하며 둘 중 하나의 NotAfter 날짜가 향후 약 1년 정도이고 IsPrimary 값은 False입니다.

- 다음으로, Office 365 페더레이션 트러스트 속성을 수동으로 업데이트하려면 다음 단계를 수행합니다.




### 다음 단계에 따라 Office 365 페더레이션 트러스트 속성을 수동으로 업데이트합니다.

1.	Windows PowerShell용 Microsoft Azure Active Directory 모듈을 엽니다.
2.	$cred=Get-Credential을 실행합니다. 이 cmdlet에서 자격 증명을 물어보면 클라우드 서비스 관리자 계정 자격 증명을 입력합니다.
3.	Connect-MsolService –Credential $cred를 실행합니다. 이 cmdlet을 실행하면 클라우드 서비스에 연결됩니다. 도구를 통해 설치되는 추가 cmdlet을 실행하려면 먼저 클라우드 서비스에 연결되는 컨텍스트를 만들어야 합니다.
4.	AD FS 기본 페더레이션 서버가 아닌 컴퓨터에서 이러한 명령을 실행하는 경우 Set-MSOLAdfscontext -Computer <AD FS primary server>을 실행합니다. 여기서 <AD FS primary server>는 기본 AD FS 서버의 내부 FQDN 이름입니다. 이 cmdlet은 AD FS에 연결되는 컨텍스트를 만듭니다.
5.	Update-MSOLFederatedDomain –DomainName <domain>을 실행합니다. 이 cmdlet은 AD FS에서 클라우드 서비스로 설정을 업데이트하고 둘 사이의 트러스트 관계를 구성합니다.

>[AZURE.NOTE] contoso.com과 fabrikam.com 등의 여러 최상위 도메인을 지원해야 하는 경우에는 cmdlet과 함께 SupportMultipleDomain 스위치를 사용해야 합니다. 자세한 내용은 여러 최상위 도메인에 대한 지원을 참조하세요. 마지막으로, 모든 웹 응용 프로그램 프록시 서버가 [Windows Server May 2014](http://support.microsoft.com/kb/2955164) 롤업으로 업데이트되었는지 확인합니다. 그렇지 않으면 프록시가 새 인증서로 업데이트되지 못하여 중단될 수 있습니다.

<!---HONumber=AcomDC_0309_2016-->
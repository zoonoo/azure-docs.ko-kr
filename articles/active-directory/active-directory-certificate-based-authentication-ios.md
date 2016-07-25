<properties 
    pageTitle="iOS에서 인증서 기반 인증 시작 | Microsoft Azure" 
    description="iOS 장치에서 솔루션의 인증서 기반 인증을 구성하는 방법 알아보기" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="07/13/2016" 
    ms.author="markvi" />



# iOS에서 인증서 기반 인증 시작 - 공개 미리 보기

> [AZURE.SELECTOR]
- [iOS](active-directory-certificate-based-authentication-ios.md)
- [Android](active-directory-certificate-based-authentication-android.md)


이 항목에서는 iOS 장치에서 Office 365 Enterprise, Business 및 Education 계획의 테넌트 사용자를 위해 CBA(인증서 기반 인증)를 구성하고 활용하는 방법을 보여 줍니다.

CBA를 사용하면 Exchange Online 계정을 다음에 연결할 때 Android 또는 iOS 장치의 클라이언트 인증서를 사용하여 Azure Active Directory에서 인증을 받을 수 있습니다.

- Microsoft Outlook 및 Microsoft Word와 같은 Office 모바일 응용 프로그램
- EAS(Exchange ActiveSync) 클라이언트

이 기능을 구성하면 모바일 장치의 특정 메일 및 Microsoft Office 응용 프로그램에 사용자 이름 및 암호 조합을 입력해야 합니다.
 

## 지원되는 시나리오 및 요구 사항  



### 일반 요구 사항 


이 항목의 모든 시나리오에는 다음과 같은 작업이 필요합니다.

- 클라이언트 인증서를 발급하기 위해 인증 기관에 액세스합니다.

- Azure Active Directory에서 해당 인증 기관이 구성되어 있어야 합니다. 시작 섹션에서 구성을 완료하는 방법에 대한 자세한 단계를 찾을 수 있습니다.

- Azure Active Directory에는 루트 인증 기관 및 중간 인증 기관을 구성되어야 합니다.

- 각 인증 기관에는 인터넷 연결 URL을 통해 참조될 수 있는 CRL(인증서 해지 목록)이 있어야 합니다.

- 클라이언트 인증을 위해서는 클라이언트 인증서가 발급되어야 합니다.


- Exchange ActiveSync 클라이언트의 경우에만, 보안 주체 이름 또는 주체 대체 이름 필드의 RFC822 이름 값에 Exchange Online 사용자의 라우팅할 수 있는 전자 메일 주소가 있어야 합니다. Azure Active Directory는 디렉터리의 프록시 주소 특성에 RFC822 값을 매핑합니다.



### Office 모바일 응용 프로그램 지원 

| 앱 | 지원 |
| ---                       | ---          |
| OneDrive | 예 |
| Outlook | 서비스 예정 |
| Word / Excel / PowerPoint | 예 |
| 비즈니스용 Skype | 서비스 예정 |


### 요구 사항  

장치 OS 버전은 iOS 9 이어야 합니다.

Office 모바일 응용 프로그램에서 CBA를 수행하도록 페더레이션 서버를 구성해야 합니다.

Azure Authenticator는 iOS의 Office 응용 프로그램에 필요합니다.

Azure Active Directory에서 클라이언트 인증서를 해지하려면 ADFS 토큰에 다음 클레임이 있어야 합니다.

  - `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`(클라이언트 인증서의 일련 번호)

  - `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`(클라이언트 인증서 발급자에 대한 문자열)

Azure Active Directory는 이러한 클레임이 ADFS 토큰(또는 다른 SAML 토큰)에서 사용 가능한 경우 새로 고침 토큰에 이러한 클레임을 추가합니다. 새로 고침 토큰의 유효성을 검사해야 하는 경우 이 정보가 해지를 확인하는 데 사용됩니다.

ADFS 오류 페이지를 다음으로 업데이트하는 것이 가장 좋습니다.

- IOS에서 Azure Authenticator를 설치하기 위한 요구 사항

- 사용자 인증서를 얻는 방법에 대한 지침

자세한 내용은 [AD FS 로그인 페이지 사용자 지정](https://technet.microsoft.com/library/dn280950.aspx)을 참조하세요.



### Exchange ActiveSync 클라이언트 지원 


iOS 9 이상의 특정 Exchange ActiveSync 응용 프로그램은 지원됩니다. 전자 메일 응용 프로그램에서 이 기능을 지원하는지 확인하려면 응용 프로그램 개발자에게 문의하세요. 이 시나리오의 경우 클라이언트 인증서를 사용하는 페더레이션된 인증은 지원되지 않습니다.


## 시작 


시작하려면 Azure Active Directory에서 인증 기관을 구성해야 합니다. 각 인증 기관에 대해 다음을 업로드합니다.

- 인증서의 공개 부분(*.cer* 형식)

- CRL(인증서 해지 목록)이 있는 인터넷 연결 URL
 

다음은 인증 기관에 대한 스키마입니다.

    class TrustedCAsForPasswordlessAuth 
    { 
       CertificateAuthorityInformation[] certificateAuthorities;    
    } 

    class CertificateAuthorityInformation 

    { 
        CertAuthorityType authorityType; 
        X509Certificate trustedCertificate; 
        string crlDistributionPoint; 
        string deltaCrlDistributionPoint; 
        string trustedIssuer; 
        string trustedIssuerSKI; 
    }                

    enum CertAuthorityType 
    { 
        RootAuthority = 0, 
        IntermediateAuthority = 1 
    } 


이 정보를 업로드하려면 Windows PowerShell을 통해 Azure AD 모듈을 사용할 수 있습니다. 다음은 인증 기관을 추가, 제거 또는 수정하는 예제입니다.



### 인증서 기반 인증서에 맞게 Azure AD 테넌트 구성 

1. 관리자 권한으로 Windows PowerShell을 시작합니다.

2. Azure AD 모듈을 설치합니다. 버전 [1\.1.143.0](http://www.powershellgallery.com/packages/AzureADPreview/1.1.143.0) 이상을 설치해야 합니다.

        Install-Module -Name AzureAD –RequiredVersion 1.1.143.0 

3. 대상 테넌트에 연결합니다.

        Connect-AzureAD 

### 새 인증 기관 추가

1. 인증 기관의 다양한 속성을 설정하고 Azure Active Directory에 추가합니다.

        $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]" 
        $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation 
        $new_ca.AuthorityType=0 
        $new_ca.TrustedCertificate=$cert 
        New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca 

5. 인증 기관을 가져옵니다.

        Get-AzureADTrustedCertificateAuthority 


### 인증 기관 목록 검색

테넌트에 대한 Azure Active Directory에 현재 저장되어 있는 인증 기관을 검색합니다.

        Get-AzureADTrustedCertificateAuthority 


### 인증 기관 제거

1.	인증 기관을 검색합니다.

		$c=Get-AzureADTrustedCertificateAuthority 


2. 인증 기관의 인증서를 제거합니다.

		Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2] 


### 인증 기관 수정 

1.	인증 기관을 검색합니다.

		$c=Get-AzureADTrustedCertificateAuthority 


2. 인증 기관의 속성을 수정합니다.

		$c[0].AuthorityType=1 

3. **인증 기관**을 설정합니다.

		Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0] 




## Office 모바일 응용 프로그램 테스트  

모바일 Office 응용 프로그램에서 인증서 인증을 테스트하려면

1.	테스트 장치에서 앱 스토어의 Office 모바일 응용 프로그램(예: OneDrive)을 설치합니다.

2.	테스트 장치에 사용자 인증서가 프로비전되었는지 확인합니다.

3.	응용 프로그램을 시작합니다.

4.	사용자 이름을 입력하고 사용하려는 사용자 인증서를 선택합니다.

정상적으로 로그인되어야 합니다.





## Exchange ActiveSync 클라이언트 응용 프로그램 테스트

인증서 기반 인증을 통해 Exchange ActiveSync에 액세스하려면 클라이언트 인증서를 포함하는 EAS 프로필을 응용 프로그램에서 사용할 수 있어야 합니다. EAS 프로필은 다음 정보를 포함해야 합니다.

- 인증에 사용할 사용자 인증서

- EAS 끝점은 outlook.office365.com이어야 합니다(이 기능은 현재 Exchange Online 다중 테넌트 환경에서만 지원됨).

Intune과 같은 MDM을 활용하거나 장치의 EAS 프로필에 인증서를 수동으로 배치하여 장치에서 EAS 프로필을 구성하고 배치할 수 있습니다.

### iOS에서 EAS 클라이언트 응용 프로그램 테스트 

iOS 9 이상의 네이티브 메일 응용 프로그램으로 인증서 인증을 테스트하려면

1.	위의 요구 사항을 충족하는 EAS 프로필을 구성합니다.

2.	Intune과 같은 MDM 또는 Apple Configurator 응용 프로그램을 사용하여 iOS 장치에서 프로필을 설치합니다.

3.	프로필이 제대로 구성되면 네이티브 메일 응용 프로그램을 열고 메일이 동기화되는지 확인합니다.



## 해지

클라이언트 인증서를 해지할 수 있게 Azure Active Directory는 인증 기관 정보의 일부로 업로드된 URL에서 CRL(인증서 해지 목록)을 가져온 후 캐시합니다. CRL의 마지막 게시 타임스탬프(**개시 날짜** 속성)는 CRL이 여전히 유효한지 확인하는 데 사용됩니다. CRL은 목록의 일부인 인증서에 대한 액세스 권한을 해지하기 위해 주기적으로 참조됩니다.

추가 인스턴트 해지가 필요하면(예: 사용자가 장치를 분실한 경우) 사용자의 권한 부여 토큰이 무효화될 수 있습니다. 권한 부여 토큰을 무효화하려면 Windows PowerShell을 사용하여 이 특정 사용자에 대한 **StsRefreshTokenValidFrom** 필드를 설정합니다. 액세스 권한을 해지하려는 각 사용자에 대한 **StsRefreshTokenValidFrom** 필드를 업데이트해야 합니다.
 
해지가 지속되도록 하려면 **개시 날짜**를 **StsRefreshTokenValidFrom**으로 설정한 값 이후의 날짜로 설정하고 문제의 인증서가 CRL에 있는지 확인해야 합니다.
 
다음 단계에서는 **StsRefreshTokenValidFrom** 필드를 설정하여 권한 부여 토큰을 업데이트 및 무효화하기 위한 프로세스를 설명합니다.

1. 관리 자격 증명을 MSOL 서비스에 연결합니다.

		$msolcred = get-credential 
		connect-msolservice -credential $msolcred 

1.	사용자에 대한 현재 StsRefreshTokensValidFrom 값을 검색합니다.

		$user = Get-MsolUser -UserPrincipalName test@yourdomain.com` 
		$user.StsRefreshTokensValidFrom 


1.	사용자에 대한 새 StsRefreshTokensValidFrom 값을 현재 타임스탬프와 같게 구성합니다.

		Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")


설정하는 날짜는 이후 날짜여야 합니다. 날짜가 이후 날짜가 아닌 경우 **StsRefreshTokensValidFrom** 속성이 설정되지 않은 것입니다. 날짜가 이후 날짜인 경우 **StsRefreshTokensValidFrom**이 현재 시간(Set-MsolUser 명령으로 지정된 날짜 아님)으로 설정됩니다.

<!---HONumber=AcomDC_0713_2016-->
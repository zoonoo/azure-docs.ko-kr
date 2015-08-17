<properties 
	pageTitle="Azure AD Connect - 사용자 로그인" 
	description="사용자 지정 설정을 위한 Azure AD Connect 사용자 로그인." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>



# Azure AD Connect 사용자 로그온 옵션

Azure AD Connect를 사용하면 사용자가 동일한 암호를 사용하여 온-프레미스 및 클라우드 리소스 모두에 로그온 할 수 있습니다. 이 기능을 사용하여 여러 다른 방법 중에서 선택할 수 있습니다.


### 암호 동기화
암호 동기화를 수행하면 사용자 암호의 해시는 온-프레미스 Active Directory에서 Azure AD로까지 동기화됩니다. 암호를 변경하거나 온-프레미스에서 다시 설정하면, 새 암호는 Azure AD와 즉시 동기화되므로 온-프레미스에서처럼 사용자가 클라우드 리소스에 대해 항상 동일한 암호를 사용할 수 있습니다. 암호는 Azure AD로 전송되거나 일반 텍스트로 Azure AD에 저장되지 않습니다. 암호 동기화는 암호 쓰기 저장과 함께 사용하여 Azure AD에서 재설정한 자체 서비스 암호를 사용할 수 있습니다.

<center>![클라우드](./media/active-directory-aadconnect-user-signin/passwordhash.png)</center>

[암호 동기화에 대한 자세한 내용](https://msdn.microsoft.com/library/azure/dn246918.aspx)


### Windows Server 2012 R2 팜의 기존 또는 새 AD FS를 사용하여 페더레이션
페더레이션 로그온에서, 사용자는 자신의 온-프레미스 암호로 Azure AD 기반 서비스에 로그온 할 수 있으며 자신의 암호를 다시 입력하지 않고도 회사 네트워크에 로그온할 수 있습니다. AD FS와 페더레이션 옵션을 사용하여 새로 배포하거나 Windows Server 2012 R2 팜에서 기존 AD FS를 지정할 수 있습니다. 기존 팜에 지정하려는 경우 Azure AD Connect는 사용자가 로그인 할 수 있도록 팜 및 Azure AD 간의 트러스트를 구성합니다.

<center>![클라우드](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### Windows Server 2012 R2에서 AD FS와의 페더레이션을 배포하려면 다음이 필요합니다.
새 팜을 배포하는 경우

- 페더레이션 서버용 Windows Server 2012 R2 서버
- 웹 응용 프로그램 프록시용 Windows Server 2012 R2 서버
- 원하는 페더레이션 서비스 이름(예: fs.contoso.com)에 대한 하나의 SSL 인증서가 있는 .pfx 파일

새 팜을 배포 하거나 기존 팜을 사용하는 경우:

- 페더레이션 서버에 대한 로컬 관리자 자격 증명.
- 웹 응용 프로그램 프록시 역할을 배포하려는 임의의 작업 그룹(도메인에 가입되지 않음) 서버에 대한 로컬 관리자 자격 증명
- 마법사를 실행하는 컴퓨터는 Windows 원격 관리를 통해 AD FS나 웹 응용 프로그램 프록시를 설치하려는 다른 컴퓨터에 연결할 수 있어야 합니다.

#### 이전 버전의 AD FS 또는 타사 솔루션을 사용하여 로그온
이전 버전의 AD FS(예: AD FS 2.0) 또는 타사 페더레이션 공급자를 사용하여 클라우드 로그온을 이미 구성한 경우, Azure AD Connect를 통해 구성에서 사용자 로그인을 건너뛰도록 선택할 수 있습니다. 이렇게 하면 여전히 기존 솔루션을 사용하여 로그인하는 동안 최신 동기화 및 Azure AD 연결의 다른 기능을 얻을 수 있습니다.

### 사용자 조직에 대한 사용자 로그인 메서드 선택
Office 365, SaaS 응용 프로그램 및 기타 Azure AD 기반 리소스에 사용자가 로그인할 수 있도록 하려는 대부분의 조직의 경우, 기본 암호 동기화 옵션이 좋습니다. 그러나 일부 조직에서는 AD FS와 같은 페더레이션된 로그온 옵션을 사용하기 위한 특별한 이유가 있습니다. 내용은 다음과 같습니다.

- 사용자 조직에서 이미 AD FS 또는 타사 배포된 페더레이션 공급자를 배포했습니다
- 보안 정책은 클라우드에 대한 동기화 암호 해시를 금지합니다.
- 회사 네트워크에서 도메인에 가입된 컴퓨터에서 클라우드 리소스에 액세스 하는 경우, 사용자는 원활한 SSO(추가 암호 프롬프트가 없는) 경험이 필요합니다.
- AD FS에 있는 일부 특정 기능이 필요합니다.
	- 타사 공급자 또는 스마트 카드를 사용하는 온-프레미스 다단계 인증(Windows Server 2012 R2에서 AD FS에 대한 타사 MFA 공급자에 대해 알아보기)
	- 소프트 계정 잠금 또는 AD 암호 및 작업 시간 정책과 같은 Active Directory 통합 기능
	- 장치 등록, Azure AD 연결 또는 Intune MDM 정책을 사용하여 온-프레미스 및 클라우드 리소스 모두에 대한 조건부 액세스
 

<!---HONumber=August15_HO6-->
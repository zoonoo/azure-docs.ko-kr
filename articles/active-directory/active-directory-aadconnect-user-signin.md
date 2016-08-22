<properties
	pageTitle="Azure AD Connect: 사용자 로그인 | Microsoft Azure"
	description="사용자 지정 설정을 위한 Azure AD Connect 사용자 로그인."
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
	ms.date="08/08/2016"
	ms.author="billmath"/>



# Azure AD Connect 사용자 로그온 옵션

Azure AD Connect를 사용하면 사용자가 동일한 암호를 사용하여 온-프레미스 및 클라우드 리소스 모두에 로그온 할 수 있습니다. 이 기능을 사용하여 여러 다른 방법 중에서 선택할 수 있습니다.

## 사용자 로그인 방법 선택
Office 365, SaaS 응용 프로그램 및 기타 Azure AD 기반 리소스에 사용자가 로그인할 수 있도록 하려는 대부분의 조직의 경우, 기본 암호 동기화 옵션이 좋습니다. 그러나 일부 조직에서는 AD FS와 같은 페더레이션된 로그온 옵션을 사용하기 위한 특별한 이유가 있습니다. 내용은 다음과 같습니다.

- 사용자 조직에서 이미 AD FS 또는 타사 배포된 페더레이션 공급자를 배포했습니다
- 보안 정책은 클라우드에 대한 동기화 암호 해시를 금지합니다.
- 회사 네트워크에서 도메인에 가입된 컴퓨터에서 클라우드 리소스에 액세스 하는 경우, 사용자는 원활한 SSO(추가 암호 프롬프트가 없는) 경험이 필요합니다.
- AD FS에 있는 일부 특정 기능이 필요합니다.
	- 타사 공급자 또는 스마트 카드를 사용하는 온-프레미스 다단계 인증(Windows Server 2012 R2에서 AD FS에 대한 타사 MFA 공급자에 대해 알아보기)
	- 소프트 계정 잠금 또는 AD 암호 및 작업 시간 정책과 같은 Active Directory 통합 기능
	- 장치 등록, Azure AD 연결 또는 Intune MDM 정책을 사용하여 온-프레미스 및 클라우드 리소스 모두에 대한 조건부 액세스

### 암호 동기화
암호 동기화를 수행하면 사용자 암호의 해시는 온-프레미스 Active Directory에서 Azure AD로까지 동기화됩니다. 암호를 변경하거나 온-프레미스에서 다시 설정하면, 새 암호는 Azure AD와 즉시 동기화되므로 온-프레미스에서처럼 사용자가 클라우드 리소스에 대해 항상 동일한 암호를 사용할 수 있습니다. 암호는 Azure AD로 전송되거나 일반 텍스트로 Azure AD에 저장되지 않습니다. 암호 동기화는 암호 쓰기 저장과 함께 사용하여 Azure AD에서 재설정한 자체 서비스 암호를 사용할 수 있습니다.

<center>![클라우드](./media/active-directory-aadconnect-user-signin/passwordhash.png)</center>

[암호 동기화에 대한 자세한 내용](active-directory-aadconnectsync-implement-password-synchronization.md)


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

## 사용자 로그인 및 UPN(사용자 계정 이름)

### 사용자 계정 이름 이해

Active Directory에서 기본 UPN 접미사는 사용자 계정이 생성된 도메인의 DNS 이름입니다. 대부분의 경우에서 인터넷에서 엔터프라이즈 도메인으로 등록된 도메인 이름입니다. 그러나 Active Directory 도메인 및 트러스트를 사용하여 더 많은 UPN 접미사를 추가할 수 있습니다.

사용자의 UPN은 username@domai 형식입니다. 예를 들어, Active Directory의 경우 contoso.com 사용자 John은 UPN john@contoso.com를 가집니다. 사용자의 UPN은 RFC 822에 기반합니다. UPN 및 전자 메일이 동일한 형식을 공유하더라도 사용자의 UPN 값은 사용자의 전자 메일 주소와 같지 않을 수 있습니다.

### Azure AD의 사용자 계정 이름

Azure AD Connect 마법사는 userPrincipalName 특성을 사용하거나 온-프레미스에서 Azure AD의 사용자 계정 이름으로 사용할 수 있는 특성을 사용자 지정 설치로 지정할 수 있습니다. Azure AD에 로그인하는 데 사용할 값입니다. 사용자 계정 이름 특성의 값이 Azure AD에서 확인된 도메인에 해당하지 않으면 Azure AD는 이를 기본 .onmicrosoft.com 값으로 바꿉니다.

Azure Active Directory의 모든 디렉터리는 contoso.onmicrosoft.com 형식의 기본 제공 도메인 이름을 함께 제공되어 Azure 또는 다른 Microsoft 서비스를 사용할 수 있게 해줍니다. 사용자 지정 도메인을 사용하여 로그인 환경을 향상시키고 단순화할 수 있습니다. Azure AD에서 사용자 지정 도메인 이름 및 도메인을 확인하는 방법에 대한 내용은 [Azure Active Directory에 사용자 지정 도메인 이름 추가](active-directory-add-domain.md#add-your-custom-domain-name-to-azure-active-directory)를 참고하세요.

## Azure AD 로그인 구성

### Azure AD connect을 사용하여 Azure AD 로그인 구성
Azure AD 로그인 환경은 Azure AD를 Azure AD 디렉터리에서 확인된 사용자 지정 도메인 중 하나에 동기화된 사용자의 사용자 계정 이름 접미사와 일치시킬 수 있는지 여부에 따라 달라 집니다. 클라우드의 사용자 로그인 환경이 온-프레미스 환경과 비슷해 지도록 Azure AD 로그인 설정을 구성하는 동안 Azure AD Connect가 도움을 줍니다. Azure AD Connect는 도메인에 대해 정의된 UPN 접미사를 나열하고 Azure AD의 사용자 지정 도메인과 일치시키려 하며 취해야 하는 적절한 조치에 도움을 줍니다. Azure AD 로그인 페이지는 온-프레미스 Active Directory에 대해 정의된 UPN 접미사를 나열하고 각 접미사에 해당하는 상태를 표시합니다. 상태 값은 다음 중 하나가 될 수 있습니다.

* 확인됨: Azure AD Connect는 Azure AD에서 확인된 도메인을 찾을 수 있으며 아무 작업도 필요하지 않습니다.
* 확인되지 않음: Azure AD Connect는 Azure AD에서 사용자 지정 도메인을 찾을 수 있지만 확인되지 않습니다. 사용자는 동기화 후에 사용자의 UPN 접미사가 기본. onmicrosoft.com 접미사로 변경되지 않는지 확하기 위해 사용자 지정 도메인을 확인해야 합니다.
* 추가되지 않음: Azure AD Connect는 UPN 접미사에 해당하는 사용자 지정 도메인을 찾을 수 없습니다. 사용자는 동기화 후에 사용자의 UPN 접미사가 기본. onmicrosoft.com 접미사로 변경되지 않는지 확하기 위해 UPN 접미사에 해당하는 사용자 지정 도메인을 추가하고 확인해야 합니다.

Azure AD 로그인 페이지는 현재 확인 상태를 사용하여 Azure AD에서 온-프레미스 Active Directory와 해당 사용자 지정 도메인에 정의된 UPN 접미사를 나열합니다. 이제 사용자 지정 설치에서 **Azure AD 로그인** 페이지의 사용자 계정 이름에 대한 특성을 선택할 수 있습니다

![Azure AD 로그인 페이지](.\media\active-directory-aadconnect-user-signin\custom_azure_sign_in.png)

새로 고침 단추를 클릭하여 Azure AD에서 사용자 지정 도메인의 최신 상태를 다시 가져올 수 있습니다.

###Azure AD에서 사용자 계정 이름에 특성 선택

UserPrincipalName - userPrincipalName 특성은 Azure AD 및 Office 365에 로그인할 때 사용자가 사용하는 특성입니다. UPN-접미사로 알려진 사용된 도메인은 사용자가 동기화되기 전에 Azure AD에서 확인해야 합니다. 기본 특성 userPrincipalName을 유지하는 것이 좋습니다. 이 특성은 라우팅할 수 없고 확인할 수 없으며 전자 메일의 경우 로그인 ID를 보유하는 특성으로 다른 특성을 선택할 수 없습니다. 대체 ID로도 알려져 있습니다. 대체 ID 특성 값은 RFC822 표준을 따라야 합니다. 대체 ID는 로그인 솔루션으로 암호 Single Sign-On(SSO) 및 페더레이션 SSO 모두를 함께 사용할 수 있습니다.

>[AZURE.NOTE] 대체 ID를 사용하면 모든 Office 365 워크로드 부하와 호환되지 않습니다. 자세한 내용은 [대체 로그인 ID 구성](https://technet.microsoft.com/library/dn659436.aspx)을 참조하세요.

#### 여러 사용자 지정 도메인 상태 및 Azure 로그인 환경에 미치는 영향
Azure AD 디렉터리의 사용자 지정 도메인 상태와 UPN 접미사가 정의한 온-프레미스 간의 관계를 이해하는 데 매우 중요합니다. Azure AD Connnect를 사용하여 동기화를 설정하는 경우 다른 가능한 Azure 로그인 환경을 살펴보겠습니다.

다음 정보의 경우, 온-프레미스 디렉터리에서 UPN의 일환(예: user@contoso.com)으로 사용되는 UPN 접미사 contoso.com를 우려한다고 가정합니다.

###### Express 설정/암호 동기화
| 시스템 상태 | Azure 로그인 사용자 경험에 미치는 영향 |
|:-------------:|:----------------------------------------|
| 추가되지 않음 | 이 경우에 contoso.com에 대한 사용자 지정 도메인은 Azure AD 디렉터리에서 추가되지 않습니다. 접미사 @contoso.com이 포함된 UPN 온-프레미스를 가진 사용자는 해당 온-프레미스 UPN을 사용하여 Azure에 로그인할 수 없습니다. 대신 기본 Azure AD 디렉터리에 대한 접미사를 추가하여 Azure AD에서 제공한 새 UPN을 사용해야 합니다. 예를 들어 Azure AD 디렉터리 azurecontoso.onmicrosoft.com에 사용자를 동기화하는 경우 온-프레미스 사용자 user@contoso.com은 지정된 user@azurecontoso.onmicrosoft.com의 UPN입니다.|
| 확인되지 않음 | 이 경우에 Azure AD 디렉터리에 추가된 사용자 지정 도메인 contoso.com이 있지만 아직 확인되지 않습니다. 도메인을 확인하지 않고 사용자를 동기화하는 경우 사용자는 '추가되지 않음' 시나리오처럼 Azure AD에 의해 할당된 새 UPN입니다.|
| Verified | 이 경우에 UPN 접미사에 대한 Azure AD에서 추가되고 확인횐 사용자 지정 도메인 contoso.com이 있습니다. 사용자는 Azure AD와 동기화된 후에 해당 온-프레미스 사용자 계정 이름(예: user@contoso.com)을 사용하여 Azure에 로그인할 수 있습니다.|

###### AD FS 페더레이션
Azure AD의 기본 .onmicrosoft.com 도메인 또는 Azure AD의 확인되지 않은 사용자 지정 도메인을 사용하여 페더레이션을 만들 수 없습니다. Azure AD Connect 마법사를 실행하는 경우 확인되지 않은 도메인을 선택하여 페더레이션을 만들려면 Azure AD Connect에서는 도메인에 대해 DNS가 호스트되는 위치를 만들기 위해 필요한 레코드라는 메시지가 나타납니다. 자세한 내용은 [여기](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation)를 참조하세요.

"AD FS로 페더레이션"으로 사용자 로그인 옵션을 선택한 경우 Azure AD에서 페더레이션을 계속 만들려면 사용자 지정 도메인이 있어야 합니다. 설명하자면 즉, Azure AD 디렉터리에 추가된 사용자 지정 도메인 contoso.com이 있어야 합니다.

| 시스템 상태 | Azure 로그인 사용자 경험에 미치는 영향 |
|:-------------:|:----------------------------------------|
| 추가되지 않음 | 이 경우에 Azure AD Connect는 Azure AD 디렉터리에서 UPN 접미사 contoso.com에 일치하는 사용자 지정 도메인을 찾을 수 없습니다. 사용자가 user@contoso.com.과 같은 해당 온-프레미스 UPN으로 AD FS를 사용하여 로그인해야 하는 경우 사용자 지정 도메인 contoso.com을 추가해야 합니다.|
| 확인되지 않음 | 이 경우에 Azure AD Connect는 이후 단계에서 도메인을 확인하는 방법에 대한 적절한 정보를 메시지로 표시합니다.|
| Verified | 이 경우에 추가 작업 없이 구성을 진행할 수 있습니다.|  

## 사용자 로그인 방법 변경

마법사를 사용한 Azure AD Connect의 초기 구성 후에 Azure AD Connect에서 사용할 수 있는 작업을 사용하여 페더레이션에서 암호 동기화에 사용자 로그인 방법을 변경할 수 있습니다. Azure AD Connect 마법사를 다시 실행하면 수행할 수 있는 작업 목록이 나타납니다. 작업 목록에서 **사용자 로그인 변경**을 선택합니다

![사용자 로그인 변경"](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

다음 페이지에서 Azure AD에 대한 자격 증명을 제공하도록 요청합니다.

![Azure에 연결](./media/active-directory-aadconnect-user-signin/changeusersignin2.png)

**사용자 로그인** 페이지에서 **암호 동기화**를 선택합니다. 페더레이션된 디렉터리에서 관리되는 디렉터리로 디렉터리를 변경합니다.

![Azure에 연결](./media/active-directory-aadconnect-user-signin/changeusersignin3.png)

>[AZURE.NOTE] 임시 스위치를 암호 동기화로 만드는 경우 **사용자 계정 변환하지 않음**을 확인합니다. 옵션에서 확인하지 않은 경우 각 사용자가 페더레이션된 사용자로 변환되고 몇 시간이 걸릴 수 있습니다.
  
## 다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

[Azure AD Connect: 설계 개념](active-directory-aadconnect-design-concepts.md)에 대해 알아봅니다.

<!---HONumber=AcomDC_0810_2016-->
---
title: 온-프레미스 리소스에 암호가 없는 보안 키 로그인(미리 보기) - Azure Active Directory
description: Azure Active Directory(미리 보기)를 사용하여 온-프레미스 리소스에 암호 없는 보안 키 로그인을 활성화하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 181e8192170cd7394d6817edd655f4e8257b48a4
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654036"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory-preview"></a>Azure Active Directory(미리 보기)를 사용하여 온-프레미스 리소스에 암호 없는 보안 키 로그인 사용

이 문서에서는 **Azure AD가 조인되고** **하이브리드 Azure AD가** Windows 10 장치에 모두 가입된 환경에 대해 온-프레미스 리소스에 암호 없는 인증을 사용하도록 설정하는 데 중점을 둡니다. 이 기능은 Microsoft 호환 보안 키를 사용하여 온-프레미스 리소스에 원활한 단일 사인온(SSO)을 제공합니다.

|     |
| --- |
| FIDO2 보안 키는 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관을](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 참조하십시오.|
|     |

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>FIDO2 키를 사용하여 온-프레미스 리소스로 SSO

Azure Active Directory(AD)는 하나 이상의 Active Directory 도메인에 대해 Kerberos 티켓 승인 티켓(TGT)을 발행할 수 있습니다. 이 기능을 사용하면 사용자가 FIDO2 보안 키와 같은 최신 자격 증명으로 Windows에 로그인하고 기존 Active Directory 기반 리소스에 액세스할 수 있습니다. Kerberos 서비스 티켓 및 권한 부여는 온-프레미스 Active Directory 도메인 컨트롤러에 의해 계속 제어됩니다.

Azure AD Kerberos Server 개체는 온-프레미스 활성 디렉터리에서 생성된 다음 Azure Active Directory에 안전하게 게시됩니다. 개체가 실제 서버와 연결되지 않습니다. Azure Active Directory에서 활성 디렉터리 도메인에 대해 Kerberos TGT를 생성하는 데 사용할 수 있는 리소스일 뿐입니다.

![Azure AD 및 AD DS에서 TGT(티켓 부여 티켓) 받기](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. 사용자는 FIDO2 보안 키를 사용하여 Windows 10 장치에 로그인하고 Azure AD에 인증합니다.
1. Azure AD는 사용자의 온-프레미스 AD 도메인과 일치하는 Kerberos 서버 키에 대한 디렉터리를 확인합니다.
   1. Azure AD는 사용자의 온-프레미스 AD 도메인에 대해 Kerberos TGT를 생성합니다. TGT에는 사용자의 SID만 포함됩니다. 승인 데이터는 TGT에 포함되지 않습니다.
1. TGT는 AZURE AD 기본 새로 고침 토큰(PRT)과 함께 클라이언트에 반환됩니다.
1. 클라이언트 컴퓨터는 온-프레미스 AD 도메인 컨트롤러에 접촉하고 완전히 형성된 TGT에 대해 부분 TGT를 거래합니다.
1. 이제 클라이언트 컴퓨터에 Azure AD PRT 및 전체 Active Directory TGT가 있으며 클라우드 및 온-프레미스 리소스모두에 액세스할 수 있습니다.

## <a name="requirements"></a>요구 사항

이 문서의 단계를 완료하기 전에 조직에서는 Windows 10 장치에 암호 없는 보안 키 기호를 사용하도록 설정하는 단계를 완료해야 [합니다(미리 보기).](howto-authentication-passwordless-security-key.md)

또한 조직은 다음 소프트웨어 요구 사항을 충족해야 합니다.

- 장치는 Windows 10 내부자 빌드 18945 또는 최신 을 실행 해야 합니다.
- [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect)의 버전 1.4.32.0 이상이어야 합니다.
  - 사용 가능한 Azure AD 하이브리드 인증 옵션에 대한 자세한 내용은 [Azure Active Directory 하이브리드 ID 솔루션에 적합한 인증 방법 선택](../../security/fundamentals/choose-ad-authn.md) 및 Azure [AD Connect에 사용할 설치 유형 선택을](../hybrid/how-to-connect-install-select-installation.md)참조하십시오.
- Windows Server 도메인 컨트롤러에 다음 패치가 설치되어 있어야 합니다.
    - Windows 서버 2016 -https://support.microsoft.com/help/4534307/windows-10-update-kb4534307
    - Windows 서버 2019 -https://support.microsoft.com/help/4534321/windows-10-update-kb4534321

### <a name="supported-scenarios"></a>지원되는 시나리오

이 시나리오는 다음 두 시나리오 모두에서 단일 사인온(SSO)을 지원합니다.

- Office 365 및 기타 SAML 지원 응용 프로그램과 같은 클라우드 리소스의 경우
- 온-프레미스 리소스 및 웹 사이트에 대한 Windows 통합 인증의 경우 리소스에는 IIS 인증이 필요한 웹 사이트 및 SharePoint 사이트 및 NTLM 인증을 사용하는 리소스가 포함될 수 있습니다.

### <a name="unsupported-scenarios"></a>지원되지 않는 시나리오

다음 시나리오는 지원되지 않습니다.

- Windows 서버 활성 디렉터리 도메인 서비스(AD DS) 도메인 조인(온-프레미스 전용 장치) 배포.
- 보안 키를 사용하여 RDP, VDI 및 Citrix 시나리오.
- 보안 키를 사용하는 S/MIME.
- 보안 키를 사용하여 "로 실행"합니다.
- 보안 키를 사용하여 서버에 로그인합니다.

## <a name="create-kerberos-server-object"></a>Kerberos 서버 개체 만들기

관리자는 Azure AD Connect 서버의 PowerShell 도구를 사용하여 온-프레미스 디렉터리에서 Azure AD Kerberos Server 개체를 만듭니다. Azure AD 사용자를 포함하는 조직의 각 도메인 및 포리스트에서 다음 단계를 실행합니다.

1. Azure AD Connect의 최신 버전으로 업그레이드합니다. 이 지침은 하이브리드 환경을 지원하기 위해 Azure AD Connect를 이미 구성했다고 가정합니다.
1. Azure AD Connect 서버에서 상승된 PowerShell 프롬프트를 열고`C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. 다음 PowerShell 명령을 실행하여 온-프레미스 Active Directory 도메인과 Azure Active Directory 테넌트 모두에서 새 Azure AD Kerberos 서버 개체를 만듭니다.

> [!NOTE]
> 다음 `contoso.corp.com` 예제에서 온-프레미스 Active Directory 도메인 이름으로 바꿉니다.

```powerShell
Import-Module ".\AzureAdKerberos.psd1"

# Specify the on-premises Active Directory domain. A new Azure AD
# Kerberos Server object will be created in this Active Directory domain.
$domain = "contoso.corp.com"

# Enter an Azure Active Directory global administrator username and password.
$cloudCred = Get-Credential

# Enter a domain administrator username and password.
$domainCred = Get-Credential

# Create the new Azure AD Kerberos Server object in Active Directory
# and then publish it to Azure Active Directory.
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>Azure AD Kerberos 서버 보기 및 확인

다음 명령을 사용하여 새로 만든 Azure AD Kerberos 서버를 보고 확인할 수 있습니다.

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

이 명령은 Azure AD Kerberos 서버의 속성을 출력합니다. 속성을 검토하여 모든 것이 올바른지 확인할 수 있습니다.

| 속성 | 설명 |
| --- | --- |
| ID | AD DS DC 개체의 고유 ID입니다. 이 ID를 "슬롯" 또는 "분기 ID"라고도 합니다. |
| DomainDnsName | 활성 디렉터리 도메인의 DNS 도메인 이름입니다. |
| 컴퓨터 계정 | Azure AD Kerberos Server 개체(DC)의 컴퓨터 계정 개체입니다. |
| UserAccount | Azure AD Kerberos 서버 TGT 암호화 키를 보유하는 비활성화된 사용자 계정 개체입니다. 이 계정의 DN은`CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` |
| 키 버전 | Azure AD Kerberos 서버 TGT 암호화 키의 키 버전입니다. 키는 만들 때 버전이 할당됩니다. 그런 다음 키가 회전할 때마다 버전이 증가합니다. 증분은 복제 메타 데이터를 기반으로 하며 둘 보다 클 가능성이 높습니다. 예를 들어 초기 *키버전은* *192272일*수 있습니다. 키가 처음 회전할 때 버전은 *212621로*진행될 수 있습니다. 확인 해야 할 중요 한 것은 온-프레미스 개체에 대 한 *키 버전* 및 클라우드 개체에 대 한 *CloudKeyVersion* 동일 하다. |
| 키 업데이트 온 | Azure AD Kerberos 서버 TGT 암호화 키가 업데이트되거나 생성된 날짜 및 시간입니다. |
| 키업데이트부터 | Azure AD Kerberos 서버 TGT 암호화 키가 마지막으로 업데이트된 DC입니다. |
| 클라우드 ID | Azure AD 개체의 ID입니다. 위의 ID와 일치해야 합니다. |
| 클라우드도메인DnsName | Azure AD 개체의 *DomainDnsName입니다.* 위의 *DomainDnsName과* 일치해야 합니다. |
| 클라우드키버전 | Azure AD 개체의 *키버전입니다.* 위의 *키버전과* 일치해야 합니다. |
| 클라우드키업데이트온 | Azure AD 개체의 *키업데이트온입니다.* *키업데이트와* 일치해야 합니다. |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>Azure AD 커베로스 서버 키 회전

Azure AD Kerberos 서버 암호화 krbtgt 키는 정기적으로 회전해야 합니다. 다른 모든 Active Directory 도메인 컨트롤러 krbtgt 키를 회전 하는 데 사용 하는 동일한 일정을 따르는 것이 좋습니다.

> [!WARNING]
> krbtgt 키를 회전 할 수있는 다른 도구가 있습니다, 그러나, 당신은 당신의 Azure AD Kerberos 서버의 krbtgt 키를 회전하려면이 문서에서 언급 한 도구를 사용해야합니다. 이렇게 하면 온-프레미스 AD 및 Azure AD 모두에서 키가 업데이트됩니다.

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>Azure AD 커베로스 서버 제거

시나리오를 되돌리고 온-프레미스 활성 디렉터리 및 Azure Active Directory모두에서 Azure AD Kerberos 서버를 제거하려면 다음 명령을 실행합니다.

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>다중 포리스트 및 다중 도메인 시나리오

Azure AD Kerberos 서버 개체는 Azure AD에서 *KerberosDomain* 개체로 표시됩니다. 각 온-프레미스 활성 디렉터리 도메인은 Azure AD의 단일 *KerberosDomain* 개체로 표시됩니다.

예를 들어 조직에두 개의 도메인이 있는 Active `contoso.com` Directory 포리스트와 `fabrikam.com`. Azure AD가 전체 포리스트에 대해 Kerberos TT를 발행하도록 허용하도록 선택한 경우 Azure AD에는 두 개의 *KerberosDomain* 개체가 있습니다. 에 대한 `contoso.com` *KerberosDomain* 개체 `fabrikam.com`하나와 에 대한 개체 하나 Active Directory 포리스트가 여러 개 있는 경우 각 포리스트의 각 도메인에 대해 *KerberosDomain* 개체가 하나 있습니다.

Azure AD 사용자를 포함하는 조직의 각 도메인 및 포리스트에서 [Kerberos 서버 개체 만들기](#create-kerberos-server-object) 단계를 실행해야 합니다.

## <a name="known-behavior"></a>알려진 동작

비밀번호가 만료되면 FIDO로 로그인이 차단됩니다. 사용자가 FIDO를 사용하여 로그인하기 전에 암호를 재설정해야 합니다.

## <a name="troubleshooting-and-feedback"></a>문제 해결 및 피드백

이 기능을 미리 볼 때 피드백을 공유하거나 문제가 발생하려면 다음 단계를 사용하여 Windows 피드백 허브 앱을 통해 공유하십시오.

1. **피드백 허브를** 시작하고 로그인했는지 확인합니다.
1. 다음 분류에 따라 피드백을 제출합니다.
   - 카테고리: 보안 및 개인 정보 보호
   - 하위 범주: FIDO
1. 로그를 캡처하려면 **내 문제를 다시 만드는** 옵션을 사용합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="does-this-work-in-my-on-premises-environment"></a>내 온-프레미스 환경에서 이 작업이 작동합니까?

이 기능은 순수 온-프레미스 Active Directory 도메인 서비스(AD DS) 환경에서는 작동하지 않습니다.

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>조직에서 리소스에 액세스하려면 두 가지 요소 인증이 필요합니다. 이 요구 사항을 지원하려면 어떻게 해야 합니까?

보안 키는 다양한 폼 팩터로 제공됩니다. 관심 있는 장치 제조업체에 문의하여 두 번째 요소로 PIN 또는 생체 인식을 사용하여 장치를 활성화하는 방법에 대해 설명합니다.

### <a name="can-admins-set-up-security-keys"></a>관리자가 보안 키를 설정할 수 있습니까?

이 기능의 일반 가용성(GA)을 위해 이 기능을 연구하고 있습니다.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>규정을 준수하는 보안 키는 어디에서 찾을 수 있습니까?

[FIDO2 보안 키](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>보안 키를 분실한 경우 어떻게 해야 하나요?

**보안 정보** 페이지로 이동하여 보안 키를 제거하여 Azure 포털에서 키를 제거할 수 있습니다.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>하이브리드 Azure AD 조인 컴퓨터를 만든 후 즉시 FIDO를 사용할 수 없습니다.

하이브리드 Azure AD를 새로 설치하는 경우 도메인 조인 및 다시 시작 프로세스 후 암호로 로그인하고 FIDO를 사용하여 로그인할 수 있도록 정책이 동기화될 때까지 기다려야 합니다.

- 명령 창에 입력하여 `dsregcmd /status` 현재 상태를 확인하고 *AzureAdJoined* 및 *DomainJoined가* *모두 YES를*표시하고 있는지 확인합니다.
- 이 지연은 도메인 에 가입된 장치에 대한 알려진 제한 사항이며 FIDO에 특정되지 않습니다.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>FIDO로 로그인한 후 NTLM 네트워크 리소스에 SSO를 받고 자격 증명 프롬프트를 받을 수 없습니다.

리소스 요청을 서비스하는 데 적시에 응답할 수 있도록 충분한 도메인 컨트롤러가 패치되어 있는지 확인합니다. 기능을 실행 중인 도메인 컨트롤러를 볼 수 있는지 확인하려면 `nltest /dsgetdc:contoso /keylist /kdc`의 출력을 검토합니다.

## <a name="next-steps"></a>다음 단계

[암호 없는 것에 대해 자세히 알아보기](concept-authentication-passwordless.md)

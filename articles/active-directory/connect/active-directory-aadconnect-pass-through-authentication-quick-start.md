---
title: "Azure AD 통과 인증 - 빠른 시작 | Microsoft Docs"
description: "이 문서에서는 Azure AD(Azure Active Directory) 통과 인증을 시작하는 방법을 설명합니다."
services: active-directory
keywords: "Azure AD Connect 통과 인증, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: b0327b37b56987d52557f3f4e305f27eb2e5296d
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Azure Active Directory 통과 인증: 빠른 시작

## <a name="how-to-deploy-azure-ad-pass-through-authentication"></a>Azure AD 통과 인증을 배포하는 방법

Azure AD(Azure Active Directory) 통과 인증을 사용하면 사용자가 온-프레미스와 클라우드 기반 응용 프로그램 둘 다에서 동일한 암호로 로그인할 수 있습니다. 온-프레미스 Active Directory와 직접 비교해서 암호의 유효성을 검사하여 사용자를 로그인합니다.

>[!IMPORTANT]
>미리 보기를 통해 이 기능을 사용한 경우 [여기](./active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md) 제공된 지침에 따라 인증 에이전트의 미리 보기 버전을 업그레이드했는지 확인해야 합니다.

통과 인증을 배포하려면 다음 지침을 따라야 합니다.

## <a name="step-1-check-prerequisites"></a>1단계: 필수 조건 확인

다음 필수 조건이 충족되는지 확인합니다.

### <a name="on-the-azure-active-directory-admin-center"></a>Azure Active Directory 관리 센터에서

1. Azure AD 테넌트에서 클라우드 전용 전역 관리자 계정을 만듭니다. 이러한 방식으로 온-프레미스 서비스가 실패하거나 사용할 수 없게 될 때 테넌트의 구성을 관리할 수 있습니다. [클라우드 전용 전역 관리자 계정 추가](../active-directory-users-create-azure-portal.md)에 대해 자세히 알아봅니다. 테넌트에서 잠기지 않도록 하려면 이 단계를 수행하는 것이 중요합니다.
2. Azure AD 테넌트에 [사용자 지정 도메인 이름](../active-directory-add-domain.md)을 하나 이상 추가합니다. 사용자는 이러한 도메인 이름 중 하나를 사용하여 로그인합니다.

### <a name="in-your-on-premises-environment"></a>온-프레미스 환경에서

1. Azure AD Connect를 실행할, Windows Server 2012 R2 이상이 실행되는 서버를 식별합니다. 암호의 유효성이 검사되어야 하는 사용자와 동일한 AD 포리스트에 서버를 추가합니다.
2. 이전 단계에서 식별한 서버에 [최신 버전의 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)를 설치합니다. Azure AD Connect가 이미 실행되고 있는 경우 버전이 1.1.557.0 이상인지 확인합니다.
3. 독립 실행형 인증 에이전트를 실행할, Windows Server 2012 R2 이상이 실행되는 추가 서버를 식별합니다. 인증 에이전트 버전이 1.5.193.0 이상이어야 합니다. 로그인 요청의 고가용성을 보장하려면 이 서버가 필요합니다. 암호의 유효성이 검사되어야 하는 사용자와 동일한 AD 포리스트에 서버를 추가합니다.
4. 서버와 Azure AD 사이에 방화벽이 있는 경우 다음 항목을 구성해야 합니다.
   - 인증 에이전트가 다음 포트를 통해 Azure AD에 대한 **아웃바운드** 요청을 만들 수 있는지 확인합니다.
   
   | 포트 번호 | 사용 방법 |
   | --- | --- |
   | **80** | SSL 인증서의 유효성을 검사하는 동안 CRL(인증서 해지 목록) 다운로드 |
   | **443** | 서비스와의 모든 아웃바운드 통신 |
   
   방화벽이 원래 사용자에 따라 규칙에 적용되는 경우 네트워크 서비스로 실행하는 Windows 서비스의 트래픽에 대해 이러한 포트를 엽니다.
   - 방화벽이나 프록시에서 DNS 허용 목록을 허용하면 **\*.msappproxy.net** 및 **\*.servicebus.windows.net**에 대한 연결을 허용 목록에 추가합니다. 그렇지 않으면 매주 업데이트되는 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)에 액세스하도록 허용합니다.
   - 인증 에이전트는 초기 등록에 대한 **login.windows.net** 및 **login.microsoftonline.com**에 대한 액세스가 필요하므로 해당 URL에 대한 방화벽도 엽니다.
   - 인증서 유효성 검사를 위해 다음 URL을 차단 해제합니다. **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80** 및 **www.microsoft.com:80** 이러한 URL은 다른 Microsoft 제품과의 인증서 유효성 검사에 사용되므로 이러한 URL을 이미 차단 해제했을 수 있습니다.

## <a name="step-2-enable-exchange-activesync-support-optional"></a>2단계: Exchange ActiveSync 지원 활성화(선택 사항)

Exchange ActiveSync 지원을 활성화하려면 다음 지침을 따릅니다.

1. [Exchange PowerShell](https://technet.microsoft.com/library/mt587043(v=exchg.150).aspx)을 사용하여 다음 명령을 실행합니다.
```
Get-OrganizationConfig | fl per*
```

2. `PerTenantSwitchToESTSEnabled` 설정 값을 확인합니다. 값이 **true**인 경우 테넌트가 올바르게 구성되었습니다. 이는 일반적으로 대부분의 고객의 경우에 해당합니다. 값이 **false**인 경우 다음 명령을 실행합니다.
```
Set-OrganizationConfig -PerTenantSwitchToESTSEnabled:$true
```

3. `PerTenantSwitchToESTSEnabled` 설정 값이 이제 **true**로 설정되었는지 확인합니다. 다음 단계로 이동하기 전에 한 시간을 기다립니다.

이 단계 동안 문제가 발생하면 자세한 정보는 [문제 해결 가이드](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#exchange-activesync-configuration-issues)를 확인하세요.

## <a name="step-3-enable-the-feature"></a>3단계: 기능 활성화

[Azure AD Connect](active-directory-aadconnect.md)를 통해 통과 인증을 사용하도록 설정할 수 있습니다.

>[!IMPORTANT]
>Azure AD Connect 주 서버나 준비 서버에서 통과 인증을 사용하도록 설정할 수 있습니다. 주 서버에서 사용하도록 설정하는 것이 좋습니다.

Azure AD Connect를 처음 설치하는 경우 [사용자 지정 설치 경로](active-directory-aadconnect-get-started-custom.md)를 선택합니다. **사용자 로그인** 페이지에서 **통과 인증**을 로그온 방법으로 선택합니다. 성공적으로 완료되면 통과 인증 에이전트가 Azure AD Connect와 동일한 서버에 설치됩니다. 또한 테넌트에서 통과 인증 기능이 사용됩니다.

![Azure AD Connect - 사용자 로그인](./media/active-directory-aadconnect-sso/sso3.png)

Azure AD Connect를 이미 설치한 경우([빠른 설치](active-directory-aadconnect-get-started-express.md) 또는 [사용자 지정 설치](active-directory-aadconnect-get-started-custom.md) 경로 사용), Azure AD Connect에서 **사용자 로그인 변경 페이지**를 선택하고 **다음**을 클릭합니다. 그런 다음 **통과 인증**을 로그온 방법으로 선택합니다. 성공적으로 완료되면 통과 인증 에이전트가 Azure AD Connect와 동일한 서버에 설치되고 테넌트에서 기능이 사용됩니다.

![Azure AD Connect - 사용자 로그인 변경](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>통과 인증은 테넌트 수준 기능입니다. 기능을 켜면 테넌트의 _모든_ 관리되는 도메인에서 사용자의 로그인에 영향을 줍니다. AD FS에서 통과 인증으로 전환하는 경우 AD FS 인프라를 종료하기 전에 12시간 이상 기다리는 것이 좋습니다. 이 대기 시간은 사용자가 전환하는 동안 Exchange ActiveSync에 대한 로그인을 유지할 수 있는지 확인하는 것입니다.

## <a name="step-4-test-the-feature"></a>4단계: 기능 테스트

다음 지침에 따라 통과 인증을 올바르게 설정했는지 확인합니다.

1. 테넌트에 대한 전역 관리자 자격 증명을 사용하여 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에 로그인합니다.
2. 왼쪽 탐색에서 **Azure Active Directory**를 선택합니다.
3. **Azure AD Connect**를 선택합니다.
4. **통과 인증** 기능이 **사용**으로 표시되는지 확인합니다.
5. **통과 인증**을 선택합니다. 이 블레이드는 인증 에이전트가 설치된 서버를 나열합니다.

![Azure Active Directory 관리 센터 - Azure AD Connect 블레이드](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Azure Active Directory 관리 센터 - 통과 인증 블레이드](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

이 단계에서는, 테넌트에 있는 모든 관리되는 도메인의 사용자가 통과 인증을 사용하여 로그인할 수 있습니다. 하지만 페더레이션된 도메인의 사용자는 AD FS(Active Directory Federation Services) 또는 이전에 구성한 기타 페더레이션 공급자를 사용하여 계속 로그인합니다. 도메인을 페더레이션된 도메인에서 관리되는 도메인으로 전환하면 해당 도메인의 모든 사용자가 자동으로 통과 인증을 사용하여 로그인하기 시작합니다. 클라우드 전용 사용자는 통과 인증 기능의 영향을 받지 않습니다.

## <a name="step-5-ensure-high-availability"></a>5단계: 고가용성 보장

프로덕션 환경에 통과 인증을 배포하려는 경우 독립 실행형 인증 에이전트를 설치해야 합니다. 이 두 번째 인증 에이전트는 Azure AD Connect 및 첫 번째 인증 에이전트를 실행하는 서버가 아닌 _다른_ 서버에 설치합니다. 이렇게 설치하면 로그인 요청의 고가용성이 제공됩니다. 다음 지침에 따라 독립 실행형 인증 에이전트를 배포합니다.

1. **인증 에이전트의 최신 버전(버전 1.5.193.0 이상) 다운로드**: 테넌트의 전역 관리자 자격 증명을 사용하여 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에 로그인합니다.
2. 왼쪽 탐색에서 **Azure Active Directory**를 선택합니다.
3. **Azure AD Connect**를 선택하고 **통과 인증**을 선택합니다. **에이전트 다운로드**를 선택합니다.
4. **약관 동의 및 다운로드** 단추를 클릭합니다.
5. **인증 에이전트의 최신 버전 설치**: 이전 단계에서 다운로드한 실행 파일을 실행합니다. 메시지가 표시되면 테넌트의 전역 관리자 자격 증명을 입력합니다.

![Azure Active Directory 관리 센터 - 인증 에이전트 다운로드 버튼](./media/active-directory-aadconnect-pass-through-authentication/pta9.png)

![Azure Active Directory 관리 센터 - 에이전트 다운로드 블레이드](./media/active-directory-aadconnect-pass-through-authentication/pta10.png)

>[!NOTE]
>[여기](https://aka.ms/getauthagent)에서 인증 에이전트를 다운로드할 수도 있습니다. 설치하기 _전에_ 인증 에이전트의 [서비스 약관](https://aka.ms/authagenteula)을 검토하고 동의해야 합니다.

## <a name="next-steps"></a>다음 단계
- [**현재 제한 사항** ](active-directory-aadconnect-pass-through-authentication-current-limitations.md) - 지원되는 시나리오와 지원되지 않는 시나리오를 알아봅니다.
- [**기술 심층 분석**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) - 이 기능의 작동 방식을 이해합니다.
- [**FAQ(질문과 대답)**](active-directory-aadconnect-pass-through-authentication-faq.md) - 질문과 대답을 다루고 있습니다.
- [**문제 해결**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) - 기능과 관련된 일반적인 문제를 해결하는 방법에 대해 알아봅니다.
- [**Azure AD 원활한 SSO**](active-directory-aadconnect-sso.md) - 이 보완 기능에 대해 자세히 알아봅니다.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 새로운 기능 요청을 제출합니다.


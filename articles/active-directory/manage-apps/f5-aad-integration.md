---
title: F5 키를 사용 하 여 Azure AD 보안 하이브리드 액세스 Microsoft Docs
description: 보안 하이브리드 액세스를 위한 F5 빅 IP 액세스 정책 관리자 및 Azure Active Directory 통합
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 11/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38b484bd67f3354132675f343dcc06bd7f9d48a0
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499817"
---
# <a name="f5-big-ip-access-policy-manager-and-azure-active-directory-integration-for-secure-hybrid-access"></a>보안 하이브리드 액세스를 위한 F5 빅 IP 액세스 정책 관리자 및 Azure Active Directory 통합

이동성 및 진화 하는 위협 환경의 확산은 리소스 액세스와 거 버 넌 스에 대 한 추가 조사를 하 여 모든 현대화 프로그램의 위치를 [0](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/) 으로 설정 하는 것입니다.
Microsoft 및 F5에서이 디지털 변환은 일반적으로 현대화 될 때까지 중요 한 리소스가 노출 되는 모든 비즈니스에 대 한 다단계의 여행입니다. F5 genesis 및 Azure Active Directory의 SHA (Secure Hybrid Access)는 온-프레미스 응용 프로그램에 대 한 원격 액세스를 개선 하는 것 뿐만 아니라 이러한 취약 한 서비스의 전반적인 보안 상태를 강화 하는 것이 좋습니다.

컨텍스트의 경우 온-프레미스 응용 프로그램의 60-80%는 본질적으로 레거시 또는 Azure Active Directory (AD)와 직접 통합 될 수 없는 것으로 예상 됩니다. 또한 동일한 연구를 통해 SAP, Oracle, SAGE 및 중요 한 서비스를 제공 하는 기타 잘 알려진 작업의 하위 버전에서 이러한 시스템의 상당 부분을 실행 하 고 있음을 알 수 있습니다.

SHA는 조직이 더 뛰어난 네트워크 및 응용 프로그램 배달에 대 한 F5 투자를 계속 사용할 수 있도록 하 여이 시각 효과를 해결 합니다. Azure AD와 함께 사용 하면 다른 유형의 응용 프로그램 환경을 최신 Id 제어 평면에 연결할 수 있습니다.

Azure AD에서 대규모 IP 게시 된 서비스에 대 한 사전 인증 액세스를 사용 하면 다음과 같은 여러 이점이 있습니다.

- [Windows Hello](/windows/security/identity-protection/hello-for-business/hello-overview), [MS Authenticator](../user-help/user-help-auth-app-download-install.md), [FIDO (Fast Identity Online) 키](../authentication/howto-authentication-passwordless-security-key.md)및 [인증서 기반 인증](../authentication/active-directory-certificate-based-authentication-get-started.md) 을 통한 암호 없는 인증

- 선점형 [조건부 액세스](../conditional-access/overview.md) 및 [MFA (multi-factor authentication)](../authentication/concept-mfa-howitworks.md)

- [Id 보호](../identity-protection/overview-identity-protection.md) -사용자 및 세션 위험 프로 파일링을 통한 적응 제어


- [누출 자격 증명 검색](../identity-protection/concept-identity-protection-risks.md)

- [SSPR (셀프 서비스 암호 재설정)](../authentication/tutorial-enable-sspr.md)

- [파트너 공동 작업](../governance/entitlement-management-external-users.md) -관리 되는 게스트 액세스를 위한 자격 관리

- [Cloud App Security (CASB)](/cloud-app-security/what-is-cloud-app-security) -전체 앱 검색 및 제어

- 위협 모니터링-advanced threat analytics 용 [Azure 센티널](https://azure.microsoft.com/services/azure-sentinel/)

- [AZURE AD 포털](https://azure.microsoft.com/features/azure-portal/) -id 및 액세스를 관리 하기 위한 단일 제어 평면

## <a name="scenario-description"></a>시나리오 설명

ADC (응용 프로그램 배달 컨트롤러) 및 SSL-VPN은 다음을 비롯 한 모든 유형의 서비스에 대 한 로컬 및 원격 액세스를 제공 합니다.

- 최신 및 레거시 웹 응용 프로그램

- 웹 기반이 아닌 응용 프로그램

- REST 및 SOAP Web API 서비스

LTM (로컬 Traffic Manager)는 역방향 프록시 기능을 통해 서비스의 보안 게시를 허용 합니다. 동시에, APM (고급 액세스 정책 관리자)은 더 다양 한 기능 집합으로 큰 IP를 확장 하 여 페더레이션 및 SSO (Single sign-on)를 사용 하도록 설정 합니다.

통합은 [SSL VPN 시나리오](f5-aad-password-less-vpn.md)를 포함 하는 대부분의 SHA 사용 사례에 공통적으로 사용 되는 APM과 Azure AD 간의 표준 페더레이션 트러스트를 기반으로 합니다. SAML (Security Assertion Markup Language), OAuth 및 OIDC (Open ID Connect) 리소스는 원격 액세스에 대해 보안을 유지할 수 없으므로 예외가 아닙니다. SaaS 앱을 포함 하 여 모든 서비스에 대 한 0 신뢰 액세스의 경우 큰 IP가 제한이 없는 경우가 있습니다.

Azure AD와 통합 하는 데 필요한 큰 IP의 기능을 통해 [암호 없는 인증](https://www.microsoft.com/security/business/identity/passwordless) 및 [조건부 액세스](../conditional-access/overview.md)와 같은 최신 제어를 사용 하 여 레거시 또는 비 Azure AD 통합 서비스를 보호 하는 데 필요한 프로토콜 전환을 사용할 수 있습니다. 이 시나리오에서 빅 IP는 서비스를 기준으로 Azure AD에 대 한 사전 인증 및 권한 부여를 처리 하는 동시 프록시로 해당 역할을 계속 해 서 수행 합니다.

다이어그램의 1-4 단계는 서비스 공급자가 시작한 흐름에서 사용자, 대규모 IP 및 Azure AD 간의 프런트 엔드 사전 인증 교환을 보여 줍니다. 5-6 단계는 개별 백엔드 서비스에 대 한 후속 APM 세션 보강 및 SSO를 표시 합니다.

![상위 수준 아키텍처를 보여 주는 이미지](./media/f5-aad-integration/integration-flow-diagram.png)

| 단계 | 설명 |
|:------|:-----------|
| 1. | 사용자가 포털에서 응용 프로그램 아이콘을 선택 하 여 SAML SP (빅 IP)에 대 한 URL을 확인 합니다. |
| 2. | 사전 인증을 위해 사용자를 SAML IDP (Azure AD)로 리디렉션하는 대규모 IP|
| 3. | Azure AD는 권한 부여에 대 한 조건부 액세스 정책 및 [세션 컨트롤](../conditional-access/concept-conditional-access-session.md) 을 처리 합니다.|
| 4. | 사용자가 Azure AD에서 발급 한 SAML 클레임을 제공 하는 빅 IP로 다시 리디렉션됩니다. |
| 5. | 큰 IP는 [SSO](../hybrid/how-to-connect-sso.md) 및 [RBAC (역할 기반 액세스 제어)](../../role-based-access-control/overview.md) 에 포함 될 추가 세션 정보를 게시 된 서비스에 요청 합니다. |
| 6. | 빅 IP는 클라이언트 요청을 백 엔드 서비스에 전달 합니다.

## <a name="user-experience"></a>사용자 환경

직원, 계열사 또는 소비자가 든 상관 없이, 대부분의 사용자는 이미 Office 365 로그인 환경을 사용 하 고 있으므로 SHA를 통해 큰 IP 서비스에 액세스 하는 것은 매우 익숙할 것입니다.

이제 사용자는 장치 또는 위치 유형에 관계 없이, 사용자는  [Myapps](../user-help/my-apps-portal-end-user-access.md) 또는 [O365 launchpads](https://o365pp.blob.core.windows.net/media/Resources/Microsoft%20365%20Business/Launchpad%20Overview_for%20Partners_10292019.pdf) 에서 통합 된 광범위 한 서비스 집합에 대 한 셀프 서비스 기능과 함께 통합 된 대규모 IP 게시 서비스를 찾을 수 있습니다. 사용자는 원하는 경우 BIG-IPs 소유 Webtop 포털을 통해 직접 게시 된 서비스에 계속 액세스할 수 있습니다. 로그 오프할 때 SHA는 사용자의 세션이 두 끝, 즉 빅 IP와 Azure AD에서 모두 종료 되도록 하 여 서비스가 무단 액세스 로부터 완전히 보호 되도록 합니다.  

제공 되는 스크린샷은 Azure AD 앱 포털에서 사용자가 큰 IP 게시 서비스를 찾고 계정 속성을 관리 하기 위해 안전 하 게 액세스 하는 것입니다.  

![스크린샷은 woodgrove myapps 갤러리를 보여줍니다.](media/f5-aad-integration/woodgrove-app-gallery.png)

![스크린샷은 woodgrove myaccounts 셀프 서비스 페이지를 표시 합니다.](media/f5-aad-integration/woodgrove-myaccount.png)

## <a name="insights-and-analytics"></a>정보 및 분석

대규모 IP의 역할은 모든 비즈니스에 중요 하므로 배포 된 빅 IP 인스턴스는 SHA 수준 및 운영상 모두에서 게시 된 서비스를 항상 사용할 수 있도록 모니터링 해야 합니다.

로컬에서 또는 SIEM (보안 정보 및 이벤트 관리) 솔루션을 통해 원격으로 이벤트를 기록 하는 여러 가지 옵션이 있습니다 .이를 통해 원격 분석의 외부 저장 및 처리를 사용할 수 있습니다. Azure AD 및 SHA 관련 활동을 모니터링 하는 매우 효율적인 솔루션은 다음과 같은 기능을 함께 사용 하 여 [Azure Monitor](../../azure-monitor/overview.md) 및 [azure 센티널](../../sentinel/overview.md)을 사용 하는 것입니다.

- 조직의 상세 개요, 여러 클라우드 및 대규모 IP 인프라를 비롯 한 온-프레미스 위치에서 잠재적으로

- 모든 신호의 결합 된 보기를 제공 하 여 복잡 하 고 다양 한 도구에 의존 하지 않는 단일 제어 평면

![이미지에 모니터링 흐름이 표시 됩니다.](media/f5-aad-integration/azure-sentinel.png)

## <a name="prerequisites"></a>필수 구성 요소

F i s h i p s를 Azure AD와 통합 하는 데에는 다음과 같은 필수 구성 요소가 있습니다.

- 다음 플랫폼 중 하나에서 실행 되는 F5 큰 IP 인스턴스입니다.

  - 물리적 어플라이언스

  - Microsoft Hyper-V, VMware ESXi, Linux KVM 및 Citrix 하이퍼바이저와 같은 하이퍼바이저 가상 버전

  - Azure, VMware, KVM, Community Xen, MS Hyper-v, AWS, OpenStack 및 Google Cloud와 같은 클라우드 가상 버전

    IP 인스턴스의 실제 위치는 온-프레미스 또는 Azure를 비롯 하 여 지원 되는 모든 클라우드 플랫폼 일 수 있습니다. 인터넷에 연결 되어 있는 경우, 게시 되는 리소스 및 기타 필수 서비스 (예: Active Directory)가 제공 됩니다.  

- 다음 옵션 중 하나를 통해 활성화 된 F5 큰 IP APM 라이선스:

   - F5 BIG-IP® Best 번들 (또는)

   - F5 빅 IP 액세스 정책 관리자™ 독립 실행형 라이선스

   - 기존 빅 IP F5 빅 ip® 로컬 Traffic Manager™ (LTM)에 대 한 F5 빅 IP 액세스 정책 관리자™ (APM) 추가 기능 라이선스

   - 90 일™ APM (빅 IP 액세스 정책 관리자) [평가판 라이선스](https://www.f5.com/trial/big-ip-trial.php)

- 다음 옵션 중 하나를 통해 Azure AD 라이선스:

   - Azure AD [무료 구독은](/windows/client-management/mdm/register-your-free-azure-active-directory-subscription#:~:text=%20Register%20your%20free%20Azure%20Active%20Directory%20subscription,will%20take%20you%20to%20the%20Azure...%20More%20) 암호 없는 인증을 사용 하 여 SHA를 구현 하기 위한 최소 핵심 요구 사항을 제공 합니다.

   - [프리미엄 구독은](https://azure.microsoft.com/pricing/details/active-directory/) [조건부 액세스](../conditional-access/overview.md), [MFA](../authentication/concept-mfa-howitworks.md)및 [id 보호](../identity-protection/overview-identity-protection.md) 를 포함 하 여 앞에 설명 된 추가 값 추가 값을 제공 합니다.

SHA를 구현 하기 위해 이전 경험이 나 F5 큰 IP 지식이 필요 하지는 않지만 F5 큰 IP 용어를 사용 하 여 익숙해질 하는 것이 좋습니다. F 5 bigip 리치 [기술](https://www.f5.com/services/resources/glossary) 자료도 큰 IP 기술 자료를 빌드하기 위한 좋은 장소입니다.

## <a name="deployment-scenarios"></a>배포 시나리오

다음 자습서에서는 큰 IP 및 Azure AD SHA에 대 한 몇 가지 일반적인 패턴을 구현 하는 방법에 대 한 자세한 지침을 제공 합니다.

- [Azure 배포 연습에서 F5 빅 IP](f5-bigip-deployment-guide.md)

- [F5 빅 IP APM 및 Azure AD SSO에서 Kerberos 응용 프로그램](../saas-apps/kerbf5-tutorial.md#configure-f5-single-sign-on-for-kerberos-application)

- [F5 빅 IP APM 및 Azure AD SSO에서 헤더 기반 응용 프로그램](../saas-apps/headerf5-tutorial.md#configure-f5-single-sign-on-for-header-based-application)

- [Azure AD SHA를 사용 하 여 F5 빅 IP SSL-VPN 보안](f5-aad-password-less-vpn.md)

## <a name="additional-resources"></a>추가 리소스

- [암호의 끝, 암호 없는 수준으로 이동 합니다.](https://www.microsoft.com/security/business/identity/passwordless)

- [Azure Active Directory 보안 하이브리드 액세스](https://azure.microsoft.com//services/active-directory/sso/secure-hybrid-access/)

- [원격 작업을 사용 하도록 설정 하는 Microsoft 제로 신뢰 프레임 워크](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Azure 센티널 시작](https://azure.microsoft.com/services/azure-sentinel/?&OCID=AID2100131_SEM_XfknpgAAAHoVMTvh:20200922160358:s&msclkid=5e0e022409fc1c94dab85d4e6f4710e3&ef_id=XfknpgAAAHoVMTvh:20200922160358:s&dclid=CJnX6vHU_esCFUq-ZAod1iQF6A)

## <a name="next-steps"></a>다음 단계

기존 대규모 IP 인프라를 사용 하거나 평가판 인스턴스를 배포 하 여 SHA 개념 증명 (POC)을 실행 하는 것이 좋습니다. [Azure에 빅 IP 가상 버전 (VE) VM을 배포](f5-bigip-deployment-guide.md) 하는 데 30 분 정도 걸리며,이 시점에는 다음이 포함 됩니다.

- SHA 개념 증명을 모델링 하는 완전히 안전한 플랫폼

- 새 대규모 IP 시스템 업데이트 및 핫픽스를 테스트 하는 데 사용할 사전 프로덕션 인스턴스, 완전히 안전한 플랫폼

동시에, 대규모 IP를 통한 게시를 대상으로 하 고 SHA로 보호할 수 있는 하나 또는 두 개의 응용 프로그램을 식별 해야 합니다.  

프로덕션 서비스의 잠재적인 중단을 방지 하기 위해 대규모 IP를 통해 아직 게시 되지 않은 응용 프로그램으로 시작 하는 것이 좋습니다. 이 문서에 설명 된 지침은 다양 한 큰 IP 구성 개체를 만들고 SHA를 설정 하는 일반적인 절차를 이해 하는 데 도움이 됩니다. 완료 되 면 다른 새로운 서비스와 동일한 작업을 수행할 수 있을 뿐만 아니라, 최소한의 노력으로 기존 대규모 IP 게시 된 서비스를 SHA로 변환 하는 데 충분 한 정보를 얻을 수 있습니다.

아래 대화형 가이드는 SHA를 구현 하 고 최종 사용자 환경을 보는 개략적인 절차를 안내 합니다.

[![이미지에 대화형 가이드 표지가 표시 됩니다.](media/f5-aad-integration/interactive-guide.png)](https://aka.ms/Secure-Hybrid-Access-F5-Interactive-Guide)

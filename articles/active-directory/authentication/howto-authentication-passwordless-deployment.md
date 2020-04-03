---
title: Azure AD를 통해 암호 없는 인증 배포 계획
description: Azure Active Directory 암호없는 인증 구현을 계획하고 배포하는 방법 알아보기
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: baselden
author: iainfoulds
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c9933e75a39af43af9e2745d5f7732d40027b34
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582467"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>Azure Active Directory에서 암호 없는 인증 배포 계획

> [!NOTE]
> 이 배포 계획의 오프라인 버전을 만들려면 브라우저의 PDF 인쇄 기능을 사용합니다.

대부분의 사이버 공격은 손상된 사용자 이름과 암호로 시작됩니다. 조직은 사용자에게 다음 방법 중 하나를 사용하도록 요구하여 위협에 대응하려고 합니다.

- 긴 암호
- 복잡한 암호
- 빈번한 암호 변경
- MFA(Multi-Factor authentication)

Microsoft의 [연구에 따르면](https://aka.ms/passwordguidance) 이러한 노력은 사용자를 괴롭히고 지원 비용을 증가시고 있습니다. 자세한 내용은 [Pa$$word 중요하지 않습니다.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)

### <a name="benefits-of-passwordless-authentication"></a>암호 없는 인증의 이점

- **보안 강화.** 공격 노출 로 암호를 제거 하 여 피싱 및 암호 스프레이 공격의 위험을 줄일 수 있습니다.
-  **더 나은 사용자 경험**. 사용자에게 어디서나 데이터에 액세스할 수 있는 편리한 방법을 제공합니다. 모바일 에서 Outlook, OneDrive 또는 Office와 같은 응용 프로그램 및 서비스에 쉽게 액세스할 수 있습니다.
-  **강력한 인사이트.** 강력한 로깅 및 감사를 통해 암호없는 사용자 활동에 대한 통찰력을 얻을 수 있습니다.

암호가없는 경우 암호는 당신이 가지고있는 것과 당신이 알고있는 무언가또는 알고있는 것으로 대체됩니다. 예를 들어 비즈니스용 Windows Hello는 얼굴이나 지문과 같은 생체 인식 제스처 또는 네트워크를 통해 전송되지 않는 장치별 PIN을 사용할 수 있습니다.

## <a name="passwordless-authentication-methods"></a>암호 없는 인증 방법
Microsoft는 많은 시나리오를 다루는 세 가지 암호 없는 인증 옵션을 제공합니다. 이러한 메서드는 나란히 사용할 수 있습니다.

- [비즈니스용 Windows Hello는](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) 전용 Windows 컴퓨터의 사용자에게 가장 적합합니다.
- [FIDO2 보안 키가](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) 있는 보안 키 로그인은 키오스크와 같은 공유 컴퓨터에 로그인하는 사용자, 휴대폰 사용이 제한된 경우 및 권한이 높은 ID에 특히 유용합니다.
- [Microsoft 인증자 앱으로](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) 전화 로그인하면 모바일 장치를 가진 사용자에게 암호 없는 옵션을 제공하는 데 유용합니다. 인증자 앱은 사용자가 모든 플랫폼이나 브라우저에 로그인할 수 있도록 하여 모든 iOS 또는 Android 휴대폰을 암호가 없는 강력한 자격 증명으로 전환합니다. 사용자는 휴대폰에 알림을 받고 화면에 표시된 번호를 휴대폰의 번호와 일치한 다음 생체 인식 데이터 또는 PIN을 사용하여 확인하여 로그인합니다.

### <a name="passwordless-authentication-scenarios"></a>암호 없는 인증 시나리오

Microsoft의 암호 없는 인증 방법은 다양한 시나리오를 사용할 수 있습니다. 암호 없는 인증 전략을 선택 하기 위해 조직의 요구, 필수 구성 조건 및 각 인증 방법의 기능을 고려 합니다. Windows 10 장치를 사용하는 모든 조직에서 비즈니스용 Windows Hello를 사용하는 것이 좋습니다. 그런 다음 추가 시나리오에 대한 전화 로그인(Microsoft 인증자 앱) 또는 보안 키를 추가합니다.

| 시나리오 | 전화 인증 | 보안 키 | 비즈니스용 Windows Hello |
| --- | --- | --- | --- |
| **컴퓨터 로그인:** <br> 할당된 Windows 10 장치에서 | **아니요** | **예** <br> 생체 인식, PIN | **예**<br>생체 인식 및 PIN |
| **컴퓨터 로그인:** <br> 공유 된 윈도우에서 10 장치 | **아니요** | **예** <br> 생체 인식, PIN  | **아니요** |
| **웹 앱 로그인**: <br>사용자 전용 컴퓨터에서 | **예** | **예** <br> 제공된 앱에 대한 단일 로그인은 컴퓨터 로그인에 의해 활성화됩니다. | **예**<br> 제공된 앱에 대한 단일 로그인은 컴퓨터 로그인에 의해 활성화됩니다. |
| **웹 앱 로그인**: <br> 모바일 또는 비창 장치에서 | **예** | **아니요** | **아니요** |
| **컴퓨터 로그인:** <br> Windows 이외의 컴퓨터 | **아니요** | **아니요** | **아니요** |

조직에 가장 적합한 방법을 선택하는 방법에 대한 자세한 내용은 [암호없는 방법 결정을](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#choose-a-passwordless-method)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

암호 없는 배포를 시작하기 전에 조직은 다음 필수 구성 조건을 충족해야 합니다.

| 필수 요소 | 인증자 앱 | FIDO2 보안 키 |
| --- | --- | --- |
| [Azure 다단계 인증 및 셀프 서비스 암호 재설정(SSPR)에 대한 결합 등록이](howto-registration-mfa-sspr-combined.md) 활성화되어 있습니다(미리 보기 기능). | √ | √ |
| [사용자는 Azure 다단계 인증을 수행할 수 있습니다.](howto-mfa-getstarted.md) | √ | √ |
| [사용자가 Azure 다단계 인증 및 SSPR에 등록했습니다.](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [사용자가 Azure Active Directory에 모바일 장치를 등록했습니다.](../devices/overview.md) | √ |   |
| 윈도우 10 버전 1809 이상 마이크로 소프트 에지 또는 모질라 파이어 폭스와 같은 지원되는 브라우저를 사용하여 <br> (버전 67 이상). <br> *기본 지원을 위해 버전 1903 이상을 권장합니다.* |   | √ |
| 호환 FIDO2 보안 키. [Microsoft에서 테스트하고 검증한](howto-authentication-passwordless-enable.md) FIDO2 보안 장치 또는 기타 호환되는 FIDO2 보안 장치를 사용하고 있는지 확인합니다. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>비즈니스를 위한 Windows Hello의 필수 구성 조건

Windows Hello의 전제 조건은 온-프레미스, 하이브리드 또는 클라우드 전용 구성에 배포할지 여부에 따라 크게 달라집니다. 자세한 내용은 [비즈니스용 Windows Hello에 대한 필수 구성 조건의 전체 목록을](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)참조하십시오.

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

사용자는 Azure 다단계 인증 등록 흐름의 일부로 암호 없는 메서드를 등록합니다. 사용자 이름과 암호와 다른 등록된 메서드를 사용한 다단계 인증은 일부 시나리오에서 휴대폰 또는 보안 키를 사용할 수 없는 경우 대체 방법으로 사용할 수 있습니다.

### <a name="licensing"></a>라이선스 
일부 필수 구성 요소는 프리미엄 구독이 필요할 수 있지만 암호 없는 인증에는 추가 비용이 들지 않습니다. [Azure Active Directory 라이선스 페이지에서](https://azure.microsoft.com/pricing/details/active-directory/)자세한 기능 및 라이선스 정보를 보려면 

## <a name="develop-a-plan"></a>계획 수립

각 인증 방법에 대한 비즈니스 요구 사항과 사용 사례를 고려합니다. 그런 다음 필요에 가장 적합한 방법을 선택합니다.

### <a name="use-cases"></a>사용 사례

다음 표에서는 이 프로젝트 중에 구현할 사용 사례를 간략하게 설명합니다.

| Area | 설명 |
| --- | --- |
| **액세스** | 암호 없는 로그인은 회사 네트워크 내부 또는 외부의 회사 또는 개인 장치에서 사용할 수 있습니다. |
| **감사** | 사용 현황 데이터는 관리자가 거의 실시간으로 감사할 수 있습니다. <br> 사용 현황 데이터는 적어도 29일마다 회사 시스템에 다운로드하거나 SIEM 도구가 사용됩니다. |
| **거버넌스** | 적절한 인증 방법 및 관련 그룹에 대한 사용자 할당의 수명 주기가 정의되고 모니터링됩니다. |
| **보안** | 적절한 인증 방법에 대한 액세스는 사용자 및 그룹 할당을 통해 제어됩니다. <br> 권한이 있는 사용자만 암호 없는 로그인을 사용할 수 있습니다. |
| **성능** | 액세스 할당 전파 타임라인이 문서화되고 모니터링됩니다. <br> 로그인 시간은 사용 편의성을 위해 측정됩니다. |
| **사용자 경험** | 사용자는 모바일 호환성을 알고 있습니다. <br> 사용자는 인증자 앱 암호 없는 로그인을 구성할 수 있습니다. |
| **지원** | 사용자는 암호없는 로그인 문제에 대한 지원을 찾는 방법을 알고 있습니다. |

### <a name="engage-the-right-stakeholders"></a>올바른 이해 관계자 참여

기술 프로젝트가 실패하면 일반적으로 영향, 결과 및 책임에 대한 불일치 기대가 있기 때문입니다. 이러한 위험을 방지하려면 [올바른 이해 관계자와](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) 프로젝트의 이해 관계자 역할을 잘 이해해야 합니다.

### <a name="plan-communications"></a>통신 계획

통신은 새로운 서비스의 성공에 매우 중요합니다. 사용자의 경험이 어떻게 변할지, 언제 변경될지, 문제가 발생할 경우 지원을 얻는 방법을 사전에 전달합니다.

최종 사용자와의 통신에는 다음 정보가 포함되어야 합니다.

- [통합된 보안 등록 환경 사용](howto-authentication-passwordless-phone.md)
- [마이크로소프트 인증자 응용 프로그램 다운로드](../user-help/user-help-auth-app-download-install.md)
- [Microsoft 인증자 앱에 등록](howto-authentication-passwordless-phone.md)
- [휴대폰으로 로그인](../user-help/user-help-auth-app-sign-in.md)

Microsoft는 다단계 인증 [통신 템플릿,](https://aka.ms/mfatemplates)SSPR(셀프 서비스 암호 재설정) [통신 템플릿](https://www.microsoft.com/download/details.aspx?id=56768)및 최종 [사용자 설명서를](../user-help/security-info-setup-signin.md) 제공하여 통신 초안을 작성합니다. 해당 페이지에서 보안 [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) **정보** 링크를 선택하여 직접 등록하도록 사용자를 보낼 수 있습니다.

### <a name="plan-to-pilot"></a>파일럿 계획

암호 없는 인증을 배포할 때 먼저 하나 이상의 파일럿 그룹을 사용하도록 설정 해야 합니다. 이 목적을 위해 특별히 [그룹을 만들](../fundamentals/active-directory-groups-create-azure-portal.md) 수 있습니다. 파일럿에 참여할 사용자를 그룹에 추가합니다. 그런 다음 선택한 그룹에 대해 암호가 없는 새 인증 방법을 사용하도록 설정합니다.

온-프레미스 디렉터리 또는 Azure AD에서 그룹을 동기화할 수 있습니다. 파일럿 결과에 만족하면 모든 사용자에 대해 암호 없는 인증을 켤 수 있습니다.

배포 계획 페이지에서 [파일럿에 대한 모범 사례를](https://aka.ms/deploymentplans) 참조하십시오.

## <a name="plan-passwordless-authentication-with-the-microsoft-authenticator-app"></a>Microsoft 인증자 앱으로 암호 없는 인증 계획

마이크로 소프트 인증 앱은 구글 플레이 또는 애플 앱 스토어에서 무료로 다운로드할 수 있습니다. [Microsoft 인증자 앱 다운로드에 대해 자세히 알아보십시오.](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) 사용자가 Microsoft 인증자 앱을 다운로드해 보라고 합니다. 지침에 따라 전화 로그인을 활성화합니다. 

그것은 강력한에 어떤 iOS 또는 안 드 로이드 전화 집니다., 암호없는 자격 증명. 사용자는 휴대폰에 알림을 받고 화면에 표시된 번호를 휴대폰의 번호와 일치한 다음 생체 인식 또는 PIN을 사용하여 확인하여 모든 플랫폼 또는 브라우저에 로그인합니다. [Microsoft 인증자 앱의 작동 방식에 대한 자세한 내용을 참조하십시오.](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless#microsoft-authenticator-app)

![인증자 앱으로 로그인](./media/howto-authentication-passwordless-deployment/passwordless-dp-sign-in.png)

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Microsoft 인증자 앱에 대한 기술적 고려 사항

**AD FS 통합** - 사용자가 Microsoft 인증자 암호없는 자격 증명을 활성화하면 해당 사용자에 대한 인증은 승인을 위한 알림을 보내는 데 기본설정됩니다. 하이브리드 테넌트의 사용자는 "대신 암호를 사용"을 선택하지 않는 한 로그인을 위해 ADFS로 연결되지 않습니다. 또한 이 프로세스는 온-프레미스 조건부 액세스 정책 및 통과 인증 흐름을 우회합니다. 그러나 *login_hint* 지정하면 사용자는 ADFS로 전달되고 암호없는 자격 증명을 사용하는 옵션을 우회합니다.

**Azure 다단계 인증 서버** - 조직의 온-프레미스 Azure MFA 서버를 통해 다단계 인증을 사용하도록 설정된 최종 사용자는 단일 암호없는 전화 로그인 자격 증명을 만들고 사용할 수 있습니다. 사용자가 자격 증명을 사용하여 Microsoft 인증자의 여러 설치(5개 이상)를 업그레이드하려고 하면 이 변경으로 인해 오류가 발생할 수 있습니다.

**장치 등록** - 암호 없는 인증에 인증자 앱을 사용하려면 장치가 Azure AD 테넌트에 등록되어야 하며 공유 장치일 수 없습니다. 장치는 단일 테넌트에만 등록할 수 있습니다. 이 제한은 인증자 앱을 사용하여 전화 로그인에 대해 하나의 직장 또는 학교 계정만 지원된다는 것을 의미합니다.

## <a name="plan-passwordless-authentication-with-fido2-security-keys"></a>FIDO2 보안 키로 암호 없는 인증 계획
보안 키와 함께 사용할 수 있는 암호 없는 로그인 배포에는 세 가지 유형이 있습니다.

-    지원되는 브라우저에서 Azure Active Directory 웹 앱
-    Azure Active Directory Windows 에 가입 된 10 장치
-    하이브리드 Azure Active Directory Windows 10 장치 에 가입(미리 보기)
     -    클라우드 기반 및 온프레미스 리소스에 대한 액세스를 제공합니다. 온-프레미스 리소스에 대한 액세스에 대한 자세한 내용은 [FIDOP2 키를 사용하여 온-프레미스 리소스에 대한 SSO를](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises) 참조하십시오.

**호환 FIDO2 보안 키를**사용하도록 설정해야 합니다. 마이크로소프트는 [FIDO2 주요 공급 업체와 주요 파트너십을](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Microsoft-passwordless-partnership-leads-to-innovation-and-great/ba-p/566493)발표 했다.

**Azure AD 웹 앱 및 Azure AD Windows 조인 장치의 경우:**

-    윈도우 10 버전 1809 이상 마이크로 소프트 에지 또는 모질라 파이어 폭스 (버전 67 이상)와 같은 지원되는 브라우저를 사용하여. 
-    Windows 10 버전 1809는 FIDO2 로그인을 지원하며 FIDO2 키 제조업체의 소프트웨어를 배포해야 할 수 있습니다. 버전 1903 이상에서는 사용하는 것이 좋습니다. 

**하이브리드 Azure Active Directory 도메인 조인 장치의 경우:** 
-    윈도우 10 내부자 빌드 18945 이상
-    Windows Server 2016 또는 2019를 실행하는 완전히 패치된 도메인 서버입니다.
-    Azure AD 연결의 최신 버전

전체 요구 사항 목록은 [Azure Active Directory가 있는 Windows 10 장치에 암호없는 보안 키 로그인 을 활성화를](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-windows#requirements)참조하십시오.


### <a name="security-key-life-cycle"></a>보안 키 수명 주기

보안 키를 사용하면 리소스에 액세스할 수 있으므로 해당 물리적 장치의 관리를 계획해야 합니다.

1. **키 배포**: 조직에 키를 프로비전하는 방법을 계획합니다. 중앙 집중식 프로비저닝 프로세스가 있거나 최종 사용자가 FIDO 2.0 호환 키를 구매할 수 있도록 허용할 수 있습니다.
1. **키 활성화**: 최종 사용자는 보안 키를 자체 활성화해야 합니다. 최종 사용자는 보안 키를 등록하고 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 처음 사용할 때 두 번째 요소(PIN 또는 생체 인식)를 사용하도록 설정합니다.
1. **키 사용 안 하기:** 보안 키 기능은 미리 보기 단계에 있지만 관리자가 사용자 계정에서 키를 제거할 수 있는 방법은 없습니다. 사용자가 제거해야 합니다. 키를 분실하거나 도난당한 경우:
   1. 암호 없는 인증을 위해 사용하도록 설정된 모든 그룹에서 사용자를 제거합니다.
   1. 키를 인증 방법으로 제거했는지 확인합니다.
   1. 새 키를 발행합니다. **키 교체**: 사용자는 두 개의 보안 키를 동시에 활성화할 수 있습니다. 보안 키를 교체할 때 사용자가 교체할 키도 제거했는지 확인합니다.

### <a name="enable-windows-10-support"></a>Windows 10 지원 사용

FIDO2 보안 키를 사용하여 Windows 10 로그인을 사용하도록 설정하려면 Windows 10에서 자격 증명 공급자 기능을 사용하도록 설정해야 합니다. 다음 중 하나를 선택합니다.

- [Intune을 사용하여 자격 증명 공급자 를 활성화](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - Intune 배포가 권장되는 옵션입니다.
- [프로비저닝 패키지를 사용하여 자격 증명 공급자 를 활성화](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Intune 배포가 불가능한 경우 관리자는 자격 증명 공급자 기능을 사용하도록 설정하기 위해 각 컴퓨터에 패키지를 배포해야 합니다. 패키지 설치는 다음 옵션 중 하나에 의해 수행될 수 있습니다.
      - 그룹 정책 또는 구성 관리자
      - Windows 10 컴퓨터에 로컬 설치
- [그룹 정책을 사용하여 자격 증명 공급자 를 활성화](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - 하이브리드 Azure AD 조인 장치에대해서만 지원됩니다.

#### <a name="enable-on-premises-integration"></a>온-프레미스 통합 사용

온-프레미스 리소스에 대한 액세스를 사용하려면 [온-프레미스 리소스에 암호가 없는 보안 키 로그인(미리 보기)을 활성화하는](howto-authentication-passwordless-security-key-on-premises.md)단계를 따릅니다.

> [!IMPORTANT]
> 또한 Windows 10 로그인용 FIDO2 보안 키를 활용하려면 하이브리드 Azure AD 조인 된 장치에 대해서도 이러한 단계를 완료해야 합니다.

### <a name="register-security-keys"></a>보안 키 등록

사용자는 Windows 10 에 가입한 각 Azure Active Directory에 보안 키를 등록해야 합니다.

자세한 내용은 [FIDO2 보안 키의 사용자 등록 및 관리를](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)참조하십시오.


## <a name="plan-auditing-security-and-testing"></a>감사, 보안 및 테스트 계획
조직 및 규정 준수 프레임워크를 충족하는 감사 계획은 배포의 필수적인 부분입니다.

### <a name="auditing-passwordless"></a>암호 없는 감사

Azure AD에는 기술 및 비즈니스 통찰력을 제공하는 보고서가 있습니다. 비즈니스 및 기술 응용 프로그램 소유자가 조직의 요구 사항에 따라 이러한 보고서의 소유권을 소유하고 사용하도록 합니다.

Azure Active Directory 포털내의 **인증** 방법 섹션은 관리자가 암호없는 자격 증명에 대한 설정을 활성화하고 관리할 수 있는 부분입니다.

Azure AD는 다음과 같은 경우 감사 로그에 항목을 추가합니다.

- 관리자는 인증 방법 섹션에서 변경합니다.
- 사용자는 Azure Active Directory 내에서 자격 증명을 변경합니다.

다음 표에서는 일반적인 보고 시나리오의 몇 가지 예를 제공합니다.

|   | 위험 관리 | 생산성 향상 | 거버넌스 및 규정 준수 |
| --- | --- | --- | --- |
| **보고서 유형** | 인증 방법 - 결합 보안 등록에 등록 된 사용자 | 인증 방법 - 앱 알림에 등록된 사용자 | 로그인: 테넌트에 액세스하는 사용자 및 방법 검토 |
| **잠재적인 작업** | 아직 등록되지 않은 대상 사용자 | Microsoft 인증자 앱 또는 보안 키 채택 촉진 | 관리자에 대한 액세스 취소 또는 추가 보안 정책 적용 |

**Azure AD는 대부분의 감사 데이터를 30일 동안 유지하고** Azure Admin 포털 또는 API를 통해 데이터를 사용할 수 있도록 하여 분석 시스템에 다운로드할 수 있도록 합니다. 더 긴 보존이 필요한 경우 [Azure Sentinel,](../../sentinel/connect-azure-active-directory.md)Splunk 또는 스모 로직과 같은 SIEM 도구에서 로그를 내보내고 사용합니다. [액세스 및 사용 보고서를 보는 방법에 대해 자세히 알아보세요.](../reports-monitoring/overview-reports.md)

사용자는 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)로 이동하여 자격 증명을 등록하고 관리할 수 있습니다. 이 링크는 결합된 SSPR/다단계 인증 등록 환경을 통해 활성화된 최종 사용자 자격 증명 관리 환경으로 사용자를 안내합니다. Azure AD는 FIDO2 보안 장치의 등록을 기록하고 사용자가 인증 방법을 변경합니다.

### <a name="plan-security"></a>보안 계획
이 롤아웃 계획의 일부로 Microsoft는 권한이 있는 모든 관리자 계정에 대해 암호 없는 인증을 사용하도록 설정하는 것이 좋습니다.

사용자가 보안 키에서 계정을 사용 하거나 비활성화 하거나 Windows 10 컴퓨터에서 보안 키에 대 한 두 번째 요소를 재설정 하는 경우 항목 보안 로그에 추가 되 고 다음 이벤트 아이디: *4670* 및 *5382*.

### <a name="plan-testing"></a>계획 테스트

시나리오 및 채택을 테스트할 때 배포의 각 단계에서 결과가 예상대로 이루어지도록 합니다.

#### <a name="testing-the-microsoft-authenticator-app"></a>Microsoft 인증자 앱 테스트

다음은 Microsoft 인증자 앱의 암호 없는 인증에 대한 샘플 테스트 사례입니다.

| 시나리오 | 예상 결과 |
| --- | --- |
| 사용자는 마이크로 소프트 인증자 응용 프로그램을 등록 할 수 있습니다 | 사용자는 aka.ms/mysecurityinfo 앱을 등록할 수 있습니다. |
| 사용자는 전화 로그인을 활성화할 수 있습니다. | 직장 계정에 대해 구성된 전화 로그인 |
| 사용자는 전화 로그인을 통해 앱에 액세스할 수 있습니다. | 사용자는 전화 로그인 흐름을 통해 응용 프로그램에 도달합니다. |
| Azure Active Directory 포털의 인증 방법 화면에서 Microsoft 인증자 암호 없는 로그인을 해제하여 전화 로그인 등록을 롤백하는 테스트 | 이전에는 Microsoft 인증자에서 암호 없는 로그인을 사용할 수 없도록 설정한 사용자입니다. |
| Microsoft 인증앱에서 전화 로그인 제거 | Microsoft 인증자에서 더 이상 사용할 수 없는 작업 계정 |

#### <a name="testing-security-keys"></a>보안 키 테스트

다음은 보안 키가 있는 암호 없는 인증에 대한 샘플 테스트 사례입니다.

**Azure Active Directory에 암호가 없는 FIDO 로그인 Windows 10 장치 가입**

| 시나리오 | 예상 결과 |
| --- | --- |
| 사용자는 FIDO2 장치를 등록할 수 있다(1809) | 사용자는 설정 > 계정에서 사용하여 FIDO2 장치를 등록할 수 > 보안 키 >에 로그인 할 > 옵션입니다. |
| 사용자는 FIDO2 장치를 재설정 할 수 있습니다 (1809) | 사용자는 제조업체 소프트웨어를 사용하여 FIDO2 장치를 재설정할 수 있습니다. |
| 사용자는 FIDO2 장치(1809)로 로그인할 수 있습니다. | 사용자는 로그인 창에서 보안 키를 선택하고 성공적으로 로그인할 수 있습니다. |
| 사용자는 FIDO2 장치를 등록할 수 있습니다(1903) | 사용자는 설정 > 계정에서 FIDO2 장치를 등록할 수 > 보안 키 >에 로그인 할 > 옵션입니다. |
| 사용자는 FIDO2 장치를 재설정 할 수 있습니다 (1903) | 사용자는 설정 > 계정에서 FIDO2 장치를 재설정 할 수 있습니다 > 보안 키에 로그인 옵션 > |
| 사용자는 FIDO2 장치(1903)로 로그인할 수 있습니다. | 사용자는 로그인 창에서 보안 키를 선택하고 성공적으로 로그인할 수 있습니다. |

**Azure AD 웹 앱에 암호없는 FIDO 로그인**

| 시나리오 | 예상 결과 |
| --- | --- |
| 사용자는 마이크로 소프트 에지를 사용하여 aka.ms/mysecurityinfo FIDO2 장치를 등록 할 수 있습니다 | 등록이 성공해야 합니다. |
| 사용자는 파이어 폭스를 사용하여 aka.ms/mysecurityinfo FIDO2 장치를 등록 할 수 있습니다 | 등록이 성공해야 합니다. |
| 사용자는 마이크로 소프트 에지를 사용하여 FIDO2 장치를 사용하여 온라인으로 OneDrive에 로그인 할 수 있습니다 | 로그인이 성공해야 합니다. |
| 사용자는 파이어 폭스를 사용하여 FIDO2 장치를 사용하여 온라인으로 OneDrive에 로그인 할 수 있습니다 | 로그인이 성공해야 합니다. |
| Azure Active Directory 포털의 인증 방법 창 내에서 FIDO2 보안 키를 해제하여 FIDO2 장치 등록을 롤백하는 테스트 | 보안 키를 사용하여 로그인하라는 메시지가 표시됩니다. 사용자가 성공적으로 로그인하고 "회사 정책에 따라 다른 방법으로 로그인해야 합니다"라는 오류가 표시됩니다. 그러면 사용자는 다른 방법을 선택하고 성공적으로 로그인할 수 있어야 합니다. 창을 닫고 다시 로그인하여 동일한 오류 메시지가 표시되지 않는지 확인합니다. |

### <a name="plan-for-rollback"></a>롤백 계획

암호 없는 인증은 최종 사용자에게 미치는 영향을 최소화하는 간단한 기능이지만 롤백해야 할 수 있습니다.

롤백하려면 관리자가 Azure Active Directory 포털에 로그인하고, 원하는 강력한 인증 방법을 선택하고, 사용 옵션을 **아니오로**변경해야 합니다. 이 프로세스는 모든 사용자에 대해 암호없는 기능을 해제합니다.

FIDO2 보안 장치를 이미 등록한 사용자는 다음 로그인 시 보안 장치를 사용하라는 메시지가 표시되며 다음 오류가 표시됩니다.

![로그인할 수 있는 다른 방법을 선택하십시오.](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

## <a name="deploy-and-troubleshoot-passwordless-authentication"></a>암호 없는 인증 배포 및 문제 해결

아래 선택한 방법에 정렬 된 단계를 따르십시오.

### <a name="required-administrative-roles"></a>필수 관리 역할

| Azure AD 역할 | 설명 |
| --- | --- |
| 전역 관리자|결합된 등록 환경을 구현할 수 있는 최소 권한 역할입니다. |
| 인증 관리자 | 인증 방법을 구현하고 관리할 수 있는 최소 권한 역할입니다. |
| 사용자 | 장치에서 인증자 앱을 구성하거나 웹 또는 Windows 10 로그인을 위해 보안 키 장치를 등록하는 최소 권한 역할입니다. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Microsoft 인증앱으로 전화 로그인 배포

이 문서의 단계에 따라 [Microsoft 인증자 앱에서 암호 없는 로그인을 사용하도록](howto-authentication-passwordless-phone.md) 설정하여 Microsoft 인증자 앱을 조직에서 암호 없는 인증 방법으로 사용하도록 설정합니다.

### <a name="deploy-fido2-security-key-sign-in"></a>FIDO2 보안 키 로그인 배포

이 문서의 단계를 따르십시오, [Azure AD에 대해 암호 없는 보안 키 로그인을 활성화하여](howto-authentication-passwordless-security-key.md) FIDO2 보안 키를 암호 없는 인증 방법으로 사용하도록 설정합니다.

### <a name="troubleshoot-phone-sign-in"></a>전화 로그인 문제 해결

| 시나리오 | 해결 방법 |
| --- | --- |
| 사용자는 결합된 등록을 수행할 수 없습니다. | [결합된 등록이](concept-registration-mfa-sspr-combined.md) 활성화되어 있는지 확인합니다. |
| 사용자가 전화 로그인 인증자 앱을 사용할 수 없습니다. | 사용자가 배포 범위에 있는지 확인합니다. |
| 사용자는 암호없는 인증의 범위에 있지 않지만 암호가없는 로그인 옵션이 제공되며 완료 할 수 없습니다. | 이 시나리오는 사용자가 정책을 생성하기 전에 응용 프로그램에서 전화 로그인을 사용하도록 설정한 경우에 발생합니다. <br> *로그인 을 사용하려면*: 암호없는 로그인에 대해 사용하도록 설정된 사용자 범위에 사용자를 추가합니다. <br> *로그인을 차단하려면*: 사용자가 해당 응용 프로그램에서 자격 증명을 제거하도록 합니다. |

### <a name="troubleshoot-security-key-sign-in"></a>보안 키 로그인 문제 해결

| 시나리오 | 해결 방법 |
| --- | --- |
| 사용자는 결합된 등록을 수행할 수 없습니다. | [결합된 등록이](concept-registration-mfa-sspr-combined.md) 활성화되어 있는지 확인합니다. |
| 사용자는 [보안 설정에서](https://aka.ms/mysecurityinfo)보안 키를 추가할 수 없습니다. | 보안 [키가](howto-authentication-passwordless-security-key.md) 활성화되어 있는지 확인합니다. |
| 사용자는 Windows 10 로그인 옵션에서 보안 키를 추가할 수 없습니다. | [Windows의 보안 키가 로그인되어 있는지 확인합니다.](howto-authentication-passwordless-enable.md) |
| **오류 메시지**: 이 브라우저 또는 OS가 FIDO2 보안 키를 지원하지 않는 것을 발견했습니다. | 암호가없는 FIDO2 보안 장치는 윈도우 10 버전 1809 이상에서 지원되는 브라우저 (마이크로 소프트 에지, 파이어 폭스 버전 67)에 등록 할 수 있습니다. |
| **오류 메시지:** 회사 정책에 따라 다른 방법을 사용하여 로그인해야 합니다. | 확실하지 않은 보안 키는 테넌트에서 활성화됩니다. |
| 사용자가 Windows 10 버전 1809에서 내 보안 키를 관리할 수 없습니다. | 버전 1809는 FIDO2 키 공급업체에서 제공하는 보안 키 관리 소프트웨어를 사용해야 합니다. 공급업체에 문의하여 지원을 요청하십시오. |
| FIDO2 보안 키에 결함이 있을 수 있다고 생각합니다. | [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/)을 탐색합니다. **+** 이 시나리오에 실패하면 장치에 결함이 있을 수 있습니다. |

## <a name="next-steps"></a>다음 단계

- [Azure AD에 로그인할 때 암호 없는 보안 키 사용](howto-authentication-passwordless-security-key.md)
- [Microsoft 인증자 앱에서 암호 없는 로그인 사용](howto-authentication-passwordless-phone.md)
- [인증 방법 사용 & 통찰력에 대해 자세히 알아보기](howto-authentication-methods-usage-insights.md)


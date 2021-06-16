---
title: Azure AD를 사용하여 암호 없는 인증 배포 계획
description: Azure Active Directory 암호 없는 인증 구현을 계획하고 배포하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: baselden
author: justinha
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b5bbe10c07897c1e33dc4c5a156bc5acd0d7779
ms.sourcegitcommit: e832f58baf0b3a69c2e2781bd8e32d4f1ae932c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110584878"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>Azure Active Directory에서 암호 없는 인증 배포 계획

> [!NOTE]
> 이 배포 계획의 오프라인 버전을 만들려면 브라우저의 PDF로 인쇄 기능을 사용합니다.

대부분의 사이버 공격은 손상된 사용자 이름 및 암호로 시작합니다. 조직에서는 사용자에게 다음 방법 중 하나를 사용하도록 요구하여 위협에 대처하려고 합니다.

- 긴 암호
- 복잡한 암호
- 잦은 암호 변경
- MFA(다단계 인증)

Microsoft [Research에서는 이러한 노력이 사용자를 힘들게 하고 지원 비용을 초래할 수 있다는 사실을 것을 보여 주고 있습니다](https://aka.ms/passwordguidance). 자세한 내용은 [는 중요하지 않음](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)을 참조하세요.

### <a name="benefits-of-passwordless-authentication"></a>암호 없는 인증의 이점

- **보안 강화**. 공격 노출 영역인 암호를 제거하여 피싱 및 암호 스프레이 공격의 위험을 줄입니다.
-  **사용자 환경 개선**. 사용자에게 어디서나 데이터에 액세스할 수 있는 편리한 방법을 제공합니다. 모바일 상태일 때 Outlook, OneDrive 또는 Office와 같은 애플리케이션 및 서비스에 쉽게 액세스할 수 있습니다.
-  **강력한 인사이트**. 강력한 로깅 및 감사를 사용하여 사용자의 암호 없는 작업에 대한 인사이트를 얻을 수 있습니다.

암호 없는 방식을 사용할 경우 암호는 사용자가 보유하고 있는 사항에 사용자 자신 또는 사용자가 알고 있는 사항을 추가한 항목으로 대체됩니다. 예를 들어, 비즈니스용 Windows Hello는 얼굴 또는 지문과 같은 생체 인식 제스처나 네트워크를 통해 전송되지 않는 디바이스 특정 PIN을 사용할 수 있습니다.

## <a name="passwordless-authentication-methods"></a>암호 없는 인증 방법
Microsoft는 다양한 시나리오를 포함하는 세 가지 암호 없는 인증 옵션을 제공합니다. 이러한 방법은 다음과 함께 사용할 수 있습니다.

- [비즈니스용 Windows Hello](./concept-authentication-passwordless.md)는 전용 Windows 컴퓨터의 사용자에게 가장 적합합니다.
- [FIDO2 보안 키](./concept-authentication-passwordless.md)를 사용하는 보안 키 로그인은 휴대폰 사용이 제한되는 경우에 키오스크와 같은 공유 머신에 로그인하는 사용자 및 높은 권한이 있는 ID에 특히 유용합니다.
- [Microsoft Authenticator 앱](./concept-authentication-passwordless.md)을 사용하는 휴대폰 로그인은 모바일 디바이스를 사용하는 사용자에게 암호 없는 옵션을 제공하는 데 유용합니다. Authenticator 앱은 사용자가 모든 플랫폼 또는 브라우저에 로그인할 수 있도록 허용하여 iOS 또는 Android 휴대폰을 강력하고 암호 없는 자격 증명으로 전환합니다. 사용자는 휴대폰으로 알림을 받고, 화면에 표시되는 숫자를 휴대폰에 표시되는 숫자와 일치시킨 다음, 생체 인식 데이터 또는 PIN을 사용하여 확인함으로써 로그인합니다.

### <a name="passwordless-authentication-scenarios"></a>암호 없는 인증 시나리오

Microsoft의 암호 없는 인증 방법으로 다양한 시나리오가 가능해집니다. 사용자의 조직 요구 사항, 필수 구성 요소 및 각 인증 방법의 기능을 고려하여 암호 없는 인증 전략을 선택합니다. Windows 10 디바이스를 사용하는 모든 조직에서 비즈니스용 Windows Hello를 사용하는 것이 좋습니다. 그런 다음, 추가 시나리오를 위해 휴대폰 로그인(Microsoft Authenticator 앱 사용) 또는 보안 키를 추가합니다.

| 시나리오 | 전화 인증 | 보안 키 | 비즈니스용 Windows Hello |
| --- | --- | --- | --- |
| **컴퓨터 로그인**: <br> 할당된 Windows 10 디바이스에서 | **아니요** | **예** <br> 생체 인식, PIN 사용 | **예**<br>생체 인식 및/또는 PIN 사용 |
| **컴퓨터 로그인**: <br> 공유 Windows 10 디바이스에서 | **아니요** | **예** <br> 생체 인식, PIN 사용  | **아니요** |
| **웹앱 로그인**: <br>사용자 전용 컴퓨터에서 | **예** | **예** <br> 컴퓨터 로그인을 통해 앱에 대해 제공된 Single Sign-On이 사용하도록 설정됩니다. | **예**<br> 컴퓨터 로그인을 통해 앱에 대해 제공된 Single Sign-On이 사용하도록 설정됩니다. |
| **웹앱 로그인**: <br> 모바일 또는 비 Windows 디바이스에서 | **예** | **아니요** | **아니요** |
| **컴퓨터 로그인**: <br> 비 Windows 컴퓨터 | **아니요** | **아니요** | **아니요** |

조직에 가장 적합한 방법을 선택하는 데 대한 자세한 내용은 [암호 없는 방법 결정](./concept-authentication-passwordless.md#choose-a-passwordless-method)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

조직에서는 암호 없는 배포를 시작하기 전에 다음 필수 구성 요소를 충족해야 합니다.

| 필수 요소 | 인증자 앱 | FIDO2 보안 키 |
| --- | --- | --- |
| [결합된 Azure AD Multi-Factor Authenticaiton 및 셀프 서비스 암호 재설정 등록](howto-registration-mfa-sspr-combined.md)이 사용하도록 설정됩니다. | √ | √ |
| [사용자가 Azure AD Multi-Factor Authentication을 수행할 수 있습니다.](howto-mfa-getstarted.md) | √ | √ |
| [사용자가 Azure AD Multi-Factor Authentication 및 SSPR에 등록했습니다.](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [사용자가 모바일 디바이스를 Azure Active Directory에 등록했습니다.](../devices/overview.md) | √ |   |
| Microsoft Edge 또는 Mozilla Firefox와 같은 지원되는 브라우저를 사용하는 Windows 10 버전 1809 이상 <br> (버전 67 이상) <br> *네이티브 지원을 위해 버전 1903 이상이 권장됩니다*. |   | √ |
| 호환되는 FIDO2 보안 키. [Microsoft에서 테스트하고 확인한](./concept-authentication-passwordless.md) FIDO2 보안 디바이스 또는 기타 호환되는 FIDO2 보안 디바이스를 사용하고 있는지 확인합니다. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>비즈니스용 Windows Hello에 대한 필수 구성 요소

Windows Hello에 대한 필수 구성 요소는 온-프레미스, 하이브리드 또는 클라우드 전용 구성에서 배포하는지에 따라 달라집니다. 자세한 내용은 [비즈니스용 Windows Hello에 대한 전체 필수 구성 요소 목록](/windows/security/identity-protection/hello-for-business/hello-identity-verification)을 참조하세요.

### <a name="azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication

사용자는 Azure AD Multi-Factor Authentication 등록 흐름의 일부로 암호 없는 방법을 등록합니다. 일부 시나리오에서는 휴대폰 또는 보안 키를 사용할 수 없는 경우 등록된 다른 방법과 함께 사용자 이름 및 암호를 사용하는 Multi-Factor Authentication을 대체 방법으로 사용할 수 있습니다.

### <a name="licensing"></a>라이선스 
일부 필수 구성 요소에는 프리미엄 구독이 필요할 수도 있지만 암호 없는 인증에 대한 추가 비용은 없습니다. 자세한 기능 및 라이선스 정보는 [Azure Active Directory 라이선스 페이지](https://azure.microsoft.com/pricing/details/active-directory/)에 나와 있습니다. 

## <a name="develop-a-plan"></a>플랜 개발

비즈니스 요구 사항과 각 인증 방법에 대한 사용 사례를 고려합니다. 그런 다음. 사용자의 요구에 가장 적합한 방법을 선택합니다.

### <a name="use-cases"></a>사용 사례

다음 표에서는 이 프로젝트 중에 구현되는 사용 사례를 간략하게 설명합니다.

| 영역 | Description |
| --- | --- |
| **Access** | 암호 없는 로그인은 회사 네트워크 내부 또는 외부의 회사나 개인 디바이스에서 사용할 수 있습니다. |
| **감사** | 사용 현황 데이터는 관리자가 거의 실시간으로 감사하는 데 사용할 수 있습니다. <br> 사용 현황 데이터가 최소 29일마다 SIEM 도구를 사용할 때마다 회사 시스템에 다운로드됩니다. |
| **거버넌스** | 적절한 인증 방법 및 관련 그룹에 대한 사용자 할당의 수명 주기를 정의하고 모니터링합니다. |
| **보안** | 적절한 인증 방법에 대한 액세스는 사용자 및 그룹 할당을 통해 제어됩니다. <br> 권한 있는 사용자만 암호 없는 로그인을 사용할 수 있습니다. |
| **성능** | 액세스 할당 전파 타임라인은 문서화 및 모니터링됩니다. <br> 사용 편의성을 위해 로그인 시간이 측정됩니다. |
| **사용자 환경** | 사용자는 모바일 호환성을 인식합니다. <br> 사용자는 Authenticator 앱 암호 없는 로그인을 구성할 수 있습니다. |
| **지원** | 사용자는 암호 없는 로그인 문제에 대한 지원을 찾는 방법을 알고 있습니다. |

### <a name="engage-the-right-stakeholders"></a>올바른 관련자 참여

기술 프로젝트가 실패하는 이유는 일반적으로 영향, 결과 및 책임에 대한 기대 수준이 일치하지 않기 때문입니다. 이러한 문제를 방지하려면 [올바른 관련자를 참여](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders)시키고 프로젝트의 관련자 역할이 명확하게 이해되도록 해야 합니다.

### <a name="plan-communications"></a>통신 계획

통신은 새 서비스의 성공에 대단히 중요합니다. 사용자의 경험이 어떻게 변화하고 언제 변할 것인지, 문제가 발생할 경우 지원을 받는 방법에 대해 사전에 소통합니다.

최종 사용자에 대한 통신은 다음 정보를 포함해야 합니다.

- [결합된 보안 등록 환경 사용](howto-authentication-passwordless-phone.md)
- [Microsoft Authenticator 앱 다운로드](../user-help/user-help-auth-app-download-install.md)
- [Microsoft Authenticator 앱에 등록](howto-authentication-passwordless-phone.md)
- [휴대폰으로 로그인](../user-help/user-help-auth-app-sign-in.md)

Microsoft는 Multi-Factor Authentication [통신 템플릿](https://aka.ms/mfatemplates), SSPR(셀프 서비스 암호 재설정) [통신 템플릿](https://www.microsoft.com/download/details.aspx?id=56768) 및 [최종 사용자 설명서](../user-help/security-info-setup-signin.md)를 제공하여 통신을 대략적으로 설명하도록 지원합니다. 사용자를 [https://myprofile.microsoft.com](https://myprofile.microsoft.com/)으로 보내 해당 페이지에서 **보안 정보** 링크를 선택하여 직접 등록하게 할 수 있습니다.

### <a name="plan-to-pilot"></a>파일럿 계획

암호 없는 인증을 배포하는 경우 먼저 하나 이상의 파일럿 그룹을 사용하도록 설정해야 합니다. 이 용도로만 사용되는 [그룹을 만들 수 있습니다](../fundamentals/active-directory-groups-create-azure-portal.md). 파일럿에 참여할 사용자를 그룹에 추가합니다. 그런 다음, 선택한 그룹에 대해 새 암호 없는 인증 방법을 사용하도록 설정합니다.

온-프레미스 디렉터리 또는 Azure AD에서 그룹을 동기화할 수 있습니다. 파일럿 결과에 만족하는 경우 모든 사용자에 대해 암호 없는 인증을 켤 수 있습니다.

배포 계획 페이지에서 [파일럿에 대한 모범 사례](../fundamentals/active-directory-deployment-plans.md)를 참조하세요.

## <a name="plan-passwordless-authentication-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱을 사용하여 암호 없는 인증 계획

Microsoft Authenticator 앱은 Google Play 또는 Apple App Store에서 무료로 다운로드할 수 있습니다. [Microsoft Authenticator 앱 다운로드에 대해 자세히 알아봅니다](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). 사용자가 Microsoft Authenticator 앱을 다운로드하도록 합니다. 지침에 따라 휴대폰 로그인을 사용하도록 설정합니다. 

iOS 또는 Android 휴대폰을 강력한 암호 없는 자격 증명으로 바꿉니다. 사용자는 휴대폰으로 알림을 받고, 화면에 표시되는 숫자를 휴대폰에 표시되는 숫자와 일치시킨 다음, 생체 인식 데이터 또는 PIN을 사용하여 확인함으로써 플랫폼 또는 브라우저에 로그인합니다. [Microsoft Authenticator 앱의 작동 방식에 대한 세부 정보를 확인합니다](./concept-authentication-passwordless.md#microsoft-authenticator-app).

![Authenticator 앱을 사용하여 로그인](./media/howto-authentication-passwordless-deployment/passwordless-dp-sign-in.png)

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱에 대한 기술 고려 사항

**AD FS 통합** - 사용자가 Microsoft Authenticator 암호 없는 자격 증명을 사용하도록 설정하면 해당 사용자의 인증은 승인을 위해 알림을 보내는 것으로 기본 설정됩니다. 하이브리드 테넌트의 사용자는 "암호를 대신 사용합니다."를 선택하지 않는 한, 로그인을 위해 ADFS로 이동되지 않습니다. 또한 이 프로세스는 온-프레미스 조건부 액세스 정책 및 통과 인증 흐름을 무시합니다. 그러나 *login_hint* 가 지정된 경우 사용자가 ADFS로 전달되고 암호 없는 자격 증명을 사용하는 옵션이 우회됩니다.

**Azure AD Multi-Factor Authentication 서버** - 조직의 온-프레미스 Azure MFA 서버를 통한 Multi-Factor Authentication을 사용하는 최종 사용자는 단일 암호 없는 휴대폰 로그인 자격 증명을 만들고 사용할 수 있습니다. 사용자가 자격 증명으로 Microsoft Authenticator의 여러 설치(5개 이상)를 업그레이드하려고 시도하면 이 변경으로 인해 오류가 발생할 수 있습니다.

**디바이스 등록** - 암호 없는 인증을 위해 Authenticator 앱을 사용하려면 디바이스가 Azure AD 테넌트에 등록되어 있어야 하며 공유 디바이스일 수 없습니다. 디바이스는 단일 테넌트에만 등록될 수 있습니다. 이 제한은 Authenticator 앱을 사용하는 휴대폰 로그인에 대해 하나의 회사 또는 학교 계정만 지원됨을 의미합니다.

## <a name="plan-passwordless-authentication-with-fido2-security-keys"></a>FIDO2 보안 키로 암호 없는 인증 계획
보안 키에는 다음과 같은 세 가지 유형의 암호 없는 로그인 배포를 사용할 수 있습니다.

-    지원되는 브라우저의 Azure Active Directory 웹앱
-    Azure Active Directory에서 Windows 10 디바이스에 조인함
-    하이브리드 Azure Active Directory에서 Windows 10 디바이스에 조인함
     -    클라우드 기반 및 온-프레미스 리소스 모두에 대한 액세스를 제공합니다. 온-프레미스 리소스에 액세스하는 방법에 대한 자세한 내용은 [FIDO2 키를 사용하여 온-프레미스 리소스에 SSO](./howto-authentication-passwordless-security-key-on-premises.md)를 참조하세요.

**호환되는 FIDO2 보안 키** 를 사용하도록 설정해야 합니다. Microsoft는 [FIDO2 키 공급업체와의 주요 파트너 관계](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Microsoft-passwordless-partnership-leads-to-innovation-and-great/ba-p/566493)를 발표했습니다.

**Azure AD 웹앱 및 Azure AD Windows 조인 디바이스**:

-    Microsoft Edge 또는 Mozilla Firefox(버전 67 이상)와 같은 지원되는 브라우저를 사용하는 Windows 10 버전 1809 이상 
-    Windows 10 버전 1809는 FIDO2 로그인을 지원하며, FIDO2 키 제조업체의 소프트웨어를 배포해야 할 수 있습니다. 버전 1903 이상을 사용하는 것이 좋습니다. 

**하이브리드 Azure Active Directory 도메인 가입 디바이스**: 
-    Windows 10 버전 2004 이상
-    Windows Server 2016 또는 2019를 실행하는 완전히 패치된 도메인 서버입니다.
-    최신 버전의 Azure AD Connect

요구 사항의 전체 목록은 [Azure Active Directory를 통해 Windows 10 디바이스에 대한 암호 없는 보안 키 로그인 사용 설정](./howto-authentication-passwordless-security-key-windows.md#requirements)을 참조하세요.


### <a name="security-key-life-cycle"></a>보안 키 수명 주기

보안 키를 사용하여 리소스에 액세스하고 해당 물리적 디바이스의 관리를 계획해야 합니다.

1. **키 배포**: 조직에 키를 프로비저닝하는 방법을 계획합니다. 중앙 집중식 프로비저닝 프로세스가 있거나 최종 사용자가 FIDO 2.0 호환 키를 구매하도록 할 수 있습니다.
1. **키 활성화**: 최종 사용자는 보안 키를 직접 활성화해야 합니다. 최종 사용자는 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)에 보안 키를 등록하고 처음 사용할 때 두 번째 요소(PIN 또는 생체 인식)를 사용하도록 설정합니다.
1. **키 사용 안 함**: 보안 키 기능이 미리 보기 단계에 있는 동안 관리자가 사용자 계정에서 키를 제거할 수 있는 방법이 없습니다. 사용자가 제거해야 합니다. 키를 분실하거나 도난당한 경우:
   1. 암호 없는 인증을 사용하도록 설정된 그룹에서 사용자를 제거합니다.
   1. 인증 방법으로서의 키를 제거했는지 확인합니다.
   1. 새 키를 발급합니다. **대체 키**: 사용자는 동시에 두 개의 보안 키를 사용하도록 설정할 수 있습니다. 보안 키를 교체하는 경우 사용자는 대체되는 키도 제거해야 합니다.

### <a name="enable-windows-10-support"></a>Windows 10 지원 사용

FIDO2 보안 키를 사용하여 Windows 10 로그인을 사용하도록 설정하려면 Windows 10에서 자격 증명 공급자 기능을 사용하도록 설정해야 합니다. 다음 중 하나를 선택합니다.

- [Intune에서 자격 증명 공급자 사용](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - Intune 배포를 권장되는 옵션입니다.
- [프로비저닝 패키지를 사용하여 자격 증명 공급자 사용](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Intune 배포를 수행할 수 없는 경우 관리자는 자격 증명 공급자 기능을 사용하도록 설정하기 위해 각 머신에 패키지를 배포해야 합니다. 다음 옵션 중 하나를 수행하여 패키지를 설치할 수 있습니다.
      - 그룹 정책 또는 Configuration Manager
      - Windows 10 머신에서 로컬 설치
- [그룹 정책에서 자격 증명 공급자 사용](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - 하이브리드 Azure AD 조인 디바이스에 대해서만 지원됩니다.

#### <a name="enable-on-premises-integration"></a>온-프레미스 통합 사용

온-프레미스 리소스에 대한 액세스를 사용하도록 설정하려면 [온-프레미스 리소스에 대한 암호 없는 보안 키 로그인을 사용하도록 설정](howto-authentication-passwordless-security-key-on-premises.md)하는 단계를 수행합니다.

> [!IMPORTANT]
> Windows 10 로그인에 대한 FIDO2 보안 키를 활용하려면 하이브리드 Azure AD 조인 디바이스에서 이러한 단계도 완료해야 합니다.

### <a name="register-security-keys"></a>보안 키 등록

사용자는 각각의 Azure Active Directory 조인 Windows 10 머신에 보안 키를 등록해야 합니다.

자세한 내용은 [FIDO2 보안 키의 사용자 등록 및 관리](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)를 참조하세요.


## <a name="plan-auditing-security-and-testing"></a>감사, 보안 및 테스트 계획
조직 및 규정 준수 프레임워크를 충족하는 감사 계획은 배포의 필수적인 부분입니다.

### <a name="auditing-passwordless"></a>암호 없이 감사

Azure AD에는 기술 및 비즈니스 인사이트를 제공하는 보고서가 있습니다. 비즈니스 및 기술 애플리케이션 소유자에게 조직의 요구 사항에 따라 이러한 보고서를 소유하고 사용하도록 합니다.

Azure Active Directory 포털의 **인증** 방법 섹션에서 관리자는 암호 없는 자격 증명에 대한 설정을 사용하도록 설정하고 관리할 수 있습니다.

Azure AD는 다음과 같은 경우 감사 로그에 항목을 추가합니다.

- 관리자가 인증 방법 섹션에서 변경을 수행합니다.
- 사용자는 Azure Active Directory 내에서 자격 증명을 다각도로 변경합니다.

다음 표에서는 일반적인 보고 시나리오의 몇 가지 예를 제공합니다.

|   | 위험 관리 | 생산성 향상 | 거버넌스 및 규정 준수 |
| --- | --- | --- | --- |
| **보고서 유형** | 인증 방법 - 사용자가 결합된 보안 등록에 등록함 | 인증 방법 – 사용자가 앱 알림에 등록함 | 로그인: 테넌트에 액세스하는 사용자 및 방법 검토 |
| **잠재적 작업** | 대상 사용자가 아직 등록되지 않음 | Microsoft Authenticator 앱 또는 보안 키의 도입 주도 | 관리자에 대해 액세스 해지 또는 추가 보안 정책 적용 |

**Azure AD는 대부분의 감사 데이터를 30일 동안 유지** 하고 데이터를 분석 시스템으로 다운로드하기 위해 Azure 관리 포털 또는 API를 통해 데이터를 사용할 수 있도록 합니다. 좀 더 긴 보존이 필요한 경우 [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk 또는 Sumo Logic과 같은 SIEM 도구에서 로그를 내보내고 사용합니다. [액세스 및 사용 보고서를 보는 방법에 대해 자세히 알아봅니다](../reports-monitoring/overview-reports.md).

사용자는 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)로 이동하여 자격 증명을 등록하고 관리할 수 있습니다. 이 링크를 클릭하면 결합된 SSPR/Multi-Factor Authentication 등록 환경을 통해 사용하도록 설정된 최종 사용자 자격 증명 관리 환경으로 이동됩니다. Azure AD는 FIDO2 보안 디바이스의 등록과 사용자의 인증 방법 변경을 기록합니다.

### <a name="plan-security"></a>보안 계획
이 롤아웃 계획의 일부로, 권한 있는 모든 관리자 계정에 대해 암호 없는 인증을 사용하도록 설정하는 것이 좋습니다.

사용자가 보안 키에서 계정을 사용 또는 사용하지 않도록 설정하거나, Windows 10 머신에서 보안 키에 대한 두 번째 단계를 다시 설정하면 항목이 보안 로그의 이벤트 ID *4670* 및 *5382* 아래에 추가됩니다.

### <a name="plan-testing"></a>테스트 계획

시나리오 및 채택을 테스트하는 배포의 각 단계에서 결과가 예상대로 작동하는지 확인합니다.

#### <a name="testing-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱 테스트

다음은 Microsoft Authenticator 앱을 사용한 암호 없는 인증에 대한 샘플 테스트 사례입니다.

| 시나리오 | 예상 결과 |
| --- | --- |
| 사용자가 Microsoft Authenticator 앱을 등록할 수 있습니다. | 사용자가 aka.ms/mysecurityinfo에서 앱을 등록할 수 있습니다. |
| 사용자가 휴대폰 로그인을 사용하도록 설정할 수 있습니다. | 회사 계정에 대해 휴대폰 로그인을 구성했습니다. |
| 사용자가 휴대폰 로그인을 사용하여 앱에 액세스할 수 있습니다. | 사용자가 휴대폰 로그인 흐름을 통해 이동하고 애플리케이션에 연결합니다. |
| Azure Active Directory 포털의 인증 방법 화면에서 Microsoft Authenticator 암호 없는 로그인을 해제하여 휴대폰 로그인 등록 롤백을 테스트합니다. | 이전에 사용하도록 설정된 사용자는 Microsoft Authenticator에서 암호 없는 로그인을 사용할 수 없습니다. |
| Microsoft Authenticator 앱에서 휴대폰 로그인 제거합니다. | Microsoft Authenticator에서 회사 계정을 더 이상 사용할 수 없습니다. |

#### <a name="testing-security-keys"></a>보안 키 테스트

다음은 보안 키를 사용하는 암호 없는 인증에 대한 샘플 테스트 사례입니다.

**Azure Active Directory 조인 Windows 10 디바이스에 대한 암호 없는 FIDO 로그인**

| 시나리오 | 예상 결과 |
| --- | --- |
| 사용자가 FIDO2 디바이스에 등록할 수 있습니다(1809). | 사용자가 설정 > 계정 > 로그인 옵션 > 보안 키를 사용하여 FIDO2 디바이스를 등록할 수 있습니다. |
| 사용자가 FIDO2 디바이스를 다시 설정할 수 있습니다(1809). | 사용자가 제조업체 소프트웨어를 사용하여 FIDO2 디바이스를 다시 설정할 수 있습니다. |
| 사용자가 FIDO2 디바이스에 로그인할 수 있습니다(1809). | 사용자가 로그인 창에서 보안 키를 선택하고 성공적으로 로그인할 수 있습니다. |
| 사용자가 FIDO2 디바이스에 등록할 수 있습니다(1903). | 사용자가 설정 > 계정 > 로그인 옵션 > 보안 키에서 FIDO2 디바이스를 등록할 수 있습니다. |
| 사용자가 FIDO2 디바이스를 다시 설정할 수 있습니다(1903). | 사용자가 설정 > 계정 > 로그인 옵션 > 보안 키에서 FIDO2 디바이스를 다시 설정할 수 있습니다. |
| 사용자가 FIDO2 디바이스에 로그인할 수 있습니다(1903). | 사용자가 로그인 창에서 보안 키를 선택하고 성공적으로 로그인할 수 있습니다. |

**Azure AD 웹앱에 대한 암호 없는 FIDO 로그인**

| 시나리오 | 예상 결과 |
| --- | --- |
| 사용자가 Microsoft Edge를 사용하여 aka.ms/mysecurityinfo에서 FIDO2 디바이스를 등록할 수 있음 | 등록 성공 |
| 사용자가 Firefox를 사용하여 aka.ms/mysecurityinfo에서 FIDO2 디바이스를 등록할 수 있음 | 등록 성공 |
| 사용자가 Microsoft Edge를 사용하여 FIDO2 디바이스에서 온라인으로 OneDrive에 로그인할 수 있음 | 로그인 성공 |
| 사용자가 Firefox를 사용하여 FIDO2 디바이스에서 온라인으로 OneDrive에 로그인할 수 있음 | 로그인 성공 |
| Azure Active Directory 포털의 인증 방법 창 내에서 FIDO2 보안 키를 해제하여 FIDO2 디바이스 등록 롤백을 테스트 | 사용자에게 보안 키를 사용하여 로그인하라는 메시지가 표시됩니다. 사용자가 성공적으로 로그인되고 "회사 정책에 따라 다른 방법으로 로그인해야 합니다."라는 오류가 표시됩니다. 그러면 사용자가 다른 방법을 선택하여 성공적으로 로그인할 수 있습니다. 창을 닫은 후 다시 로그인하여 동일한 오류 메시지가 표시되지 않는지 확인합니다. |

### <a name="plan-for-rollback"></a>롤백 계획

암호 없는 인증은 최종 사용자에게 미치는 영향을 최소화하는 간단한 기능이지만 롤백해야 할 수 있습니다.

롤백하려면 관리자가 Azure Active Directory 포털에 로그인하고, 원하는 강력한 인증 방법을 선택한 다음, 사용 옵션을 **아니요** 로 변경해야 합니다. 이 프로세스는 모든 사용자에 대해 암호 없는 기능을 해제합니다.

FIDO2 보안 디바이스를 이미 등록한 사용자에게 다음 로그인 시 보안 디바이스를 사용하라는 메시지가 표시되고 다음 오류가 표시됩니다.
  
![다른 로그인 방법 선택](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

## <a name="deploy-and-troubleshoot-passwordless-authentication"></a>암호 없는 인증 배포 및 문제 해결

아래에서 선택한 방법에 맞게 정렬된 단계를 따르세요.

### <a name="required-administrative-roles"></a>필요한 관리 역할

| Azure AD 역할 | Description |
| --- | --- |
| 전역 관리자|결합된 등록 환경을 구현할 수 있는 최소 권한 있는 역할입니다. |
| 인증 관리자 | 인증 방법을 구현하고 관리할 수 있는 최소 권한 있는 역할입니다. |
| 사용자 | 디바이스에서 Authenticator 앱을 구성하거나 웹 또는 Windows 10 로그인을 위해 보안 키 디바이스를 등록하기 위한 최소 권한 있는 역할입니다. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱을 사용하여 휴대폰 로그인 배포

[Microsoft Authenticator 앱에서 암호 없는 로그인 사용](howto-authentication-passwordless-phone.md) 문서의 단계를 수행하여 조직에서 Microsoft Authenticator 앱을 암호 없는 인증 방법으로 사용하도록 설정합니다.

### <a name="deploy-fido2-security-key-sign-in"></a>FIDO2 보안 키 로그인 배포

[Azure AD에 대해 암호 없는 보안 키 로그인 사용](howto-authentication-passwordless-security-key.md) 문서의 단계를 수행하여 FIDO2 보안 키를 암호 없는 인증 방법으로 사용하도록 설정합니다.

### <a name="troubleshoot-phone-sign-in"></a>휴대폰 로그인 문제 해결

| 시나리오 | 솔루션 |
| --- | --- |
| 사용자가 결합된 등록을 수행할 수 없습니다. | [결합된 등록](concept-registration-mfa-sspr-combined.md)이 사용하도록 설정되어 있는지 확인합니다. |
| 사용자가 휴대폰 로그인 인증자 앱을 사용하도록 설정할 수 없습니다. | 사용자가 배포 범위에 있는지 확인하세요. |
| 사용자가 암호 없는 인증에 대한 범위 내에 있지 않지만, 암호 없는 로그인 옵션으로 제공되며, 이 옵션은 완료할 수 없습니다. | 이 시나리오는 사용자가 정책 생성 전에 애플리케이션에서 휴대폰 로그인을 사용하도록 설정한 경우에 발생합니다. <br> *로그인을 사용하도록 설정하려면*: 암호 없는 로그인이 사용하도록 설정된 사용자 범위에 사용자를 추가합니다. <br> *로그인을 차단하려면*: 사용자가 해당 애플리케이션에서 자격 증명을 제거하도록 합니다. |

### <a name="troubleshoot-security-key-sign-in"></a>보안 키 로그인 문제 해결

| 시나리오 | 솔루션 |
| --- | --- |
| 사용자가 결합된 등록을 수행할 수 없습니다. | [결합된 등록](concept-registration-mfa-sspr-combined.md)이 사용하도록 설정되어 있는지 확인합니다. |
| 사용자가 [보안 설정](https://aka.ms/mysecurityinfo)에서 보안 키를 추가할 수 없습니다. | [보안 키](howto-authentication-passwordless-security-key.md)가 사용하도록 설정되어 있는지 확인합니다. |
| 사용자가 Windows 10 로그인 옵션에서 보안 키를 추가할 수 없습니다. | [Windows 로그인에 대한 보안 키를 확인합니다](./concept-authentication-passwordless.md). |
| **오류 메시지**: 이 브라우저 또는 OS가 FIDO2 보안 키를 지원하지 않는 것으로 확인되었습니다. | 암호 없는 FIDO2 보안 디바이스는 Windows 10 버전 1809 이상에서 지원되는 브라우저(Microsoft Edge, Firefox 버전 67)에만 등록할 수 있습니다. |
| **오류 메시지**: 회사 정책에 따라 다른 방법으로 로그인해야 합니다. | 테넌트에서 보안 키를 사용하도록 설정했는지 확인합니다. |
| 사용자가 Windows 10 버전 1809에서 내 보안 키를 관리할 수 없습니다. | 버전 1809에서는 FIDO2 키 공급업체에서 제공하는 보안 키 관리 소프트웨어를 사용해야 합니다. 공급업체에 지원을 문의하세요. |
| FIDO2 보안 키에 결함이 있는 것 같습니다. 어떻게 테스트할 수 있나요? | [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/)으로 이동하고, 테스트 계정에 대한 자격 증명을 입력하고, 의심스러운 보안 키를 연결하고, 화면 오른쪽 위에 있는 **+** 단추를 선택하고, 만들기를 클릭하고, 만들기 프로세스를 진행합니다. 이 시나리오에서 오류가 발생하면 디바이스에 결함이 있을 수 있습니다. |

## <a name="next-steps"></a>다음 단계

- [Azure AD에 로그인하기 위해 암호 없는 보안 키를 사용하도록 설정](howto-authentication-passwordless-security-key.md)
- [Microsoft Authenticator 앱에서 암호 없는 로그인을 사용하도록 설정](howto-authentication-passwordless-phone.md)
- [인증 방법 사용 및 인사이트에 대해 자세히 알아보기](./howto-authentication-methods-activity.md)
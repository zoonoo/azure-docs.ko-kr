---
title: "EMS 및 Office 365 서비스 개요 | Microsoft Docs"
description: "이 문서는 EMS 및 Office 365 서비스의 개요를 제공합니다."
author: jeffgilb
manager: femila
ms.service: guidance
ms.topic: article
ms.date: 07/12/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
pnp.series.title: Best Practices
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 288ac3f9ec6354d4108461f32618eb95d3b3ab14
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="ems-and-office-365-services-overview"></a>EMS 및 Office 365 서비스 개요

이 문서는 명시된 권장 사항에서 활용되는 EMS 및 Office 365 서비스에 대한 개요를 제공하고 Azure 전자 메일 보호 기능을 이해하는 데 필요한 핵심 개념에 대해 설명합니다. 이러한 기능을 통해 Microsoft 클라우드 엔터프라이즈 관리자는 회사 직원의 신원과 장치를 보호할 수 있을 뿐만 아니라 전송 중이거나 미사용 중인 회사 데이터 자체에 대한 액세스도 제어할 수 있습니다.

## <a name="services"></a>Services 

이 문서에서 참조하는 서비스는 아래 테이블에 설명되어 있습니다.
 
|부여|설명|
|-------|-----------|
|[Microsoft Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)|Azure AD에는 다단계 인증, 장치 등록, 셀프 서비스 암호 관리, 셀프 서비스 그룹 관리, 역할 기반 액세스 제어, 응용 프로그램 사용 모니터링, 광범위한 감사, 보안 모니터링 및 경고를 비롯한 완벽한 ID 관리 기능이 제공됩니다.|
|[Azure AD ID 보호](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-identityprotection)|이 서비스를 사용하면 조직의 ID에 영향을 미치는 잠재적 취약성을 감지하고 낮은 수준, 중간 수준 및 높은 수준의 로그인 위험 및 사용자 위험에 대한 조건부 액세스 정책을 통해 자동 응답을 구성할 수 있습니다.|
|[Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)|이 서비스를 사용하면 조직에서 권한이 있는 작업에 영구적으로 액세스할 수 있는 사용자 수를 최소화할 수 있습니다. Azure AD Privileged Identity Management는 적격 관리자라는 개념을 소개합니다. 적격인 관리자는 매일은 아니지만 때때로 권한 있는 액세스가 필요한 사용자여야 합니다. 역할은 사용자가 액세스가 필요할 때까지 비활성으로 있다가, 활성화 프로세스를 완료하고 미리 정해진 시간 동안 활성 관리자가 됩니다.|
|[Azure Information Protection](https://docs.microsoft.com/information-protection/understand-explore/what-is-information-protection)| Azure Information Protection은 EMS E5 제품의 일부로 제공되는 클라우드 기반 솔루션으로 조직의 문서 및 전자 메일을 분류하고 레이블을 지정하고 보호하는 데 도움이 됩니다. 이러한 작업은 규칙 및 조건을 정의하는 관리자에 의해 자동으로, 사용자에 의해 또는 사용자에게 권장 사항이 제공되는 조합에 의해 수동으로 수행될 수 있습니다. Azure Information Protection 레이블을 사용하여 문서 및 전자 메일에 분류를 적용합니다. 이렇게 하면 데이터가 저장되는 위치 또는 공유 대상에 관계없이 항상 분류를 식별할 수 있습니다. <br><br>Azure Information Protection 정책 설정은 [Azure Rights Management](https://docs.microsoft.com/information-protection/understand-explore/what-is-azure-rms)에 의해 보호됩니다. 레이블이 적용되는 방식과 유사하게 Rights Management를 사용하여 적용되는 보호는 조직, 네트워크, 파일 서버 및 응용 프로그램의 내부 또는 외부에 있든 그 위치와 상관없이 문서 및 전자 메일과 함께 유지됩니다.|
|[Microsoft Intune](https://docs.microsoft.com/intune/understand-explore/introduction-to-microsoft-intune)|Intune은 기업의 데이터를 보호하는 동시에 생산적인 작업이 가능하도록 도움을 주는 클라우드 기반 EMM(Enterprise Mobility Management) 서비스입니다. Intune은 ID 및 액세스 제어를 위해 Azure AD와 긴밀하게 통합되며 장치 및 응용 프로그램 관리에 사용됩니다. [Intune의 장치 관리](https://docs.microsoft.com/intune/deploy-use/manage-settings-and-features-on-your-devices-with-microsoft-intune-policies) 기능은 Windows PC를 비롯한 사용자 장치를 구성하고 보호하는 데 사용됩니다. <br><br>Intune 장치 관리 기능은 사용자가 개인 전화, 태블릿 또는 PC를 등록할 수 있는 [BYOD(Bring Your Own Device)](https://docs.microsoft.com/enterprise-mobility-security/solutions/enable-byod) 등록 및 자동 등록, 공유 장치 또는 사전 승인된 등록 요구 사항 구성과 같은 관리 시나리오를 가능하게 하는 [COD(회사 소유 장치)](https://docs.microsoft.com/enterprise-mobility-security/solutions/issue-corp-devices) 등록을 모두 지원합니다. 보안을 강화하기 위해 MFA에 장치 등록을 요구할 수도 있습니다. 일단 관리에 등록되면 Intune은 회사 리소스에 안전하게 액세스할 수 있도록 장치 기능 및 설정을 구성할 수 있습니다.|


## <a name="ems-concepts"></a>EMS 개념
잘 알고 있어야 하는 핵심 개념과 EMS 기능은 아래 테이블에 설명되어 있습니다.

|핵심 개념|설명|
|------------|-----------|
|[Azure MFA(Multi-Factor Authentication)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud)|Microsoft의 2단계 검증 솔루션인 Azure MFA는 간단한 로그인 프로세스에 대한 사용자의 요구를 충족시키면서 데이터 및 응용 프로그램에 대한 액세스를 보호합니다. 전화 통화, 문자 메시지 또는 모바일 앱 확인과 같은 다양한 확인 방법을 통해 강력한 인증을 전달합니다.|
|[Azure AD 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access)|Azure AD의 이 기능을 사용하면 특정 조건에 따라 사용자 환경에서 클라우드 앱에 대한 액세스 제어를 적용할 수 있습니다. 제어 문을 사용하면 추가 요구 사항을 액세스에 연결하거나 액세스를 차단할 수 있습니다. 조건부 액세스의 구현은 정책을 기반으로 합니다.|
|[Exchange Online DLP(데이터 손실 방지)](https://support.office.com/article/Overview-of-data-loss-prevention-policies-1966b2a7-d1e2-4d92-ab61-42efbb137f5e)|Exchange Online 계획 2 및 Office 365 구독의 프리미엄 기능인 Exchange Online DLP(데이터 손실 방지) 정책을 사용하면 조직에서 Office 365의 중요한 정보를 식별하고 모니터링하고 자동으로 보호할 수 있습니다.<br><br>Exchange Online DLP 정책을 사용하면 Exchange Online, SharePoint Online 및 비즈니스용 OneDrive와 같은 여러 위치에서 중요한 정보를 식별할 수 있습니다. 예를 들어, 이러한 정책은 중요한 정보가 포함된 문서를 식별하거나 조직 외부 사람과 중요한 정보를 실수로 공유하는 것을 방지하는 데 도움이 됩니다.|
|[Exchange 메일 흐름/전송 규칙](https://support.office.com/en-US/article/Define-mail-flow-rules-to-encrypt-or-decrypt-email-messages-9B7DAF19-D5F2-415B-BC43-A0F5F4A585E8)|전송 규칙이라고도 알려진 Exchange 메일 흐름 규칙은 조직을 통과하는 메시지에서 특정 조건을 찾아서 조치를 취합니다. 메일 흐름 규칙은 많은 전자 메일 클라이언트에 제공되는 받은 편지함 규칙과 유사합니다. Outlook과 같은 클라이언트 응용 프로그램에서 설정하는 규칙과 메일 흐름 규칙 사이의 주요 차이는 메일 흐름 규칙의 경우 메시지가 배달된 후가 아니라 메시지 전송 중에 적용된다는 점입니다. 메일 흐름 규칙에는 다양한 유형의 메시징 정책을 구현할 수 있는 유연성을 제공하는 매우 다양한 조건, 예외 및 동작 집합이 포함되어 있습니다.|
|[Intune 모바일 장치 관리](https://docs.microsoft.com/intune/understand-explore/introduction-to-microsoft-intune)|Intune은 모바일 운영 체제에서 사용할 수 있는 프로토콜 또는 API를 사용하여 MDM(모바일 장치 관리)을 제공합니다. 여기에는 IT가 기업 서비스에 액세스하는 장치의 인벤토리를 확보하도록 장치를 관리에 등록하고, 회사 보안 및 상태 표준을 충족하도록 장치를 구성하고, 회사 서비스에 액세스하도록 인증서 및 Wi-Fi/VPN 프로필을 제공하고, 회사 표준 준수 장치를 측정 및 보고하고, 관리되는 장치의 회사 데이터를 제거하는 등의 작업이 포함됩니다.|
|[Intune 앱 보호 정책](https://docs.microsoft.com/intune/deploy-use/protect-app-data-using-mobile-app-management-policies-with-microsoft-intune)|Intune 앱 보호 정책을 사용하면 장치를 등록하거나 등록하지 않고 모바일 앱에서 회사의 데이터를 관리할 수 있습니다. 사실, [Intune으로 Office 365 정보를 보호하는 데 도움을 받으면서](https://docs.microsoft.com/enterprise-mobility-security/solutions/protect-company-data-without-managing-devices) 사용자의 모바일 장치는 다른 타사 MDM 솔루션으로 관리할 수도 있습니다. 직원이 생산적으로 작업할 수 있도록 보장하면서 의도적 또는 비의도적인 데이터 손실을 방지할 수도 있습니다. 앱 수준 정책을 구현하면 회사 리소스에 대한 액세스를 제한하면서 데이터를 IT 부서의 통제 범위 내에 유지할 수 있습니다.|
|[Azure AD 토큰 수명](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)|Azure AD(Azure Active Directory)에서 발급한 토큰의 수명을 지정할 수 있습니다. 조직의 모든 앱, 다중 테넌트(다중 조직) 응용 프로그램 또는 조직의 특정 서비스 주체에 대해 토큰 수명을 구성할 수 있습니다.|
|Microsoft ID Broker|Microsoft는 다른 공급 업체의 응용 프로그램에서 자격 증명의 브리징을 허용하고 자격 증명을 확인하는 위치에서 단일 보안 위치가 필요한 특수한 향상된 기능을 허용하는 모든 모바일 플랫폼에 대한 응용 프로그램을 제공합니다. 이것을 broker라고 합니다. iOS 및 Android에서 이러한 broker는 Microsoft Authenticator 및 Intune 회사 포털 앱을 통해 제공됩니다. Windows 10에서 이 기능은 운영 체제에 기본적으로 제공되는 기술적으로 웹 인증 broker라고 알려진 계정 선택기로 제공됩니다.|


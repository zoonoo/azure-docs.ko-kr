---
title: On-premises password protection FAQ - Azure Active Directory
description: 온-프레미스 Azure AD 암호 보호 FAQ
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ee5d6328c6a3e4ea0b4a6359d4a21494e3ae62c
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381699"
---
# <a name="azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Azure AD 암호 보호 온-프레미스 - 질문과 대답

This section provides answers to many commonly asked questions about Azure AD Password Protection.

## <a name="general-questions"></a>일반적인 질문

**Q: What guidance should users be given on how to select a secure password?**

이 항목에 대한 Microsoft의 현재 지침은 다음 링크에서 찾을 수 있습니다.

[Microsoft 암호 지침](https://www.microsoft.com/research/publication/password-guidance)

**Q: Is on-premises Azure AD Password Protection supported in non-public clouds?**

아니요 - 온-프레미스 Azure AD 암호 보호는 퍼블릭 클라우드에서만 지원됩니다. 비퍼블릭 클라우드가 지원되는 날짜는 발표되지 않았습니다.

The Azure AD portal does allow modification of the on-premises-specific "Password protection for Windows Server Active Directory" configuration even in non-public clouds; such changes will be persisted but otherwise will never take effect. Registration of on-premises proxy agents or forests is unsupported when non-public cloud credentials are used, and any such registration attempts will always fail.

**Q: How can I apply Azure AD Password Protection benefits to a subset of my on-premises users?**

지원되지 않습니다. Azure AD 암호 보호가 배포되고 사용하도록 설정되면 모든 사용자가 차별 없이 동등한 보안 혜택을 받습니다.

**Q: What is the difference between a password change and a password set (or reset)?**

A password change is when a user chooses a new password after proving they have knowledge of the old password. For example, a password change is what happens when a user logs into Windows and is then prompted to choose a new password.

A password set (sometimes called a password reset) is when an administrator replaces the password on an account with a new password, for example by using the Active Directory Users and Computers management tool. This operation requires a high level of privilege (usually Domain Admin), and the person performing the operation usually does not have knowledge of the old password. Help-desk scenarios often perform password sets, for instance when assisting a user who has forgotten their password. You will also see password set events when a brand new user account is being created for the first time with a password.

The password validation policy behaves the same regardless of whether a password change or set is being done. The Azure AD Password Protection DC Agent service does log different events to inform you whether a password change or set operation was done.  See [Azure AD Password Protection monitoring and logging](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor).

**Q: Why are duplicated password rejection events logged when attempting to set a weak password using the Active Directory Users and Computers management snap-in?**

The Active Directory Users and Computers management snap-in will first try to set the new password using the Kerberos protocol. Upon failure, the snap-in will make a second attempt to set the password using a legacy (SAM RPC) protocol (the specific protocols used are not important). If the new password is considered weak by Azure AD Password Protection, this snap-in behavior will result in two sets of password reset rejection events being logged.

**Q: Why are Azure AD Password Protection password validation events being logged with an empty user name?**

Active Directory supports the ability to test a password to see if it passes the domain's current password complexity requirements, for example using the [NetValidatePasswordPolicy](https://docs.microsoft.com/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) api. When a password is validated in this way, the testing also includes validation by password-filter-dll based products such as Azure AD Password Protection - but the user names passed to a given password filter dll will be empty. In this scenario, Azure AD Password Protection will still validate the password using the currently in-effect password policy and will issue an event log message to capture the outcome, however the event log message will have empty user name fields.

**Q: Is it supported to install Azure AD Password Protection side by side with other password-filter-based products?**

예. 등록된 여러 암호 필터 DLL에 대한 지원은 Windows의 핵심 기능이며 Azure AD 암호 보호와 관련이 없습니다. 암호가 수락되기 전에 모든 등록된 암호 필터 dll에 동의해야 합니다.

**Q: How can I deploy and configure Azure AD Password Protection in my Active Directory environment without using Azure?**

지원되지 않습니다. Azure AD 암호 보호는 온-프레미스 Active Directory 환경으로 지원이 확장되는 Azure 기능입니다.

**Q: How can I modify the contents of the policy at the Active Directory level?**

지원되지 않습니다. The policy can only be administered using the Azure AD portal. 이전 질문도 참조하세요.

**Q: sysvol 복제에 DFSR이 필요한가요?**

FRS(DFSR에 대한 선행 기술)는 대부분의 알려진 문제를 포함하며, 최신 버전의 Windows Server Active Directory에서 완전히 지원되지 않습니다. Azure AD 암호 보호의 제로 테스트는 FRS 구성 도메인에서 수행됩니다.

자세한 내용은 다음 문서를 참조하세요.

[DFSR로 sysvol 복제를 마이그레이션하는 사례](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[FRS의 경우 종료가 가깝습니다.](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

If your domain is not already using DFSR, you MUST migrate it to use DFSR before installing Azure AD Password Protection. For more information, see the following link:

[SYSVOL Replication Migration Guide: FRS to DFS Replication](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> The Azure AD Password Protection DC Agent software will currently install on domain controllers in domains that are still using FRS for sysvol replication, but the software will NOT work properly in this environment. Additional negative side-effects include individual files failing to replicate, and sysvol restore procedures appearing to succeed but silently failing to replicate all files. You should migrate your domain to use DFSR as soon as possible, both for DFSR's inherent benefits and also to unblock the deployment of Azure AD Password Protection. Future versions of the software will be automatically disabled when running in a domain that is still using FRS.

**Q: How much disk space does the feature require on the domain sysvol share?**

정확한 공간 사용량은 Microsoft 전역 금지 목록의 금지된 토큰의 수 및 길이, 테넌트당 사용자 지정 목록, 암호화 오버헤드 등과 같은 요소에 따라 다릅니다. 이러한 목록의 내용은 향후에 증가할 수 있습니다. 이점에 유의하면 이 기능을 위해 도메인 sysvol 공유에 최소 5메가바이트의 공간이 필요한 것으로 예측할 수 있습니다.

**Q: DC 에이전트 소프트웨어를 설치하거나 업그레이드하는 데 다시 부팅이 필요한 이유는 무엇인가요?**

이 요구 사항은 핵심 Windows 동작으로 인해 발생할 수 있습니다.

**Q: 특정 프록시 서버를 사용하도록 DC 에이전트를 구성하는 방법이 있나요?**

아닙니다. 프록시 서버는 상태 비저장 서버이므로 어떤 프록시 서버를 사용하는지는 중요하지 않습니다.

**Q: Is it okay to deploy the Azure AD Password Protection Proxy service side by side with other services such as Azure AD Connect?**

예. Azure AD 암호 보호 프록시 서비스와 Azure AD Connect는 서로 직접적으로 충돌하지 않습니다.

Unfortunately, an incompatibility has been found between the version of the Microsoft Azure AD Connect Agent Updater service that is installed by the Azure AD Password Protection Proxy software and the version of the service that is installed by the [Azure Active Directory Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) software. This incompatibility may result in the Agent Updater service being unable to contact Azure for software updates. It is not recommended to install Azure AD Password Protection Proxy and Azure Active Directory Application Proxy on the same machine.

**Q: In what order should the DC agents and proxies be installed and registered?**

Any ordering of Proxy agent installation, DC agent installation, forest registration, and Proxy registration  is supported.

**Q: Should I be concerned about the performance hit on my domain controllers from deploying this feature?**

Azure AD 암호 보호 DC 에이전트 서비스는 기존의 정상적인 Active Directory 배포에서 도메인 컨트롤러 성능에 크게 영향을 주지 않아야 합니다.

대부분의 Active Directory 배포에서 암호 변경 작업은 지정된 도메인 컨트롤러의 전체 워크로드에서 작은 비율을 차지합니다. 예를 들어, 10,000개의 사용자 계정이 있고 MaxPasswordAge 정책이 30일로 설정된 Active Directory 도메인이 있다고 가정합니다. 평균적으로 이 도메인에서는 매일 10000/30=~333번의 암호 변경 작업이 진행되며, 이 수치는 단일 도메인 컨트롤러라고 해도 많지 않은 작업 수입니다. 이러한 단일 DC에 대한 ~333번의 암호 변경이 1시간 동안 수행되는 최악의 시나리오를 고려해보세요. 예를 들어 이 시나리오는 많은 직원이 모두 월요일 아침에 근무하러 오게 될 때 발생할 수 있습니다. 이러한 경우에도, ~333/60분 = 분당 6번 암호 변경이 확인되며, 역시 이 수치는 큰 부하가 아닙니다.

그러나 현재 도메인 컨트롤러가 이미 성능 제한 수준에서 실행되고 있는 경우(예: CPU, 디스크 공간, 디스크 I/O 등에 따라 혼합됨) 이 기능을 배포하기 전에 추가 도메인 컨트롤러를 추가하거나 사용 가능한 디스크 공간을 확장하는 것이 좋습니다. 위의 sysvol 디스크 공간 사용량에 대한 질문도 참조하세요.

**Q: I want to test Azure AD Password Protection on just a few DCs in my domain. Is it possible to force user password changes to use those specific DCs?**

아닙니다. Windows 클라이언트 OS는 사용자가 암호를 변경할 때 사용되는 도메인 컨트롤러를 제어합니다. The domain controller is selected based on factors such as Active Directory site and subnet assignments, environment-specific network configuration, etc. Azure AD Password Protection does not control these factors and cannot influence which domain controller is selected to change a user's password.

부분적으로 이러한 목표에 도달하는 한 가지 방법은 지정된 Active Directory 사이트의 모든 도메인 컨트롤러에서 Azure AD 암호 보호를 배포하는 것입니다. 이 방법은 해당 사이트에 할당된 Windows 클라이언트와 해당 클라이언트에 로그인하고 암호를 변경하는 사용자를 적절히 검사합니다.

**Q: If I install the Azure AD Password Protection DC Agent service on just the Primary Domain Controller (PDC), will all other domain controllers in the domain also be protected?**

아닙니다. 지정된 비 PDC 도메인 컨트롤러에서 사용자 암호가 변경되면 일반 텍스트 암호가 PDC로 절대 전송되지 않습니다(이 생각은 일반적인 오해임). 지정된 DC에서 새 암호가 수락되면 해당 DC는 해당 암호를 사용하여 암호의 다양한 인증-프로토콜 관련 해시를 만든 후 해당 해시를 디렉터리에 유지합니다. 일반 텍스트 암호는 유지되지 않습니다. 그런 후에 업데이트된 해시가 PDC로 복제됩니다. 경우에 따라 네트워크 토폴로지 및 Active Directory 사이트 디자인과 같은 다양한 요인에 따라 PDC에서 직접 변경될 수 있습니다. (이전 질문을 참조하세요.)

요약하자면, PDC에서 Azure AD 암호 보호 DC 에이전트 서비스를 배포하려면 도메인에서 100%의 기능 보안 검사에 도달해야 합니다. PDC에만 기능을 배포하면 도메인의 다른 DC는 Azure AD 암호 보호 보안 이점을 얻을 수 없습니다.

**Q: Why is custom smart lockout not working even after the agents are installed in my on-premises Active Directory environment?**

Custom smart lockout is only supported in Azure AD. Changes to the custom smart lockout settings in the Azure AD portal have no effect on the on-premises Active Directory environment, even with the agents installed.

**Q: Is a System Center Operations Manager management pack available for Azure AD Password Protection?**

아닙니다.

**Q: Why is Azure AD still rejecting weak passwords even though I've configured the policy to be in Audit mode?**

Audit mode is only supported in the on-premises Active Directory environment. Azure AD is implicitly always in "enforce" mode when it evaluates passwords.

**Q: My users see the traditional Windows error message when a password is rejected by Azure AD Password Protection. Is it possible to customize this error message so that users know what really happened?**

아닙니다. The error message seen by users when a password is rejected by a domain controller is controlled by the client machine, not by the domain controller. This behavior happens whether a password is rejected by the default Active Directory password policies or by a password-filter-based solution such as Azure AD Password Protection.

## <a name="additional-content"></a>추가 콘텐츠

다음 링크는 핵심 Azure AD 암호 보호 설명서에는 포함되지 않지만 기능에 대한 유용한 추가 정보를 제공할 수 있습니다.

[Azure AD Password Protection is now generally available!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Email Phishing Protection Guide – Part 15: Implement the Microsoft Azure AD Password Protection Service (for On-Premises too!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[이제 Azure AD 암호 보호 및 스마트 잠금이 공개 미리 보기로 제공됨](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Microsoft 프리미어\통합 지원 교육 사용 가능

Azure AD 암호 보호에 대해 자세히 알아보고 작업 환경에 배포하려는 경우 프리미어 또는 통합 지원 계약이 있는 고객을 위한 Microsoft 자동 관리 서비스의 활용할 수 있습니다. The service is called Azure Active Directory: Password Protection. 자세한 내용은 기술 계정 관리자에게 문의하세요.

## <a name="next-steps"></a>다음 단계

여기서 답변되지 않은 온-프레미스 Azure AD 암호 보호 질문이 있으면 아래의 피드백 항목을 제출해 주세요. 감사합니다!

[Azure AD 암호 보호 배포](howto-password-ban-bad-on-premises-deploy.md)

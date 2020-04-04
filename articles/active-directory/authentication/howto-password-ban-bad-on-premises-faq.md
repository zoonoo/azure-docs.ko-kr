---
title: 온-프레미스 Azure AD 암호 보호 FAQ
description: 온-프레미스 Active Directory 도메인 서비스 환경에서 Azure AD 암호 보호에 대해 자주 묻는 질문 검토
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16537ba72eb9f11abd8de47256150818c121a140
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652648"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>Azure AD 암호 보호 온-프레미스 자주 묻는 질문

이 섹션에서는 Azure AD 암호 보호에 대해 자주 묻는 많은 질문에 대한 답변을 제공합니다.

## <a name="general-questions"></a>일반적인 질문

**Q: 사용자에게 보안 암호를 선택하는 방법에 대한 지침은 무엇입니까?**

이 항목에 대한 Microsoft의 현재 지침은 다음 링크에서 찾을 수 있습니다.

[Microsoft 암호 지침](https://www.microsoft.com/research/publication/password-guidance)

**Q: 온-프레미스 Azure AD 암호 보호가 비공개 클라우드에서 지원되나요?**

아니요 - 온-프레미스 Azure AD 암호 보호는 퍼블릭 클라우드에서만 지원됩니다. 비퍼블릭 클라우드가 지원되는 날짜는 발표되지 않았습니다.

Azure AD 포털에서는 비공개 클라우드에서도 온-프레미스 별 "Windows 서버 Active Directory용 암호 보호" 구성을 수정할 수 있습니다. 이러한 변경 사항은 유지되지만 그렇지 않으면 적용되지 않습니다. 비퍼블릭 클라우드 자격 증명을 사용할 때 온-프레미스 프록시 에이전트 또는 포리스트등록은 지원되지 않으며 이러한 등록 시도는 항상 실패합니다.

**Q: 온-프레미스 사용자의 하위 집합에 Azure AD 암호 보호 혜택을 적용하려면 어떻게 해야 합니까?**

지원되지 않습니다. Azure AD 암호 보호가 배포되고 사용하도록 설정되면 모든 사용자가 차별 없이 동등한 보안 혜택을 받습니다.

**Q: 암호 변경과 암호 집합(또는 재설정)의 차이점은 무엇입니까?**

암호 변경은 사용자가 이전 암호에 대한 지식을 가지고 있음을 증명한 후 새 암호를 선택하는 경우입니다. 예를 들어 암호 변경은 사용자가 Windows에 로그인한 다음 새 암호를 선택하라는 메시지가 표시될 때 발생하는 것입니다.

암호 집합(암호 재설정이라고도 함)은 관리자가 Active Directory 사용자 및 컴퓨터 관리 도구를 사용하여 계정의 암호를 새 암호로 바꾸는 경우입니다. 이 작업에는 높은 수준의 권한(일반적으로 도메인 관리자)이 필요하며 작업을 수행하는 사람은 일반적으로 이전 암호를 알지 못합니다. 예를 들어 암호를 잊어버린 사용자를 지원할 때 도움말 데스크 시나리오는 암호 집합을 수행하는 경우가 많습니다. 또한 암호로 새 사용자 계정을 처음 만들 때 암호 집합 이벤트도 표시됩니다.

암호 유효성 검사 정책은 암호 변경 또는 집합수행 여부에 관계없이 동일하게 수행됩니다. Azure AD 암호 보호 DC 에이전트 서비스는 다른 이벤트를 기록하여 암호 변경 또는 설정 작업이 수행되었는지 여부를 알려줍니다.  [Azure AD 암호 보호 모니터링 및 로깅](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor)을 참조하십시오.

**Q: Active Directory 사용자 및 컴퓨터 관리 스냅인을 사용하여 약한 암호를 설정하려고 할 때 중복된 암호 거부 이벤트가 기록되는 이유는 무엇입니까?**

Active Directory 사용자 및 컴퓨터 관리 스냅인먼저 Kerberos 프로토콜을 사용하여 새 암호를 설정하려고 합니다. 오류가 발생하면 스냅인은 레거시(SAM RPC) 프로토콜을 사용하여 암호를 설정하는 두 번째 시도를 합니다(사용된 특정 프로토콜은 중요하지 않음). 새 암호가 Azure AD 암호 보호에 의해 약한 것으로 간주되면 이 스냅인 동작으로 인해 두 세트의 암호 재설정 거부 이벤트가 기록됩니다.

**Q: Azure AD 암호 보호 암호 유효성 검사 이벤트가 빈 사용자 이름으로 기록되는 이유는 무엇입니까?**

Active Directory는 [NetValidatePasswordPolicy](https://docs.microsoft.com/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) API를 사용하는 등 도메인의 현재 암호 복잡성 요구 사항을 통과하는지 확인하기 위해 암호를 테스트하는 기능을 지원합니다. 이러한 방식으로 암호의 유효성을 검사하면 테스트에는 Azure AD 암호 보호와 같은 암호 필터 dll 기반 제품의 유효성 검사도 포함되지만 지정된 암호 필터 dll에 전달된 사용자 이름은 비어 있습니다. 이 시나리오에서 Azure AD 암호 보호는 현재 사용 중인 암호 정책을 사용하여 암호의 유효성을 검사하고 결과를 캡처하는 이벤트 로그 메시지를 발행하지만 이벤트 로그 메시지에는 빈 사용자 이름 필드가 있습니다.

**Q: 다른 암호 필터 기반 제품과 나란히 Azure AD 암호 보호를 설치하도록 지원되나요?**

예. 등록된 여러 암호 필터 DLL에 대한 지원은 Windows의 핵심 기능이며 Azure AD 암호 보호와 관련이 없습니다. 암호가 수락되기 전에 모든 등록된 암호 필터 dll에 동의해야 합니다.

**Q: Azure를 사용하지 않고 Active Directory 환경에서 Azure AD 암호 보호를 배포하고 구성할 수 있는 방법은 무엇입니까?**

지원되지 않습니다. Azure AD 암호 보호는 온-프레미스 Active Directory 환경으로 지원이 확장되는 Azure 기능입니다.

**Q: Active Directory 수준에서 정책 내용을 수정하면 어떻게 해야 합니까?**

지원되지 않습니다. 정책은 Azure AD 포털을 통해서만 관리할 수 있습니다. 이전 질문도 참조하세요.

**Q: sysvol 복제에 DFSR이 필요한가요?**

FRS(DFSR에 대한 선행 기술)는 대부분의 알려진 문제를 포함하며, 최신 버전의 Windows Server Active Directory에서 완전히 지원되지 않습니다. Azure AD 암호 보호의 제로 테스트는 FRS 구성 도메인에서 수행됩니다.

자세한 내용은 다음 문서를 참조하세요.

[DFSR로 sysvol 복제를 마이그레이션하는 사례](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[FRS의 경우 종료가 가깝습니다.](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

도메인이 아직 DFSR을 사용하고 있지 않은 경우 Azure AD 암호 보호를 설치하기 전에 DFSR을 사용하도록 도메인을 마이그레이션해야 합니다. 자세한 내용은 다음 링크를 참조하십시오.

[SYSVOL 복제 마이그레이션 가이드: FRS에서 DFS 복제](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> Azure AD 암호 보호 DC 에이전트 소프트웨어는 현재 sysvol 복제를 위해 FRS를 사용하고 있는 도메인의 도메인 컨트롤러에 설치되지만 이 환경에서는 소프트웨어가 제대로 작동하지 않습니다. 추가 부정적인 부작용은 개별 파일을 복제하지 못하고, sysvol 복원 프로시저가 성공하는 것처럼 보이지만 모든 파일을 자동으로 복제하지 못하는 것을 포함합니다. DFSR의 고유한 이점과 Azure AD 암호 보호 배포를 차단 해제하려면 가능한 한 빨리 DFSR을 사용하도록 도메인을 마이그레이션해야 합니다. FRS를 여전히 사용 중인 도메인에서 실행할 때 소프트웨어의 향후 버전이 자동으로 비활성화됩니다.

**Q: 이 기능은 도메인 sysvol 공유에 필요한 디스크 공간입니다.**

정확한 공간 사용량은 Microsoft 전역 금지 목록의 금지된 토큰의 수 및 길이, 테넌트당 사용자 지정 목록, 암호화 오버헤드 등과 같은 요소에 따라 다릅니다. 이러한 목록의 내용은 향후에 증가할 수 있습니다. 이점에 유의하면 이 기능을 위해 도메인 sysvol 공유에 최소 5메가바이트의 공간이 필요한 것으로 예측할 수 있습니다.

**Q: DC 에이전트 소프트웨어를 설치하거나 업그레이드하는 데 다시 부팅이 필요한 이유는 무엇인가요?**

이 요구 사항은 핵심 Windows 동작으로 인해 발생할 수 있습니다.

**Q: 특정 프록시 서버를 사용하도록 DC 에이전트를 구성하는 방법이 있나요?**

아니요. 프록시 서버는 상태 비저장 서버이므로 어떤 프록시 서버를 사용하는지는 중요하지 않습니다.

**Q: Azure AD 연결과 같은 다른 서비스와 함께 Azure AD 암호 보호 프록시 서비스를 나란히 배포해도 되나요?**

예. Azure AD 암호 보호 프록시 서비스와 Azure AD Connect는 서로 직접적으로 충돌하지 않습니다.

안타깝게도 Azure AD 암호 보호 프록시 소프트웨어에서 설치한 Microsoft Azure AD 연결 에이전트 업데이트 서비스 버전과 [Azure Active Directory 응용 프로그램 프록시](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) 소프트웨어에서 설치한 서비스 버전 간에 비호환성이 발견되었습니다. 이 비호환성으로 인해 에이전트 Updater 서비스가 Azure에 소프트웨어 업데이트를 문의할 수 없게 될 수 있습니다. 동일한 컴퓨터에 Azure AD 암호 보호 프록시 및 Azure Active Directory 응용 프로그램 프록시를 설치하지 않는 것이 좋습니다.

**Q: DC 에이전트와 프록시는 어떤 순서로 설치및 등록해야 합니까?**

프록시 에이전트 설치, DC 에이전트 설치, 포리스트 등록 및 프록시 등록의 모든 순서가 지원됩니다.

**Q: 이 기능을 배포할 때 도메인 컨트롤러의 성능 저하에 대해 염려해야 합니까?**

Azure AD 암호 보호 DC 에이전트 서비스는 기존의 정상적인 Active Directory 배포에서 도메인 컨트롤러 성능에 크게 영향을 주지 않아야 합니다.

대부분의 Active Directory 배포에서 암호 변경 작업은 지정된 도메인 컨트롤러의 전체 워크로드에서 작은 비율을 차지합니다. 예를 들어, 10,000개의 사용자 계정이 있고 MaxPasswordAge 정책이 30일로 설정된 Active Directory 도메인이 있다고 가정합니다. 평균적으로 이 도메인에서는 매일 10000/30=~333번의 암호 변경 작업이 진행되며, 이 수치는 단일 도메인 컨트롤러라고 해도 많지 않은 작업 수입니다. 이러한 단일 DC에 대한 ~333번의 암호 변경이 1시간 동안 수행되는 최악의 시나리오를 고려해보세요. 예를 들어 이 시나리오는 많은 직원이 모두 월요일 아침에 근무하러 오게 될 때 발생할 수 있습니다. 이러한 경우에도, ~333/60분 = 분당 6번 암호 변경이 확인되며, 역시 이 수치는 큰 부하가 아닙니다.

그러나 현재 도메인 컨트롤러가 이미 성능 제한 수준에서 실행되고 있는 경우(예: CPU, 디스크 공간, 디스크 I/O 등에 따라 혼합됨) 이 기능을 배포하기 전에 추가 도메인 컨트롤러를 추가하거나 사용 가능한 디스크 공간을 확장하는 것이 좋습니다. 위의 sysvol 디스크 공간 사용량에 대한 질문도 참조하세요.

**Q: 도메인의 몇 DC에서 Azure AD 암호 보호를 테스트하려고 합니다. 사용자 암호 변경을 강제로 사용하여 특정 DC를 사용할 수 있습니까?**

아니요. Windows 클라이언트 OS는 사용자가 암호를 변경할 때 사용되는 도메인 컨트롤러를 제어합니다. 도메인 컨트롤러는 Active Directory 사이트 및 서브넷 할당, 환경별 네트워크 구성 등과 같은 요인에 따라 선택됩니다. Azure AD 암호 보호는 이러한 요소를 제어하지 않으며 사용자의 암호를 변경하도록 선택된 도메인 컨트롤러에 영향을 미치지 않습니다.

부분적으로 이러한 목표에 도달하는 한 가지 방법은 지정된 Active Directory 사이트의 모든 도메인 컨트롤러에서 Azure AD 암호 보호를 배포하는 것입니다. 이 방법은 해당 사이트에 할당된 Windows 클라이언트와 해당 클라이언트에 로그인하고 암호를 변경하는 사용자를 적절히 검사합니다.

**Q: Azure AD 암호 보호 DC 에이전트 서비스를 PDC(기본 도메인 컨트롤러)에만 설치하는 경우 도메인의 다른 모든 도메인 컨트롤러도 보호됩니까?**

아니요. 지정된 비 PDC 도메인 컨트롤러에서 사용자 암호가 변경되면 일반 텍스트 암호가 PDC로 절대 전송되지 않습니다(이 생각은 일반적인 오해임). 지정된 DC에서 새 암호가 수락되면 해당 DC는 해당 암호를 사용하여 암호의 다양한 인증-프로토콜 관련 해시를 만든 후 해당 해시를 디렉터리에 유지합니다. 일반 텍스트 암호는 유지되지 않습니다. 그런 후에 업데이트된 해시가 PDC로 복제됩니다. 경우에 따라 네트워크 토폴로지 및 Active Directory 사이트 디자인과 같은 다양한 요인에 따라 PDC에서 직접 변경될 수 있습니다. (이전 질문을 참조하세요.)

요약하자면, PDC에서 Azure AD 암호 보호 DC 에이전트 서비스를 배포하려면 도메인에서 100%의 기능 보안 검사에 도달해야 합니다. PDC에만 기능을 배포하면 도메인의 다른 DC는 Azure AD 암호 보호 보안 이점을 얻을 수 없습니다.

**Q: 에이전트가 온-프레미스 Active Directory 환경에 설치된 후에도 사용자 지정 스마트 잠금이 작동하지 않는 이유는 무엇입니까?**

사용자 지정 스마트 잠금은 Azure AD에서만 지원됩니다. Azure AD 포털의 사용자 지정 스마트 잠금 설정을 변경하면 에이전트가 설치된 경우에도 온-프레미스 Active Directory 환경에는 영향을 주지 않습니다.

**Q: Azure AD 암호 보호에 시스템 센터 운영 관리자 관리 팩을 사용할 수 있습니까?**

아니요.

**Q: Azure AD가 감사 모드로 정책을 구성했음에도 불구하고 약한 암호를 거부하는 이유는 무엇입니까?**

감사 모드는 온-프레미스 Active Directory 환경에서만 지원됩니다. Azure AD는 암호를 평가할 때 항상 암시적으로 "적용" 모드에 있습니다.

**Q: Azure AD 암호 보호에 의해 암호가 거부되면 사용자에게 기존 Windows 오류 메시지가 표시됩니다. 사용자가 실제로 무슨 일이 일어났는지 알 수 있도록 이 오류 메시지를 사용자 지정할 수 있습니까?**

아니요. 도메인 컨트롤러에서 암호를 거부할 때 사용자에게 보이는 오류 메시지는 도메인 컨트롤러가 아닌 클라이언트 컴퓨터에 의해 제어됩니다. 이 동작은 기본 Active Directory 암호 정책또는 Azure AD 암호 보호와 같은 암호 필터 기반 솔루션에 의해 암호가 거부되는지 여부에 관계없이 발생합니다.

## <a name="additional-content"></a>추가 콘텐츠

다음 링크는 핵심 Azure AD 암호 보호 설명서에는 포함되지 않지만 기능에 대한 유용한 추가 정보를 제공할 수 있습니다.

[Azure AD 암호 보호를 일반적으로 사용할 수 있습니다!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[이메일 피싱 보호 가이드 – 파트 15: Microsoft Azure AD 암호 보호 서비스 구현(온-프레미스용)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[이제 Azure AD 암호 보호 및 스마트 잠금이 공개 미리 보기로 제공됨](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Microsoft 프리미어\통합 지원 교육 사용 가능

Azure AD 암호 보호에 대해 자세히 알아보고 작업 환경에 배포하려는 경우 프리미어 또는 통합 지원 계약이 있는 고객을 위한 Microsoft 자동 관리 서비스의 활용할 수 있습니다. 이 서비스를 Azure Active Directory: 암호 보호라고 합니다. 자세한 내용은 기술 계정 관리자에게 문의하세요.

## <a name="next-steps"></a>다음 단계

여기서 답변되지 않은 온-프레미스 Azure AD 암호 보호 질문이 있으면 아래의 피드백 항목을 제출해 주세요. 감사합니다!

[Azure AD 암호 보호 배포](howto-password-ban-bad-on-premises-deploy.md)

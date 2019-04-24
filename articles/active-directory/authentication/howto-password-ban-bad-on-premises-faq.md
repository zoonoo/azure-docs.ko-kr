---
title: 온-프레미스 Azure AD 암호 보호 FAQ-Azure Active Directory
description: 온-프레미스 Azure AD 암호 보호 FAQ
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8c3395345093ae9a3d35deb27a08f12d331c9f3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60358160"
---
# <a name="azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Azure AD 암호 보호 온-프레미스 - 질문과 대답

## <a name="general-questions"></a>일반적인 질문

**Q: 사용자에게 보안 암호 선택 방법에 대해 어떤 지침을 제공해야 하나요?**

이 항목에 대한 Microsoft의 현재 지침은 다음 링크에서 찾을 수 있습니다.

[Microsoft 암호 지침](https://www.microsoft.com/en-us/research/publication/password-guidance)

**Q: 온-프레미스 Azure AD 암호 보호는 비공용 클라우드에서 지원되나요?**

아니요 - 온-프레미스 Azure AD 암호 보호는 공용 클라우드에서만 지원됩니다. 비공용 클라우드가 지원되는 날짜는 발표되지 않았습니다.

**Q: Azure AD 암호 보호 혜택을 내 온-프레미스 사용자의 하위 집합에 적용하려면 어떻게 해야 할까요?**

지원되지 않습니다. Azure AD 암호 보호가 배포되고 사용하도록 설정되면 모든 사용자가 차별 없이 동등한 보안 혜택을 받습니다.

**Q: 암호 변경을 암호 설정 (또는 다시 설정) 간의 차이 무엇입니까?**

암호 변경을 때 사용자가 이전 암호를 알고 증명 한 후 새 암호를 선택 합니다. 예를 들어,이 사용자가 Windows에 로그인 하 고 새 암호를 선택 하 라는 메시지가 표시 됩니다.

관리자가 대체는 계정의 암호를 새 암호를 사용 하 여 예를 들어 Active Directory 사용자 및 컴퓨터 관리 도구를 사용 하 여 때 (암호 재설정을 라고도 함) 암호 집합을 표시 합니다. 이 작업을 수행 하려면 높은 수준의 권한 (일반적으로 도메인 관리자), 했으며 일반적으로 작업을 수행 하는 사람에 이전 암호를 알고 있어야 합니다. 지원 센터 시나리오 종종 그렇게 예를 들어 경우 암호를 잊어버린 경우에 사용자를 지원 합니다. 암호는 새로운 사용자 계정 암호를 사용 하 여 처음으로 만들어질 때 이벤트를 설정도 표시 됩니다.

암호 유효성 검사 정책을 암호 변경 또는 집합 수행 되는 여부에 관계 없이 동일 하 게 동작 합니다. 암호 변경 여부를 사용자에 게 알리는 다른 이벤트를 기록 하는 Azure AD 암호 보호 DC 에이전트 서비스를 않습니다 또는 설정 작업이 완료 되었습니다.  참조 [Azure AD 암호 보호 모니터링 및 로깅](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor)합니다.

**Q: Azure AD 암호 보호는 다른 암호 필터 기반 제품과 함께 설치할 수 있나요?**

예. 등록된 여러 암호 필터 DLL에 대한 지원은 Windows의 핵심 기능이며 Azure AD 암호 보호와 관련이 없습니다. 암호가 수락되기 전에 모든 등록된 암호 필터 dll에 동의해야 합니다.

**Q: Azure를 사용하지 않고 Active Directory 환경 내에서 Azure AD 암호 보호를 배포 및 구성하려면 어떻게 해야 하나요?**

지원되지 않습니다. Azure AD 암호 보호는 온-프레미스 Active Directory 환경으로 지원이 확장되는 Azure 기능입니다.

**Q: Active Directory 수준에서 정책의 콘텐츠를 수정하려면 어떻게 해야 하나요?**

지원되지 않습니다. 정책은 Azure AD 관리 포털을 통해서만 관리할 수 있습니다. 이전 질문도 참조하세요.

**Q: sysvol 복제에 DFSR이 필요한가요?**

FRS(DFSR에 대한 선행 기술)는 대부분의 알려진 문제를 포함하며, 최신 버전의 Windows Server Active Directory에서 완전히 지원되지 않습니다. Azure AD 암호 보호의 제로 테스트는 FRS 구성 도메인에서 수행됩니다.

자세한 내용은 다음 문서를 참조하세요.

[DFSR로 sysvol 복제를 마이그레이션하는 사례](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[FRS의 경우 종료가 가깝습니다.](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**Q: 이 기능을 사용하려면 도메인 sysvol 공유에 얼마나 많은 공간이 필요한가요?**

정확한 공간 사용량은 Microsoft 전역 금지 목록의 금지된 토큰의 수 및 길이, 테넌트당 사용자 지정 목록, 암호화 오버헤드 등과 같은 요소에 따라 다릅니다. 이러한 목록의 내용은 향후에 증가할 수 있습니다. 이점에 유의하면 이 기능을 위해 도메인 sysvol 공유에 최소 5메가바이트의 공간이 필요한 것으로 예측할 수 있습니다.

**Q: DC 에이전트 소프트웨어를 설치하거나 업그레이드하는 데 다시 부팅이 필요한 이유는 무엇인가요?**

이 요구 사항은 핵심 Windows 동작으로 인해 발생할 수 있습니다.

**Q: 특정 프록시 서버를 사용하도록 DC 에이전트를 구성하는 방법이 있나요?**

아니요. 프록시 서버는 상태 비저장 서버이므로 어떤 프록시 서버를 사용하는지는 중요하지 않습니다.

**Q: Azure AD 암호 보호 프록시 서비스를 Azure AD Connect 등의 다른 서비스와 함께 배포해도 되나요?**

예. Azure AD 암호 보호 프록시 서비스와 Azure AD Connect는 서로 직접적으로 충돌하지 않습니다.

**Q: 어떤 순서로 DC 에이전트 및 프록시를 설치 하 고 해야 등록?**

프록시 에이전트를 설치, DC 에이전트 설치, 포리스트 등록 및 프록시 등록의 순서는 지원 됩니다.

**Q: 이 기능을 배포할 때 도메인 컨트롤러의 성능 저하가 우려될 수 있나요?**

Azure AD 암호 보호 DC 에이전트 서비스는 기존의 정상적인 Active Directory 배포에서 도메인 컨트롤러 성능에 크게 영향을 주지 않아야 합니다.

대부분의 Active Directory 배포에서 암호 변경 작업은 지정된 도메인 컨트롤러의 전체 워크로드에서 작은 비율을 차지합니다. 예를 들어, 10,000개의 사용자 계정이 있고 MaxPasswordAge 정책이 30일로 설정된 Active Directory 도메인이 있다고 가정합니다. 평균적으로 이 도메인에서는 매일 10000/30=~333번의 암호 변경 작업이 진행되며, 이 수치는 단일 도메인 컨트롤러라고 해도 많지 않은 작업 수입니다. 이러한 단일 DC에 대한 ~333번의 암호 변경이 1시간 동안 수행되는 최악의 시나리오를 고려해보세요. 예를 들어 이 시나리오는 많은 직원이 모두 월요일 아침에 근무하러 오게 될 때 발생할 수 있습니다. 이러한 경우에도, ~333/60분 = 분당 6번 암호 변경이 확인되며, 역시 이 수치는 큰 부하가 아닙니다.

그러나 현재 도메인 컨트롤러가 이미 성능 제한 수준에서 실행되고 있는 경우(예: CPU, 디스크 공간, 디스크 I/O 등에 따라 혼합됨) 이 기능을 배포하기 전에 추가 도메인 컨트롤러를 추가하거나 사용 가능한 디스크 공간을 확장하는 것이 좋습니다. 위의 sysvol 디스크 공간 사용량에 대한 질문도 참조하세요.

**Q: 도메인의 일부 DC에서만 Azure AD 암호 보호를 테스트하려고 합니다. 강제로 사용자 암호 변경에 해당 특정 DC를 사용하도록 할 수 있나요?**

아니요. Windows 클라이언트 OS는 사용자가 암호를 변경할 때 사용되는 도메인 컨트롤러를 제어합니다. 도메인 컨트롤러는 Active Directory 사이트 및 서브넷 할당, 환경별 네트워크 구성 등의 요소에 따라 선택됩니다. Azure AD 암호 보호는 이러한 요소를 제어하지 않으며 사용자 암호를 변경하기 위해 선택하는 도메인 컨트롤러에 영향을 주지 않습니다.

부분적으로 이러한 목표에 도달하는 한 가지 방법은 지정된 Active Directory 사이트의 모든 도메인 컨트롤러에서 Azure AD 암호 보호를 배포하는 것입니다. 이 방법은 해당 사이트에 할당된 Windows 클라이언트와 해당 클라이언트에 로그인하고 암호를 변경하는 사용자를 적절히 검사합니다.

**Q: PDC(주 도메인 컨트롤러)에만 Azure AD 암호 보호 DC 에이전트 서비스를 설치해도 도메인의 다른 모든 도메인 컨트롤러가 보호되나요?**

아니요. 지정된 비 PDC 도메인 컨트롤러에서 사용자 암호가 변경되면 일반 텍스트 암호가 PDC로 절대 전송되지 않습니다(이 생각은 일반적인 오해임). 지정된 DC에서 새 암호가 수락되면 해당 DC는 해당 암호를 사용하여 암호의 다양한 인증-프로토콜 관련 해시를 만든 후 해당 해시를 디렉터리에 유지합니다. 일반 텍스트 암호는 유지되지 않습니다. 그런 후에 업데이트된 해시가 PDC로 복제됩니다. 경우에 따라 네트워크 토폴로지 및 Active Directory 사이트 디자인과 같은 다양한 요인에 따라 PDC에서 직접 변경될 수 있습니다. (이전 질문을 참조하세요.)

요약하자면, PDC에서 Azure AD 암호 보호 DC 에이전트 서비스를 배포하려면 도메인에서 100%의 기능 보안 검사에 도달해야 합니다. PDC에만 기능을 배포하면 도메인의 다른 DC는 Azure AD 암호 보호 보안 이점을 얻을 수 없습니다.

**Q: Azure AD 암호 보호에 System Center Operations Manager 관리 팩을 사용할 수 있나요?**

아니요.

**Q: 이유는 Azure 여전히 거부 취약 한 암호 감사 모드로 설정 될 정책을 구성 하는 경우에?**

감사 모드는 온-프레미스 Active Directory 환경에만 지원 됩니다. Azure는 암시적으로 항상 "강제 적용" 모드에서 암호를 평가 하는 경우.

## <a name="additional-content"></a>추가 콘텐츠

다음 링크는 핵심 Azure AD 암호 보호 설명서에는 포함되지 않지만 기능에 대한 유용한 추가 정보를 제공할 수 있습니다.

[Azure AD 암호 보호 출시 되었습니다!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[이메일 피싱 보호 가이드 - 15부: Microsoft Azure AD 암호 보호 서비스 구현(온-프레미스에도 사용 가능)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[이제 Azure AD 암호 보호 및 스마트 잠금이 공개 미리 보기로 제공됨](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Microsoft 프리미어\통합 지원 교육 사용 가능

Azure AD 암호 보호에 대해 자세히 알아보고 작업 환경에 배포하려는 경우 프리미어 또는 통합 지원 계약이 있는 고객을 위한 Microsoft 자동 관리 서비스의 활용할 수 있습니다. 이 서비스를 Azure Active Directory: 암호 보호라고 합니다. 자세한 내용은 기술 계정 관리자에게 문의하세요.

## <a name="next-steps"></a>다음 단계

여기서 답변되지 않은 온-프레미스 Azure AD 암호 보호 질문이 있으면 아래의 피드백 항목을 제출해 주세요. 감사합니다!

[Azure AD 암호 보호 배포](howto-password-ban-bad-on-premises-deploy.md)

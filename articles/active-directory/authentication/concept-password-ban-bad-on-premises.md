---
title: Azure AD 암호 보호 미리 보기
description: Azure AD 암호 보호 미리 보기를 사용하여 온-프레미스 Active Directory에서 약한 암호 금지하기
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1beae186f6eb276b9aa302d3d51f0ba8688e591
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415751"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>미리 보기: Windows Server Active Directory에 Azure AD 암호 보호 강제 적용

|     |
| --- |
| Azure AD 암호 보호 및 사용자 지정 금지 암호 목록은 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

Azure AD 암호 보호는 조직의 암호 정책을 강화하기 위해 Azure AD(Azure Active Directory)에 제공되는 공개 미리 보기의 새로운 기능입니다. Azure AD 암호 보호의 온-프레미스 배포에는 Azure AD에 저장된 전역 및 사용자 지정 금지 암호 목록이 모두 사용되고 Azure AD 클라우드 기반 변경 내용과 동일한 검사를 온-프레미스에서 수행합니다.

## <a name="design-principles"></a>디자인 원칙

Azure AD Password Protection for Active Directory는 다음 원칙을 염두에 두고 설계되었습니다.

* 도메인 컨트롤러 없이 인터넷과 직접 통신할 수 있습니다.
* 새 네트워크 포트가 도메인 컨트롤러에서 열려 있지 않습니다.
* Active Directory 스키마 변경이 필요하지 않습니다. 기존 Active Directory 컨테이너 및 serviceConnectionPoint 스키마 개체가 소프트웨어에 사용됩니다.
* 최소 Active Directory 도메인 또는 포리스트 기능 수준(DFL\FFL) 요구 사항은 없습니다.
* 이 소프트웨어는 보호하는 Active Directory 도메인의 계정을 만들거나 요구하지 않습니다.
* 사용자 일반 텍스트 암호가 절대로 도메인 컨트롤러를 벗어나지 않습니다(암호 유효성 검사 작업 중에 또는 그 외에도 어느 때라도).
* 증분 배포는 암호 정책이 도메인 컨트롤러 에이전트가 설치된 위치에만 적용된다는 절충안과 함께 지원됩니다.
* 어디서나 암호 보호 보안 기능이 적용되도록 모든 DC에 DC 에이전트를 설치하는 것이 좋습니다.

## <a name="architectural-diagram"></a>아키텍처 다이어그램

온-프레미스 Active Directory 환경에 Azure AD 암호 보호를 배포하기 전에 기본 디자인 및 기능 개념을 이해해야 합니다. 다음 다이어그램은 Azure AD 암호 보호 구성 요소가 함께 작동하는 방법을 보여줍니다.

![Azure AD 암호 보호 구성 요소가 함께 작동하는 방법](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

위 다이어그램은 Azure AD 암호 보호를 구성하는 세 가지 기본 소프트웨어 구성 요소를 보여줍니다.

* Azure AD 암호 보호 프록시 서비스는 현재 Active Directory 포리스트의 도메인에 조인된 모든 머신에서 실행됩니다. 주 목적은 도메인 컨트롤러의 암호 정책 다운로드 요청을 Azure AD로 전달하고 Azure AD의 응답을 도메인 컨트롤러로 반환하는 것입니다.
* Azure AD Password Protection DC 에이전트 암호 필터 dll은 운영 체제의 암호 유효성 검사 요청을 수신하여 도메인 컨트롤러에서 로컬로 실행되는 Azure AD 암호 보호 DC 에이전트 서비스로 전달합니다.
* Azure AD 암호 보호 DC 에이전트 서비스는 DC 에이전트 암호 필터 dll의 암호 유효성 검사 요청을 수신하여 로컬에서 현재 사용 가능한 암호 정책을 사용하여 처리한 다음, 결과(pass\fail)를 반환합니다.

## <a name="theory-of-operations"></a>작업 이론

위의 다이어그램과 디자인 원칙을 고려할 때 Azure AD 암호 보호는 실제로 어떻게 작동할까요?

각 Azure AD 암호 보호 프록시 서비스는 Active Directory에 serviceConnectionPoint 개체를 만들어서 자신을 포리스트의 도메인 컨트롤러에 보급합니다.

각 Azure AD 암호 보호 DC 에이전트 서비스도 Active Directory에 serviceConnectionPoint 개체를 만듭니다. 그러나 이 개체는 보고 및 진단에 주로 사용됩니다.

Azure AD 암호 보호 DC 에이전트 서비스는 Azure AD에서 새 암호 정책 다운로드를 시작하는 일을 담당합니다. 첫 번째 단계는 프록시 serviceConnectionPoint 개체에 대한 포리스트를 쿼리하여 Azure AD 암호 보호 프록시 서비스를 찾는 것입니다. 사용 가능한 프록시 서비스가 발견되면 DC 에이전트 서비스에서 프록시 서비스로 암호 정책 다운로드 요청이 전송되고, 프록시 서비스는 해당 요청을 Azure AD로 보낸 다음, 응답을 DC 에이전트 서비스에 반환합니다. Azure AD에서 새 암호 정책을 받은 후, DC 에이전트 서비스는 도메인의 sysvol 공유의 루트에 있는 전용 폴더에 정책을 저장합니다. 또한 DC 에이전트 서비스는 도메인의 다른 DC 에이전트 서비스로부터 최신 정책이 복제되는 경우 이 폴더를 모니터링합니다.

Azure AD 암호 보호 DC 에이전트 서비스는 서비스 시작 시 항상 새 정책을 요청합니다. 시작된 DC 에이전트 서비스는 현재 로컬로 사용할 수 있는 정책의 수명을 주기적으로(1시간마다) 확인합니다. 현재 정책이 1시간보다 오래된 경우에는 위의 설명처럼 DC 에이전트 서비스가 Azure AD에서 새 정책을 요청하고, 그렇지 않으면 DC 에이전트가 현재 정책을 계속 사용합니다.

Azure AD 암호 보호 DC 에이전트 서비스는 TCP를 통해 RPC(원격 프로시저 호출)를 사용하여 Azure AD 암호 보호 프록시 서비스와 통신합니다. 프록시 서비스는 동적 또는 정적(구성된 대로) RPC 포트에서 호출을 수신 대기합니다.

Azure AD 암호 보호 DC 에이전트는 절대로 네트워크에 사용 가능한 포트에서 수신 대기하지 않고, 프록시 서비스는 절대로 DC 에이전트 서비스를 호출하지 않습니다.

Azure AD 암호 보호 프록시 서비스는 상태 비저장 서비스로, 정책 또는 Azure에서 다운로드한 다른 상태를 절대로 캐시하지 않습니다.

Azure AD 암호 보호 DC 에이전트 서비스는 로컬로 사용 가능한 가장 최근의 암호 정책을 사용하여 사용자의 암호를 평가합니다. 로컬 DC에 사용 가능한 암호 정책이 없는 경우 암호가 자동으로 수락되고 관리자에게 경고하는 이벤트 로그 메시지가 기록됩니다.

Azure AD 암호 보호는 실시간 정책 적용 엔진이 아닙니다. Azure AD에서 암호 정책 구성이 변경되는 시간과 변경 내용이 모든 도메인 컨트롤러에 도달하여 적용되는 시간 사이에 지연이 있을 수 있습니다.

## <a name="foresttenant-binding-for-azure-ad-password-protection"></a>Azure AD 암호 보호에 대한 포리스트/테넌트 바인딩

Active Directory 포리스트에 Azure AD 암호 보호를 배포하려면 Active Directory 포리스트 및 배포된 Azure AD 암호 보호 프록시 서비스를 Azure AD에 등록해야 합니다. 두 등록(포리스트 및 프록시)은 등록에 사용된 자격 증명을 통해 암시적으로 식별되는 Azure AD 테넌트에 연결됩니다. Azure AD 암호 보호 암호 정책이 다운로드될 때마다 항상 이 테넌트로 한정됩니다(즉, 해당 정책은 언제나 Microsoft 글로벌 금지 암호 목록과 테넌트별 사용자 지정 금지 암호 목록의 조합). 다른 Azure AD 테넌트에 바인딩할 포리스트에서 다른 도메인 또는 프록시를 구성하는 기능은 지원되지 않습니다.

## <a name="license-requirements"></a>라이선스 요구 사항

전역 금지 암호 목록의 이점은 Azure AD(Azure Active Directory)의 모든 사용자에게 적용됩니다.

사용자 지정 금지 암호 목록에는 Azure AD Basic 라이선스가 필요합니다.

Windows Server Active Directory용 Azure AD 암호 보호에는 Azure AD Premium 라이선스가 필요합니다.

비용을 비롯한 추가 라이선스 정보는 [Azure Active Directory 가격 책정 사이트](https://azure.microsoft.com/pricing/details/active-directory/)에서 확인할 수 있습니다.

## <a name="download"></a>다운로드

Azure AD 암호 보호에 필요한 두 가지 설치 관리자는 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=57071)에서 다운로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure AD 암호 보호 배포](howto-password-ban-bad-on-premises-deploy.md)

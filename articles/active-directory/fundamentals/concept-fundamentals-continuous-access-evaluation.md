---
title: Azure AD의 지속적인 액세스 평가
description: Azure AD에서 지속적인 액세스 평가를 통해 사용자 상태의 변화에 더 빠르게 대응
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5b70c11cd6bc24f945b437decf22586cfb97557
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873304"
---
# <a name="continuous-access-evaluation"></a>지속적인 액세스 평가

Azure Active Directory(Azure AD) 및 Office 365와 같은 Microsoft 서비스는 개방형 표준 및 프로토콜을 사용하여 상호 운용성을 최대화합니다. 가장 중요한 것 중 하나는 오픈 ID 연결 (OIDC)입니다. Outlook과 같은 클라이언트 응용 프로그램이 Exchange Online과 같은 서비스에 연결하면 OAuth 2.0 액세스 토큰을 사용하여 API 요청이 승인됩니다. 기본적으로 이러한 액세스 토큰은 1시간 동안 유효합니다. 만료되면 클라이언트가 Azure AD로 다시 리디렉션되어 새로 고칩니다. 또한 사용자 액세스에 대한 정책을 다시 평가할 수 있는 기회를 제공하므로 조건부 액세스 정책으로 인해 토큰을 새로 고치지 않거나 디렉터리에서 사용자가 비활성화되었기 때문에 토큰을 새로 고치지 않도록 선택할 수 있습니다. 

조건부 액세스 정책을 다시 적용하기 위한 액세스 토큰 수명 및 사용자 상태 변경(예: furlough으로 인해 비활성화됨)으로 인해 1시간 지연이 충분하지 않다는 고객의 피드백을 들었습니다.

Microsoft는 OpenID 재단의 [공유 신호 및 이벤트](https://openid.net/wg/sse/) 작업 그룹의 일환으로 CAEP(지속적인 액세스 평가 프로토콜) 이니셔티브에 초기 참여했습니다. ID 공급자와 통신 사업자는 작업 그룹에서 정의한 보안 이벤트 및 신호를 활용하여 액세스를 재인증하거나 종료할 수 있습니다. 그것은 흥미로운 작업이며 많은 플랫폼과 응용 프로그램에서 보안을 향상시킬 것입니다.

보안 상의 이점이 매우 크므로 표준 기관 내에서 계속 작업하는 데 병렬로 Microsoft 관련 초기 구현을 출시하고 있습니다. Microsoft 서비스 전반에 이러한 지속적인 액세스 평가(CAE) 기능을 배포하기 위해 노력하면서 많은 것을 배웠으며 이 정보를 표준 커뮤니티와 공유하고 있습니다. 배포 경험이 더 나은 업계 표준을 알리는 데 도움이 될 수 있으며 비준되면 해당 표준을 구현하여 모든 참여 서비스가 혜택을 누릴 수 있기를 바랍니다.

## <a name="how-does-cae-work-in-microsoft-services"></a>CAE는 Microsoft 서비스에서 어떻게 작동합니까?

우리는 Exchange 및 Teams에 대한 지속적인 액세스 평가의 초기 구현에 초점을 맞추고 있습니다. 앞으로 다른 Microsoft 서비스에 대한 지원을 확장할 수 있기를 바랍니다. 조건부 액세스 정책이 없는 테넌에 대해서만 지속적인 액세스 평가를 사용하도록 설정합니다. CAE의 이 단계에서 얻은 학습을 사용하여 CAE의 지속적인 롤아웃을 알릴 것입니다.

## <a name="service-side-requirements"></a>서비스 측면 요구 사항

지속적인 액세스 평가는 서비스(리소스 공급자)가 Azure AD에서 중요한 이벤트를 구독할 수 있도록 하여 이러한 이벤트를 거의 실시간으로 평가하고 적용할 수 있도록 하여 구현됩니다. 이 초기 CAE 롤아웃에서는 다음 이벤트가 적용됩니다.

- 사용자 계정이 삭제되었거나 사용 안 됨
- 사용자의 암호가 변경되거나 재설정됩니다.
- 관리자는 사용자에 대한 모든 새로 고침 토큰을 명시적으로 해지합니다.
- Azure AD ID 보호에서 검색된 높은 사용자 위험

앞으로 위치 및 장치 상태 변경과 같은 이벤트를 포함하여 더 많은 이벤트를 추가할 수 있기를 바랍니다. **우리의 목표는 즉시 시행하는 것이지만, 경우에 따라 이벤트 전파 시간으로 인해 최대 15 분의 대기 시간이 관찰 될 수 있습니다.** 

## <a name="client-side-claim-challenge"></a>클라이언트 측 클레임 챌린지

지속적인 액세스 평가 전에 클라이언트는 항상 만료되지 않은 한 해당 캐시에서 액세스 토큰을 재생하려고 시도합니다. CAE를 사용하면 리소스 공급자가 만료되지 않은 경우에도 토큰을 거부할 수 있는 새로운 사례를 소개합니다. 캐시된 토큰이 만료되지 않았음에도 캐시를 우회하도록 클라이언트에게 알리기 위해 claim **챌린지라는**메커니즘을 소개합니다. CAE는 클레임 챌린지를 이해하기 위해 클라이언트 업데이트를 필요로 합니다. 지원 클레임 챌린지 아래에 있는 다음 응용 프로그램의 최신 버전:

- 윈도우에 대한 전망 
- 아웃룩 iOS 
- 아웃룩 안드로이드 
- 아웃룩 맥 
- Windows용 팀
- 팀 iOS 
- 팀 안드로이드 
- 팀 맥 

## <a name="token-lifetime"></a>토큰 수명

위험 및 정책이 실시간으로 평가되기 때문에 지속적인 액세스 평가 인식 세션을 협상하는 클라이언트는 기존 정적 액세스 토큰 수명 정책 대신 CAE에 의존하므로 CAE 인식 세션을 협상하는 CAE 지원 클라이언트에서는 구성 가능한 토큰 수명 정책이 더 이상 적용되지 않습니다.

CAE 세션에서 액세스 토큰 수명을 24시간으로 늘릴 것입니다. 해지는 임의기간이 아닌 중요한 이벤트 및 정책 평가에 의해 좌우됩니다. 이렇게 변경하면 보안 태세에 영향을 주지 않으면서 응용 프로그램의 안정성이 향상됩니다. 

## <a name="example-flows"></a>예제 흐름

### <a name="user-revocation-event-flow"></a>사용자 해지 이벤트 흐름:

![사용자 해지 이벤트 흐름](./media/concept-fundamentals-continuous-access-evaluation/user-revocation-event-flow.png)

1. CAE 지원 클라이언트는 일부 리소스에 대한 액세스 토큰을 요청하는 자격 증명 또는 새로 고침 토큰을 AAD에 제공합니다.
1. 액세스 토큰은 클라이언트에 다른 아티팩트와 함께 반환됩니다.
1. 관리자는 [사용자에 대한 모든 새로 고침 토큰을 명시적으로 해지합니다.](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) 해지 이벤트는 Azure AD에서 리소스 공급자에게 전송됩니다.
1. 액세스 토큰은 리소스 공급자에게 제공됩니다. 리소스 공급자는 토큰의 유효성을 평가하고 사용자에 대한 해지 이벤트가 있는지 여부를 확인합니다. 리소스 공급자는 이 정보를 사용하여 리소스에 대한 액세스 권한을 부여할지 여부를 결정합니다.
1. 이 경우 리소스 공급자는 액세스를 거부하고 401+ 클레임 챌린지를 클라이언트로 다시 보냅니다.
1. CAE 지원 클라이언트는 401+ 클레임 챌린지를 이해합니다. 캐시를 무시하고 1단계로 돌아가서 클레임 챌린지와 함께 새로 고침 토큰을 Azure AD로 다시 보냅니다. 그런 다음 Azure AD는 모든 조건을 다시 평가하고 이 경우 사용자에게 다시 인증하라는 메시지를 표시합니다.
 
## <a name="faqs"></a>FAQ

### <a name="what-is-the-lifetime-of-my-access-token"></a>내 액세스 토큰의 수명은 어떻게 됩니까?

CAE 지원 클라이언트를 사용하지 않는 경우 [CTL(구성 가능한 토큰 수명)](../develop/active-directory-configurable-token-lifetimes.md) 미리 보기 기능으로 Access 토큰 수명을 구성하지 않는 한 기본 액세스 토큰 수명은 1시간입니다.

CAE 인식 세션을 협상하는 CAE 지원 클라이언트를 사용하는 경우 액세스 토큰 수명에 대한 CTL 설정이 덮어지고 액세스 토큰 수명이 24시간입니다.

### <a name="how-quick-is-enforcement"></a>시행은 얼마나 빠를까요?

우리의 목표는 즉시 시행하는 것이지만, 경우에 따라 이벤트 전파 시간으로 인해 최대 15분의 대기 시간이 관찰될 수 있습니다.

### <a name="how-will-cae-work-with-sign-in-frequency"></a>CAE는 로그인 빈도로 어떻게 작동합니까?

로그인 빈도는 CAE 유무에 관계없이 적용됩니다.

## <a name="next-steps"></a>다음 단계

[지속적인 액세스 평가 발표](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)

---
title: Azure AD에서 지속적인 액세스 평가
description: Azure AD에서 지속적인 액세스 평가로 사용자 상태 변경에 더 빠르게 응답
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
ms.openlocfilehash: f0cb402741163c657b3e7961eb5a4f9c8e18dafd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84673023"
---
# <a name="continuous-access-evaluation"></a>지속적인 액세스 평가

Azure Active Directory (Azure AD) 및 Office 365와 같은 Microsoft 서비스는 오픈 표준 및 프로토콜을 사용 하 여 상호 운용성을 최대화 합니다. 가장 중요 한 항목 중 하나는 OIDC (Open ID Connect)입니다. Outlook과 같은 클라이언트 응용 프로그램이 Exchange Online과 같은 서비스에 연결 하는 경우 API 요청은 OAuth 2.0 액세스 토큰을 사용 하 여 인증 됩니다. 기본적으로 해당 액세스 토큰은 1 시간 동안 유효 합니다. 만료 되 면 클라이언트는 새로 고침을 위해 Azure AD로 다시 리디렉션됩니다. 또한 사용자 액세스를 위한 정책을 다시 평가 하는 기회를 제공 합니다. 조건부 액세스 정책 때문에 토큰을 새로 고치지 않거나 사용자가 디렉터리에서 사용 하지 않도록 설정 되었기 때문에 토큰을 새로 고치지 않도록 선택할 수 있습니다. 

토큰 만료 및 새로 고침은 업계의 표준 메커니즘입니다. 즉, 고객은 사용자에 대 한 위험 조건이 변경 될 때 (예: 회사 사무실에서 로컬 커피숍으로 이동 또는 블랙 market에서 검색 된 사용자 자격 증명으로 전환) 해당 변경과 관련 된 정책을 적용할 수 있는 시간 사이의 지연에 대 한 문제를 나타냅니다. 토큰 수명이 감소 하는 "무딘 개체" 접근 방식으로 실험, 위험을 제거 하지 않고 사용자 환경 및 안정성을 저하 시킬 수 있습니다.

정책 위반이 나 보안 문제에 대 한 시기 적절 한 응답에는 Azure AD와 같은 토큰 발급자와 Exchange Online과 같은 신뢰 당사자 간의 "대화"가 필요 합니다. 이 양방향 대화는 두 가지 중요 한 기능을 제공 합니다. 신뢰 당사자는 새 위치에서 들어오는 클라이언트와 같이 항목이 변경 된 경우를 확인할 수 있으며 토큰 발급자에 게 알립니다. 또한 토큰 발급자에 게 계정 손상, 비활성화 또는 기타 문제로 인해 지정 된 사용자에 대 한 토큰을 중지 하도록 신뢰 당사자에 게 지시 하는 방법을 제공 합니다. 이 대화에 대 한 메커니즘은 연속 액세스 평가 (CAE)입니다.

Microsoft는 Openid connect Foundation에서 [공유 신호 및 이벤트](https://openid.net/wg/sse/) 작업 그룹의 일부로 서 Caep (연속 액세스 평가 프로토콜) 이니셔티브의 초기 참가자입니다. Id 공급자와 신뢰 당사자는 작업 그룹에 정의 된 보안 이벤트 및 신호를 활용 하 여 액세스를 다시 인증 하거나 종료할 수 있습니다. 이는 흥미로운 작업 이며 많은 플랫폼과 응용 프로그램에서 보안을 향상 시킵니다.

보안상의 이점이 있으므로 Microsoft에서 제공 하는 초기 구현을 표준 본문 내에서 지속적인 작업에 동시에 배포 합니다. Microsoft 서비스에서 이러한 CAE (연속 액세스 평가) 기능을 배포 하기 위해 노력 하 고 있으므로이 정보를 표준 커뮤니티와 공유 하 고 있습니다. Microsoft는이를 배포 하는 데 도움이 될 것입니다 .이를 통해 더 나은 업계 표준에 대 한 정보를 제공 하 고, 비준 한 번 구현 하 여 참여 하는 모든 서비스의 혜택을

## <a name="how-does-cae-work-in-microsoft-services"></a>Microsoft 서비스에서 CAE는 어떻게 작동 하나요?

Microsoft는 Exchange 및 팀에 대 한 지속적인 액세스 평가의 초기 구현을 집중적으로 다룹니다. 향후 다른 Microsoft 서비스에 대 한 지원을 확장 하고자 합니다. 조건부 액세스 정책이 없는 테 넌 트에 대해서만 지속적인 액세스 평가를 사용 하기 시작 합니다. Microsoft는이 CAE 단계에서 학습를 사용 하 여 CAE의 지속적인 출시를 알립니다.

## <a name="service-side-requirements"></a>서비스 측 요구 사항

연속 액세스 평가는 서비스 (리소스 공급자)가 Azure AD의 중요 한 이벤트를 구독 하 여 거의 실시간으로 해당 이벤트를 평가 하 고 적용할 수 있도록 하 여 구현 됩니다. 다음 이벤트는 초기 CAE 롤아웃에서 적용 됩니다.

- 사용자 계정이 삭제 되었거나 사용 하지 않도록 설정 됨
- 사용자 암호 변경 또는 다시 설정
- 사용자에 대해 MFA를 사용할 수 있습니다.
- 관리자가 사용자에 대 한 모든 새로 고침 토큰을 명시적으로 해지
- Azure AD ID 보호에서 검색 된 승격 된 사용자 위험

나중에 위치 및 장치 상태 변경과 같은 이벤트를 포함 하 여 더 많은 이벤트를 추가 하려고 합니다. **우리의 목표는 즉시 적용 되는 것 이지만, 경우에 따라 최대 15 분의 대기 시간은 이벤트 전파 시간으로 인해 관찰 될 수 있습니다**. 

## <a name="client-side-claim-challenge"></a>클라이언트 쪽 클레임 챌린지

연속 액세스를 평가 하기 전에 클라이언트는 만료 되지 않은 경우 항상 캐시에서 액세스 토큰을 재생 하려고 시도 합니다. CAE를 사용 하는 경우 리소스 공급자가 만료 되지 않은 경우에도 토큰을 거부할 수 있는 새로운 사례를 소개 합니다. 캐시 된 토큰이 만료 되지 않았더라도 클라이언트에 캐시를 사용 하지 않도록 알리기 위해 **클레임 챌린지**라는 메커니즘이 도입 되었습니다. CAE에는 클레임 챌린지를 이해 하기 위해 클라이언트 업데이트가 필요 합니다. 아래 응용 프로그램의 최신 버전은 다음과 같은 클레임 챌린지를 지원 합니다.

- Windows 용 Outlook 
- IOS 용 Outlook 
- Android 용 Outlook 
- Outlook for Mac 
- Windows 용 팀
- IOS 용 팀 
- Android 용 팀 
- Mac 용 팀 

## <a name="token-lifetime"></a>토큰 수명

위험 및 정책이 실시간으로 평가 되므로 지속적인 액세스 평가 인식 세션을 협상 하는 클라이언트는 기존 정적 액세스 토큰 수명 정책 대신 CAE를 사용 합니다. 즉, 구성 가능한 토큰 수명 정책이 CAE 인식 세션을 협상 하는 CAE 지원 클라이언트에 더 이상 적용 되지 않습니다.

CAE 세션에서 액세스 토큰 수명이 24 시간으로 증가 합니다. 해지는 임의의 기간이 아니라 중요 한 이벤트 및 정책 평가에 의해 결정 됩니다. 이러한 변경으로 인해 보안 상태에 영향을 주지 않고 응용 프로그램의 안정성이 향상 됩니다. 

## <a name="example-flows"></a>예제 흐름

### <a name="user-revocation-event-flow"></a>사용자 해지 이벤트 흐름:

![사용자 해지 이벤트 흐름](./media/concept-fundamentals-continuous-access-evaluation/user-revocation-event-flow.png)

1. CAE 지원 클라이언트는 일부 리소스에 대 한 액세스 토큰을 요청 하는 AAD에 자격 증명 또는 새로 고침 토큰을 제공 합니다.
1. 액세스 토큰은 클라이언트에 대 한 다른 아티팩트와 함께 반환 됩니다.
1. 관리자는 [사용자에 대 한 모든 새로 고침 토큰을](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)명시적으로 해지 합니다. Azure AD에서 리소스 공급자로 해지 이벤트가 전송 됩니다.
1. 액세스 토큰이 리소스 공급자에 게 표시 됩니다. 리소스 공급자는 토큰의 유효성을 평가 하 고 사용자에 대 한 해지 이벤트가 있는지 여부를 확인 합니다. 리소스 공급자는이 정보를 사용 하 여 리소스에 대 한 액세스 권한을 부여 하도록 결정 합니다.
1. 이 경우 리소스 공급자는 액세스를 거부 하 고 401 + 클레임 챌린지를 다시 클라이언트에 보냅니다.
1. CAE 지원 클라이언트는 401 + 클레임 챌린지를 이해 합니다. 캐시를 우회 하 고 1 단계로 돌아가서 클레임 챌린지와 함께 새로 고침 토큰을 Azure AD로 다시 보냅니다. 그러면 Azure AD는 모든 조건을 다시 평가 하 고이 경우 사용자에 게 다시 인증 하 라는 메시지를 표시 합니다.

## <a name="faqs"></a>FAQ

### <a name="what-is-the-lifetime-of-my-access-token"></a>내 액세스 토큰의 수명은 무엇 인가요?

CAE 지원 클라이언트를 사용 하지 않는 경우, 구성 토큰 수명 [(CTL)](../develop/active-directory-configurable-token-lifetimes.md) 미리 보기 기능을 사용 하 여 액세스 토큰 수명을 구성 하지 않으면 기본 액세스 토큰 수명은 1 시간입니다.

CAE 인식 세션을 협상 하는 CAE 지원 클라이언트를 사용 하는 경우 액세스 토큰 수명에 대 한 CTL 설정이 덮어쓰여집니다. 액세스 토큰 수명은 24 시간이 됩니다.

### <a name="how-quick-is-enforcement"></a>빠른 적용 방법

우리의 목표는 즉시 적용 되는 것 이지만, 경우에 따라 최대 15 분의 대기 시간은 이벤트 전파 시간으로 인해 관찰 될 수 있습니다.

### <a name="how-will-cae-work-with-sign-in-frequency"></a>CAE가 로그인 빈도로 어떻게 작동 하나요?

로그인 빈도는 CAE를 사용 하거나 사용 하지 않고 적용 됩니다.

## <a name="next-steps"></a>다음 단계

[연속 액세스 평가 발표](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)

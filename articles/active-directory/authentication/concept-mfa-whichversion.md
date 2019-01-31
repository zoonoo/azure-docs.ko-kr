---
title: Azure MFA 서버 또는 서비스, 온-프레미스, 클라우드 중 무엇인가요?
description: Azure AD 관리자로서 어떤 MFA 버전을 배포해야 하는지 궁금합니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.openlocfilehash: 783564f8c924cc0b7f94d94748ead35300250ded
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2019
ms.locfileid: "55075570"
---
# <a name="which-version-of-azure-mfa-is-right-for-my-organization"></a>우리 조직에 적합한 Azure MFA 버전은 무엇입니까?

MFA(Multi-Factor Authentication)를 어디에 어떻게 배포할 것인지 결정하기 전에, 세 가지 기본적인 질문에 답해야 합니다.

* [보안을 유지하려는 대상은 무엇입니까](#what-am-i-trying-to-secure)
* [사용자는 어디에 있습니까](#where-are-the-users-located)
* [어떤 기능이 필요합니까?](#what-features-do-i-need)

각각의 다음 섹션에서는 이러한 질문에 답하는 데 도움이 되는 정보를 제공합니다.

## <a name="what-am-i-trying-to-secure"></a>보안을 유지하려는 대상은 무엇입니까?

적합한 2단계 확인 솔루션을 결정하려면, 먼저 추가 인증 방법으로 보안을 유지하려는 대상이 무엇인지 답해야 합니다. Azure에 있는 애플리케이션입니까? 또는 원격 액세스 시스템입니까? 보안을 유지하려는 대상이 무엇인지 결정하면 Multi-Factor Authentication 활성화가 어디에 필요한지 대답할 수 있습니다.

| 보안을 유지하려는 대상은 무엇입니까 | 클라우드의 MFA | MFA 서버  |
| --- |:---:|:---:|
| 자사 Microsoft 앱 |● |● |
| 앱 갤러리의 SaaS 앱 |● |  |
| Azure AD 앱 프록시를 통해 웹 애플리케이션 게시됨 |● |  |
| Azure AD 애플리케이션 프록시를 통해 IIS 애플리케이션 게시되지 않음 | |● |
| VPN, RDG와 같은 원격 액세스 | ● | ● |

## <a name="where-are-the-users-located"></a>사용자는 어디에 있습니까

다음으로, 조직의 사용자가 어디에 있는지 확인하면 MFA 서버를 사용하는 클라우드 또는 온-프레미스 중 어떤 솔루션이 더 적합한지 결정하는 데 도움이 됩니다.

| 사용자 위치 | 클라우드의 MFA | MFA 서버  |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| Azure AD 및 AD FS로 페더레이션을 사용한 온-프레미스 AD |● |● |
| Azure AD Connect를 사용하는 Azure AD 및 온-프레미스 AD - 암호 해시 동기화 또는 통과 인증 없음 |● |● |
| Azure AD Connect를 사용하는 Azure AD 및 온-프레미스 AD - 암호 해시 동기화 또는 통과 인증 사용 |● | |
| 온-프레미스 Active Directory | |● |

## <a name="what-features-do-i-need"></a>어떤 기능이 필요합니까?

다음 표는 클라우드에서 Multi-Factor Authentication과 함께 사용할 수 있는 경우와 Multi-Factor Authentication 서버와 함께 사용할 수 있는 경우에 대한 기능 비교입니다.

| 기능 | 클라우드의 MFA | MFA 서버  |
| --- |:---:|:---:|
| 두 번째 단계로 모바일 앱 알림 | ● | ● |
| 두 번째 단계로 모바일 앱 확인 코드 | ● | ● |
| 두 번째 단계로 전화 통화 | ● | ● |
| 두 번째 단계로 단방향 SMS | ● | ● |
| 두 번째 단계로 하드웨어 토큰 | ● (공개 미리 보기) | ● |
| MFA를 지원하지 않는 Office 365 클라이언트에 대한 앱 암호 | ● | |
| 인증 방법에 대한 관리자 제어 | ● | ● |
| PIN 모드 | | ● |
| 사기 행위 경고 | ● | ● |
| MFA 보고서 | ● | ● |
| 일회성 바이패스 | | ● |
| 전화 통화에 대한 사용자 지정 인사말 | ● | ● |
| 전화 통화에 대한 사용자 지정 가능한 발신자 번호 | ● | ● |
| 신뢰할 수 있는 IP | ● | ● |
| 신뢰할 수 있는 디바이스에 대한 MFA 기억 | ● | |
| 조건부 액세스 | ● | ● |
| 캐시 |  | ● |

## <a name="next-steps"></a>다음 단계

이제 클라우드에서 Azure Multi-Factor Authentication과 MFA 서버 온-프레미스 간의 차이점을 이해했으므로 Azure Multi-Factor Authentication을 설정하고 사용해보겠습니다. **시나리오를 나타내는 아이콘 선택**

<center>

[![클라우드의 MFA](./media/concept-mfa-whichversion/cloud2.png)](howto-mfa-getstarted.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [![MFA 서버](./media/concept-mfa-whichversion/server2.png)](howto-mfaserver-deploy.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </center>

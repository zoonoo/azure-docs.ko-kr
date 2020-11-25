---
title: Azure Active Directory에서 지속적인 액세스 평가를 사용 하 여 복원 력 빌드
description: CAE 사용에 대 한 설계자 및 IT 관리자 가이드
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ad36c2a7f47948d9362b85e78355e6046cda703
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919871"
---
# <a name="build-resilience-by-using-continuous-access-evaluation"></a>연속 액세스 평가를 사용 하 여 복원 력 빌드

CAE ( [연속 액세스 평가](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation) )를 사용 하면 Azure AD 응용 프로그램에서 중요 한 이벤트를 구독 하 여 평가 하 고 적용할 수 있습니다. 여기에는 다음 이벤트의 평가가 포함 됩니다.

* 삭제 되거나 사용 하지 않도록 설정 된 사용자 계정

* 사용자의 암호가 변경 되었습니다.

* 사용자에 대해 MFA를 사용 하도록 설정 합니다.

* 관리자가 토큰을 명시적으로 해지 합니다.

* 높은 수준의 사용자 위험이 검색 되었습니다.

따라서 응용 프로그램에서는 다음 다이어그램에 표시 된 것 처럼 Azure AD에서 신호를 받은 이벤트를 기준으로 만료 되지 않은 토큰을 거부할 수 있습니다.

![CAE의 conceptualiagram](./media/resilience-with-cae/admin-resilience-continuous-access-evaluation.png)

## <a name="how-does-cae-help"></a>CAE는 어떻게 도움이 되나요?

이 메커니즘을 사용 하면 Azure AD에서 수명이 긴 토큰을 발급할 수 있으며, 응용 프로그램을 사용 하 여 필요한 경우에만 액세스를 취소 하 고 다시 인증을 강제로 수행할 수 있습니다. 이 패턴의 순 결과는 토큰을 획득 하기 위한 호출 수가 적기 때문에 종단 간 흐름이 복원 력이 향상 됩니다. 

CAE를 사용 하려면 서비스와 클라이언트 둘 다 CAE 가능 해야 합니다. Microsoft 365 서비스 (예: Exchange Online, 팀 및 SharePoint Online)는 CAE을 지원 합니다. 클라이언트 쪽에서 이러한 Office 365 서비스 (예: Outlook Web App) 및 특정 버전의 Office 365 native client를 사용 하는 브라우저 기반 환경을 CAE 수 있습니다. Microsoft 클라우드 서비스를 더 많이 CAE 수 있습니다.

Microsoft는 타사 응용 프로그램에서이 기능을 사용할 수 있는 [표준을](https://openid.net/wg/sse/) 빌드하기 위해 업계와 협력 하 고 있습니다. CAE 지원 되는 응용 프로그램을 개발할 수도 있습니다. 자세한 내용은 응용 프로그램에서 복원 력을 빌드하는 방법을 참조 하세요.

## <a name="how-do-i-implement-cae"></a>CAE 구현 어떻게 할까요??

* Azure AD 보안 구성에서 [CAE을 사용 하도록 설정](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation) 합니다.

* 조직에서 Microsoft Office 네이티브 응용 프로그램의 [호환 가능한 버전](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-continuous-access-evaluation) 을 사용 하 고 있는지 확인 합니다.

* [재인증 프롬프트를 최적화](https://docs.microsoft.com/azure/active-directory/authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime)합니다.

 
## <a name="next-steps"></a>다음 단계
관리자 및 설계자를 위한 복원 력 리소스
 
* [자격 증명 관리를 사용 하 여 복원 력 빌드](resilience-in-credentials.md)

* [장치 상태를 사용 하 여 복원 력 빌드](resilience-with-device-states.md)

* [외부 사용자 인증에서 복원 력 빌드](resilience-b2b-authentication.md)

* [하이브리드 인증의 빌드 복원 력](resilience-in-hybrid.md)

* [응용 프로그램 프록시를 사용 하 여 응용 프로그램 액세스에서 복원 력 빌드](resilience-on-premises-access.md)

개발자 용 복원 력 리소스

* [응용 프로그램의 빌드 IAM 복원 력](resilience-app-development-overview.md)

* [CIAM 시스템에서 복원 력 빌드](resilience-b2c.md)

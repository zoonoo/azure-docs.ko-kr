---
title: Azure Active Directory에서 지속적인 액세스 평가를 사용하여 복원력 빌드
description: CAE 사용에 대한 설계자 및 IT 관리자 가이드
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
ms.openlocfilehash: 2d792c1eb0a85f2a898d1dc5c63047a4553f13b0
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106775"
---
# <a name="build-resilience-by-using-continuous-access-evaluation"></a>지속적인 액세스 권한 평가를 사용하여 복원력 빌드

CAE([지속적인 액세스 권한 평가](../conditional-access/concept-continuous-access-evaluation.md))를 사용하면 Azure AD 응용 프로그램에서 중요한 이벤트를 구독하여 평가하고 적용할 수 있습니다. 여기에는 다음 이벤트의 평가가 포함됩니다.

* 삭제되거나 사용하지 않도록 설정된 사용자 계정

* 사용자의 암호가 변경되었습니다

* 사용자에 대해 MFA를 사용하도록 설정합니다.

* 관리자가 토큰을 명시적으로 해지합니다.

* 높은 수준의 사용자 위험이 검색되었습니다.

따라서 응용 프로그램에서는 다음 다이어그램에 표시된 것 처럼 Azure AD에서 신호를 받은 이벤트를 기준으로 만료되지 않은 토큰을 거부할 수 있습니다.

![CAE 개념도](./media/resilience-with-cae/admin-resilience-continuous-access-evaluation.png)

## <a name="how-does-cae-help"></a>CAE는 어떻게 도움을 주나요?

이 메커니즘을 사용하면 Azure AD에서 수명이 긴 토큰을 발급할 수 있으며, 응용 프로그램을 사용하여 필요한 경우에만 액세스를 취소하고 다시 인증을 강제로 수행할 수 있습니다. 이 패턴의 순 결과는 토큰을 획득하기 위한 호출 수가 적기 때문에 엔드투엔드 흐름의 복원력이 향상되는 것입니다. 

CAE를 사용하려면 서비스와 클라이언트 둘 다 CAE를 지원해야 합니다. Microsoft 365 서비스(예: Exchange Online, Teams, SharePoint Online)는 CAE을 지원합니다. 클라이언트 쪽에서, 이러한 Office 365 서비스(예: Outlook Web App) 및 특정 버전의 Office 365 네이티브 클라이언트를 사용하는 브라우저 기반 환경이 CAE를 지원합니다. 더 많은 Microsoft 클라우드 서비스가 CAE를 지원할 것입니다.

Microsoft는 제3자 응용 프로그램에서 이 기능을 사용할 수 있는 [표준](https://openid.net/wg/sse/)을 빌드하기 위해 업계와 협력하고 있습니다. CAE 지원되는 응용 프로그램을 개발할 수도 있습니다. 자세한 내용은 응용 프로그램에서 복원력을 빌드하는 방법을 참조하세요.

## <a name="how-do-i-implement-cae"></a>어떻게 CAE를 구현하나요?

* [CAE 지원 API를 사용하도록 코드를 업데이트](../develop/app-resilience-continuous-access-evaluation.md)합니다.

* Azure AD 보안 구성에서 [CAE을 사용하도록 설정](../conditional-access/concept-continuous-access-evaluation.md)합니다.

* 조직에서 Microsoft Office 네이티브 응용 프로그램의 [호환 가능한 버전](../conditional-access/concept-continuous-access-evaluation.md)을 사용하고 있는지 확인합니다.

* [재인증 프롬프트를 최적화](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)합니다.

 
## <a name="next-steps"></a>다음 단계
관리자 및 설계자를 위한 복원력 리소스
 
* [자격 증명 관리를 사용하여 복원력 빌드](resilience-in-credentials.md)

* [장치 상태를 사용하여 복원력 빌드](resilience-with-device-states.md)

* [외부 사용자 인증에서 복원력 빌드](resilience-b2b-authentication.md)

* [하이브리드 인증에서 복원력 빌드](resilience-in-hybrid.md)

* [응용 프로그램 프록시를 사용하여 응용 프로그램 액세스에서 복원력 빌드](resilience-on-premises-access.md)

개발자를 위한 복원력 리소스

* [응용 프로그램에서 IAM 복원력 빌드](resilience-app-development-overview.md)

* [CIAM 시스템에서 복원력 빌드](resilience-b2c.md)

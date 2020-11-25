---
title: Azure Active Directory 장치 상태를 사용 하 여 복원 력 빌드
description: 장치 상태를 사용 하 여 복원 력을 구축 하는 설계자 및 IT 관리자를 위한 가이드
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
ms.openlocfilehash: 4075cb71cb4dae7ac506c16e3987070cfa10bb09
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919859"
---
# <a name="build-resilience-with-device-states"></a>장치 상태를 사용 하 여 복원 력 빌드

Azure AD에서 [장치 상태](../devices/overview.md) 를 사용 하도록 설정 하면 관리자는 장치 상태에 따라 응용 프로그램에 대 한 액세스를 제어 하는 [조건부 액세스 정책을](../conditional-access/overview.md) 작성할 수 있습니다. 장치의 추가 혜택은 리소스에 대 한 액세스를 위한 강력한 인증 요구 사항을 충족 하므로 추가 MFA 인증 요청을 줄이고 복원 력을 개선 한다는 것입니다. 

다음 순서도는 장치 상태를 사용 하도록 설정 하는 Azure AD에서 장치를 등록 하는 다양 한 방법을 보여 줍니다. 조직에서 두 개 이상을 사용할 수 있습니다.

![장치 상태 선택에 대 한 흐름도](./media/resilience-with-device-states/admin-resilience-devices.png)

[장치 상태](../devices/overview.md)를 사용 하는 경우 사용자는 대부분의 경우 [주 새로 고침 토큰](../devices/concept-primary-refresh-token.md) (PRT)을 통해 리소스에 Single Sign-On 합니다. PRT는 사용자 및 장치에 대 한 클레임을 포함 하며 장치에서 응용 프로그램에 액세스 하는 인증 토큰을 가져오는 데 사용할 수 있습니다. PRT는 14 일 동안 유효 하며 사용자가 장치를 적극적으로 사용 하 여 사용자에 게 복원 력을 제공 하는 동안 계속 갱신 됩니다. 또한 PRT는 여러 가지 방법으로 multi-factor authentication 클레임을 가져올 수 있습니다. 자세한 내용은 [언제 PRT가 MFA 클레임을 가져올 때](../devices/concept-primary-refresh-token.md)를 참조 하세요.

## <a name="how-do-device-states-help"></a>장치 상태는 어떻게 도움이 되나요?

응용 프로그램에 대 한 액세스를 요청 하는 데 사용 되는 PRT는 Azure AD에서 신뢰할 수 있습니다. 관리자가 장치 기반 컨트롤이 나 Multi-factor authentication 제어를 요구 하는 정책을 만들 때 Multi-factor authentication을 시도 하지 않고 장치 상태를 통해 정책 요구 사항을 충족할 수 있습니다. 사용자에 게 동일한 장치에서 추가 Multi-factor authentication 프롬프트가 표시 되지 않습니다. 이렇게 하면 Azure MFA 서비스 중단 또는 로컬 통신 공급자와 같은 종속성에 대 한 복원 력이 높아집니다.

## <a name="how-do-i-implement-device-states"></a>장치 상태를 구현 어떻게 할까요??

* 회사 소유의 Windows 장치에 대 한 [하이브리드 AZURE ad](../devices/hybrid-azuread-join-plan.md) 조인 및 [azure ad 조인을](../devices/azureadjoin-plan.md) 사용 하도록 설정 하 고 가능 하면 연결 해야 합니다. 가능 하지 않은 경우 등록 해야 합니다.

  조직에 이전 버전의 Windows가 있는 경우 해당 장치를 업그레이드 하 여 Windows 10을 사용 합니다.

* 사용자 브라우저 액세스 표준화-PRT를 사용 하는 웹 응용 프로그램에 대해 원활한 SSO를 사용 하도록 설정 하는 [지원 되](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) 는 [확장과](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb) 함께 [Microsoft Edge](https://docs.microsoft.com/deployedge/microsoft-edge-security-identity) 또는 Google Chrome을 사용 합니다.

* 개인 또는 회사 소유의 iOS 및 Android 장치에는 [Microsoft Authenticator 앱](../user-help/user-help-auth-app-overview.md)을 배포 합니다. Multi-factor authentication 및 암호 없는 로그인 기능 외에도, Microsoft Authenticator 앱은 최종 사용자에 게 더 낮은 인증 프롬프트를 사용 하 여 조정 된 [인증](../develop/brokered-auth.md) 을 통해 네이티브 응용 프로그램에서 single sign-on을 사용 하도록 설정 합니다.

* 개인 또는 회사 소유의 iOS 및 Android 장치에 대해 [모바일 응용 프로그램 관리](https://docs.microsoft.com/mem/intune/apps/app-management.md) 를 사용 하 여 인증 요청 수를 줄이고 회사 리소스에 안전 하 게 액세스 합니다. 

* [Apple 장치에 대 한 Microsoft Enterprise SSO 플러그 인 (미리 보기)을 사용](../develop/apple-sso-plugin.md)합니다. 그러면 장치를 등록 하 고 브라우저 및 네이티브 Azure AD 응용 프로그램에서 SSO를 제공 합니다. 

## <a name="next-steps"></a>다음 단계
관리자 및 설계자를 위한 복원 력 리소스
 
* [자격 증명 관리를 사용 하 여 복원 력 빌드](resilience-in-credentials.md)

* [CAE (연속 액세스 평가)를 사용 하 여 복원 력 빌드](resilience-with-continuous-access-evaluation.md)

* [외부 사용자 인증에서 복원 력 빌드](resilience-b2b-authentication.md)

* [하이브리드 인증의 빌드 복원 력](resilience-in-hybrid.md)

* [응용 프로그램 프록시를 사용 하 여 응용 프로그램 액세스에서 복원 력 빌드](resilience-on-premises-access.md)


개발자 용 복원 력 리소스

* [응용 프로그램의 빌드 IAM 복원 력](resilience-app-development-overview.md)

* [CIAM 시스템에서 복원 력 빌드](resilience-b2c.md)

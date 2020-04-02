---
title: 공유 장치 모드 개요
titleSuffix: Microsoft identity platform | Azure
description: 공유 장치 모드에 대해 알아보고 Firstline 작업자의 장치 공유를 활성화합니다.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 514782d62f117af5bfff4a5d2b3354c4e263eece
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550241"
---
# <a name="overview-of-shared-device-mode"></a>공유 장치 모드 개요

공유 장치 모드는 Firstline 작업자를 지원하는 응용 프로그램을 빌드하고 배포된 장치에서 공유 장치 모드를 사용하도록 설정할 수 있는 Azure Active Directory의 기능입니다.

> [!NOTE]
> 이 기능은 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="what-are-firstline-workers"></a>일선 근로자는 무엇입니까?

Firstline 근로자는 소매 직원, 유지 보수 및 현장 에이전트, 의료 인력 및 컴퓨터 앞에 앉아 있지 않거나 공동 작업을 위해 회사 전자 메일을 사용하지 않는 기타 사용자입니다. 다음 섹션에서는 Firstline 근로자를 지원하는 데 따르는 측면과 과제에 대해 소개하고, 그 다음에는 조직의 일선 작업자가 응용 프로그램을 사용할 수 있도록 하는 Microsoft에서 제공하는 기능을 소개합니다.

### <a name="challenges-of-supporting-firstline-workers"></a>일선 근로자 지원의 과제

Firstline Worker 워크플로를 사용하도록 설정하면 일반적으로 일반적인 정보 근로자가 제시하지 않는 과제가 포함됩니다. 이러한 과제에는 높은 이직률과 조직의 핵심 생산성 도구에 대한 익숙하지 않은 문제가 포함될 수 있습니다. 일선 근로자에게 권한을 부여하기 위해 조직은 다양한 전략을 채택하고 있습니다. 일부는 직원들이 개인 휴대 전화에서 비즈니스 앱을 사용하는 BYOD (BYOD) 전략을 채택하고 있으며 다른 일부는 직원에게 iPad 또는 Android 태블릿과 같은 공유 장치를 제공합니다.

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>한 사용자를 위해 설계된 장치에서 여러 사용자 지원

iOS 또는 Android를 실행하는 모바일 장치는 단일 사용자를 위해 설계되었기 때문에 대부분의 응용 프로그램은 단일 사용자가 사용할 수 있도록 환경을 최적화합니다. 이 최적화된 환경의 일부는 응용 프로그램 간에 단일 사인온을 활성화하고 사용자가 장치에서 로그인상태를 유지하는 것을 의미합니다. 사용자가 응용 프로그램에서 계정을 제거하면 앱은 일반적으로 보안 관련 이벤트로 간주하지 않습니다. 대부분의 앱은 빠른 로그인을 위해 사용자의 자격 증명을 유지합니다. 모바일 장치에서 응용 프로그램을 삭제한 다음 다시 설치한 경우에도 로그인한 상태를 확인할 수 있습니다.

### <a name="global-sign-in-and-sign-out-sso"></a>글로벌 로그인 및 사인아웃(SSO)

조직의 직원이 해당 직원이 공유하는 장치 풀에서 해당 앱을 사용할 수 있도록 하려면 개발자는 반대의 환경을 사용하도록 설정해야 합니다. 직원은 풀에서 장치를 선택하고 교대 근무 기간 동안 "자신의 장치를 만들"라는 단일 제스처를 수행할 수 있어야 합니다. 교대 근무가 끝나면 다른 제스처를 수행하여 장치에서 전역적으로 로그아웃할 수 있으며 모든 개인 및 회사 정보가 제거되어 장치 풀로 반환할 수 있습니다. 또한 직원이 로그아웃을 잊어버린 경우 교대 근무가 끝날 때 및/또는 비활성 기간 이후에 자동으로 로그아웃해야 합니다.

Azure Active Directory를 사용하면 **공유 장치 모드라는**기능을 사용하여 이러한 시나리오를 사용할 수 있습니다.

## <a name="introducing-shared-device-mode"></a>공유 장치 모드 도입

앞서 언급했듯이 공유 장치 모드는 다음과 같은 기능을 사용할 수 있는 Azure Active Directory의 기능입니다.

* 일선 작업자를 지원하는 응용 프로그램 빌드
* 퍼스트라인 작업자에게 장치를 배포하고 공유 장치 모드를 켜십시오.

### <a name="build-applications-that-support-firstline-workers"></a>일선 작업자를 지원하는 응용 프로그램 빌드

Microsoft 인증 라이브러리(MSAL) 및 [Microsoft 인증자 앱을](../user-help/user-help-auth-app-overview.md) 사용하여 *공유 장치 모드라는*장치 상태를 활성화하여 응용 프로그램에서 Firstline 작업자를 지원할 수 있습니다. 장치가 공유 장치 모드에 있는 경우 Microsoft는 응용 프로그램에 장치의 사용자 상태에 따라 동작을 수정하여 사용자 데이터를 보호할 수 있는 정보를 제공합니다.

지원되는 기능은 다음과 같습니다.

* 지원되는 모든 응용 프로그램을 통해 **사용자 장치 전체에서 로그인합니다.**
* 지원되는 모든 응용 프로그램을 통해 **사용자 장치 전체로 로그아웃합니다.**
* **장치의 상태를 쿼리하여** 응용 프로그램이 공유 장치 모드에 있는 장치에 있는지 확인합니다.
* 장치에서 **사용자의 장치 상태를 쿼리하여** 응용 프로그램을 마지막으로 사용한 이후 변경된 항목이 있는지 확인합니다.

공유 장치 모드를 지원하는 것은 응용 프로그램에 대한 보안 향상 및 기능 업그레이드로 간주되어야 하며 의료 및 금융과 같이 규제가 엄격한 환경에서 채택을 늘리는 데 도움이 될 수 있습니다.

사용자는 사용자의 데이터가 다른 사용자에게 유출되지 않도록 사용자에 의존합니다. 장치 공유 모드는 관리해야 하는 변경이 발생했음을 응용 프로그램에 나타내는 유용한 신호를 제공합니다. 응용 프로그램은 앱을 사용할 때마다 장치의 사용자 상태를 확인하여 이전 사용자의 데이터를 지운 다. 여기에는 멀티태스킹에서 백그라운드에서 다시 로드되는 경우가 포함됩니다. 사용자 변경 시 이전 사용자의 데이터가 모두 지워지고 응용 프로그램에 표시되는 캐시된 데이터가 모두 제거되었는지 확인해야 합니다. 앱에 공유 장치 모드 기능을 추가한 후 항상 철저한 보안 검토 프로세스를 수행하는 것이 좋습니다.

공유 장치 모드를 지원하도록 응용 프로그램을 수정하는 방법에 대한 자세한 내용은 이 문서의 끝에 있는 [다음 단계](#next-steps) 섹션을 참조하십시오.

### <a name="deploy-devices-to-firstline-workers-and-turn-on-shared-device-mode"></a>퍼스트라인 작업자에게 장치를 배포하고 공유 장치 모드를 켜십시오.

응용 프로그램이 공유 장치 모드를 지원하고 필요한 데이터 및 보안 변경 사항을 포함하면 Firstline 작업자가 사용할 수 있는 것으로 보급할 수 있습니다.

조직의 장치 관리자는 Microsoft Intune과 같은 모바일 장치 관리(MDM) 솔루션을 통해 해당 장치 및 응용 프로그램을 매장 및 직장에 배포할 수 있습니다. 프로비저닝 프로세스의 일부는 장치를 *공유 장치로*표시하는 것입니다. 관리자는 [Microsoft 인증자 앱을](../user-help/user-help-auth-app-overview.md) 배포하고 구성 매개 변수를 통해 공유 장치 모드를 설정하여 공유 장치 모드를 구성합니다. 이러한 단계를 수행한 후 공유 장치 모드를 지원하는 모든 응용 프로그램은 Microsoft 인증자 응용 프로그램을 사용하여 사용자 상태를 관리하고 장치 및 조직에 대한 보안 기능을 제공합니다.

## <a name="next-steps"></a>다음 단계

공유 장치 모드에 대한 iOS 및 Android 플랫폼을 지원합니다. 플랫폼이 응용 프로그램에서 Firstline 근로자 지원을 시작하려면 아래 설명서를 검토하십시오.

* [iOS용 공유 장치 모드 지원](msal-ios-shared-devices.md)
* [안드로이드에 대한 공유 장치 모드 지원](msal-android-shared-devices.md)

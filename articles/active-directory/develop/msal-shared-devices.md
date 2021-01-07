---
title: 공유 장치 모드 개요
titleSuffix: Microsoft identity platform | Azure
description: Firstline 작업자에 대해 장치 공유를 사용 하도록 설정 하는 공유 장치 모드에 대해 알아봅니다.
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
ms.openlocfilehash: 3cd7074467332f89d4d6c60830be34f4e2a638c1
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94562086"
---
# <a name="overview-of-shared-device-mode"></a>공유 장치 모드 개요

공유 장치 모드는 Firstline 작업자를 지 원하는 응용 프로그램을 작성 하 고 배포 된 장치에서 공유 장치 모드를 사용 하도록 설정 하는 Azure Active Directory의 기능입니다.

>[!IMPORTANT]
> 이 기능 [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="what-are-firstline-workers"></a>Firstline 작업자 란?

Firstline 작업자는 컴퓨터 앞에 앉아 있지 않거나 회사 전자 메일을 사용 하 여 공동 작업할 수 없는 소매 직원, 유지 관리 및 현장 에이전트, 의료 담당자 및 기타 사용자입니다. 다음 섹션에서는 Firstline Worker를 지 원하는 데 필요한 측면과 과제를 소개 하 고, 조직의 Firstline Worker에서 응용 프로그램을 사용할 수 있도록 하는 Microsoft에서 제공 하는 기능에 대해 소개 합니다.

### <a name="challenges-of-supporting-firstline-workers"></a>Firstline Worker 지원 문제

Firstline Worker 워크플로를 사용 하도록 설정 하면 일반적으로 일반적인 정보 근로자가 제공 하지 않는 과제가 포함 됩니다. 이러한 과제는 높은 회전율을 포함 하 고 조직의 핵심 생산성 도구를 사용 하 여 더 익숙해질 수 있습니다. 여러 가지 전략을 채택 하는 데는 여러 가지 전략을 채택 하 고 있습니다. 일부는 직원이 개인 휴대폰에서 비즈니스 앱을 사용 하는 BYOD (사용자 소유-장치) 전략을 채택 하는 반면, 다른 장치는 직원에 게 Ipad 또는 Android 태블릿과 같은 공유 장치를 제공 합니다.

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>한 사용자 용으로 설계 된 장치에서 여러 사용자 지원

IOS 또는 Android를 실행 하는 모바일 장치는 단일 사용자 용으로 설계 되었기 때문에 대부분의 응용 프로그램은 단일 사용자가 사용할 수 있도록 환경을 최적화 합니다. 이러한 최적화 된 환경의 일부는 응용 프로그램 전체에서 Single Sign-On 하 고 사용자의 장치에 로그인 된 상태를 유지 하는 것을 의미 합니다. 사용자가 응용 프로그램에서 해당 계정을 제거 하는 경우 일반적으로 앱은 보안 관련 이벤트를 고려 하지 않습니다. 많은 앱이 빠른 로그인을 위해 사용자의 자격 증명을 유지 합니다. 사용자가 모바일 장치에서 응용 프로그램을 삭제 한 다음 다시 설치 하 여 로그인 한 경우에만이 문제가 발생 했을 수도 있습니다.

### <a name="global-sign-in-and-sign-out-sso"></a>전역 로그인 및 로그 아웃 (SSO)

조직의 직원이 해당 직원 들이 공유 하는 장치 풀에서 해당 앱을 사용할 수 있도록 하려면 개발자가 반대 환경을 사용 하도록 설정 해야 합니다. 직원은 풀에서 장치를 선택 하 고, 이동 기간 동안 단일 제스처를 수행 하 여 "자신 있게" 할 수 있습니다. 이동이 끝나면 다른 제스처를 수행 하 여 장치에서 전체적으로 로그 아웃 하 고 모든 개인 및 회사 정보가 제거 되어 장치 풀로 돌아올 수 있습니다. 또한 직원이 로그 아웃 하는 것을 잊은 경우에는 해당 장치는 교대조의 끝 이나 비활성 기간 후에 자동으로 로그 아웃 됩니다.

Azure Active Directory **공유 장치 모드** 라는 기능을 사용 하 여 이러한 시나리오를 가능 하 게 합니다.

## <a name="introducing-shared-device-mode"></a>공유 장치 모드 소개

앞서 언급 했 듯이 공유 장치 모드는 다음 작업을 수행할 수 있는 Azure Active Directory의 기능입니다.

* Firstline Worker를 지 원하는 응용 프로그램 빌드
* Firstline 작업자에 장치를 배포 하 고 공유 장치 모드 설정

### <a name="build-applications-that-support-firstline-workers"></a>Firstline Worker를 지 원하는 응용 프로그램 빌드

MSAL (Microsoft 인증 라이브러리) 및 [Microsoft Authenticator 앱](../user-help/user-help-auth-app-overview.md) 을 사용 하 여 *공유 장치 모드* 라는 장치 상태를 사용 하도록 설정 하 여 응용 프로그램의 firstline worker를 지원할 수 있습니다. 장치가 공유 장치 모드에 있는 경우 Microsoft는 장치에서 사용자의 상태에 따라 해당 동작을 수정 하 여 사용자 데이터를 보호 하는 정보를 응용 프로그램에 제공 합니다.

지원 되는 기능은 다음과 같습니다.

* 지원 되는 응용 프로그램 **을 통해 사용자 장치 전체에 로그인** 합니다.
* 지원 되는 응용 프로그램 **을 통해 사용자 장치 전체에 로그 아웃** 합니다.
* **장치의 상태를 쿼리하여** 응용 프로그램이 공유 장치 모드에 있는 장치에 있는지 확인 합니다.
* 장치에서 **사용자의 장치 상태를 쿼리하여** 응용 프로그램을 마지막으로 사용한 이후 변경 된 내용이 있는지 확인 합니다.

공유 장치 모드를 지 원하는 것은 응용 프로그램에 대 한 보안 향상 및 기능 업그레이드를 모두 고려해 야 하며, 의료 및 재무와 같은 매우 규제 된 환경에서 도입을 향상 시킬 수 있습니다.

사용자는 자신의 데이터가 다른 사용자에 게 유출 되지 않도록 하는 데 의존 합니다. 공유 장치 모드는 응용 프로그램에 관리 해야 하는 변경 내용이 발생 했음을 나타내는 데 유용한 신호를 제공 합니다. 응용 프로그램은 앱을 사용할 때마다 장치에서 사용자의 상태를 확인 하 여 이전 사용자의 데이터를 지우는 역할을 담당 합니다. 이는 다중 태스킹의 백그라운드에서 다시 로드 되는 경우를 포함 합니다. 사용자가 변경 하는 경우 이전 사용자의 데이터를 모두 지우고 응용 프로그램에 표시 되는 캐시 된 데이터가 모두 제거 되었는지 확인 해야 합니다. 앱에 공유 장치 모드 기능을 추가한 후에는 항상 철저 한 보안 검토 프로세스를 수행 하는 것이 좋습니다.

공유 장치 모드를 지원 하도록 응용 프로그램을 수정 하는 방법에 대 한 자세한 내용은이 문서의 끝에 있는 [다음 단계](#next-steps) 섹션을 참조 하세요.

### <a name="deploy-devices-to-firstline-workers-and-turn-on-shared-device-mode"></a>Firstline 작업자에 장치를 배포 하 고 공유 장치 모드 설정

응용 프로그램이 공유 장치 모드를 지원 하 고 필요한 데이터 및 보안 변경 내용을 포함 하는 경우 Firstline Worker에서 사용할 수 있는 것으로 알릴 수 있습니다.

조직의 장치 관리자는 Microsoft Intune와 같은 MDM (모바일 장치 관리) 솔루션을 통해 장치와 응용 프로그램을 자신의 저장소에 배포 하 고 작업 영역 등에 수 있습니다. 프로 비전 프로세스의 일부로 장치를 *공유 장치로* 표시 하 고 있습니다. 관리자는 [Microsoft Authenticator 앱](../user-help/user-help-auth-app-overview.md) 을 배포 하 고 구성 매개 변수를 통해 공유 장치 모드를 설정 하 여 공유 장치 모드를 구성 합니다. 이러한 단계를 수행한 후에는 공유 장치 모드를 지 원하는 모든 응용 프로그램에서 Microsoft Authenticator 응용 프로그램을 사용 하 여 사용자 상태를 관리 하 고 장치와 조직의 보안 기능을 제공 합니다.

## <a name="next-steps"></a>다음 단계

공유 장치 모드에는 iOS 및 Android 플랫폼을 지원 합니다. 응용 프로그램에서 Firstline Worker 지원을 시작 하려면 아래 설명서를 검토 하세요.

* [IOS에 대 한 공유 장치 모드 지원](msal-ios-shared-devices.md)
* [Android 용 공유 장치 모드 지원](msal-android-shared-devices.md)

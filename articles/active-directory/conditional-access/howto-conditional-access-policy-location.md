---
title: 조건부 액세스 - 위치별 액세스 차단 - Azure Active Directory
description: IP 위치별 리소스에 대한 액세스를 차단하는 사용자 지정 조건부 액세스 정책 만들기
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34b29ceadaaf85e69d1214039fa1b563ed21a77d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295180"
---
# <a name="conditional-access-block-access-by-location"></a>조건부 액세스: 위치별 액세스 차단

조건부 액세스의 위치 조건을 사용하여 사용자의 네트워크 위치를 기반으로 클라우드 앱에 대한 액세스를 제어할 수 있습니다. 위치 조건은 일반적으로 조직에서 트래픽이 들어오지 않아야 한다는 것을 알고 있는 국가의 액세스를 차단하는 데 사용됩니다.

## <a name="define-locations"></a>위치 정의

1. **Azure 포털에** 글로벌 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. Azure **Active Directory** > **보안** > **조건부 액세스** > **명명된 위치로**이동합니다.
1. **새 위치를**선택합니다.
1. 사용자의 위치에 이름을 지정합니다.
1. 해당 위치 또는 **국가/지역을**구성하는 외부에서 액세스할 수 있는 특정 IPv4 주소 범위를 알고 있는 경우 **IP** 범위를 선택합니다.
   1. IP **범위를** 제공하거나 지정하려는 위치에 대한 **국가/지역을** 선택합니다.
      * 국가/지역을 선택하는 경우 선택적으로 알 수 없는 지역을 포함하도록 선택할 수 있습니다.
1. **저장** 선택

조건부 액세스의 위치 조건에 대한 자세한 내용은 문서에서 찾을 수 [있습니다.](location-condition.md)

## <a name="create-a-conditional-access-policy"></a>조건부 액세스 정책 만들기

1. **Azure 포털에** 글로벌 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. Azure **Active 디렉터리** > **보안** > **조건부 액세스로**이동합니다.
1. **새 정책**을 선택합니다.
1. 정책 이름을 지정합니다. 조직에서 정책 이름에 대해 의미 있는 표준을 만드는 것이 좋습니다.
1. 할당에서 사용자 **및 그룹을** **선택합니다.**
   1. **포함**에서 **모든 사용자를**선택합니다.
   1. **완료를 선택합니다.**
1. **클라우드 앱 또는 작업** > **에서 포함**, 모든 클라우드 **앱을**선택하고 **완료**를 선택합니다.
1. **조건** > **위치**.
   1. **구성을** **예로** 설정
   1. 선택한 **위치** **포함**
   1. 조직에 대해 만든 차단된 위치를 선택합니다.
   1. 완료 **완료 선택을** > **클릭합니다.** > **Done**
1. **조건 클라이언트** > **앱(미리 보기)에서** **구성을** **예로**설정하고 **완료를**선택합니다.
1. **액세스 제어 에서** > **차단**및 선택 **을**선택합니다.
1. 설정을 확인하고 **정책 활성화를** **설정 합니다.**
1. 정책을 활성화하려면 **만들기를** 선택합니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 보고서 전용 모드를 사용하여 영향 확인](howto-conditional-access-report-only.md)

[조건부 액세스 What If 도구를 사용하여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)

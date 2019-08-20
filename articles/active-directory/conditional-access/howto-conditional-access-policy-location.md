---
title: 조건부 액세스-위치별 액세스 차단-Azure Active Directory
description: IP 위치로 리소스에 대 한 액세스를 차단 하는 사용자 지정 조건부 액세스 정책 만들기
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b31fc3aed0d412646d9924e87170dffcd5145409
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576603"
---
# <a name="conditional-access-block-access-by-location"></a>조건부 액세스: 위치별 액세스 차단

조건부 액세스의 위치 조건을 사용 하 여 사용자의 네트워크 위치를 기준으로 클라우드 앱에 대 한 액세스를 제어할 수 있습니다. 위치 조건은 조직에서 트래픽이 도착 하지 않아야 하는 것으로 알고 있는 국가에서의 액세스를 차단 하는 데 주로 사용 됩니다.

## <a name="define-locations"></a>위치 정의

1. 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자 권한으로 **Azure Portal** 에 로그인 합니다.
1. **Azure Active Directory** > **조건부 액세스**로 이동 합니다.
1. **새 위치**를 선택 합니다.
1. 위치에 이름을 지정 합니다.
1. 해당 위치나 **국가/지역을**구성 하는 외부에서 액세스할 수 있는 특정 IPv4 주소 범위를 알고 있는 경우 **IP 범위** 를 선택 합니다.
   1. **IP 범위** 를 제공 하거나 지정 하는 위치에 대 한 **국가/지역을** 선택 합니다.
      * 국가/지역을 선택한 경우에는 알 수 없는 영역을 포함 하도록 선택할 수 있습니다.
1. **저장** 선택

조건부 액세스의 위치 조건에 대 한 자세한 내용은 [Azure Active Directory 조건부 액세스의 위치 조건](location-condition.md) 문서에서 찾을 수 있습니다.

## <a name="create-a-conditional-access-policy"></a>조건부 액세스 정책 만들기

1. 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자 권한으로 **Azure Portal** 에 로그인 합니다.
1. **Azure Active Directory** > **조건부 액세스**로 이동 합니다.
1. **새 정책**을 선택합니다.
1. 정책에 이름을 지정 합니다. 조직에서 정책 이름에 대해 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당**아래에서 **사용자 및 그룹** 을 선택 합니다.
   1. **포함**아래에서 **모든 사용자**를 선택 합니다.
   1. **완료** 를 선택합니다.
1. **클라우드 앱 또는 작업** > 에**는** **모든 클라우드 앱**을 선택 하 고 **완료**를 선택 합니다.
1. **조건** > **위치**에 있습니다.
   1. **구성** 을 **예** 로 설정 합니다.
   1. **선택한 위치** 선택 **포함**
   1. 조직에 대해 만든 차단 된 위치를 선택 합니다.
   1. 완료 **선택** > 을 > 클릭 합니다.
1. **액세스 제어** > **블록**아래에서 **선택**을 선택 합니다.
1. 설정을 확인 하 고 **정책 사용** 을 **켜기**로 설정 합니다.
1. 만들기 를 선택 하 여 정책을 사용 하도록 설정 합니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 What If 도구를 사용 하 여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)

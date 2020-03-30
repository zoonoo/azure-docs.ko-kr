---
title: 내 로그인(미리 보기) 페이지에서 최근 로그인 활동 보기 및 검색 - Azure Active Directory | 마이크로 소프트 문서
description: 내 계정 포털의 내 로그인 페이지에서 최근 로그인 활동을 보고 검색하는 방법에 대한 자세한 내용입니다.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: curtand
ms.openlocfilehash: b68e7b517ddaa9b2aaef00cf87d5b6e63871654b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064022"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>내 로그인(미리 보기) 페이지에서 최근 로그인 활동 보기 및 검색

**내** 계정 포털의 내 로그인 페이지에서 최근 직장 또는 학교 계정 로그인 활동을 모두 볼 수 **있습니다.** 로그인 기록을 검토하면 다음을 확인하여 비정상적인 활동을 확인하는 데 도움이 됩니다.

- 누군가 가 암호를 추측하려고 하는 경우.

- 공격자가 계정에 로그인한 경우 및 위치.

- 공격자가 액세스하려고 시도한 앱입니다.

## <a name="view-your-recent-sign-in-activity"></a>최근 로그인 활동 보기

1. 회사 또는 학교 계정에 로그인한 다음, https://myprofile.microsoft.com/ 페이지로 이동합니다.

2. 왼쪽 탐색 창에서 **내 로그인(미리 보기)을** 선택하거나 **내 로그인(미리 보기)** 블록에서 최근 활동 링크 **검토를** 선택합니다.

    ![내 계정 페이지, 강조 표시된 최근 활동 링크 표시](media/my-account-portal/my-account-portal-sign-ins.png)

3. 각 로그인 항목을 확장하고 검토하여 각 항목을 인식할 수 있는지 확인합니다. 익숙하지 않은 로그인 항목을 찾으면 계정이 손상된 경우 계정을 보호하기 위해 암호를 변경하는 것이 좋습니다.

    ![확장 로그인 세부 정보가 포함된 최근 활동 페이지](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>성공적인 로그인이 표시되는 경우

자신의 활동을 정상으로 인식해야 합니다. 그러나 이상한 위치, 브라우저 또는 운영 체제에서 성공적인 로그인을 발견하면 공격자가 계정에 액세스권한을 얻었을 수 있습니다. 이 경우 즉시 암호를 변경 한 다음 [보안 정보](https://mysignins.microsoft.com/security-info) 페이지로 이동 하여 보안 설정을 업데이트 하는 것이 좋습니다.

잘못된 것을 결정하기 전에 가양성이 표시되지 않았는지 확인합니다(항목이 의심스럽지만 괜찮습니다). 예를 들어, 당사는 귀하의 IP 주소를 기반으로 대략적인 위치와 지도를 결정합니다. 모바일 네트워크는 때로는 먼 위치를 통해 트래픽을 라우팅하기 때문에 특히 파악하기가 어렵습니다. 따라서 워싱턴 주에서 모바일 장치를 사용하여 로그인한 경우 해당 위치에 캘리포니아에서 오는 로그인이 표시될 수 있습니다. 이 때문에, 우리는 강력하게 당신이 더 많은 세부 사항을 확인하는 것이 좋습니다, 단지 위치 넘어. 또한 운영 체제, 브라우저 및 앱도 모두 의미가 있는지 확인해야 합니다.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>로그인에 실패한 경우

세션 활동이 없는 로그인에 실패하면 기본 확인 방법(사용자 이름/암호)이 실패했습니다. 사용자 이름이나 암호를 잘못 입력했거나 공격자가 암호를 추측하려고 했다는 의미일 수도 있습니다. 공격자가 암호를 추측하는 데 실패했다고 생각되면 암호를 변경할 필요가 없지만 MFA(Azure 다단계 인증)에 등록하는 것이 좋습니다. MFA를 사용하면 해커가 결국 암호를 추측하더라도 계정에 액세스하는 것만으로는 충분하지 않습니다.

세션 활동 아래에 **추가 확인실패, 잘못된 코드라는**메모가 있는 로그인에 실패한 경우 기본 인증(사용자 이름/암호)이 성공했지만 MFA가 실패했습니다. 공격자인 경우 암호를 올바르게 추측했지만 MFA 챌린지를 통과할 수 없었습니다. 이 경우 공격자가 해당 부품을 올바르게 사용했기 때문에 암호를 계속 변경한 다음 [보안 정보](https://mysignins.microsoft.com/security-info) 페이지로 이동하여 보안 설정을 업데이트하는 것이 좋습니다.

## <a name="search-for-specific-sign-in-activity"></a>특정 로그인 활동 검색

사용 가능한 정보로 최근 로그인 활동을 검색할 수 있습니다. 예를 들어 운영 체제, 위치, 앱 등을 통해 최근 로그인 활동을 검색할 수 있습니다.

1. 최근 **활동 검토** 페이지에서 검색할 정보를 **검색** 모음에 입력합니다. 예를 들어 `My Account` 내 계정 앱에서 수집한 모든 활동을 검색하려면 입력합니다.

2. 검색을 시작하려면 **검색** 단추를 선택합니다.

    ![최근 활동 페이지, 강조 표시된 검색표시줄, 검색 버튼 및 결과 표시](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>다음 단계

최근 로그인 활동을 본 후 다음을 수행할 수 있습니다.

- [보안 정보를](user-help-security-info-overview.md)보거나 관리합니다.

- 연결된 [장치를](my-account-portal-devices-page.md)보거나 관리합니다.

- [조직을](my-account-portal-organizations-page.md)보거나 관리합니다.

- 조직에서 개인 정보 관련 데이터를 사용하는 방법을 [확인합니다.](my-account-portal-privacy-page.md)
